# Using the Webxdc Development Tool

This is a little tool to make creation of webxdc content easier.
It is found on <https://github.com/deltachat/webxdc-dev>.

Advantages:

- Super easy to get started.
- No server or installation required.
- You don't need your DeltaChat app for testing. (you save the packaging step)

## Contents

The devtool has 2 parts:

- `webxdc.js` which acts as simulator
- `create-xdc.sh`, a script that packs your webxdc into a [`.xdc`](./03_api_reference.md#zip-format) file for you

## Use the Simulator

For developing your webxdc, just copy the [`webxdc.js` from the repo](https://github.com/deltachat/webxdc-dev/blob/master/webxdc.js) beside your `index.html` and you are ready to go.

> Platform Quirks:
>
> - On Safari you need to [disable local file restrictions](./02_02_01_dev_tool_safari.md).
> - On Android you might need to [serve it using a local http server](./02_02_02_dev_tool_android.md).

Click on `Add Peer` to add another instance.

Now you can test your webxdc while developing it with a simple reload, instead of repacking it and testing it inside of deltachat, also you don't need deltachat to develop webxdc content using this method.

> `Clear Storage` resets the simulator (removes all previous state updates).

## Use it as a template for your webxdc project

(fork and) clone `https://github.com/deltachat/webxdc-dev`

modify `index.html` to as you like, then open `index.html` in your webbrowser to try out your webxdc.
