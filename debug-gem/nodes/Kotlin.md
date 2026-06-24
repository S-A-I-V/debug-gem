---
tags: [language, runtime, backend, mobile, android]
aliases: [kotlin, kt]
---
# Kotlin

Modern JVM language by JetBrains. Null-safe, concise, interoperable with [[Java]]. Official language for Android development.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `KotlinNullPointerException` from `!!` (force unwrap), Java interop nulls
- [[Concurrency & Race Condition Errors]] — Coroutine races, shared mutable state across coroutines
- [[State & Lifecycle Errors]] — Android lifecycle vs coroutine scope, `lateinit` property not initialized
- [[Dependency & Import Errors]] — Kotlin/Java version mismatch, Gradle plugin version conflicts

## Common Errors
- `kotlin.KotlinNullPointerException` — Used `!!` on null. Replace with safe calls (`?.`), Elvis (`?:`) or proper null checks.
- `UninitializedPropertyAccessException: lateinit property X has not been initialized` — Accessed `lateinit var` before assignment. Check initialization order.
- `ClassCastException` — Unsafe cast (`as`) failed. Use safe cast (`as?`) instead.
- `IllegalStateException: Flow exception transparency is violated` — Throwing/catching in `flow {}` incorrectly. Use `catch {}` operator.
- `JobCancellationException` — Coroutine scope cancelled. Normal during lifecycle cleanup, but unexpected = scope cancelled too early.
- `StackOverflowError` in `by lazy` — Circular lazy initialization

## Coroutine Gotchas
- `GlobalScope.launch` leaks — use structured concurrency (`viewModelScope`, `lifecycleScope`, custom scope)
- `Dispatchers.Main` not available without dependency — Android needs `kotlinx-coroutines-android`
- Exception handling: `launch` propagates exceptions up, `async` stores them until `await()`
- `SupervisorJob` — child failure doesn't cancel siblings (unlike regular Job)
- `flowOn` changes upstream dispatcher only — confusing if you expect it to change downstream

## Kotlin vs Java Null Safety
- Kotlin types are non-null by default (`String` vs `String?`)
- Java interop is the weak spot — platform types (`String!`) bypass null safety
- Add `@Nullable`/`@NotNull` annotations in Java code for better Kotlin interop
- `!!` operator is a code smell — every `!!` is a potential NPE

## Common Gotchas
- `data class` `copy()` is shallow — nested objects are shared
- `==` compares values (like Java `.equals()`), `===` compares references
- `object` declarations are lazily initialized singletons
- Extension functions are resolved statically (compile time), not dynamically
- Sealed classes vs enums — sealed classes allow instances with different data
- `inline` functions and reified types — powerful but can increase bytecode size

## Related
- [[Java]] — fully interoperable, runs on JVM
- [[Gradle]] — Kotlin DSL for build scripts
- [[MOC — Languages & Runtimes]]

## My Notes

