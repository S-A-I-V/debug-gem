---
tags: [language, systems, performance]
aliases: [cpp, c-plus-plus]
---
# C++

Systems language with manual memory management. Powerful but dangerous — undefined behavior is the root of countless security vulnerabilities.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Buffer overflow, segfault (security-critical)
- [[Null & Undefined Reference Errors]] — Dangling pointers, use-after-free
- [[Resource Exhaustion Errors]] — Memory leaks, stack overflow
- [[Dependency & Import Errors]] — Linker errors, missing headers

## Common Gotchas
- Undefined behavior — compiler can do ANYTHING (including "working" in debug, crashing in release)
- Manual memory management — use smart pointers (unique_ptr, shared_ptr)
- Template error messages can be pages long — read bottom-up
- Static initialization order fiasco between translation units

## Debug Tools
- GDB / LLDB, Valgrind, AddressSanitizer, ThreadSanitizer, UBSan

## Related
- [[Rust]] — memory-safe alternative
- [[C]] — C++ superset of C
- [[CUDA]] — GPU programming extends C++
- [[MOC — Languages & Runtimes]]

## My Notes

