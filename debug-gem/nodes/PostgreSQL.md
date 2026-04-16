---
tags: [database, relational, sql]
aliases: [postgresql, postgres, pg]
---
# PostgreSQL

Advanced open-source relational database. Strong consistency, ACID compliance, extensible type system.

## Known For These Error Patterns
- [[Concurrency & Race Condition Errors]] — Deadlocks, serialization failures, lock timeouts
- [[Resource Exhaustion Errors]] — Too many connections, disk full, WAL accumulation
- [[Permission & Authorization Errors]] — GRANT missing, pg_hba.conf blocking connections
- [[State & Lifecycle Errors]] — `current transaction is aborted` (must ROLLBACK)
- [[Offset & Boundary Errors]] — Constraint violations (unique, foreign key, not null)

## Common Gotchas
- Multiple NULLs allowed in unique columns (NULL != NULL)
- VACUUM must run to prevent table bloat and transaction ID wraparound
- `OFFSET` pagination is slow for large offsets — use keyset pagination
- `LIKE` without leading wildcard can use index, `ILIKE` needs `pg_trgm`

## Debug
- `EXPLAIN ANALYZE` — query execution plan with actual timing
- `pg_stat_activity` — active connections and queries
- `pg_locks` — current locks
- `pg_stat_user_tables` — table statistics (dead tuples, last vacuum)

## Related
- [[MySQL]], [[SQLite]] — other relational databases
- [[MOC — Databases & Data]]

## My Notes

