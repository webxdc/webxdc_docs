# Webxdc Specification

This document is for app developers and outlines the [webxdc API](#webxdc-api) and [`.xdc` file format](#webxdc-file-format). It also describes the constraints for a [messenger implementation](#messenger-implementation) for hosting and running webxdc apps. 

## Webxdc API

Webxdc apps are shared in a chat and each device runs its own instance on the recipients device when they click "Start". The apps are network-isolated but can share state via [`sendUpdate()`](./sendUpdate.md) and [`setUpdateListener()`](./setUpdateListener.md).

Messenger implementations expose the API through a `webxdc.js` module. To activate the webxdc API you need to use a script reference for `webxdc.js` in your HTML5 app:

```html
<script src="webxdc.js"></script>
```

`webxdc.js` must not be added to your `.xdc` file as they are provided by the messenger. To simulate webxdc in a browser, 
you may use the `webxdc.js` file from [Hello](https://github.com/webxdc/hello),
or use the [webxdc-dev tool](https://github.com/webxdc/webxdc-dev) which
both allow to simulate and debug webxdc apps without any messenger.

## Webxdc File Format

- a Webxdc app is a **ZIP-file** with the extension `.xdc`
- the ZIP-file MUST use the default compression methods as of RFC 1950,
  this is "Deflate" or "Store"
- the ZIP-file MUST contain at least the file `index.html`
- the ZIP-file MAY contain a `manifest.toml` and `icon.png` or
  `icon.jpg` files
- if the webxdc app is started, `index.html` MUST be opened in a [restricted webview](#webview-constraints-for-running-apps) that only allows accessing
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
  [Other APIs and Tags Usage Hints](/tips_and_tricks/compatibility.md#other-apis-and-tags-usage-hints).

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


