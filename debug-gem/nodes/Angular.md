---
tags: [frontend, framework, ui, typescript]
aliases: [angular]
---
# Angular

Full-featured TypeScript framework by Google. Opinionated, enterprise-focused. Dependency injection, RxJS, modules.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — `ExpressionChangedAfterItHasBeenCheckedError`
- [[Dependency & Import Errors]] — `NullInjectorError`, circular dependencies, missing module imports

## Common Gotchas
- Change detection checks all components by default — use `OnPush` for performance
- RxJS subscriptions not unsubscribed = memory leaks — use `async` pipe or `takeUntilDestroyed`
- AOT compilation (production) is stricter than JIT (development)

## Related
- [[React]], [[Vue]] — alternative frameworks
- [[TypeScript]] — Angular is TypeScript-first
- [[MOC — Frontend & UI]]

## My Notes

