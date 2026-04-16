---
tags: [database, cache, nosql, in-memory]
aliases: [redis]
---
# Redis

In-memory data store. Used as cache, message broker, session store, and real-time leaderboard.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — `OOM command not allowed`, max clients reached
- [[Type & Casting Errors]] — `WRONGTYPE` (operating on key with wrong data structure)
- [[Connection & Network Errors]] — `CLUSTERDOWN`, cluster node failures
- [[State & Lifecycle Errors]] — Writing to replica, loading dataset

## Common Gotchas
- Single-threaded — one slow command blocks everything. Avoid `KEYS *` in production (use `SCAN`)
- Keys without TTL grow forever — always set expiration for cache data
- Pub/Sub messages are lost if subscriber disconnects — use Streams for reliability
- Memory fragmentation — `used_memory` vs `used_memory_rss`

## Related
- [[Kafka]] — message streaming alternative
- [[Elasticsearch]] — search alternative
- [[MOC — Databases & Data]]

## My Notes

