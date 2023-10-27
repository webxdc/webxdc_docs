# Questions about storing application state

## Can I use localStorage or IndexedDB in my webxdc app? 

Yes, you can use both [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
and [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) in your app
but be aware of some limitations, [especially during webxdc app simulation/development](#simdb). <!-- XXX this link was already broken on the master branch, but maybe we can figure out what it was supposed to point to? -->

LocalStorage [has a limit of 4-10MB](https://stackoverflow.com/questions/2989284/what-is-the-max-size-of-localstorage-values/) which you can fill up quickly if not careful. 
IndexedDB is an alternative you can use that doesn't have this size limitation. 

Note that browsers might reclaim storage for both localStorage and IndexedDB
after a longer time of not using a webxdc app. 
If you want to safely persist data, 
you must [send an application update](/spec/sendUpdate.html)
which will be safely persisted by the messenger,
and which also allows to use an app on multiple devices. 

## Why doesn't localStorage/IndexedDB work with some development simulators? {simdb}

When you run your webxdc app with the [hello simulator](https://github.com/webxdc/hello)
then all browser tabs share the same localStorage or indexedDB instance
which is unlike when your webxdc app will be run by messengers. 
However, the [webxdc-dev simulator](https://github.com/webxdc/webxdc-dev) 
manages to separate storage per webxdc app instance
because each running app uses a separate localhost port for connecting
to the webxdc-dev simulator server. 

## Are application updates guaranteed to be delivered to chat peers? 

No, there is no guaranteed message delivery and also 
no feedback on delivery status.
There is only a "best effort" approach. 
Messengers will typically queue messages and attempt delivery repeatedly. 

If you want guarantees for peers receiving updates,
you need to implement your own reliability protocol in your app. 
Common techniques include assigning sequence numbers or linked IDs to all updates you send,
and implementing a way for receivers to request re-sending if updates are missing. 

As with all "network synchronization" topics there are some theoretical limits. 
In particular it is useful to study and think about 
the [Two-Generals problem](https://en.wikipedia.org/wiki/Two_Generals'_Problem) 
and learn about existing "reliability layer" protocols 
before attempting to implement one yourself. 
 
