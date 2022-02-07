# Transpile newer Javascript with Babel.js

Older devices might not have the newest javascript features/syntax in their webview, you may want to transpile your code down to an older js version eg. with <https://babeljs.io>

TODO guide, TODO find out what the oldest browser/webview version is that we support.

Targets:

- Desktop (electron -> is chrome 91)
- iOS (oldest iOS 11? -> webkit 604.1.38)
- android (android 5 -> the webview system component can be updated by the user: <https://play.google.com/store/apps/details?id=com.google.android.webview>)

If you want to use a newer API make sure to check on <https://caniuse.com>. If you just want to use newer JavaScript syntax, babel.js is the right tool for you - it translates new JS into older JS, that can be interpreted.
