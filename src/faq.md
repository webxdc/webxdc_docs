# Frequently Asked Questions 

## Can I use localStorage or IndexedDB in my webxdc app? 

Yes, you can use both [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
and [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) in your app
but be aware of some limitations. 

LocalStorage [has a limit of 4-10MB](https://stackoverflow.com/questions/2989284/what-is-the-max-size-of-localstorage-values/) which you can fill up quickly if not careful. 
IndexedDB is an alternative you can use that doesn't have this size limitation. 

Note that browsers might reclaim storage for both localStorage and IndexedDB
after a longer time of not using a webxdc app. 
If you want to safely persist data, 
you must [send an application update](https://docs.webxdc.org/spec.html#sendupdate)
which will be safely persisted by the messenger,
and which also allows to use an app on multiple devices. 

## Why doesn't localStorage/IndexedDB work with some development simulators? 

When you run your webxdc app with the [hello simulator](https://github.com/webxdc/hello)
then all browser tabs share the same localStorage or indexedDB instance
which is unlike when your webxdc app will be run by messengers. 
However, the [webxdc-dev simulator](https://github.com/webxdc/webxdc-dev) 
manages to separate storage per webxdc app instance
because each running app uses a separate localhost port for connecting
to the webxdc-dev simulator server. 
