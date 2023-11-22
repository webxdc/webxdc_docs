# CRDTs

The previous section described a variety of errors that can occur when concurrent modifications are made to data that is stored in multiple locations.
This section will present [**Conflict-free Replicated Data Types**](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type), a technology designed with the explicit goal of enabling exactly these types of behaviours.
It will define the specialized terminology which seems to keep many from adopting CRDTs,
provide some basic examples,
lay out clear expectations of what they can and cannot accomplish,
and hopefully prepare you to avoid concurrency-related bugs and enjoy the benefits of offline-first applications.

## Terminology

Just like in the conventional sense of a [Data Type](https://en.wikipedia.org/wiki/Data_type),
a CRDT can be defined by the set of its possible values,
the set of valid operations on those values,
and low-level representation of those values.
There are many possible types, and while some may be completely inapplicable to a given situation, there are often many ways to accomplish any particular goal.
An understanding of many types' behaviours will help you write software that is more suited for its tasks.

_CRDTs_ go further than the basic data types that are included in almost any programming language in that they are designed to be [replicated](https://en.wikipedia.org/wiki/Replication_(computing)) across multiple processes.
These processes might run on the same machine, but are more commonly spread across a network.
To accomplish this they generally define additional methods to track the state of remote peers, deliver the minimal set of updates they lack, and to request updates that are absent locally.
In order to make this process as efficient as possible many CRDT implementations output updates as a maximally compact binary format.
Because webxdc updates cannot include binary data it is therefore necessary to convert binary updates into a format like [base64](https://developer.mozilla.org/en-US/docs/Glossary/Base64), and from base64 back to binary when remote updates are received.

Most importantly, CRDTs are **conflict-free**, meaning that they are designed to handle every possible combination of concurrent operations for a given data type in a [deterministic](https://en.wikipedia.org/wiki/Deterministic_system#In_computer_science) way.
The states of peers can diverge temporarily, but a CRDT guarantees [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency),
that any two peers that are aware of the same set of updates will converge to have the same state.

## Examples

A [tally](https://www.merriam-webster.com/dictionary/tally) (or count) is relatively simple to implement compared to many other data types.
If two people want to count the number of books on two shelves, they can each count one of the shelves and then sum their results.
They could also call out numbers on an ongoing basis, adding both their own discoveries and those they hear to a mental sum.
It doesn't matter in what order values are recorded because addition is [commutative](https://en.wikipedia.org/wiki/Commutative_property#Commutative_operations), at least for simple numbers.
The important thing to understand is that a _tally_ differs from a _number_, because a number supports many operations which cannot be applied in _any order_.
By choosing to only support addition it can be guaranteed that all peers' tallies will converge on the same final value.

For a slightly more complex version of a tally we can consider a situation in which the organizers of a festival must ensure that none of its zones exceed their maximum safe occupancy.
A tally of the number of attendees in every zone can be kept by recording the number of people passing through entrances and exits.
Rather than assuming that all additions pertain to a single value as in the last example, we can modify our operation to specify both _the relevant zone_ and the _change in occupancy_.
Thus, as three people move from zone A to zone B, the checkpoint can record two events (`update('A', -3); update('B', 3)`).
As long as all checkpoints for a given zone are able to communicate with each other with relatively little delay
then they should be able to use the information available to them to decide whether to admit additional participants through their checkpoint.

These examples describe very basic CRDTs with narrowly scoped problems.
If you have such a problem that maps well to a commutative operation then it might be worthwhile to design a specialized CRDT to solve it, and it may be reassuring to know that these data structures do not always require much complexity.

In practice the order of applications will matter, especially when dealing with data structures like lists or arrays.
If two concurrent operations append items to the end of a list then it's reasonable to insert them in an arbitrary sequence.
Most would agree, however, that an another append operation occurring after those two would rightfully be placed at the end of the list.
Such types must encode enough information to allow peeers to determine the correct order of a sequence, and must also define strategies for resolving any possible dispute.
The topic of how popular CRDT libraries implement these mechanisms is very interesting, but unfortunately beyond the scope of this document.
As a prospective user of such a library the important thing to understand is that they do typically encode some opinions about the correct way to interpret such concurrent operations.

## Expectations

* a very broad class of data structures
* a matter of design, no silver bullet

---

* eventual consistency
* automatic resolution without interactive review
* guaranteed to converge - but on what?
* not guaranteed to match user intuition or expectations

---

* defines a limited set of possible values and permitted operations
* might be a subset of what you are used to in the host language
* it is easy to confuse the CRDT implementation with other superficially similar types (e.g. numbers and tallies)
* you may need to write a schema layer which translates user actions into operations that are supported by the CRDT

---

* provides a subjective view, not objective
* eliminates concerns about multiple concurrent authors perceiving inconsistent states
* does not eliminate the need to consider network behaviour
  * different CRDT libraries might do more or less to simplify these tasks
* requires that we reason about state differently
  * no => not that I know of

