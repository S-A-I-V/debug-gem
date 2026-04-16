---
tags: [messaging, streaming, data]
aliases: [kafka, apache-kafka]
---
# Kafka

Distributed event streaming platform. High-throughput, fault-tolerant message broker.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — `OffsetOutOfRangeException` (consumer offset past retention)
- [[Serialization & Encoding Errors]] — Schema registry conflicts, serialization failures
- [[Resource Exhaustion Errors]] — Message too large, throughput exceeded
- [[State & Lifecycle Errors]] — Partition leader changes, consumer group rebalancing
- [[Concurrency & Race Condition Errors]] — Producer fencing, idempotent producer conflicts

## Common Gotchas
- Message ordering guaranteed within partition only — use same key for related messages
- Consumer group rebalancing storms — increase `session.timeout.ms`
- Partition count can't decrease after creation — plan ahead
- Exactly-once semantics is complex to set up correctly

## Related
- [[Redis]] — simpler pub/sub alternative
- [[Elasticsearch]] — common downstream consumer
- [[MOC — Databases & Data]]

## My Notes

