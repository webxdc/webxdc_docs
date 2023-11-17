# Shared state

Webxdc applications simplify the task of creating a private, offline-first, multi-user application by leveraging the existing the encrypted message delivery of their host application.
One of the trade-offs of this format is that there is no central server to ensure that peers are able to synchronize the application's state.

This chapter will:

* describe common ways in which application state can become desynchronized in a peer-to-peer context
* provide an introduction to to **CRDTs** (Conflict-free Replicated Data Types) and illustrate under what circumstances their use is beneficial
* demonstrate how to adapt an application to use CRDTs to consistently synchronize shared state between multiple users or devices using practical examples

