---
tags: [error-pattern, universal-failure-pattern]
aliases: [offset-errors, boundary-errors, index-out-of-bounds, buffer-overflow, off-by-one]
---
# Offset & Boundary Errors

> Went past the edge. Accessed an index, position, or range that doesn't exist.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The code tried to access a position beyond the valid range of a data structure — array, buffer, string, file, or memory region.

## The Pattern
Every indexed data structure has boundaries. This pattern fires when code accesses beyond those boundaries:
- Array/list index out of range
- Buffer overflow/underflow
- Off-by-one errors
- Constraint violations (unique, foreign key, not null)
- Shape/dimension mismatches in numerical computing

## Universal Symptoms
- `IndexError`, `ArrayIndexOutOfBoundsException`
- `RangeError: Invalid array length`
- `Segmentation fault` (C/C++ buffer overflow)
- `KeyError`, `key not found`
- `Duplicate key error` (database constraint)
- Tensor shape mismatch errors
- `RenderFlex overflowed` (Flutter — UI boundary)
- `panic: index out of range` (Go)

## Diagnostic Decision Tree
1. **WHAT boundary was crossed?** Array length, buffer size, valid key set, DB constraint?
2. **WHAT index/key was used?** Log the actual value that caused the error.
3. **WHY was that value wrong?** Off-by-one? Unvalidated input? Calculation error?
4. **IS IT AN EDGE CASE?** Empty collection? Last element? Boundary value?
5. **IS IT DATA-DEPENDENT?** Only happens with certain input data?

## Common Causes

### Array/Index Errors
- Off-by-one: using `<=` instead of `<` in loop condition
- Empty collection not checked before access
- Assuming fixed size that changed
- Negative index not handled
- 0-based vs 1-based indexing confusion

### Buffer Overflow (Security Critical)
- Writing past buffer boundary (C/C++)
- Format string vulnerabilities
- Integer overflow causing small allocation for large data
- Stack overflow from deep recursion

### Key/Constraint Errors
- Dictionary/map key doesn't exist
- Database unique constraint violation (duplicate insert)
- Foreign key constraint (referenced row doesn't exist)
- NOT NULL constraint on missing data

### Shape Mismatches (Numerical/ML)
- Matrix dimensions incompatible for multiplication
- Tensor shapes don't broadcast
- Label index >= number of classes
- Batch size mismatch between input and target

## Fix Strategies
1. **Bounds checking** — validate index before access
2. **Use safe access** — `.get()`, `?.`, `at()` instead of direct indexing
3. **Handle empty case** — check length/existence before access
4. **Validate input** — ensure data meets expectations before processing
5. **Use iterators** — prefer `for...of`, `forEach`, `.map()` over manual indexing
6. **Defensive defaults** — provide fallback values for missing keys

## Technologies That Commonly Produce This
- [[C++]] — Buffer overflow, segfault (security-critical, undefined behavior)
- [[Python]] — `IndexError`, `KeyError`
- [[Go]] — Slice/array index out of range panic
- [[Java]] — `ArrayIndexOutOfBoundsException`
- [[JavaScript]] — Returns `undefined` (doesn't throw — [[The Silent Failure]])
- [[Swift]] — Array index out of range (fatal)
- [[Rust]] — Panics on out-of-bounds (caught at runtime)
- [[PyTorch]], [[TensorFlow]], [[NumPy]] — Shape mismatches
- [[Flutter]] — RenderFlex overflow
- [[Kafka]] — `OffsetOutOfRangeException`
- [[PostgreSQL]], [[MongoDB]] — Constraint violations
- [[Solidity]] — Integer overflow (pre-0.8), array bounds

## Related Patterns
- [[Null & Undefined Reference Errors]] — accessing property on null is related
- [[Type & Casting Errors]] — wrong type can lead to wrong index
- [[The Assumption Trap]] — assuming valid index without checking

## My Notes

