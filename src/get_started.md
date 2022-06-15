# A first "chat" example

The following complete `index.html` webxdc app example shows an input field and every input is show on all peers.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8"/>
    <script src="webxdc.js"></script>
  </head>
  <body>
    <input id="input" type="text"/>
    <a href="" onclick="sendMsg(); return false;">Send</a>
    <p id="output"></p>
    <script>
    
      function sendMsg() {
        msg = document.getElementById("input").value;
        window.webxdc.sendUpdate({payload: msg}, 'Someone typed "'+msg+'".');
      }
    
      function receiveUpdate(update) {
        document.getElementById('output').innerHTML += update.payload + "<br>";
      }
    
      window.webxdc.setUpdateListener(receiveUpdate, 0);
    </script>
  </body>
</html>
```

More examples at [github.com/webxdc](https://github.com/webxdc) and
[the github topic #webxdc](https://github.com/topics/webxdc)

[github.com/webxdc/hello](https://github.com/webxdc/hello)
offers an **Webxdc Tool** that can be used in many browsers without any installation needed.
You can also use that repository as a template for your own Webxdc -
just clone and start adapting things to your need.

# Developing in Safari

To use the devtool in safari you need to disable the local file restrictions
under `Develop` -> `Disable Local File Restrictions`:

<p>
<img
src="images/dev_tool_settings_safari.png"
alt="'Disable Local File Restrictions' entry in the 'Develop' menu in safari"
style="max-height:40vh"
/>
</p>

After doing this you can use the dev tool simulator.

Make sure to reload (`Cmd + R`) all simulator tabs/windows to apply this setting.

Without this option `Add Peer` seems to work (it opens a new instance), but **the instances will not be able to communicate**.
# Developing on Android

TODO - turn notes into a nice guide

- install Termux
- install python & git in termux (or is it preinstalled??)
- git clone the devtool repo or your fork of it
- use `python -m http.server` (TODO check complete syntax) to serve it for development and use nano / vim
- when your done use the packaging script
- and then copy the file to a location from where you can access and send it via deltachat.

- pro tip: you can create symbolic link to a folder in the external storage


<!-- there are some gui apps on fdroid we could try:
IDE: https://f-droid.org/packages/com.blacksquircle.ui/
HTTP server: https://f-droid.org/packages/com.example.flutter_http_server/
 --># Using the Webxdc Development Tool

This is a little tool to make creation of webxdc content easier.
It is found on <https://github.com/deltachat/webxdc-dev>.

Advantages:

- Super easy to get started.
- No server or installation required.
- You don't need your DeltaChat app for testing. (you save the packaging step)

## Contents

The devtool has 2 parts:

- `webxdc.js` which acts as simulator
- `create-xdc.sh`, a script that packs your webxdc into a [`.xdc`](./03_spec.md#zip-format) file for you

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
# Getting started 

## Complete "chat" example app 

The following `index.html` is (when packaged as a zip file) a webxdc chat app. It presents an input field and every input from all peers is shown on all peers. 

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8"/>
    <script src="webxdc.js"></script>
  </head>
  <body>
    <input id="input" type="text"/>
    <a href="" onclick="sendMsg(); return false;">Send</a>
    <p id="output"></p>
    <script>
    
      function sendMsg() {
        msg = document.getElementById("input").value;
        window.webxdc.sendUpdate({payload: msg}, 'Someone typed "'+msg+'".');
      }
    
      function receiveUpdate(update) {
        document.getElementById('output').innerHTML += update.payload + "<br>";
      }
    
      window.webxdc.setUpdateListener(receiveUpdate, 0);
    </script>
  </body>
</html>
```

## "hello" development tool 

[github.com/webxdc/hello](https://github.com/webxdc/hello)
offers an **Webxdc Tool** that can be used in many browsers without any installation needed.
You can also use that repository as a template for your own Webxdc -
just clone and start adapting things to your need.

## More examples 

More examples are at [github.com/webxdc](https://github.com/webxdc) and
can be discovered by [the github topic #webxdc](https://github.com/topics/webxdc)

> To use the devtool in safari you need to disable the local file restrictions
under `Develop` -> `Disable Local File Restrictions`:

<p>
<img
src="images/dev_tool_settings_safari.png"
alt="'Disable Local File Restrictions' entry in the 'Develop' menu in safari"
style="max-height:40vh"
/>
</p>

After doing this you can use the dev tool simulator.

Make sure to reload (`Cmd + R`) all simulator tabs/windows to apply this setting.

Without this option `Add Peer` seems to work (it opens a new instance), but **the instances will not be able to communicate**.
# Developing on Android

TODO - turn notes into a nice guide

- install Termux
- install python & git in termux (or is it preinstalled??)
- git clone the devtool repo or your fork of it
- use `python -m http.server` (TODO check complete syntax) to serve it for development and use nano / vim
- when your done use the packaging script
- and then copy the file to a location from where you can access and send it via deltachat.

- pro tip: you can create symbolic link to a folder in the external storage


<!-- there are some gui apps on fdroid we could try:
IDE: https://f-droid.org/packages/com.blacksquircle.ui/
HTTP server: https://f-droid.org/packages/com.example.flutter_http_server/
 --># Using the Webxdc Development Tool

This is a little tool to make creation of webxdc content easier.
It is found on <https://github.com/deltachat/webxdc-dev>.

Advantages:

- Super easy to get started.
- No server or installation required.
- You don't need your DeltaChat app for testing. (you save the packaging step)

## Contents

The devtool has 2 parts:

- `webxdc.js` which acts as simulator
- `create-xdc.sh`, a script that packs your webxdc into a [`.xdc`](./03_spec.md#zip-format) file for you

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
# Get started

First, you need to add the following script import to your `index.html`:

```html
<script src="webxdc.js"></script>
```

This loads the platform specific `webxdc.js` which wraps the platform's internal calls to provide the same interface on all platforms.



# Example Projects

Here you can find some example projects. If you have a project that you want to share feel free to make a pull request to add it here on [github](https://github.com/deltachat/webxdc_docs). (Or just click on the edit icon in the top right corner, it will take you directly to editing this page)

## Productivity

- Poll: <https://github.com/r10s/webxdc-poll>
- Draw: <https://github.com/adbenitez/draw.xdc>

## Games

- TicTacToe: <https://github.com/Simon-Laux/tictactoe.xdc>
- 2048: <https://github.com/adbenitez/2048.xdc>
- Chess Board: <https://github.com/adbenitez/ChessBoard.xdc>
- StackUp: <https://github.com/adbenitez/StackUp.xdc>
- Othello: <https://github.com/adbenitez/Othello.xdc>

Even more examples can be found searching for the [`#webxdc` Topic on GitHub](https://github.com/topics/webxdc).