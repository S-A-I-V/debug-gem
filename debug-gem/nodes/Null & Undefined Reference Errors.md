---
tags: [error-pattern, universal-failure-pattern]
aliases: [null-errors, npe, null-pointer, undefined-reference, nil-error]
---
# Null & Undefined Reference Errors

> Tried to use something that doesn't exist. The variable, object, or reference points to nothing.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. Code assumes a value exists but encounters null, nil, undefined, or None instead.

## The Pattern
A variable or expression evaluates to "nothing" (null/nil/undefined/None/nullptr), and the code tries to use it as if it were a real value — accessing a property, calling a method, or dereferencing a pointer.

## Universal Symptoms
- `NullPointerException` (Java)
- `TypeError: Cannot read properties of undefined/null` (JavaScript)
- `AttributeError: 'NoneType' has no attribute 'X'` (Python)
- `NoMethodError: undefined method for nil:NilClass` (Ruby)
- `NullReferenceException` (C#)
- `panic: nil pointer dereference` (Go)
- `Fatal error: Unexpectedly found nil while unwrapping` (Swift)
- Segfault from null pointer dereference (C/C++)

## Diagnostic Decision Tree
1. **WHAT is null?** Which exact variable/expression is null?
2. **WHY is it null?** Was it never assigned? Was it cleared? Did a function return null?
3. **SHOULD it be null?** Is null a valid state here, or is it always a bug?
4. **WHERE did it come from?** Trace the value back to its source (API response? DB query? User input?)
5. **IS IT ASYNC?** Data not loaded yet? Race condition between fetch and render?

## Common Causes

### Never Assigned
- Variable declared but never given a value
- Object property that doesn't exist
- Function parameter not passed

### Function Returned Null
- Database query found no results
- API returned empty/null response
- Array `.find()` found no match
- Map/dict lookup for missing key

### Async/Timing
- Data not loaded yet when component renders
- API call hasn't completed
- Ref not attached yet in React

### Cleared/Disposed
- Object was garbage collected
- Weak reference target was collected
- Component unmounted, references cleared

### Chain of Nulls
- Nested access: `user.address.city` — any level could be null
- Optional chaining missing: `user?.address?.city`

## Fix Strategies
1. **Null check** — `if (x != null)` before use
2. **Optional chaining** — `x?.y?.z` (JS/TS), `x&.y` (Ruby), `x?.y` (Kotlin/Swift)
3. **Default values** — `x ?? defaultValue`, `x || fallback`
4. **Type system** — use `Optional<T>`, strict null checks, non-nullable types
5. **Fail fast** — throw meaningful error at the source, not deep in the chain
6. **Guard clauses** — validate inputs at function entry
7. **Loading states** — handle "not yet loaded" explicitly in UI

## Language-Specific Null Handling

| Language | Null Value | Safe Access | Null Type |
|----------|-----------|-------------|-----------|
| JavaScript | `null`, `undefined` | `?.`, `??` | Both are falsy |
| TypeScript | `null`, `undefined` | `?.`, `??`, `!` (unsafe) | `strictNullChecks` |
| Python | `None` | `if x is not None` | `Optional[T]` |
| Java | `null` | `Optional<T>`, null checks | NPE at runtime |
| Kotlin | `null` | `?.`, `?:`, `!!` (unsafe) | Nullable types `T?` |
| Swift | `nil` | `if let`, `guard let`, `?` | `Optional<T>` |
| Go | `nil` | Manual check | Zero values |
| Rust | N/A | `Option<T>`, `?`, `.unwrap()` | No null — uses Option |
| C# | `null` | `?.`, `??` | Nullable reference types |

## Technologies That Commonly Produce This
- [[JavaScript]] — `Cannot read properties of undefined` (#1 JS error)
- [[Java]] — `NullPointerException` (the classic)
- [[Python]] — `NoneType has no attribute`
- [[C#]] — `NullReferenceException`
- [[Go]] — Nil pointer dereference panic
- [[Swift]] — Force unwrap on nil crash
- [[Ruby]] — `NoMethodError` for nil
- [[React]], [[Vue]] — Data not loaded yet during render
- [[TypeScript]] — `Object is possibly undefined`
- [[Rust]] — `unwrap()` on `None` panics (but compiler prevents most null issues)

## Related Patterns
- [[Offset & Boundary Errors]] — accessing index that returns undefined
- [[State & Lifecycle Errors]] — value null because not initialized yet
- [[The Assumption Trap]] — assuming "this will never be null"

## My Notes

