
# Compatibility

## Other APIs and Tags Usage Hints

Webxdc apps run in a restricted environment, but the following practices are permitted:

- `localStorage`, `sessionStorage`, `indexedDB`
- `visibilitychange` events
- `window.navigator.language`
- internal links, such as `<a href="localfile.html">`
- `mailto` links, such as `<a href="mailto:addr@example.org?body=...">`
- `<meta name="viewport" ...>` is useful especially as webviews from different platforms have different defaults
- `<input type="file">` allows importing of files for further
  processing; see [`sendToChat()`](/spec/sendToChat.md) for a way to export files

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



## Transpile Newer JavaScript With Babel.js

Older devices might not have the newest javascript features/syntax in their webview, you may want to transpile your code down to an older JavaScript version eg. with [Babel](https://babeljs.io).

Targets:

- Desktop (electron -> is chrome 91)
- iOS (iOS 11 -> webkit 604.1.38)
- android (android 5 -> the webview system component can be updated by the user: <https://play.google.com/store/apps/details?id=com.google.android.webview>)

If you want to use a newer API make sure to check on <https://caniuse.com>. If you just want to use newer JavaScript syntax, babel.js is the right tool for you - it translates new JS into older JS, that can be interpreted.
