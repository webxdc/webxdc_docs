# Frequently Asked Questions 

## Can i use local storage or IndexDB in my webxdc app? 

Yes, you can use both local storage and IndexDB in your app
but aware of some limitations. 

LocalStorage [has a limit of 4-10MB](https://stackoverflow.com/questions/2989284/what-is-the-max-size-of-localstorage-values/) which you can fill up quickly if not careful. 
[IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 
is an alternative you can use that doesn't have this size limitation. 

Note that browsers might reclaim storage for both localStorage and IndexedDB
after a longer time of not using a webxdc app. 
If you want to safely persist data, you must send an application update 
which will be safely persisted by the messenger,
and which also allows to use an app on multiple devices. 

