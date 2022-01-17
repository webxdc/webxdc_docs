# Typescript support

How to get autocompletion for window.webxdc api in you IDE via typescript.

## Get the typescript definitions

Just copy [`webxdc.d.ts`](#TODO-LINK) into your source dir:

```typescript
{{#include ../webxdc.d.ts:3:}}
```

you can find it also on #TODO-LINK or just copy the code block above.

> In the future this might become an @types npm module, but for now it is what it is: a simple file copy with no automatic updates.

## Using the types

for using the types you need to import the file.

### Using in typescript

```tyspescript
import type { WEBxDC } from './webxdc.d.ts'
```

### Using in Javascript

```javascript
/**
 * @typedef {import('./webxdc').WEBxDC} WEBxDC
 */
```

This works in vscode nicely together with the `//@ts-check` comment on top of your source file.

## Own Payload type

If you have a type for your state update **payloads**, replace the `any` in `WEBxDC<any>` with your own payload type:

```typescript
{{#include ../webxdc.d.ts:global}}
```
