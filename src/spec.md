# Webxdc Specification 

Webxdc is a fresh and still evolving way of running web apps in chat messengers. 
This document describes the [webxdc API](#webxdc-api) and [`.xdc` file format](#webxdc-file-format) for app developers. It also describes the constraints for a [messenger implementation] for when it launches webxdc apps for its users. 


## Webxdc API

A webxdc app is shared in a chat and run independently on each device when a user clicks "start". 
To share application state, the otherwise network-isolated app instances use [`sendUpdate()`](#sendupdate) and [`setUpdateListener()`](#setupdatelistener) to exchange information. Messenger implementations expose implementations for this API through the `webxdc.js` module. To activate the webxdc API you need to use a script reference for `webxdc.js` in your HTML5 app:

```html
<script src="webxdc.js"></script>
```


### sendUpdate()

```js
window.webxdc.sendUpdate(update, descr);
```

- `update`: an object with the following properties:  
    - `update.payload`: any javascript primitive, array or object.
    - `update.info`: optional, short, informational message that will be added to the chat,
       eg. "Alice voted" or "Bob scored 123 in MyGame".
       usually only one line of text is shown
       and if there are series of info messages, older ones may be dropped.
       use this option sparingly to not spam the chat.
    - `update.document`: optional, name of the document in edit,
       must not be used eg. in games where the webxdc does not create documents
    - `update.summary`: optional, short text, shown beside Webxdc icon;
       it is recommended to use some aggregated value,  eg. "8 votes", "Highscore: 123"

- `descr`: short, human-readable description what this update is about.
  this is shown eg. as a fallback text in an e-mail program.

All peers, including the sending one,
will receive the update by the callback given to [`setUpdateListener()`](#setupdatelistener).

There are situations where the user cannot send messages to a chat,
eg. if the webxdc instance comes as a contact request or if the user has left a group.
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
  implementations show the document name eg. beside the app icon or in the title bar

- `update.summary`: optional, short text, shown beside icon (see [`sendUpdate()`](#sendupdate))


### selfAddr

```js
window.webxdc.selfAddr
```

Property with the peer's own address.
This is esp. useful if you want to differ between different peers -
just send the address along with the payload,
and, if needed, compare the payload addresses against `selfAddr` later on.


### selfName

```js
window.webxdc.selfName
```

Property with the peer's own name.
This is name chosen by the user in their settings,
if there is nothing set, that defaults to the peer's address.



## Other APIs and Tags Usage Hints

- `localStorage`, `sessionStorage`, `indexedDB` are okay to be used
- `visibilitychange`-events are okay to be used
- `window.navigator.language` is okay to be used, on desktop it is the system language
- `<a href="localfile.html">` and other internal links are okay to be used
- `<a href="mailto:addr@example.org?body=...">`- mailto links are okay to be used
- `<meta name="viewport" ...>` usage is okay to be used
  and useful esp. different webviews have different defaults


### Discouraged Practises 

- `document.cookie` is known not to work on desktop and iOS
  use `localStorage` instead
- `unload`-, `beforeunload`- and `pagehide`-events are known not to work on iOS and are flaky on other systems
  (also partly discouraged by [mozilla](https://developer.mozilla.org/en-US/docs/Web/API/Window/unload_event))
  use `visibilitychange` instead
- `<title>` and `document.title` is ignored by Webxdc;
  use the `name` property from `manifest.toml` instead
- newest js features may not work on all webviews,
  you may want to transpile your code down to an older js version
  eg. with <https://babeljs.io>
- `<a href="https://example.org/foo">` and other external links are blocked by definition;
  instead, embed content or use `mailto:` link to offer a way for contact
- `<input type="file">` is discouraged currently; this may change in future


## Webxdc File Format

- a **Webxdc** is a **ZIP-file** with the extension `.xdc`
- the ZIP-file MUST use the default compression methods as of RFC 1950,
  this is "Deflate" or "Store"
- the ZIP-file MUST contain at least the file `index.html`
- the ZIP-file MAY contain a `manifest.toml` and `icon.png` or
  `icon.jpg` files
- if the webxdc app is started, `index.html` MUST be opened in a
- [restricted webview](spec.md#webview-constraints-for-running-apps) that allow accessing 
  resources only from the ZIP-file.

### The manifest.toml File

If the ZIP-file contains a `manifest.toml` in its root directory,
the following basic information MUST be read from it: 

```toml
name = "My Name"
source_code_url = "https://example.org/orga/repo"
```

- `name` - The name of the webxdc app.
  If no name is set or if there is no manifest, the filename is used as the webxdc name.

- `source_code_url` - Optional URL where the source code of the webxdc and maybe other information can be found.
  Messenger implementors may make the url accessible via a "Help" menu in the webxdc window.


### Icon Files 

If the ZIP-root contains an `icon.png` or `icon.jpg`,
these files are used as the icon for the webxdc.
The icon should be a square at reasonable width/height;
round corners etc. will be added by the implementations as needed.
If no icon is set, a default icon will be used.



## Messenger Implementation

### Webview Constraints for Running Apps 

Messenger implementors need to implement the following restrictions when starting a web view for a webxdc app to run:

- You MUST deny all forms of internet access. If you don't do this
  unsuspecting users may leak data of their private interactions to outside third parties. 
  You do not need to offer "privacy" or "cookie" consent screens as 
  there is no way the app can transfer user data to anything outside the chat. 

- You MUST allow unrestricted use of DOM storage (local storage, indexed db and co), 
  but make sure it is scoped to each webxdc app so they can not delete or modify 
  the data of other webxdc content.

- You MUST offer an implementation for `webxdc.js` implementing the
  [Webxdc API] such that messages are relayed and shown in chats. 

### UI Interactions in Chats

- Info messages  in chats -> click on them should jump to their webxdc message

