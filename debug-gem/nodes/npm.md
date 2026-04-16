---
tags: [devops, package-manager, javascript]
aliases: [npm, node-package-manager]
---
# npm

Default package manager for [[Node.js]]/[[JavaScript]]. Registry at npmjs.com.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — `ERESOLVE` peer dep conflicts, 404 package not found, `EINTEGRITY`
- [[Permission & Authorization Errors]] — `EACCES` (don't use sudo)
- [[Resource Exhaustion Errors]] — Heap out of memory during install

## Key Commands
- `npm ci` — clean install from lock file (for CI)
- `npm ls` — dependency tree
- `npm audit` — security vulnerability check
- `npm cache clean --force` — clear cache

## Related
- [[yarn]], [[pnpm]] — alternatives
- [[MOC — DevOps & CI-CD]]

## My Notes

