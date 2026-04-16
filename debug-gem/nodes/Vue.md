---
tags: [frontend, framework, ui, javascript]
aliases: [vue, vuejs]
---
# Vue

Progressive JavaScript framework. Approachable, performant, versatile. Composition API (Vue 3) or Options API.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — Reactive data not initialized
- [[State & Lifecycle Errors]] — Mutating props directly, maximum recursive updates
- [[Resource Exhaustion Errors]] — Watcher triggering itself

## Common Gotchas
- Vue 2 reactivity: adding new object properties isn't reactive (use `Vue.set`)
- Vue 3: destructuring `reactive` loses reactivity (use `toRefs`)
- `v-if` removes from DOM, `v-show` hides with CSS — different lifecycle behavior

## Related
- [[React]], [[Angular]] — alternative frameworks
- [[MOC — Frontend & UI]]

## My Notes

