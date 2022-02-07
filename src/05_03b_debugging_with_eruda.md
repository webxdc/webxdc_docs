# Debugging with eruda.js

When you can not use [debugging inside Deltachat](05_03_debugging_in_deltachat.md), either because you have no computer to connect to or are on iOS, you can use [eruda.js](https://github.com/liriliri/eruda) as an alternative to the native dev tools.

## Installing eruda

download `eruda.min.js` from [TODO link], copy it next to your `index.html` and then add this snippet into the head section of your index.html, before all other scripts:

```html
<script src="eruda.min.js"></script>
<script>
  eruda.init();
</script>
```

## Using eruda

[TODO small introduction, how to open it (click on the button)]
