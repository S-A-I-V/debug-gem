---
tags: [language, systems, performance]
aliases: [c, clang]
---
# C

The original systems programming language. Manual memory management, minimal abstraction, maximum control. Foundation of operating systems, embedded systems, and most language runtimes.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Buffer overflow (security-critical), segfault, off-by-one
- [[Null & Undefined Reference Errors]] — NULL pointer dereference, dangling pointers, use-after-free
- [[Resource Exhaustion Errors]] — Memory leaks (no garbage collector), stack overflow, file descriptor leaks
- [[Type & Casting Errors]] — Implicit integer promotion, void pointer misuse, signed/unsigned confusion

## Common Errors
- `Segmentation fault (core dumped)` — Dereferencing NULL/invalid pointer, buffer overflow, use-after-free, stack overflow
- `*** buffer overflow detected ***` — Writing past array boundary (caught by stack protector)
- `double free or corruption` — Freeing memory that was already freed
- `undefined reference to 'X'` — Linker error. Function declared but not defined. Missing library in link step (`-lm`, `-lpthread`).
- `implicit declaration of function 'X'` — Missing `#include` for function prototype
- `warning: format '%d' expects type 'int', but argument has type 'long'` — printf format mismatch (undefined behavior)

## Memory Management Rules
1. Every `malloc` needs exactly one `free`
2. Never use memory after `free` (use-after-free)
3. Never `free` twice (double-free)
4. Always check `malloc` return value (can be NULL)
5. `sizeof` the type, not the variable when allocating arrays
6. Use `valgrind` to find leaks and invalid memory access

## Common Gotchas
- **Undefined Behavior (UB)** — signed integer overflow, null dereference, buffer overflow, uninitialized variables. Compiler can assume UB never happens and optimize accordingly — bugs appear/disappear with optimization levels.
- **No bounds checking** — arrays don't know their own size. Buffer overflows are silent until they corrupt something.
- **String handling** — no built-in string type. `char*` with null terminator. Off-by-one forgetting null terminator is extremely common.
- **Integer promotion** — `char` and `short` promote to `int` in expressions. Unsigned/signed comparison is always unsigned.
- **`sizeof` on array decays** — `sizeof(array)` gives array size, but `sizeof(pointer)` gives pointer size. Arrays decay to pointers when passed to functions.
- **`==` on strings** — compares pointers, not content. Use `strcmp()`.
- **No RAII** — must manually clean up in all code paths (especially error paths). Use `goto cleanup` pattern.

## Debug Tools
- `gdb` / `lldb` — step debugger, backtrace, watchpoints
- `valgrind --tool=memcheck` — memory leak detection, invalid access
- AddressSanitizer (`-fsanitize=address`) — runtime buffer overflow/use-after-free detection
- UndefinedBehaviorSanitizer (`-fsanitize=undefined`) — detect UB at runtime
- ThreadSanitizer (`-fsanitize=thread`) — race condition detection
- `strace` / `ltrace` — system call / library call tracing

## Related
- [[C++]] — superset of C with classes, templates, RAII
- [[Rust]] — memory-safe systems language alternative
- [[Linux]] — kernel written in C
- [[MOC — Languages & Runtimes]]

## My Notes

