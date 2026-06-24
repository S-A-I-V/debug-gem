---
tags: [devops, package-manager, javascript]
aliases: [yarn, yarn-berry]
---
# yarn

Alternative [[JavaScript]] package manager. Originally by Facebook. Classic (v1) uses node_modules with hoisting. Berry (v2+) uses Plug'n'Play (PnP) — no `node_modules` directory.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — PnP resolution failures, missing peer deps, package not found in zip
- [[Configuration & Environment Errors]] — `.yarnrc.yml` misconfiguration, node version mismatch, PnP incompatibility with tools
- [[Permission & Authorization Errors]] — Registry auth failures, workspace access issues
- [[Resource Exhaustion Errors]] — Network timeouts during install, cache corruption

## Common Errors (Classic v1)
- `ESOCKETTIMEDOUT` — Registry timeout. Check network/proxy settings.
- `Couldn't find package on registry` — Package name typo or private registry not configured
- `There appears to be trouble with your network connection` — Firewall, VPN, or registry down
- `EINTEGRITY` — Cache corruption. Fix: `yarn cache clean`
- Merge conflicts in `yarn.lock` — Run `yarn install` to regenerate, don't manually resolve

## Common Errors (Berry v2+/PnP)
- `Module not found` (with PnP) — Package not declared in dependencies. PnP is strict like pnpm.
- `Your application tried to access X, but it isn't declared in your dependencies` — Phantom dependency blocked. Add it explicitly.
- `The file X isn't part of the loading package` — Wrong exports/main field in package
- Tool incompatibility — Some tools don't support PnP. Use `nodeLinker: node-modules` as fallback.

## Key Commands
- `yarn install` — install dependencies
- `yarn add <pkg>` — add dependency
- `yarn why <pkg>` — show why package is installed
- `yarn dlx <pkg>` — run package without installing (like `npx`)
- `yarn dedupe` — deduplicate dependency tree
- `yarn patch <pkg>` — patch a dependency

## Common Gotchas
- Yarn Berry PnP breaks many tools — ESLint, Jest, TypeScript need PnP plugins/SDKs (`yarn dlx @yarnpkg/sdks vscode`)
- Zero-installs (committing `.yarn/cache`) — fast CI but large repo size
- `yarn.lock` format differs between v1 and Berry — migration can be bumpy
- Workspaces `nohoist` (v1) — needed for React Native and other tools that expect flat `node_modules`
- Berry plugins system — some v1 features require explicit plugin installation
- `packageExtensions` in `.yarnrc.yml` — fix broken packages that don't declare all their deps

## yarn Classic vs Berry vs npm vs pnpm
| Feature | Yarn Classic | Yarn Berry | npm | pnpm |
|---------|-------------|------------|-----|------|
| node_modules | Hoisted | PnP (no node_modules) | Hoisted | Symlinked |
| Speed | Fast | Faster (PnP) | Moderate | Fastest |
| Strictness | Loose | Strict | Loose | Strict |
| Zero-install | No | Yes | No | No |
| Disk usage | Duplicates | Zipped cache | Duplicates | Shared store |

## Related
- [[npm]], [[pnpm]] — alternatives
- [[JavaScript]] — ecosystem
- [[MOC — DevOps & CI-CD]]

## My Notes

