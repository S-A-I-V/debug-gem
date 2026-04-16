---
tags: [language, runtime, frontend, backend]
aliases: [javascript, js, node, nodejs]
---
# JavaScript

The language of the web. Runs in browsers and on servers (Node.js). Single-threaded, event-driven, dynamically typed.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `Cannot read properties of undefined` is the #1 JS error
- [[Type & Casting Errors]] — Implicit coercion madness (`"5" + 3 = "53"`)
- [[Resource Exhaustion Errors]] — Heap out of memory, event loop blocking
- [[Dependency & Import Errors]] — `MODULE_NOT_FOUND`, ESM vs CJS confusion

## Common Gotchas
- `this` binding depends on call site, not definition
- `==` does type coercion, always use `===`
- `NaN !== NaN` — use `Number.isNaN()`
- `0.1 + 0.2 !== 0.3` — floating point
- `typeof null === "object"` — historical bug
- Prototype pollution — security vulnerability

## Related
- [[TypeScript]] — typed superset
- [[Node.js]] — server-side runtime
- [[React]], [[Vue]], [[Angular]] — frontend frameworks
- [[MOC — Languages & Runtimes]]
- [[MOC — Frontend & UI]]

## My Notes

