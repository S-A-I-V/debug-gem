---
tags: [error-pattern, universal-failure-pattern]
aliases: [type-errors, casting-errors, type-mismatch, coercion]
---
# Type & Casting Errors

> Wrong type. The system expected one data type but received another, or a type conversion failed.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. Values have types, and operations expect specific types. This pattern fires when types don't match.

## The Pattern
A value is of the wrong type for the operation being performed:
- Passing a string where a number is expected
- Implicit coercion producing unexpected results
- Failed cast/conversion between types
- Schema mismatch between systems
- Type system violations caught at compile time (TypeScript, Java) or runtime (Python, JavaScript)

## Universal Symptoms
- `TypeError`, `ClassCastException`
- `Type 'X' is not assignable to type 'Y'` (TypeScript)
- `WRONGTYPE` (Redis)
- `400 Bad Request`, `422 Unprocessable Entity` (API validation)
- `mapper_parsing_exception` (Elasticsearch)
- `INVALID_ARGUMENT` (gRPC)
- Implicit coercion bugs (`"5" + 3 = "53"` in JavaScript)
- `ValueError: invalid literal for int() with base 10` (Python)

## Diagnostic Decision Tree
1. **WHAT type was expected?** Check function signature, API schema, database column type
2. **WHAT type was received?** Log `typeof`, `type()`, `.getClass()` of the actual value
3. **WHERE did the wrong type come from?** User input? API response? Database? Deserialization?
4. **IS IT IMPLICIT COERCION?** Language auto-converting between types unexpectedly?
5. **IS IT A SCHEMA MISMATCH?** Two systems disagree on the data shape/types?

## Common Causes

### Dynamic Language Coercion
- JavaScript `+` concatenates strings but adds numbers: `"5" + 3 = "53"`
- PHP loose comparison: `"0" == false` is true
- Python `int("12.5")` fails (must use `float()` first)
- Truthy/falsy values misinterpreted

### Static Type Violations
- TypeScript: `Type 'string' is not assignable to type 'number'`
- Java: `ClassCastException` from unsafe downcasting
- Generic type erasure causing runtime type issues

### Serialization/API Boundary
- JSON numbers becoming strings (or vice versa)
- Integer IDs becoming floats after JSON parse (`{ "id": 12345678901234 }` loses precision)
- API returning null where object expected
- Database returning different types based on query

### Schema Mismatch
- Database column type doesn't match application type
- Elasticsearch mapping rejects new field type
- Protobuf field type changed between versions
- CSV column parsed as wrong type

## Fix Strategies
1. **Validate at boundaries** — parse and validate types at API/DB/user input boundaries
2. **Use strict comparison** — `===` not `==`, strict mode
3. **Parse explicitly** — `parseInt()`, `Number()`, `str()` — never rely on implicit conversion
4. **Type narrowing** — TypeScript `typeof` guards, Java `instanceof` checks
5. **Schema validation** — JSON Schema, Zod, io-ts, Pydantic at API boundaries
6. **Strong typing** — enable strict mode, use TypeScript, use type hints in Python

## Technologies That Commonly Produce This
- [[JavaScript]] — Implicit coercion madness
- [[TypeScript]] — Compile-time type errors (the most common TS error)
- [[PHP]] — Loose comparison security bugs
- [[Python]] — Runtime type errors, duck typing surprises
- [[Java]] — `ClassCastException`, generics erasure
- [[Redis]] — `WRONGTYPE` operating on wrong data structure
- [[Elasticsearch]] — Mapping type mismatch
- [[gRPC]] — `INVALID_ARGUMENT` protobuf validation
- [[NumPy]], [[Pandas]] — dtype mismatches
- [[REST API]] — 400/422 validation errors
- [[Spark]] — Column resolution, schema mismatch

## Related Patterns
- [[Serialization & Encoding Errors]] — type issues during serialization
- [[Null & Undefined Reference Errors]] — null is often a type issue
- [[The Boundary Problem]] — type mismatches happen at system boundaries

## My Notes

