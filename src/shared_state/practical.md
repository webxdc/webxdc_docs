# Practical CRDT usage

If you've read the previous two chapters
then you should already have a good understanding of what a CRDT is,
and the circumstances under which they can be helpful tools.
Otherwise, if you're not interested in the theory
and prefer to just jump in with tangible examples then this page is for you.
We'll focus on [Yjs](https://github.com/yjs/yjs/)
and particularly how it can be integrated into a webxdc app.

## What is Yjs?

**Yjs** is a widely used CRDT library written in JavaScript
which supports a number of complex data types,
including Arrays, Maps, Text (including rich text, not just strings), and XML.
It is highly efficient compared to many other CRDTs
in terms of space (disk and memory usage),
time (the computational effort required to formulate a new update or apply one authored by a remote client),
and network transmission cost (the size of updates sent to other clients over the internet).

Yjs is designed to be network agnostic,
meaning that it doesn't care whether it is reliably online,
or how one client's device might connect
to that of another when a network connection is available.
Normally this would mean that it is the app author's responsibility
to provide connectivity between the library and remote clients,
but Yjs supports the use of [_Providers_](https://github.com/yjs/yjs/#providers)
which manage the complicated details of that process.
For webxdc app authors, there is already
a [webxdc Yjs provider](https://codeberg.org/webxdc/y-webxdc) available.
In addition to simplifying initial app development,
the use of providers makes it easier to port apps to other platforms,
or to port existing Yjs-based apps to webxdc.

The library is available under the terms of
the highly permissive [MIT license](https://choosealicense.com/licenses/mit/),
meaning that it can be easily included in any webxdc app
whether the source is public or not.

## How does it work?

The core of each Yjs-based application is its _document_:

```javascript
import * as Y from 'yjs';

const ydoc = new Y.Doc();
```

Whenever this document changes it will emit `update` events.
Before you make any local changes
it's important to set up an event listener to handle these updates.

```javascript
ydoc.on('update', (update) => {
    console.log(update);
    /* send the emitted update to remote clients */
});
```

### Encoding updates and handling delivery

Updates are encoded as
[Uint8Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array),
a form of [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)
which can only contain 8-bit unsigned integers
(whole numbers  between `0` and `255`) as elements:

```javascript
Uint8Array(20) [
    1,   1, 253, 161, 163, 244,  12,
    0,   8,   1,   5, 111, 114, 100,
  101, 114,   1, 125,   5,   0
]
```

In some circumstances this could sent over the network in binary format,
but [webxdc updates](../spec/sendUpdate.html)
do not permit binary data in their payloads.
This makes it necessary to convert it to a serializeable string format
like [Base64](https://developer.mozilla.org/en-US/docs/Glossary/Base64).
Conversion can be done with an npm module like [js-base64](https://www.npmjs.com/package/js-base64),
or via the following snippets taken from the MDN web docs:


```javascript
function base64ToBytes(base64) {
    const binString = atob(base64);
    return Uint8Array.from(binString, (m) => m.codePointAt(0));
}

function bytesToBase64(bytes) {
    const binString = String.fromCodePoint(...bytes);
    return btoa(binString);
}
```

So, an update can be handled like this:

```javascript
ydoc.on('update', (update) => {
    const base64 = bytesToBase64(update);
    webxdc.sendUpdate({
        payload: base64,
        info: 'A new update for your Yjs app',
    }, 'Yjs update');
});
```

The app will also need a corresponding listener to load your past updates
and handle incoming events from other clients in real-time.

```javascript
webxdc.setUpdateListener((update) => {
    const decoded = base64ToBytes(update.payload);
    Y.applyUpdate(ydoc, decoded);
});
```

The underlying transport for webxdc apps typically introduces some size overhead,
and Yjs is able to save space when several updates are bundled together into one,
so sending an update for every minor change is inefficient.
These snippets are given as examples to help understand how Yjs-based webxdc apps work,
but for practical usage you will probably want to use
the [y-webxdc provider](https://codeberg.org/webxdc/y-webxdc)
which maintains a queue of updates which are periodically bundled together
and sent as a single update.

### Using shared types

A Yjs document is essentially a collection of all the shared types
that your app will need to replicate between peers.
Once your document has been instantiated
and connected to webxdc you can start adding data to it.

#### Arrays

The snippet below creates a [Yjs Array](https://docs.yjs.dev/api/shared-types/y.array)
which is similar but not identical to a normal JavaScript array.

```javascript
// myList is a reference to a named part of the top-level Yjs document
const myList = ydoc.getArray('myList');

// pushing to the array modifies it and triggers an update
myList.push(['a', 'b']);
```

Suppose the above changes were triggered by one client (_Alice_),
while another (_Bob_) pushed some different values to the array at the same time:

```javascript
const myList = ydoc.getArray('myList');
myList.push(['c']);
```

Both clients attempted to push their changes onto the end of an empty array.
Alice pushed her two strings as a single action,
so it's clear that she intends for those two items to be adjacent.
Bob pushed only a single item.

It is possible to apply both of these operations, however,
it's ambiguous which should be applied first.
Yjs resolves such changes using the relevant clients' client ids
(randomly self-assigned integers) to break ties.
Depending on their client ids, the result will be either

1. `['a', 'b', 'c']`

...or

2. `['c', 'a', 'b']`

#### Maps

Yjs is described by its author as a _Sequence CRDT_,
meaning that all of its shared types
are internally represented as sequences of values.
That might be relatively intuitive for _Arrays_,
but it's much less obvious how a Map might be implemented
with that underlying representation.
The explanation is that each value in a map is stored as _its own sequence_,
with the surface-level value derived from the final element of the sequence.

[Yjs Maps](https://docs.yjs.dev/api/shared-types/y.map) are defined in a similar manner as arrays:

```javascript
const mymap = ydoc.getMap('mymap');
```

...from here, Alice and Bob can make concurrent changes to their local maps:

```javascript
// Alice makes two consecutive changes

mymap.set('value', 'a');
mymap.set('value', 'b');
```

```javascript
// Bob makes a single change to the same attribute on his map
mymap.set('value', 'c');
```

As with the previous _array_ example,
Yjs can determine from context that
Alice's two successive values have a meaningful relationship,
namely that she intended for `"b"` to replace the previous value of `"a"`.
When arranging these values in a sequence,
it will therefore ensure that they remain adjacent
and that their order is preserved.
As before, there are then two equally valid arrangements,
and Yjs can arbitrarily decide which to choose
based on their authors' client IDs, either:

1. `['a', 'b', 'c']`

...or

2. `['c', 'a', 'b']`

The final value of mymap.value will therefore be the last element
of either of these sequences (`'c'` or `'b'`).

#### Text

The [Yjs Text type](https://docs.yjs.dev/api/shared-types/y.text)
once again differs from
the [native JavaScript String type](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String).
It is intended to represent not just plain text,
but rich text with very complex formatting.

As with all other types,
_Text_ elements are created as a part of the Yjs document
and are represented internally as sequences.
Each text node can optionally have formatting information associated with it,
and unless you are experienced with the internals of text editors
the API for managing this can be quite intimidating.
Fortunately, there are already a variety of open-source editors based on Yjs,
so rather than having to manage text nodes
and styles directly it is more practical to choose
one of the existing [editor bindings](https://docs.yjs.dev/ecosystem/editor-bindings)
and adapt their examples to suit your needs.

The [webxdc editor app](https://codeberg.org/webxdc/editor)
can serve as an example of how to use both Prosemirror
and the webxdc Yjs provider by simply passing in the entire Yjs document.
Alternatively, the [y-quill editor binding](https://github.com/yjs/y-quill/)
binds to a single instance of a Text type,
which might be more appropriate if your app requires
a number of collaborative text fields rather than a single shared editor.

#### XML

Yjs supports a number of other types related to XML:

* [XmlElements](https://docs.yjs.dev/api/shared-types/y.xmlelement)
* [XmlFragments](https://docs.yjs.dev/api/shared-types/y.xmlfragment)
* [XmlText](https://docs.yjs.dev/api/shared-types/y.xmltext)

Like Text, these types are very powerful,
but they can also be quite difficult to use.
They allow you to replicate a complex XML document structure
made up of tags with arbitrary attributes and nested text nodes.
Aside from the complexity of working with
an arbitrarily complex tree structure,
there are also possible security implications involved,
as it might be possible for other users to inject scripts into such documents
and trigger code execution on remote devices depending on your usage.

## Testing 

In most cases it will not be necessary to consider
exactly how concurrent operations will be resolved, however,
having a basic understanding of these principles may help avoid surprising edge cases.
Yjs does a fairly good job of matching most people's expectations
for how conflicts should be handled,
but when in doubt it is best to test your assumptions explicitly.

It is possible to test different situations by instantiating
two or more documents in a single script
and manually applying operations in different orders
to confirm whether the expected outcomes are produced.


```javascript
import * as Y from 'yjs';

const docA = new Y.Doc();
const docB = new Y.Doc();

// define arrays to store each document's pending operations
const aOperations = [];
const bOperations = [];

// listen for update events and store them in the array
docA.on('update', (update) => aOperations.push(update));
docB.on('update', (update) => bOperations.push(update));

// copy references to both local arrays
const aList = docA.getArray('list');
const bList = docB.getArray('list');

// make concurrent overlapping changes to both arrays
aList.push([5]);
bList.push([7]);

// apply A's updates to B
aOperations.forEach(update => {
    Y.applyUpdate(docB, update);
});

// apply B's updates to A
bOperations.forEach(update => {
    Y.applyUpdate(docA, update);
});

// we expect A and B to be equal
// stringifying is a cheap way to compare object equality
// if we know their keys will be in the order
if (JSON.stringify(aList) !== JSON.stringify(bList)) {
    throw new Error("A and B did not converge!");
}

// it is not easy to know which of two outcomes will occur
// but we can prepare an array of valid outcomes
// and check that the eventual result is in that array
const expected = [
    [5,7],
    [7,5]
].map(array => JSON.stringify(array));

// throw an error if an unexpected result occurs
[
    aList,
    bList
].forEach(array => {
    const stringified = JSON.stringify(array);
    if (!expected.includes(stringified)) {
        throw new Error("CRDT output did not match expected values");
    }
});
```

Minimal test cases like this can
confirm or disprove your intuition about how Yjs
or any CRDT will perform in practice.
This becomes increasingly important
the more complex your document's structure becomes.

## Nested structures

The Yjs docs describe another way of declaring shared types
not demonstrated in the examples above:

```javascript
// the basic way

// Method 1: Define a top-level type
const ymap = ydoc.getMap('my map type')
// Method 2: Define Y.Map that can be included into the Yjs document
const ymapNested = new Y.Map()

// Nested types can be included as content into any other shared type
ymap.set('my nested map', ymapNested)
```

This is one area where it might be particularly important
to verify that different concurrent operations will behave as expected.
For example, if one client adds a field to a nested map
while another client deletes that map,
then the addition of the new field will have no effect.
Under most circumstances that will line up with people's intuition,
but there are some surprising edge cases to consider.

One notable edge case occurs because
Yjs does not provide a mechanism to express an intention to _move_
a value from one location to another,
instead forcing developers to delete from the original location
and insert a copy at a new location.
In applications where such a procedure occurs frequently
it becomes more likely that one client's change
will be silently dropped because another client moved an item.

## Designing data structures

A well-specified document structure can make surprising behaviour less likely in an application.
Consider a multi-user to-do list application,
in which users can collaboratively create, move, and delete cards
with a variety of data, such as titles, descriptions, expected completion dates,
and a checkbox to indicate its completion.

One way to represent this data is an array of Maps.

```json
[
    {
        title: "wash dishes",
        description: "don't forget the thermos in your bag",
        complete: false
    },
    {
        title: "water houseplants",
        description: "don't overwater the aloe or it will get mites",
        complete: false
    }
]
```

As mentioned in the previous section,
reordering the _"water houseplants"_ to the top position
would mean deleting one and recreating it at the beginning of the array.
If someone had watered the plants
and updated the card concurrently with that move,
their change would get ignored
and it would remain in an incomplete state,
making it likely that the plants would receive too much water.

An alternative structure which avoids this problem could look like this:

```json
{
    order: [
        "025322791196985772",
        "34064380536730887"
    ],
    cards: {
        "025322791196985772": {
            title: "wash dishes",
            description: "don't forget the thermos in your bag",
            complete: false
        },
        "34064380536730887": {
            title: "water houseplants",
            description: "don't overwater the aloe or it will get mites",
            complete: false
        }
    }
}
```

This structure assigns each card a random id,
stores the values of its fields in a map
which can be referenced by its id,
and indicates cards ordering
by the id's position in the `order` array.
Removing an id from the order array
and reinserting it elsewhere will not affect the card's underlying data,
allowing for concurrent edits and move operations.

One side effect of this design is that
removing an id from the `order` array
will not automatically delete the associated data.
If this is overlooked then the data for old cards
might just build up over time.
This could be handled with an option to view _archived_ cards,
possibly with an option to delete them.

This approach introduces the possibility for some new problems.
Two clients could create their own cards which share the same id,
in which case the data from one might overwrite that of another.
Similarly, the same id could be injected into two places in the `order` array.

It is hard to guarantee zero chance of a collision,
but in practice they are incredibly unlikely to occur
if the random ids are sufficiently long.
They could be made even less likely by prefixing a per-user
or per-device value to the id,
along with some checks to ensure
that an id is not known to be in use by any other clients before adopting it.

As for the matter of duplicate ids in the `order` array,
the rendering code which constructs the app's UI from this data
could ignore repeated elements when iterating over them.

## Learning more

Many more examples can be found throughout
[the Yjs docs](https://docs.yjs.dev/)
or by reviewing [projects who have used Yjs](https://docs.yjs.dev/)
(though not all of these are open-source).

There is also [a forum](https://discuss.yjs.dev/)
where Yjs users and contributors can ask questions or share insights.

The author of Yjs has [written](https://blog.kevinjahns.de/are-crdts-suitable-for-shared-editing/)
and [talked](https://youtu.be/0l5XgnQ6rB4?si=TyPzJ1A0EQl8o1Zq)
extensively about the library's design and implemention.

As for webxdc-specific implementation details,
try the list of [webxdc topic on the delta.chat forum](https://support.delta.chat/c/webxdc/20).

There are also several existing CRDT-based webxdc apps which can be used as references:

* [webxdc/editor](https://codeberg.org/webxdc/editor)
(mentioned above) demonstrates the use of Yjs
with [prosemirror](https://prosemirror.net/)
and the [y-webxdc-provider](https://npmjs.org/package/y-webxdc)
* [webxdc/checklist](https://codeberg.org/webxdc/checklist/)
uses the [Automerge CRDT](https://automerge.org/)
to implement a collaborative checklist

