---
tags: [database, relational, sql, embedded]
aliases: [sqlite]
---
# SQLite

Embedded relational database. Single file, zero configuration. Used in mobile apps, browsers, and small applications.

## Known For These Error Patterns
- [[Concurrency & Race Condition Errors]] — `SQLITE_BUSY: database is locked` (single writer)
- [[Offset & Boundary Errors]] — Constraint violations
- [[Serialization & Encoding Errors]] — Database corruption

## Common Gotchas
- Single writer at a time — use WAL mode for better concurrency
- Not designed for multi-process concurrent writes — use PostgreSQL/MySQL for that
- Type affinity, not strict types — you CAN store string in integer column

## Related
- [[PostgreSQL]], [[MySQL]] — server-based alternatives
- [[MOC — Databases & Data]]

## My Notes

