# Intro 

Webxdc is a new way to create and share web apps in messenger chat groups. Anyone can build HTML5 apps, package it as a [`.xdc` file](spec.md#webxdc-file-format), and drop it in a chat to share with friends. Once shared, chat participants can simply click "Start" to run the app and interact with each other through the app. 

<video controls style="width:560px; max-width: 100%;"><source src="https://webxdc.org/assets/just-web-apps.mp4" type="video/mp4"><a href="https://www.youtube.com/watch?v=I1K4pBvb2pI">watch "just web apps" on youtube</a></video>

Webxdc apps can only send and receive messages in the chat via the messenger's [JavaScript API]. Thus they automatically get end-to-end encryption and peer discovery for free.

Webxdc is specified through its [file format](spec.md#webxdc-file-format) for packaging as a `.xdc` file, [JavaScript API] for developing web apps, and [Messenger implementation] for running and sharing Webxdc apps in chats. Webxdc is supported by and evolving with the [Delta Chat](https://delta.chat) e-mail messenger, which serves as the default reference. 

See the [webxdc GitHub organization](https://github.com/webxdc) for further developments and feel free to post questions or suggestions in the [Webxdc support forum](https://support.delta.chat/c/webxdc/20).

[JavaScript API]: spec.md#webxdc-api
[Messenger implementation]: spec.md#messenger-implementation
