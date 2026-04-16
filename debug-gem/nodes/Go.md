---
tags: [language, runtime, backend, systems]
aliases: [go, golang]
---
# Go

Statically typed, compiled, built for concurrency. Dominant in cloud infrastructure, CLI tools, and microservices.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — Nil pointer dereference panic
- [[Concurrency & Race Condition Errors]] — Goroutine races, map concurrent access, deadlocks
- [[Offset & Boundary Errors]] — Slice/array index out of range
- [[Dependency & Import Errors]] — Module resolution, go.sum mismatches

## Common Gotchas
- Error swallowing (`_ = riskyFunc()`) hides failures
- Nil interface vs nil pointer — interface holding nil pointer is NOT nil
- Goroutine leaks — blocked goroutines are never GC'd
- Map iteration order is randomized
- `defer` arguments evaluated immediately

## Related
- [[Kubernetes]], [[Docker]] — written in Go
- [[MOC — Languages & Runtimes]]

## My Notes

