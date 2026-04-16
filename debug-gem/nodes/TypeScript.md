---
tags: [language, frontend, backend]
aliases: [typescript, ts]
---
# TypeScript

Typed superset of [[JavaScript]]. Types exist at compile time only — runtime is still JS.

## Known For These Error Patterns
- [[Type & Casting Errors]] — `Type X is not assignable to type Y` (the most common TS error)
- [[Null & Undefined Reference Errors]] — `Object is possibly undefined` (strict null checks)
- [[Dependency & Import Errors]] — Missing `@types` packages, declaration file mismatches

## Common Gotchas
- Types disappear at runtime — `as` assertions are lies to the compiler
- `any` defeats the purpose — use `unknown` and narrow
- `strict: true` catches way more than default — enable it
- Structural typing, not nominal — same shape = same type

## Related
- [[JavaScript]] — compiles to JS
- [[MOC — Languages & Runtimes]]

## My Notes

