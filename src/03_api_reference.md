## API Reference

### JavaScript Interface

TODO, please refer to <https://github.com/deltachat/deltachat-core-rust/blob/master/draft/webxdc-dev-reference.md> in the meantime.

(could we generate the markdown from the TS definion file? - one less place to update docs and so on?)


### Manifest file

If the ZIP-file contains a manifest.toml in its root directory, some basic information are read and used from there.

the manifest.toml has the following format

```toml
name = "My App Name"
```

- `name` - The name of the app. If no name is set or if there is no manifest, the filename is used as the app name.

### App Icon

If the ZIP-root contains an `icon.png` or `icon.jpg`, these files are used as the icon for the app. The icon should be a square at reasonable width/height; round corners etc. will be added by the implementations as needed. If no icon is set, a default icon will be used.
