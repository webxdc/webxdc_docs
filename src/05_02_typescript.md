# Typescript support

How to get autocompletion for window.webxdc api in you IDE via typescript.

## Get the typescript definitions

Just copy [`webxdc.d.ts`](https://github.com/deltachat/webxdc_docs/blob/master/webxdc.d.ts) into your source dir:

```typescript
{{#include ../webxdc.d.ts:3:}}
```

you can find it also on <https://github.com/deltachat/webxdc_docs/blob/master/webxdc.d.ts> or just copy the code block above.

> In the future this might become an @types npm module, but for now it is what it is: _a simple file copy with **no** automatic updates_.

## Using the types

for using the types you need to import the file.

### Using in typescript

```tyspescript
import type { Webxdc } from './webxdc.d.ts'
```

### Using in Javascript

```javascript
/**
 * @typedef {import('./webxdc').Webxdc} Webxdc
 */
```

This works in vscode nicely together with the `//@ts-check` comment on top of your source file.

If you want you can also type your own functions using [JSDoc comments](https://jsdoc.app/).

> If you don't use vscode you can still make use of the type checking with the typescript compiler:
>
> ```sh
> npm i -g typescript # -g stands for global installation
> tsc --noEmit --allowJs --lib es2015,dom *.js
> ```

## Own Payload type

If you have a type for your state update **payloads**, replace the `any` in `Webxdc<any>` with your own payload type:

```typescript
{{#include ../webxdc.d.ts:global}}
```
