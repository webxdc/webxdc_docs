# Getting Started 


## A First "chat" Example

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


## Many Examples to Work From

Each of the working examples on [webxdc home page](https://webxdc.org) comes with source-code references, usually pointing to repositories in the [Webxdc organisation on github](https://github.com/webxdc).


## Participating in Developments 

- [Webxdc Github org](https://github.com/webxdc) containing many example app repositories
  Please tag your own app experiments with the "webxdc" topic so that others may find it 
  via the [`#webxdc` Topic on GitHub](https://github.com/topics/webxdc). 

- Support Forum: You can ask questions and announce your app projects on the DeltaChat forum under the [`webxdc` category](https://support.delta.chat/c/webxdc/20). You can login into the [forum](https://support.delta.chat) via DeltaChat, Github or by creating a normal account there.

- Announcements: You may follow Delta Chat and Webxdc-related developments through the [Delta Fediverse](https://chaos.social/web/delta) or the [Delta Twitter](https://twitter.com/delta_chat) accounts. 
