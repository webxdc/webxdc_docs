# From online-HTTP to offline-first P2P web apps

Webxdc is a P2P oriented container format for sharing and hosting HTML5 web apps in messenger chats. 
The e-mail based [Delta Chat](https://delta.chat) and the XMPP-based [Cheogram](https://cheogram.com) messengers 
support [webxdc apps](https://webxdc.org/apps), which run on both messengers without any change. 

Instead of being hosted on a central always-online HTTP server, 
webxdc apps are stored with the host messenger by attaching a container file to a chat message. 
Instead of using the HTTP protocol for querying a central server to obtain authoritative application state,
webxdc apps send and receive messages via the messenger's [JavaScript API]
which allows to send and receive "application updates" between devices participating in a chat. 
App developers do not need to implement message transport, user authentication, 
discovery or bootstrapping mechanisms, and they do not need to implement end-to-end encryption either. 
All Authentication, identity management, social discovery and message transport 
is outsourced to the host messenger which runs webxdc apps,
thereby letting each app inherit offline-first and end-to-end encryption 
capabilities of the hosting messenger. 

Messengers run webxdc container files in network-isolated webviews that can not perform any DNS or HTTP queries.
Webxdc apps can only cause network messages by calling the [`sendUpdate`](./spec/sendUpdate.html)
and [`setUpdateListener`](./spec/setUpdateListener.html) JavaScript APIs,
implemented by the hosting messenger. 
In ["Bringing E2E privacy to the web"](https://delta.chat/en/2023-05-22-webxdc-security) 
Delta Chat developers described the unique security-audited privacy guarantees of webxdc 
not found with any other web container technology or [specification](./spec/index.html). 

Waving central registries and app store dependencies "good bye!", 
anyone can [get started building a HTML5 app](/get_started.html), 
package it as a [`.xdc` container file](./spec/index.html#webxdc-file-format), 
and drop it in a chat to share with friends. 
Once shared, chat participants click "Start" 
thereby instantiating an ad-hoc distributed P2P system on participant's devices,
where the hosting messenger provides the social and technical routing of "application updates". 
As long and as well as the hosting messenger manages to deliver messages, 
webxdc application state is constantly replicated and synchronized across all participating devices. 

<video controls style="width:560px; max-width: 100%;"><source src="https://webxdc.org/assets/just-web-apps.mp4" type="video/mp4"><a href="https://www.youtube.com/watch?v=I1K4pBvb2pI">watch "just web apps" on youtube</a></video>


The [webxdc-dev simulation tool](https://github.com/webxdc/webxdc-dev) is the recommended 
tool for developing webxdc apps as it allows multi-user simulation, 
and allows observing network messages between app instances. 
No messenger is required to develop a webxdc app with the `webxdc-dev` tool. 

Webxdc app development and deployment is fundamentally easier than 
developing for and maintaining an application-specific always-online HTTP server. 
But there are undeniably complications in arranging consistent web app state 
across user's devices, a typical issue for any P2P system. 
The [Shared web application state] chapter 
provides useful theoretical and practical background for writing robust offline-first web apps
without requiring a central online authority acting as the "source of truth". 
Even if you don't study the topic in depth, reading [Shared web application state] 
introduces you to the terminology and necessary considerations 
for any P2P system, with a particular eye on webxdc. 


[webxdc on Codeberg](https://codeberg.org/webxdc) and [webxdc on GitHub](https://github.com/webxdc) 
contain some of the ongoing webxdc-related developments.  

For now, please feel free to post or follow questions and answers 
in the [Webxdc support forum](https://support.delta.chat/c/webxdc/20). 
If somebody wants to help with setting up more independent 
webxdc community infrastructure please step forward. 

[Shared web application state]: /shared-state/index.html 
[JavaScript API]: /spec/index.html#webxdc-api
[Messenger implementation]: /spec/index.html#messenger-implementation
