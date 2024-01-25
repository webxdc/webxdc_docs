# Getting Started 


## A simple example

The following `index.html` shows a complete webxdc app, with an input field shown on all peers. Data submitted from the input is delivered to all members of the chat.

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

To package the app as a `.xdc` file, zip the directory containing the `index.html`:

```shell
zip -9 --recurse-paths "myapp.xdc" PATH_TO_DIR
```

Now it's possible to share the `myapp.xdc` file in any chat: recipients can hit "Start" to begin using the app to enter text in this input field and send updates to each other. 

To simulate multiple chat participants in the browser, try [Hello](https://github.com/webxdc/hello) as a minimal example; it includes everything needed to run the app and requires no build systems.


## More examples in the webxdc store 

The [webxdc store](https://webxdc.org/apps) contains working webxdc apps that you can use today. 
Each app comes with a "source code" link so that you can learn and fork as you wish. 
You can submit your own available FOSS app for inclusion into the curated store. 

## Participating in developments 

- [Support Forum](https://support.delta.chat/c/webxdc/20): the webxdc category on the DeltaChat forum is a space to ask questions and announce your app projects. Log into the [forum](https://support.delta.chat) via DeltaChat, Github, or by creating a username and password there.

- If you have any question about Webxdc support in the XMPP-based Cheogram messenger, head over
  to the [Cheogram forum channel](https://anonymous.cheogram.com/discuss@conference.soprani.ca)

- Announcements: Delta Chat and Webxdc-related developments can be
  followed on [Fediverse](https://chaos.social/@delta)
