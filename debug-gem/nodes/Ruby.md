---
tags: [language, runtime, backend, scripting]
aliases: [ruby, rb]
---
# Ruby

Dynamic, interpreted language. Known for Ruby on Rails web framework. Developer happiness focused.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `NoMethodError: undefined method for nil:NilClass`
- [[Dependency & Import Errors]] — Gem version conflicts, Bundler issues
- [[Type & Casting Errors]] — Dynamic typing, method_missing magic

## Common Gotchas
- `method_missing` makes debugging hard — objects respond to invisible methods
- Monkey patching can cause subtle conflicts between libraries
- Block/Proc/Lambda have different `return` behavior
- Implicit return — last expression is returned

## Related
- [[MOC — Languages & Runtimes]]

## My Notes

