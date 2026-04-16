---
tags: [meta-pattern, debugging-philosophy]
---
# The Environment Delta

> "Works on my machine" means the environments differ.

Diff EVERYTHING between the working and broken environments.

## What to Diff
- OS version, architecture (x86 vs ARM)
- Runtime version (Node 18 vs 20, Python 3.9 vs 3.12)
- Environment variables (`env` on both machines, then diff)
- Network topology, DNS, firewall rules
- Data volume and content
- Time/timezone/locale
- File system (case sensitivity, permissions)
- Available memory, CPU
- Installed system libraries
- Configuration files

## Debug Technique
Run `env` on both machines and diff. Check `uname -a`, runtime `--version`, `which` for binary paths.

## Related
- [[Configuration & Environment Errors]] — the pattern this produces
- [[The Assumption Trap]], [[The Boundary Problem]]
- [[Docker]] — containers reduce environment delta

## My Notes

