---
tags: [error-pattern, universal-failure-pattern]
aliases: [serialization-errors, encoding-errors, unicode-errors, marshalling]
---
# Serialization & Encoding Errors

> Data corrupted in translation. Converting data between formats or representations failed or produced wrong results.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. Data must be converted between formats when crossing boundaries (memory → network, process → file, service → service). This conversion can fail or silently corrupt data.

## The Pattern
Data exists in one representation and must be converted to another:
- Object → JSON → Object (serialization/deserialization)
- String → bytes → String (encoding/decoding)
- Memory → wire format → Memory (network protocols)
- One character encoding → another (UTF-8, Latin-1, ASCII)

## Universal Symptoms
- `UnicodeDecodeError`, `UnicodeEncodeError` (Python)
- `JSON.parse` failures, malformed JSON
- `Mojibake` — garbled text (wrong encoding assumed)
- `Task not serializable` (Spark)
- Protobuf backward compatibility violations
- `?` or `�` characters appearing in text
- Data silently truncated or corrupted
- `EILSEQ` — illegal byte sequence

## Diagnostic Decision Tree
1. **WHAT format conversion failed?** JSON, protobuf, XML, character encoding, binary?
2. **WHICH DIRECTION?** Serializing (writing) or deserializing (reading)?
3. **IS IT ENCODING?** Character encoding mismatch (UTF-8 vs Latin-1)?
4. **IS IT SCHEMA?** Schema evolved but data is in old format?
5. **IS IT SILENT?** Data appears okay but values are wrong (precision loss, truncation)?

## Common Causes

### Character Encoding
- Assuming ASCII/Latin-1 when data is UTF-8 (or vice versa)
- Database connection not specifying UTF-8 (MySQL `utf8` is NOT full UTF-8 — use `utf8mb4`)
- File saved in one encoding, read assuming another
- BOM (Byte Order Mark) not handled
- Emoji/special characters in "ASCII-only" field

### JSON
- Circular references during serialization
- Non-serializable types (functions, Date objects, BigInt, undefined)
- Precision loss for large numbers (>2^53 in JavaScript)
- Missing Content-Type header (response parsed as text, not JSON)

### Protocol/Schema
- Protobuf field number reused (breaking change)
- Avro schema evolution violation
- API response format changed without version bump
- Endianness mismatch in binary protocols

### Silent Corruption
- Float precision loss (`0.1 + 0.2 ≠ 0.3`)
- Integer overflow during conversion
- Timezone information lost in datetime serialization
- Null bytes in strings truncating data

## Fix Strategies
1. **Specify encoding explicitly** — never assume. Always declare UTF-8.
2. **Validate after deserialization** — check data integrity, not just parse success
3. **Use schema validation** — JSON Schema, protobuf, Avro for structure enforcement
4. **Handle edge cases** — null, undefined, NaN, Infinity, circular refs, special characters
5. **Version your formats** — schema evolution rules, backward compatibility
6. **Test with real data** — include emoji, unicode, large numbers, special characters in tests

## Technologies That Commonly Produce This
- [[Python]] — `UnicodeDecodeError` (extremely common)
- [[MySQL]] — `utf8` vs `utf8mb4` (emoji breaks with wrong charset)
- [[JavaScript]] — JSON serialization edge cases, BigInt
- [[Kafka]] — Schema registry conflicts
- [[gRPC]] — Protobuf backward compatibility
- [[Spark]] — `Task not serializable`
- [[React Native]] — Non-serializable data crossing JS/Native bridge
- [[SAML]] — XML canonicalization breaking signatures
- [[JWT]] — Malformed token, URL encoding issues
- [[SQLite]] — Database corruption
- [[Pandas]] — dtype mismatches during read/write

## Related Patterns
- [[Syntax & Parse Errors]] — if deserialization fails completely, it looks like parse error
- [[Type & Casting Errors]] — type conversion is a form of serialization
- [[The Boundary Problem]] — serialization happens at every boundary

## My Notes

