---
tags: [language, mobile, ios, apple]
aliases: [swift]
---
# Swift

Apple's language for iOS, macOS, watchOS, tvOS. Strong type system with Optionals for null safety.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — Force unwrap (`!`) on nil is the #1 crash cause
- [[Offset & Boundary Errors]] — Array index out of range
- [[State & Lifecycle Errors]] — UIKit/SwiftUI lifecycle issues

## Common Gotchas
- Force unwrapping (`!`) defeats Optional safety — use `if let`, `guard let`, `??`
- Retain cycles in closures — use `[weak self]`
- Value types (structs) vs reference types (classes) behave differently

## Related
- [[React Native]], [[Flutter]] — cross-platform alternatives
- [[MOC — Languages & Runtimes]]
- [[MOC — Frontend & UI]]

## My Notes

