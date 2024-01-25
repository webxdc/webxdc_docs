# Theory of Conflict-free Replicated Data Types (CRDTs)

The previous section described the circumstances
under which updates to shared state can conflict,
and introduced some techniques used to identify such conflicts.
This section will present [**Conflict-free Replicated Data Types**](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type),
a technology that automatically resolves such changes
without the need for a central authority.
It will define the specialized terminology
which seems to keep many from adopting CRDTs,
provide some basic examples,
lay out clear expectations of what they can and cannot accomplish,
and hopefully prepare you to avoid concurrency-related bugs
and enjoy the benefits of offline-first applications.

## Terminology

Just like in the conventional sense of a
[Data Type](https://en.wikipedia.org/wiki/Data_type),
a CRDT can be defined by the set of its possible values,
the set of valid operations on those values,
and low-level representation of them.
There are often many ways to represent the same information.
Knowing the range of possible values 
and the exact operations required for your particular use case
will help you choose the most effective design for the job.

_CRDTs_ go further than the basic data types
that are included in almost any programming language
in that they are designed to be
[replicated](https://en.wikipedia.org/wiki/Replication_(computing))
across multiple processes.
These processes might run on the same machine,
but are more commonly spread across a network.
To accomplish this they generally define additional methods
to track the state of remote peers,
deliver the minimal set of updates those peers lack,
and to indicate which updates are known
so that other peers can perform the same services for us.

Most importantly, CRDTs are **conflict-free**,
meaning that they are designed to handle
every possible combination of concurrent operations for a given data type
in a [deterministic](https://en.wikipedia.org/wiki/Deterministic_system#In_computer_science) way.
The states of peers can diverge temporarily,
but a CRDT guarantees [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency),
that any two peers that are aware of the same set of updates
will converge to have the same state.

## Examples

A [tally](https://www.merriam-webster.com/dictionary/tally) (or count)
is relatively simple to implement compared to many other data types.
If two people want to count the number of books on two shelves,
they can each count one of the shelves and then sum their results.
They could also call out numbers on an ongoing basis,
adding both their own discoveries
and those they hear to a mental sum.
It doesn't matter in what order values are recorded
because addition is [commutative](https://en.wikipedia.org/wiki/Commutative_property#Commutative_operations),
at least for simple numbers.
The important thing to understand is that despite any superficial similarity,
a _tally_ differs from a _number_
because a number supports many operations which cannot be applied in _any order_.
By choosing to only support addition
it can be guaranteed that all peers' tallies will converge on the same final value.

For a slightly more complex version of a tally
we can consider a situation in which the organizers of a festival
must ensure that none of its zones exceed their maximum safe occupancy.
A tally of the number of attendees in every zone can be kept
by recording the number of people passing through entrances and exits.
Rather than assuming that all additions pertain to a single value as in the last example,
we can modify our operation to specify
both _the relevant zone_ and the _change in occupancy_.
Thus, as three people move from zone A to zone B,
the checkpoint can record two events (`update('A', -3); update('B', 3)`).
As long as all checkpoints for a given zone
are able to communicate with each other with relatively little delay
then they should be able to use the information available to them
to decide whether to admit additional participants through their checkpoint.

These examples describe very basic CRDTs with narrowly scoped problems.
If you have such a problem that maps well to a commutative operation
then it might be worthwhile to design a specialized CRDT to solve it,
and it may be reassuring to know that
these data structures do not always require much complexity.

In practice the order of applications will matter,
especially when dealing with data structures like lists or arrays.
If two concurrent operations append items to the end of a list
then it's reasonable to insert them in an arbitrary sequence.
For example:

1. _Alice_ and _Bob_ both try to add elements to their local copies of an empty list (`let list = []`)
    * Alice does `list.push(5)`
    * Bob does `list.push(7)`
2. When they become aware of each others edits it would be valid to automatically resolve the concurrent changes as either `[5, 7]` or `[7, 5]`, at least under most circumstances
3. Now, if _Charlie_ comes along, learns of both changes, and then tries to append yet another item, most would expect it to be added to the end:
    * Charlie does `list.push(11)`
4. Valid outcomes are either `[5, 7, 11]` or `[7, 5, 11]`

As described in the last section of this chapter,
there are techniques to differentiate between these two types of circumstance,
identifying which operations are concurrent or consecutive.
In order to automatically resolve such conflicts when concurrent operations do occur,
such types must also define deterministic strategies
to allow all participants to choose the same ordering out of a set of all possible options.
Different CRDT libraries may use different resolution strategies,
but in most cases the choice of mechanism is essentially arbitrary
as long as it meets some basic conditions.

## Expectations

CRDTs are a very broad class of data structures with wide variety of possible implementations,
even for superficially similar types.
What they all have in common is a guarantee of _eventual consistency_,
that all participants in a system will agree on the final state of the structure
as long as they are all aware of the same set of updates.
All updates will be merged _automatically_, regardless of their order or the degree to which they intersect.

With that said, whether or not the structure's final state matches
the expectations of those using the application is a matter of _design_.
If two peers concurrently increment a number from `5` to `6`,
one system might decide that the two peers agree the new state should be `6`,
while another might consider it appropriate to increment twice to `7`.

In the event that one of the basic data types of a general-purpose CRDT like
[Yjs](https://yjs.dev/) _does not_ match your expectations the library
may still be suitable for your use.
Different behaviours can be accomplished by composing the built-in types into more complex ones.
_Yjs_ will treat concurrent changes to a number as two assignments.
If you prefer for them to be treated as increments then you can instead
encode each addition as a new member of an array of numbers to be summed.
The higher-level value can then be derived from the array whenever it is required,
while the lower-level representation serves as a simplified way to achieve consistency.
It is common for collaborative applications built on CRDTs to follow this sort of _schema_ pattern,
in which user actions are translated into operations on the shared state,
with remote changes propagating back to the UI.

A well-designed CRDT will handle all aspects of ordering messages,
including the internal implementation of a logical clock,
the detection of concurrency,
and the resolution of overlapping changes.
This enables peers to queue updates while entirely offline,
and to merge their local state with others' when they are once again able to communicate.
While this behaviour can be very helpful for application developers,
it may not free you entirely from having to think about network conditions.
_Eventually-consistent_ application state should generally be treated as
subjective, which can be a significant shift if you are used to having a server acting as an authority.
That means that conditional behaviour that you'd usually treat as _yes_ and _no_,
may instead behave more like _currently_ and _not yet_.

This section has discussed attributes of CRDTs that are mostly theoretical.
The next section will give more concrete examples using **Yjs**,
with a particular focus on how it can be used to accomplish common
goals within webxdc applications as implemented in existing webxdc platforms.

