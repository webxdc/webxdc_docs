# What is webxdc?

Webxdc is a [file format] and a [Javascript API] to run self-contained web apps in chat messengers. 

Anyone can build html5 apps without permission and 
share it with friends by simply sending a message with an [attached webxdc app file]. 
Once a webxdc app is shared in a chat, 
its members can click "start" to run the app and interact with each other through the app. 
There are no "login", "privacy" or "cookie" consent screens because there is no way 
the app can transfer user data to anything outside the chat. 
Webxdc apps can only send and receive messages in the chat 
by using the [Javascript API] provided by the messenger. 
Each Webxdc app thus gets automatic end-to-end encryption and peer discovery for free. 

## What kind of interactive content can be created with this?

Some webxdc web apps that have already been implemented with webxdc: 

- Games
  - Singleplayer games with a shared highscore 
  - Multiplayer turn-based games (like tictactoe or chess)
- Productivity
  - Polls
  - Corkboards and Checklists 
  - Collaborative Document editors

You can follow further developments in the [webxdc GitHub organization](https://github.com/webxdc) and are invited to ask questions and post suggestions in [the "webxdc" support forum](https://support.delta.chat/c/webxdc/20).

[file format]: 03_spec.md#webxdc-file-format
[Javascript API]: 03_spec.md#webxdc-api
