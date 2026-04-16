---
tags: [infrastructure, containers, devops]
aliases: [docker, container, containers]
---
# Docker

Container runtime. Packages applications with their dependencies into portable, isolated containers.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — `OOMKilled` (exit code 137), port conflicts, disk full
- [[Configuration & Environment Errors]] — Missing ENV/ARG, wrong ENTRYPOINT, architecture mismatch (ARM vs x86)
- [[Dependency & Import Errors]] — Missing system libraries in slim/alpine images
- [[Permission & Authorization Errors]] — Docker daemon socket permission, registry auth
- [[Connection & Network Errors]] — Container networking, DNS resolution between containers

## Exit Code Cheat Sheet
- `0` — Success
- `1` — Application error
- `126` — Command not executable ([[Permission & Authorization Errors]])
- `127` — Command not found ([[Dependency & Import Errors]])
- `137` — OOM killed (SIGKILL) ([[Resource Exhaustion Errors]])
- `139` — Segfault (SIGSEGV) ([[Offset & Boundary Errors]])
- `143` — SIGTERM (graceful shutdown)

## Common Gotchas
- `.dockerignore` missing = huge build context (includes node_modules, .git)
- Layer caching — order Dockerfile from least to most frequently changing
- PID 1 signal handling — use `tini` or `dumb-init`
- Alpine uses `musl` not `glibc` — some binaries won't work
- Build args and env vars are visible in image layers — don't put secrets there

## Related
- [[Kubernetes]] — container orchestration
- [[Linux]] — containers are Linux processes with namespaces/cgroups
- [[MOC — Cloud & Infrastructure]]

## My Notes

