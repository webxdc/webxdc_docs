# Shared state

**TODO** **WIP**

* unlike conventional web appllications which can rely on a server to act as an authoritative source of shared state, potential conflicts between peers must be resolved on each device that runs a webxdc app

* this section will describe:
  * what types of conflicts can occur in peer-to-peer contexts
    * unreliable system clocks
    * race conditions using _last-write-wins_
  * how to tell if a simple approach is sufficient for your app or if you require more advanced techniques

* a gentle introduction to [CRDTs](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type)

* practical examples using [Yjs](https://github.com/yjs/yjs/)
  * emphasis on lists/maps

