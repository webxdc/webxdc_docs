# UI Implementation instructions

- Deny all forms of internet access
- Disable DOM storage (local storage, indexed db and co) - until we have a reliable way to scope to one app it on all platforms.
- load resources over the `webxdc:` scheme and deny others (also, the UI should handle `webxdc:` internally)
- implement fetching `webxdc.js` over that scheme, should give back a JavaScript file wrapping all native API's to the interface described above.
- info messages -> click on them should jump to their webxdc message