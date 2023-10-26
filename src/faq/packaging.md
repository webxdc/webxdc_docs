
# Packaging

## Optimizing Your App Icon

There are several things you can do to shrink down the size of your icon:

- save without thumbnail image (in gimp it can be done in the export dialog)
- shrink the image resolution (`256px` are enough, in some cases `128px` or even lower like `64px` can sufice)
- change your PNG colors from `RGB` to `Indexed` (in gimp `Image` -> `Mode` -> `Indexed`, see <https://docs.gimp.org/en/gimp-image-convert-indexed.html>)


> For `png` you can also use the `oxipng` tool (<https://github.com/shssoichiro/oxipng>), which automagically optimizes your icon's file size without quality loss:
> ```
> oxipng icon.png -s -o max
> ```
> 
> If you have png files in your project, you should also do this them to safe even more bytes.
>
> Noteworthy parameters:
> - `--pretend` only calculates gains
> - `-Z` even more compression, but takes longer
> - for more info see `oxipng --help`# Troubleshooting

