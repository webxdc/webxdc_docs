# Get started

First, you need to add the following script import to your `index.html`:

```html
<script src="webxdc.js"></script>
```

This loads the platform specific `webxdc.js` which wraps the platform's internal calls to provide the same interface on all platforms.

There are 3 base cases you should handle in your code:

| case                                | what needs to be done                                                                                                |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| webxdc was just (re)started         | make sure the handler set with `setUpdateListener()` can use the previous state updates to process the current state |
| state update arrives                | define a handler for incoming state updates with `setUpdateListener()`                                               |
| you want to send a new state update | send state updates with `sendUpdate()`                                                                               |

> You might find premade code snippet for what you need in our [cookbook](./04_cookbook.md) section, if you just want to add common things like a scoreboard to your game.

> For testing and developing locally outside of deltachat you can use our [webxdc dev tool](./02_02_dev_tool.md)

> If you want to use Typescript or just completion in your IDE learn how to set that up [here](./05_02_typescript.md).

Also remember that the older devices might not have all new JavaScript features,
so it is recommended to transpile your code down to an older JS version (see [Transpile newer Javascript with Babel.js](./05_01_babel.md)).

## Packaging

> - a **Webxdc file** is a **ZIP**-file with the extension `.xdc`.
> - The zip file needs to contain all files that your `index.html` needs (scripts, css-stylesheets, images, fonts, etc.).
> - The resulting zipfile should be smaller than `640`KB (kilobytes), we might raise this limit in the future.

So put the following into your zip file:

- your `index.html` file and all resources it needs
- an `icon.png` or `icon.jpg` ([info about required format](./03_api_reference.md#app-icon))
- a `manifest.toml` file with your meta data. ([documentation](./03_api_reference.md#manifest-file))

manifest.toml:

```toml
name = "My Webxdc Name"
```

Then rename your file's extention from `.zip` to `.xdc`.
Now you can send it into a deltachat chat and start using it right away, there is no certification or other bureaucracy required ;)
