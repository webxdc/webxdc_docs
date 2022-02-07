# Troubleshooting

## I cannot share variables on iOS between scripts!

Your code:

`a.js`

```js
const CONFIG = { dificulty: "hard", hasCoins: true };
```

`b.js`

```js
if (CONFIG.dificulty == "hard") {
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
