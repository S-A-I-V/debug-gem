---
tags: [database, nosql, document]
aliases: [mongodb, mongo]
---
# MongoDB

Document database (NoSQL). Stores JSON-like documents. Schema-flexible but not schema-free.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Duplicate key errors (E11000)
- [[Resource Exhaustion Errors]] — 16MB document limit, sort memory limit
- [[State & Lifecycle Errors]] — Writing to secondary in replica set
- [[Connection & Network Errors]] — Server selection timeout in replica sets

## Common Gotchas
- Schema-less doesn't mean schema-free — validate your documents
- `$lookup` (joins) are expensive — denormalize for read-heavy workloads
- Default write concern `w:1` — use `w:"majority"` for durability
- `$regex` without `^` prefix can't use indexes — full collection scan

## Related
- [[Redis]] — often used alongside as cache
- [[PostgreSQL]] — relational alternative
- [[MOC — Databases & Data]]

## My Notes

