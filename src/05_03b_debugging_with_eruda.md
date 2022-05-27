# Debugging with eruda.js

When you can not use [debugging inside Deltachat](05_03_debugging_in_deltachat.md), either because you have no computer to connect to or are on iOS, you can use [eruda.js](https://github.com/liriliri/eruda) as an alternative to the native dev tools.

## Installing eruda

Get `eruda.js` from https://github.com/liriliri/eruda, copy it next to your `index.html` and then add this snippet into the head section of your index.html, before all other scripts:

```html
<script src="eruda.js"></script>
<script>
  eruda.init();
</script>
```

## Using eruda

When you open the webxdc a floating button will appear in a corner, tap it to see the developer tools
