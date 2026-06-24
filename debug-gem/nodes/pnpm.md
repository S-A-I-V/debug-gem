---
tags: [devops, package-manager, javascript]
aliases: [pnpm]
---
# pnpm

Fast, disk-efficient [[JavaScript]] package manager. Uses a content-addressable store and hard links to save disk space. Strict dependency isolation prevents phantom dependencies.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — `ERR_PNPM_PEER_DEP_ISSUES` (strict by default), missing peer deps, phantom dependency access blocked
- [[Permission & Authorization Errors]] — Store permission errors, symlink creation failures on Windows
- [[Configuration & Environment Errors]] — `.npmrc` settings not recognized, store location misconfigured, workspace protocol issues

## Common Errors
- `ERR_PNPM_PEER_DEP_ISSUES` — peer dependency version conflicts. Fix: `--legacy-peer-deps` or update versions. Unlike npm, pnpm fails by default on peer dep conflicts.
- `ERR_PNPM_NO_MATCHING_VERSION` — requested version doesn't exist in registry
- `ERR_PNPM_LOCKFILE_BREAKING_CHANGE` — lockfile format incompatible with pnpm version
- Module not found (phantom dependency) — package not in your `package.json` but was accessible with npm due to hoisting. pnpm's strict isolation catches this.
- `WARN  deprecated` packages accumulating — not an error but worth tracking

## Key Commands
- `pnpm install` — install from lockfile
- `pnpm add <pkg>` — add dependency
- `pnpm why <pkg>` — show why a package is installed (dependency path)
- `pnpm store prune` — clean unused packages from global store
- `pnpm ls --depth 0` — list direct dependencies
- `pnpm patch <pkg>` — patch a dependency inline (no fork needed)

## Common Gotchas
- Phantom dependencies — code that `require()`s a package not in your own `package.json` will BREAK with pnpm (it worked with npm/yarn because of hoisting). This is a feature, not a bug — it exposes incorrect dependency declarations.
- Symlink-based `node_modules` — some tools don't follow symlinks correctly. Use `node-linker=hoisted` in `.npmrc` as escape hatch (loses strict isolation).
- Workspace protocol (`workspace:*`) — only works within monorepo. Must be replaced with real versions before publishing.
- Global store (`~/.local/share/pnpm/store`) — if corrupted, `pnpm store prune` or delete and reinstall.
- `shamefully-hoist=true` — hoist everything like npm (defeats purpose but fixes compatibility issues)

## pnpm vs npm vs yarn
| Feature | pnpm | npm | yarn |
|---------|------|-----|------|
| Disk usage | Shared store (minimal) | Duplicates per project | Duplicates or PnP |
| Phantom deps | Blocked (strict) | Allowed (hoisting) | Allowed (hoisting) |
| Speed | Fastest (hard links) | Slower | Fast |
| Monorepo | Built-in workspaces | Workspaces | Workspaces |

## Related
- [[npm]], [[yarn]] — alternatives
- [[MOC — DevOps & CI-CD]]

## My Notes

