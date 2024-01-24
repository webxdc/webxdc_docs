# importFiles

```js
let files = await window.webxdc.importFiles(filter);
```

`importFiles()` allows a webxdc app to import files.
Depending on platform support, this just opens the system file picker or a custom one.
This custom file picker should show recent attachments that were received and sent,
to make importing of a file that you just received from someone easier
(you don't need to save it to the file system first),
but it also still shows a button to open the system file picker.

- `filter`: an object with the following properties:
  - `filter.extensions`: optional - Array of extensions the file list should be limited to.
    Extensions must start with a dot and have the format `.ext`.
    If not specified, all files are shown.
  - `filter.mimeTypes`: optional - Array of mime types
    that may be used as an additional hint eg. in case a file has no extension.
    **Files matching either `filter.mimeTypes` or `filter.extensions` are shown**.
    Specifying a mime type requires to list all typical extensions as well -
    otherwise, you may miss files.
    See <https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/accept#unique_file_type_specifiers>
    for details about the format.
  - `filter.multiple`: whether to allow multiple files to be selected, false by default

The method returns a `Promise` that resolves to an Array of `File`-Objects.

Example:
```js
// then/catch
window.webxdc.importFiles({
  mimeTypes: ["text/calendar"],
  extensions: [".ics"],
}).then((files) => {
  /* do sth with the files */
}).catch((error) => {
    console.log(error);
});
// async/await
try {
  let files = await window.webxdc.importFiles({
    mimeTypes: ["text/calendar"],
    extensions: [".ics"],
  })
  /* do sth with the files */
} catch (error) {
  console.log(error);
}
```


