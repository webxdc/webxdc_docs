# Webxdc Specification

Webxdc is a fresh and still evolving way of running web apps in chat messengers. 
This document is for app developers and outlines the [webxdc API](#webxdc-api) and [`.xdc` file format](#webxdc-file-format). It also describes the constraints for a [messenger implementation](#messenger-implementation) when running webxdc apps. 


## Webxdc API

Webxdc apps are shared in a chat and each device runs its own instance on the recipients device when they click "Start". The apps are network-isolated but can share state via [`sendUpdate()`](#sendupdate) and [`setUpdateListener()`](#setupdatelistener).

Messenger implementations expose the API through a `webxdc.js` module. To activate the webxdc API you need to use a script reference for `webxdc.js` in your HTML5 app:

```html
<script src="webxdc.js"></script>
```

`webxdc.js` must not be added to your `.xdc` file as they are provided by the messenger. To simulate webxdc in a browser, use the `webxdc.js` file from [Hello](https://github.com/webxdc/hello).


### sendUpdate()

```js
window.webxdc.sendUpdate(update, descr);
```

- `update`: an object with the following properties:  
    - `update.payload`: any javascript primitive, array or object.
    - `update.info`: optional, short, informational message that will be added to the chat,
       e.g. "Alice voted" or "Bob scored 123 in MyGame".
       usually only one line of text is shown
       and if there are series of info messages, older ones may be dropped.
       use this option sparingly to not spam the chat.
    - `update.document`: optional, name of the document in edit,
       must not be used e.g. in games where the webxdc does not create documents
    - `update.summary`: optional, short text, shown beside the app icon;
       it is recommended to use some aggregated value,  e.g. "8 votes", "Highscore: 123"

- `descr`: short, human-readable description what this update is about.
  this is shown e.g. as a fallback text in an e-mail program.

All peers, including the sending one,
will receive the update by the callback given to [`setUpdateListener()`](#setupdatelistener).

There are situations where the user cannot send messages to a chat,
e.g. if the webxdc instance comes as a contact request or if the user has left a group.
In these cases, you can still call `sendUpdate()`,
however, the update won't be sent to other peers
and you won't get the update by [`setUpdateListener()`](#setupdatelistener).


### setUpdateListener()

```js
let promise = window.webxdc.setUpdateListener((update) => {}, serial);
```

With `setUpdateListener()` you define a callback that receives the updates
sent by [`sendUpdate()`](#sendupdate). The callback is called for updates sent by you or other peers.
The `serial` specifies the last serial that you know about (defaults to 0). 
The returned promise resolves when the listener has processed all the update messages known at the time when  `setUpdateListener` was called. 

Each `update` which is passed to the callback comes with the following properties: 

- `update.payload`: equals the payload given to [`sendUpdate()`](#sendupdate)

- `update.serial`: the serial number of this update.
  Serials are larger `0` and newer serials have higher numbers.
  There may be gaps in the serials
  and it is not guaranteed that the next serial is exactly incremented by one.

- `update.max_serial`: the maximum serial currently known.
  If `max_serial` equals `serial` this update is the last update (until new network messages arrive).

- `update.info`: optional, short, informational message (see [`sendUpdate()`](#sendupdate))

- `update.document`: optional, document name as set by the sender, (see [`sendUpdate()`](#sendupdate)),
  implementations show the document name e.g. beside the app icon or in the title bar

- `update.summary`: optional, short text, shown beside icon (see [`sendUpdate()`](#sendupdate))


### selfAddr

```js
window.webxdc.selfAddr
```

Email address of the current account.
Especially useful if you want to differentiate between different peers -
just send the address along with the payload,
and, if needed, compare the payload addresses against `selfAddr` later on.


### selfName

```js
window.webxdc.selfName
```

Name of the current account, as defined in settings.
If empty, this defaults to the peer's address.



## Other APIs and Tags Usage Hints

Webxdc apps run in a restricted environment, but the following practices are permitted:

- `localStorage`, `sessionStorage`, `indexedDB`
- `visibilitychange` events
- `window.navigator.language`
- internal links, such as `<a href="localfile.html">`
- `mailto` links, such as `<a href="mailto:addr@example.org?body=...">`
- `<meta name="viewport" ...>` is useful especially as webviews from different platforms have different defaults


### Discouraged Practises 

- `document.cookie` is known not to work on desktop and iOS—use `localStorage` instead
- `unload`, `beforeunload` and `pagehide` events are known not to work on iOS and are flaky on other systems
  (also partly discouraged by [mozilla](https://developer.mozilla.org/en-US/docs/Web/API/Window/unload_event))—use `visibilitychange` instead
- `<title>` and `document.title` is ignored by Webxdc;
  use the `name` property from `manifest.toml` instead
- the latest JavaScript features may not work on all webviews,
  you may want to transpile your code down to an older js version
  e.g. with <https://babeljs.io>
- `<a href="https://example.org/foo">` and other external links are blocked by definition;
  instead, embed content or use `mailto:` link to offer a way for contact
- `<input type="file">` is currently discouraged; this may change in future


## Webxdc File Format

- a Webxdc app is a **ZIP-file** with the extension `.xdc`
- the ZIP-file MUST use the default compression methods as of RFC 1950,
  this is "Deflate" or "Store"
- the ZIP-file MUST contain at least the file `index.html`
- the ZIP-file MAY contain a `manifest.toml` and `icon.png` or
  `icon.jpg` files
- if the webxdc app is started, `index.html` MUST be opened in a [restricted webview](spec.md#webview-constraints-for-running-apps) that only allows accessing 
  resources from the ZIP-file.

### The manifest.toml File

If the ZIP-file contains a `manifest.toml` in its root directory,
the following basic information MUST be read from it: 

```toml
name = "My App Name"
source_code_url = "https://example.org/orga/repo"
```

- `name` - The name of the webxdc app.
  If no name is set or if there is no manifest, the filename is used as the webxdc name.

- `source_code_url` - Optional URL where the source code of the webxdc and maybe other information can be found.
  Messenger implementors may make the url accessible via a "Help" menu in the webxdc window.


### Icon Files 

If the ZIP-root contains an `icon.png` or `icon.jpg`,
these files are used as the icon for the webxdc.
The icon should be a square at reasonable width/height.
Round corners, circle cut out etc. will be added by the implementations as needed;
do not add borders or shapes to the icon therefore.
If no icon is set, a default icon will be used.



## Messenger Implementation

### Webview Constraints for Running Apps 

When starting a web view for a webxdc app to run, messenger implementors:

- MUST deny all forms of internet access. If you don't do this
  unsuspecting users may leak data of their private interactions to outside third parties. 
  You do not need to offer "privacy" or "cookie" consent screens as 
  there is no way the app can transfer user data to anything outside the chat. 

- MUST allow unrestricted use of DOM storage (local storage, indexed db and co), 
  but make sure it is scoped to each webxdc app so they can not delete or modify 
  the data of other webxdc content.

- MUST inject `webxdc.js` and implement the
  [Webxdc API](#webxdc-api) so that messages are relayed and shown in chats. 

- MUST make sure the standard JavaScript API works as described at
  [Other APIs and Tags Usage Hints](#other-apis-and-tags-usage-hints).

### UI Interactions in Chats

- Text from `update.info` should be shown in the chats
  and tapping them should jump to their webxdc message

- The most recent text from `update.document` and `update.summary` should be shown inside the webxdc message,
  together with name and icon.  
  Only one line of text should be shown and truncation is fine
  as webxdc devs should not be encourated to send long texts here.

- A "Start" button should run the webxdc app

### Example Messenger Implementations

- [Android using Java](https://github.com/deltachat/deltachat-android/blob/master/src/org/thoughtcrime/securesms/WebxdcActivity.java)

- [iOS using Swift](https://github.com/deltachat/deltachat-ios/blob/master/deltachat-ios/Controller/WebxdcViewController.swift)

- [Desktop using TypeScript](https://github.com/deltachat/deltachat-desktop/blob/786b7514d69ffb723bbe6e706494852a2641bfcd/src/main/deltachat/webxdc.ts)


