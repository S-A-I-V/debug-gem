---
tags: [language, runtime, backend, web]
aliases: [php]
---
# PHP

Server-side scripting language. Powers WordPress, Laravel, Symfony. Historically loose typing, improving with modern versions.

## Known For These Error Patterns
- [[Type & Casting Errors]] — Loose comparison (`==`) causes security bugs
- [[Null & Undefined Reference Errors]] — `Call to a member function on null`
- [[Configuration & Environment Errors]] — php.ini differences between environments
- [[Resource Exhaustion Errors]] — Memory limit, execution time limit

## Common Gotchas
- `"0" == false` is true, `"" == false` is true, but `"0" == ""` is false
- `empty("0")` returns true
- Type juggling can cause security vulnerabilities
- `utf8` vs `utf8mb4` in MySQL connections

## Related
- [[MySQL]] — traditional PHP database
- [[MOC — Languages & Runtimes]]

## My Notes

