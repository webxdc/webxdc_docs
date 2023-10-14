# selfAddr & selfName

## selfAddr

```js
window.webxdc.selfAddr
```

A string with an unique ID identifying the user in the current webxdc.
Every user of an webxdc must get a different ID
and that ID must be the same if the webxdc is started again later for the same user.
The same user in different webxdc, however, may have different IDs.

Especially useful if you want to differentiate between different peers -
just send the ID along with the payload,
and, if needed, compare the payload addresses against `selfAddr` later on.

## selfName

```js
window.webxdc.selfName
```

Name of the current account, as defined in settings.
If empty, this defaults to the peer's address.


