
# TypeScript support

## How to get autocompletion in your IDE via TypeScript? 

Install the [webxdc-types node package](https://www.npmjs.com/package/webxdc-types#types-for-webxdc) via `npm`
and follow [the instructions in its README](https://www.npmjs.com/package/webxdc-types#usage).

Alternatively, copy
[`webxdc.d.ts`](https://github.com/webxdc/webxdc_docs/blob/master/webxdc.d.ts)
into your source dir and follow the instructions below.

### How to use webxdc types? 

Start by importing the file.

In TypeScript: 

```typescript
import type { Webxdc } from './webxdc.d.ts'
```

In JavaScript:

```javascript
/**
 * @typedef {import('./webxdc').Webxdc} Webxdc
 */
```

This works in VS Code nicely together with the `//@ts-check` comment on top of your source file.

If you want you can also type your own functions using [JSDoc comments](https://jsdoc.app/).

> If you don't use VS Code you can still make use of the type checking with the TypeScript compiler:
>
> ```sh
> npm i -g typescript # -g stands for global installation
> tsc --noEmit --allowJs --lib es2015,dom *.js
> ```

### How to add your own application update payload type? 

If you have a type for your state update **payloads**, replace the `any` in `Webxdc<any>` with your own payload type:

```typescript
{{#include ../webxdc.d.ts:global}}
```


