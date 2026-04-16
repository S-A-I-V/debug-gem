---
tags: [database, relational, sql]
aliases: [mysql, mariadb]
---
# MySQL

Popular open-source relational database. Default for many web frameworks (WordPress, Laravel, Rails).

## Known For These Error Patterns
- [[Concurrency & Race Condition Errors]] — Deadlocks, lock wait timeouts
- [[Resource Exhaustion Errors]] — Too many connections, packet too large
- [[Serialization & Encoding Errors]] — `utf8` is NOT real UTF-8 (use `utf8mb4` for emoji)
- [[Permission & Authorization Errors]] — Access denied, host-based restrictions

## Common Gotchas
- `utf8` charset is 3-byte only — use `utf8mb4` for full Unicode
- DDL statements cause implicit commit — can't rollback ALTER TABLE
- `GROUP BY` allows non-aggregated columns by default — enable `ONLY_FULL_GROUP_BY`
- Silent data truncation depending on `sql_mode` — enable `STRICT_TRANS_TABLES`

## Related
- [[PostgreSQL]] — more feature-rich alternative
- [[MOC — Databases & Data]]

## My Notes

