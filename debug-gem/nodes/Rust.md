---
tags: [language, systems, performance]
aliases: [rust, rs]
---
# Rust

Systems language with memory safety guaranteed at compile time. No garbage collector. If it compiles, it usually works.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Panics on out-of-bounds (but catches at compile time when possible)
- [[Null & Undefined Reference Errors]] — `unwrap()` on `None` panics
- [[Dependency & Import Errors]] — Cargo feature flags, linking errors

## Common Gotchas
- Borrow checker fights — learn to work WITH it
- `unwrap()` abuse defeats Rust's safety — use `?` operator
- Async Rust is significantly more complex than sync Rust
- Lifetime annotations required when compiler can't infer

## Related
- [[C++]] — Rust aims to replace C++ for systems programming
- [[MOC — Languages & Runtimes]]

## My Notes

