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

Send a status update to all peers.

- `update`: an object with the following properties:  
    - `update.payload`: string, number, boolean, array, object or `null`.
       MUST NOT be `undefined`.
       Everything that is not JSON serializable will be skipped,
       this especially affects Binary data buffers as used in `File`, `Blob`, `Int*Array` etc.;
       if needed, use eg. base64.
    - `update.info`: optional, short, informational message that will be added to the chat,
       e.g. "Alice voted" or "Bob scored 123 in MyGame".
       Do not add linebreaks; implementations will truncate the text at about 50 characters or less.
       If there are series of info messages, older ones may be dropped.
       use this option sparingly to not spam the chat.
    - `update.document`: optional, name of the document in edit
       (eg. the title of a poll or the name of a text in an editor)
       Implementations show the document name e.g. beside the app icon or in the title bar.
       MUST NOT be used if the webxdc does not create documents, e.g. in games.
       Do not add linebreaks; implementations will truncate the text at about 20 characters or less.
    - `update.summary`: optional, short text, shown beside the app icon;
       it is recommended to use some aggregated value, e.g. "8 votes", "Highscore: 123".
       Do not add linebreaks; implementations will truncate the text at about 20 characters or less.

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

- `update.document`: optional, document name as set by the sender, (see [`sendUpdate()`](#sendupdate)).
  Implementations show the document name e.g. beside the app icon or in the title bar.
  There is no need for the receiver to further process this information.

- `update.summary`: optional, short text, shown beside icon (see [`sendUpdate()`](#sendupdate))

Calling `setUpdateListener()` multiple times is undefined behavior: in current implementations only the last invocation works.


### sendToChat()

```js
let promise = window.webxdc.sendToChat(message);
```

`sendToChat()` (requires Delta Chat 1.38 or compatible) allows a webxdc app to prepare a message
that can then be sent to a chat by the user.
Implementations may ask the user for a destination chat
and then set up the message as a draft,
so it is clear that the outgoing message is a result of some user interaction.

`message` is an object with `file`, `text` or both set:

- `message.file`: file to be sent, set `name` and **one of** `blob`, `base64` or `plainText`:

  - `message.file.name`: name of the file, including extension
  - `message.file.blob`: JavaScript `Blob`, also accepts inherit types like `File`
  - `message.file.base64`: base64 encoded data
  - `message.file.plainText`: text for textfile, will be encoded as utf-8

- `message.text`: message text to be sent

The text can usually be modified by the user and
the user may decide to send the text without the file
or abort sending at all.
These user decisions are not reported back to the webxdc app.

To let the user focus on sending the message,
calling this function may pass control back to the main app
and exit the webxdc app.
The promise may or may not be resolved before exit.

In case of errors,
the app will not exit and the promise will be rejected.

Example:

```js
window.webxdc.sendToChat({
    file: {base64: "aGVsbG8=", name: "hello.txt"},
    text: "This file was generated by GreatApp"
}).catch((error) => {
    console.log(error);
});
```

Notes:

- To send and empty file, set an empty string or blob as data.
  Not setting any data is an error.
  This is also important for messenger implementors,
  that need to check for eg. `typeof message.file.base64 === "string"`
  and not `!message.file.base64`, which would not allow empty files.

- If you want to send text don't use `btoa()`,
  rather use `message.file.plainText` directly,
  because [`btoa()` has problems with some unicode/emojis](https://developer.mozilla.org/en-US/docs/Web/API/btoa#unicode_strings)

### importFiles()

```js
let files = await window.webxdc.importFiles(filter);
```

`importFiles()` (requires Delta Chat 1.38 or compatible) allows a webxdc app to import files.
Depending on platform support, this just opens the system file picker or a custom one.
This custom file picker should show recent attachments that were received and sent,
to make importing of a file that you just received from someone easier
(you don't need to save it to the file system first),
but it also still shows a button to open the system file picker.

- `filter`: an object with the following properties:
  - `filter.extensions`: optional - Array of extensions the file list should be limited to.
    Extensions must start with a dot and have the format `.ext`.
    If not specified, all files are shown.
  - `filter.mimeTypes`: optional - Array of mime types
    that may be used as an additional hint eg. in case a file has no extension.
    **Files matching either `filter.mimeTypes` or `filter.extensions` are shown**.
    Specifying a mime type requires to list all typical extensions as well -
    otherwise, you may miss files.
    See <https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/accept#unique_file_type_specifiers>
    for details about the format.
  - `filter.multiple`: whether to allow multiple files to be selected, false by default

The method returns a `Promise` that resolves to an Array of `File`-Objects.

Example:
```js
// then/catch
window.webxdc.importFiles({
  mimeTypes: ["text/calendar"],
  extensions: [".ics"],
}).then((files) => {
  /* do sth with the files */
}).catch((error) => {
    console.log(error);
});
// async/await
try {
  let files = await window.webxdc.importFiles({
    mimeTypes: ["text/calendar"],
    extensions: [".ics"],
  })
  /* do sth with the files */
} catch (error) {
  console.log(error);
}
```


### selfAddr

```js
window.webxdc.selfAddr
```

A string with an unique ID identifying the user in the current webxdc.
Every user of an webxdc must get a different ID
and that ID must be the same if the webxdc is started again later for the same user.
The same user in different webxdc, however, may have different IDs.

Especially useful if you want to differentiate between different peers -
just send the ID along with the payload,
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
- `<input type="file">` allows importing of files for further processing (requires Delta Chat 1.38 or compatible);
  see [`sendToChat()`](#sendtochat) for a way to export files


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
- features that require user permissions
  or are enabled through the [Permissions Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Permissions_Policy) may not work,
  Geolocation, Camera, Microphone etc.
- `window.open()`, `alert()`, `prompt()`, `confirm()`, is known to not work on some implementations


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
The icon should be a square at reasonable width/height,
usually between 128 x 128 and 512 x 512 pixel.
Round corners, circle cut out etc. will be added by the implementations as needed;
do not add borders or shapes to the icon therefore.
If no icon is set, a default icon will be used.



## Messenger Implementation

### Webview Constraints for Running Apps 

When starting a web view for a webxdc app to run, messenger implementors:

- MUST deny all forms of internet access. If you don't do this
  unsuspecting users may leak data of their private interactions to outside third parties. 
  You do not need to offer "privacy" or "cookie" consent screens as 
  there is no way a WebXDC app can implicitly transfer user data to the internet. 

- MUST allow unrestricted use of DOM storage (local storage, indexed db and co), 
  but make sure it is scoped to each webxdc app so they can not delete or modify 
  the data of other webxdc content.

- MUST inject `webxdc.js` and implement the
  [Webxdc API](#webxdc-api) so that messages are relayed and shown in chats. 

- MUST make sure the standard JavaScript API works as described at
  [Other APIs and Tags Usage Hints](#other-apis-and-tags-usage-hints).

### UI Interactions in Chats

- Text from `update.info` SHOULD be shown in the chats
  and tapping them should jump to their webxdc message

- The most recent text from `update.document` and `update.summary` SHOULD be shown inside the webxdc message,
  together with name and icon.  
  Only one line of text SHOULD be shown and truncation is fine
  as webxdc devs SHOULD NOT be encouraged to send long texts here.

- A "Start" button SHOULD run the webxdc app.

### Example Messenger Implementations

- [Android using Java](https://github.com/deltachat/deltachat-android/blob/master/src/org/thoughtcrime/securesms/WebxdcActivity.java)

- [iOS using Swift](https://github.com/deltachat/deltachat-ios/blob/master/deltachat-ios/Controller/WebxdcViewController.swift)

- [Desktop using TypeScript](https://github.com/deltachat/deltachat-desktop/blob/786b7514d69ffb723bbe6e706494852a2641bfcd/src/main/deltachat/webxdc.ts)


