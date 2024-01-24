# Shared Web Application state

In a typical multi-user web application,
clients download the app's source from a central HTTP server
and rely on it to relay messages to others.
In this [client-server model](https://en.wikipedia.org/wiki/Client%E2%80%93server_model),
the server can leverage its central position of authority
to ensure that all connected clients receive messages in the same order.

Even though _webxdc applications_ can be used in situations
where all members of a given chat are hosted by a single server,
the specification does not assume this will be the case
and in fact is intended to support both federated (multi-server)
and fully [peer-to-peer](https://en.wikipedia.org/wiki/Peer-to-peer) modes of operation.
In addition, webxdc supporting messengers that implement offline-first messaging
induce arbitrary delays in message delivery.
Different clients may thus receive updates in very different orders,
and consequently apply those updates in sequences that produce conflicting outcomes.

There are a variety of strategies and technologies
for either avoiding or resolving such conflicts.
The webxdc specification is deliberately agnostic about their use,
allowing app authors to choose the approach
which is most appropriate for their needs.

This chapter will:

* describe common ways in which state can become desynchronized in a peer-to-peer context
* provide an introduction to **CRDTs** (Conflict-free Replicated Data Types)
and illustrate under what circumstances their use is beneficial
* demonstrate how to adapt an application to use CRDTs
to consistently synchronize shared state
between multiple users or devices using practical examples

