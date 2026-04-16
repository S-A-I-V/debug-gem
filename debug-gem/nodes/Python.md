---
tags: [language, runtime, backend, scripting]
aliases: [python, py, python3]
---
# Python

Dynamic, interpreted language. Dominant in AI/ML, scripting, web backends, and data science.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `NoneType has no attribute` is the Python NPE
- [[Type & Casting Errors]] — Duck typing means type errors surface at runtime
- [[Dependency & Import Errors]] — Virtualenv hell, circular imports, pip conflicts
- [[Offset & Boundary Errors]] — `IndexError`, `KeyError`
- [[Resource Exhaustion Errors]] — `MemoryError`, `RecursionError`
- [[Serialization & Encoding Errors]] — `UnicodeDecodeError` is extremely common

## Quick Debug
- `python -m pdb script.py` — debugger
- `breakpoint()` — drop into debugger at any point (3.7+)
- `python -m traceback` — better stack traces
- `pip list` — check installed packages
- `pip show <pkg>` — check package version and location

## Common Gotchas
- Mutable default arguments (`def f(x=[])`)
- `is` vs `==` (identity vs equality)
- GIL prevents true thread parallelism for CPU work
- Indentation IS syntax — mixing tabs/spaces breaks everything
- Late binding closures in loops

## Related
- [[MOC — Languages & Runtimes]]
- [[MOC — AI & ML]] — Python dominates ML/AI

## My Notes

