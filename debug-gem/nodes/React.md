---
tags: [frontend, framework, ui, javascript]
aliases: [react, reactjs]
---
# React

UI library by Meta. Component-based, declarative, virtual DOM. Dominant frontend framework.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — Hook ordering, unmounted component updates, stale closures
- [[Resource Exhaustion Errors]] — Infinite re-render loops, too many re-renders
- [[Null & Undefined Reference Errors]] — Data not loaded yet, ref not attached
- [[Offset & Boundary Errors]] — Missing/duplicate keys in lists
- [[The Environment Delta]] — Hydration mismatches (SSR)

## Common Gotchas
- Hooks must be called in same order every render — no hooks in `if`/loops
- Stale closures — useEffect/useCallback capture variables at creation time
- Object/array in dependency array — compared by reference, not value
- Context re-renders ALL consumers when value changes

## Related
- [[Next.js]] — React meta-framework (SSR, routing)
- [[JavaScript]], [[TypeScript]] — languages
- [[Vue]], [[Angular]] — alternative frameworks
- [[MOC — Frontend & UI]]

## My Notes

