# The Webxdc Documentation

## Building the Documentation

Install requirements:

```
cargo install mdbook
```

Build the documentation:

```
mdbook build
```

Building with live-reloading:

```
mdbook serve --open
```

## Add a page

Add a link to the new page in `src/SUMMARY.md` then run `mdbook build` or `mdbook serve` (it automatically creates the file for the new page). Then put content onto your new page.

---

The documentation is generated using <https://rust-lang.github.io/mdBook/>.
