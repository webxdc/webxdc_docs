# Detecting conflicts

Modifications to shared state can be considered conflicting when two conditions are met:

1. They affect an overlapping section of a data structure, such as if two users both try to set the title of the same todo-list item

2. They are [concurrent](https://en.wikipedia.org/wiki/Concurrency_(computer_science)) -
meaning that the participants in the system cannot
conclusively determine the order in which they occurred

_Concurrent updates which affect unrelated data_
can be resolved without issue, as can
_sequential updates to related data_.

Concurrency can effectively be ignored from a client's perspective
in schemes that use a central server with online clients.
Centralized web apps using an always online central HTTP server
are a popular example of this model.
Centralized HTTP servers can not only guarantee a
[total order](https://en.wikipedia.org/wiki/Total_order) of messages
but they can also authoritatively resolve conflicts
and provide the single "source for truth"
for all associated web apps running on their client devices.
In a decentralized messaging model, and in particular with end-to-end encrypted messaging,
both a Total Order of messages and a "single source of truth" can not be assumed.

## Conflict resolution in webxdc applications

Many webxdc applications are incapable of producing conflicts,
either because they do not use the network communication APIs,
or because any given data structure can only have one writer.
For example, many of the available [webxdc games](https://webxdc.org/apps/) offer a high-score table
and how users scored will typically be reported back to the chat.
Such a gaming app will simply post their user's highscore
and everybody will collect all arriving highscore data
and show it to their users in a sorted list.
There is hardly any problem if a message is lost or messages get re-ordered.
Eventually everybody will see roughly the same highscore list. 
It is worth considering that the webxdc host application
might permit a single user to use multiple devices.
For example, a password-manager webxdc app could be used in a "Saved Messages"
or "Self-messages" chat so that only a single user's devices will share the application state.
Such a multi-device setting already constitutes a minimal P2P system
where total message ordering can not be guaranteed
and there is no single source of truth.

Webxdc applications can use [`webxdc.sendUpdate()`](../spec/sendUpdate.html#sendupdate)
and [`setUpdateListener`](https://docs.webxdc.org/spec/setUpdateListener.html)
to send and receive data to and from other devices.
It then is necessary to provide some mechanism
through which all clients can determine a total order for all updates,
or can otherwise arrive at a shared consistent view on different devices,
once all messages have been delivered.
A complete solution for this is non-trivial,
but there are relatively simple mechanisms which can provide
a [partial ordering](https://en.wikipedia.org/wiki/Partially_ordered_set) for updates.

## Partial ordering

It can be tempting to add a timestamp to each update
and to order them by this value.
Computers can have inaccurate physical clocks, however,
so relying on them to order messages can be problematic,
potentially resulting in messages that appear to
come from the impossibly distant past or future.
At a more theoretical level,
[time is an entirely subjective measurement](https://en.wikipedia.org/wiki/Time_dilation),
so there are fundamental flaws with this approach
even with perfectly reliable atomic clocks.

[Logical clocks](https://en.wikipedia.org/wiki/Logical_clock) are a more reliable option 
that make it possible to order the majority of events under normal circumstances.
For example, [Lamport timestamps](https://en.wikipedia.org/wiki/Lamport_timestamp)
include a sequence number in every update,
and increment its counter to one more than the largest known value.
Multiple messages with the same sequence number
can then be taken as an indication of concurrency.

This method is popular both for its relative simplicity
and the small amount of overhead that it introduces.
For example, it is used by [Yjs](https://yjs.dev/),
which will be discussed later in this chapter.

Another popular approach which is more explicit
but less compact involves the use of
[cryptographic hash functions](https://en.wikipedia.org/wiki/Cryptographic_hash_function).
Instead of a single counter,
messages include a list of hashes of the preceding messages
which were known at the time the message was authored.
This makes it possible to detect when a message has not been delivered
or if message history has somehow been altered
(although the latter probably isn't a concern with webxdc apps).
Variations of this technique are applied in [Git](https://git-scm.com/),
[BitTorrent](https://en.wikipedia.org/wiki/BitTorrent),
[Bitcoin](https://en.wikipedia.org/wiki/Bitcoin),
[Matrix](https://en.wikipedia.org/wiki/Matrix_(protocol)),
[Secure Scuttlebutt](https://en.wikipedia.org/wiki/Secure_Scuttlebutt),
[IPFS](https://en.wikipedia.org/wiki/InterPlanetary_File_System),
and many other protocols intended to solve a variety of problems.

Either of these general techniques convey a notion of _causality_.
Techniques to resolve conflicts can be computationally expensive,
or can require manual human intervention.
Tracking causality and ruling out conflicts
are an effective first line of defense in peer-to-peer systems
where messages are not guaranteed to be ordered.

With the knowledge of how conflicts can occur in distributed systems,
and some basic techniques to reduce their frequency,
we can now move on and introduce an evolving technology
designed to eliminate them entirely.

