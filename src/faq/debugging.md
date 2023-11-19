
# Debugging

## Debugging With eruda.js

When you can not use [debugging inside Delta Chat](#debugging-inside-delta-chat), 
either because you have no computer to connect to or if you are on iOS, 
you may try [eruda.js](https://github.com/liriliri/eruda) 
as an alternative to browser-native debugging tools.

1. Download a [standalone eruda.js](https://cdn.jsdelivr.net/npm/eruda)

1. Copy `eruda.js` next to your `index.html` 

1. Prepend the following snippet to the head section of your index.html: 

    ```html
    <script src="eruda.js"></script>
    <script>
      eruda.init();
    </script>
    ```

When your webxdc app is started, 
a floating button will appear in a corner. 
Tap it to see the developer tools.

## Debugging Inside Delta Chat

### Debug a webxdc app in Android via Chrome DevTools

1. enable webView debugging in Delta Chat settings 
   `Settings` > `Advanced` > `Developer Mode`: 
   <img alt="image of andvanced screen" src="/images/android_remote_debug_enable.png" style="max-height:40vh" />

1. enable developer mode and ADB debugging on your device 
   _(go to system settings, device info, click 7+ times on build number 
   until there is a toast telling you that you are now a "Developer", 
   then go into the new developer menu and enable "ADB debugging", 
   see also [android docs: Enable ADB debugging on your device](https://developer.android.com/studio/command-line/adb#Enabling))._

1. connect your device via USB to your computer

1. open chromium (or google chrome) and go to `chrome://inspect/#devices`

1. start your webxdc that you want to debug

1. click on `inspect`:

<p>
<img
src="/images/android_remote_debug_list.png"
alt="screenshot of chrome dev tools device list"
style="max-height:40vh"
/>
</p>

| Inpect HTML                                                      | JavaScript Console                                               |
| ---------------------------------------------------------------- | ---------------------------------------------------------------- |
| ![dev tools inpector](/images/android_remote_debug_inspector.png) | ![dev tools js console](/images/android_remote_debug_console.png) |

> Make sure to **disable adb debugging again** after you are done with debugging!


### Debug a webxdc app in Delta Chat Desktop

First enable the devTools for webxdc in the Settings:

  `Settings` > `Advanced` > Experimental Features > `Enable Webxdc Devtools`

> Note that you need to close and open any active webxdcs again for changes to take effect

Start the webxdc you want to debug and press `F12` to open the developer tools:

<p>
<img
src="/images/desktop_debug_open.png"
alt="screenshot of desktop webxdc window with devtool"
style="max-height:40vh"
/>
</p>

A bit small isn't it? fix it either by resizing the window's **width** or **undock** the developer tools:

<p>
<img
src="/images/desktop_debug_undock.png"
alt="undock devtools"
style="max-height:40vh"
/>
</p>

<p>
<img
src="/images/desktop_debug_extra_window.png"
alt="undock devtools"
style="max-height:40vh"
/>
</p>

## I Cannot Share Variables on iOS Between Scripts!

Your code:

`a.js`

```js
const CONFIG = { difficulty: "hard", hasCoins: true };
```

`b.js`

```js
if (CONFIG.difficulty == "hard") {
  /** make the game harder **/
}
```

`index.html`

```html
<html>
  <head>
    <!-- ... -->
  </head>
  <body>
    <!-- ... -->
    <script src="a.js"></script>
    <script src="b.js"></script>
  </body>
</html>
```

Basically you get many errors in your JS console like this:

```
Can't find variable: CONFIG
```

There are a few ways to solve this:

- use a bundle to bundle all your JS to one file (some bundlers: parcel, webpack, esbuild)
- use esm modules (see <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules>)
- define your variables as inline script in your HTML file. (inline script means that the script is in the HTML file between the `<script>` tags: `<script>my code</script>`)
- append your global variables to the window object: `window.myVar = 1;` and use them like `console.log(window.myVar)`
