---
tags: [frontend, framework, react, ssr, fullstack]
aliases: [nextjs, next]
---
# Next.js

React meta-framework. Server-side rendering, static generation, API routes, file-based routing.

## Known For These Error Patterns
- [[The Environment Delta]] — Hydration mismatches (server vs client rendering)
- [[Configuration & Environment Errors]] — Dynamic server usage in static pages, middleware edge runtime limits
- [[Dependency & Import Errors]] — Client-only library in server component

## Common Gotchas
- Server vs Client component boundary — server can't use hooks/events, client can't be async
- Aggressive caching — `fetch` cached by default in server components
- `'use client'` marks entry point — all imports become client too
- Middleware runs on Edge Runtime — limited Node.js API support

## Related
- [[React]] — built on React
- [[Vue]] — Nuxt.js is Vue's equivalent
- [[MOC — Frontend & UI]]

## My Notes

