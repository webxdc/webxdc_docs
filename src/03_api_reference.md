## API Reference

### JavaScript Interface

TODO, please refer to <https://github.com/deltachat/deltachat-core-rust/blob/master/draft/webxdc-dev-reference.md> in the meantime.

(could we generate the markdown from the TS definion file? - one less place to update docs and so on?)

### Manifest file <a id="manifest"></a>

If the ZIP-file contains a manifest.toml in its root directory, some basic information are read and used from there.

the manifest.toml has the following format

```toml
name = "My Webxdc Name"
```

- `name` - The name of the webxdc. If no name is set or if there is no manifest, the filename is used as the name.

### Webxdc Icon <a id="icon"></a>

If the ZIP-root contains an `icon.png` or `icon.jpg`, these files are used as the icon shown in deltachat. The icon should be a square at reasonable width/height; round corners etc. will be added by the implementations as needed. If no icon is set, a default icon will be used.

### Zip File format `.xdc` <a id="zip-format"></a>

Just a zip file with the `.zip` extension renamed to `.xdc`.
Contents:

- `index.html`: entry point
- (optional) `manifest.toml`: [configuration file, specifying properties](#manifest)
- (optional) `icon.png` or `icon.jpg`: the [icon of the package](#icon)


> The ZIP-file can only use the compression methods `Deflate` and `Store`. Other compression methods are not supported at this time.
