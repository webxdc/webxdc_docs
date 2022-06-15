# Getting Started 


## A first "chat" example

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

Now zip the directory containing the `index.html`:

```shell
zip -9 --recurse-paths "myapp.xdc" PATH_TO_DIR
```
You can now share the resulting `myapp.xdc` file in a chat and everyone can hit "start" to start our little "chat app". 

However, for faster development round trips and easier developing of apps we recommend you to use the minimal [hello webxdc development](https://github.com/webxdc/hello).

## Example projects

There is a growing list of webxdc apps to be found and forked from, by searching the [`#webxdc` Topic on GitHub](https://github.com/topics/webxdc). Additionally there is a collection of example apps in the [Webxdc organisation on github](https://github.com/webxdc) and a curated list of apps on the [webxdc home page](https://webxdc.org). 


# Community

You can ask questions and announce your app projects on the DeltaChat forum under the [`webxdc` category](https://support.delta.chat/c/webxdc/20)

You can login into the [forum](https://support.delta.chat) via DeltaChat, Github or by creating a normal account there.
