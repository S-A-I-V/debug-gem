---
tags: [language, runtime, backend, enterprise]
aliases: [java, jvm]
---
# Java

Statically typed, compiled, enterprise-grade. Runs on JVM. Dominant in enterprise backends, Android, and big data.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `NullPointerException` is the classic
- [[Resource Exhaustion Errors]] — `OutOfMemoryError` (heap, metaspace, threads)
- [[Dependency & Import Errors]] — Classpath hell, `NoClassDefFoundError`
- [[Concurrency & Race Condition Errors]] — `ConcurrentModificationException`, deadlocks
- [[State & Lifecycle Errors]] — `IllegalStateException`

## Common Gotchas
- `equals()` vs `==` for objects
- Autoboxing NPE (`Integer x = null; int y = x;`)
- Generics type erasure at runtime
- Checked exceptions lead to empty catch blocks

## Related
- [[Kotlin]] — modern JVM alternative
- [[Maven]], [[Gradle]] — build tools
- [[MOC — Languages & Runtimes]]

## My Notes

