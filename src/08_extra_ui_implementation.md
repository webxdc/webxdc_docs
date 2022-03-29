# UI Implementation instructions

- Deny all forms of internet access
- enable DOM storage (local storage, indexed db and co), but make sure it is scoped to each webxdc so they can not delete or modify the data of other webxdc content.
- load resources over the `webxdc:` scheme and deny others (also, the UI should handle `webxdc:` internally) - can be also `https` if ui does not support the custom scheme properly.
- implement fetching `webxdc.js` over that scheme, should give back a JavaScript file wrapping all native API's to the interface described above.
- info messages -> click on them should jump to their webxdc message
