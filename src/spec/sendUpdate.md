# sendUpdate

```js
window.webxdc.sendUpdate(update, descr);
```

Send an update to all peers.

- `update`: an object with the following properties:  
    - `update.payload`: string, number, boolean, array, object or `null`.
       MUST NOT be `undefined`.
       Everything that is not JSON serializable will be skipped,
       this especially affects Binary data buffers as used in `File`, `Blob`, `Int*Array` etc.;
       if needed, use eg. base64.
    - `update.info`: optional, short, informational message that will be added to the chat,
       e.g. "Alice voted" or "Bob scored 123 in MyGame".
       Do not add linebreaks; implementations will truncate the text at about 50 characters or less.
       If there are series of info messages, older ones may be dropped.
       use this option sparingly to not spam the chat.
    - `update.document`: optional, name of the document in edit
       (eg. the title of a poll or the name of a text in an editor)
       Implementations show the document name e.g. beside the app icon or in the title bar.
       MUST NOT be used if the webxdc does not create documents, e.g. in games.
       Do not add linebreaks; implementations will truncate the text at about 20 characters or less.
    - `update.summary`: optional, short text, shown beside the app icon;
       it is recommended to use some aggregated value, e.g. "8 votes", "Highscore: 123".
       Do not add linebreaks; implementations will truncate the text at about 20 characters or less.

- `descr`: short, human-readable description what this update is about.
  this is shown e.g. as a fallback text in an e-mail program.

All peers, including the sending one,
will receive the update by the callback given to [`setUpdateListener()`](#setupdatelistener).

There are situations where the user cannot send messages to a chat,
e.g. if the webxdc instance comes as a contact request or if the user has left a group.
In these cases, you can still call `sendUpdate()`,
however, the update won't be sent to other peers
and you won't get the update by [`setUpdateListener()`](#setupdatelistener).


