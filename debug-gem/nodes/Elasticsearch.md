---
tags: [database, search, nosql]
aliases: [elasticsearch, elastic, es]
---
# Elasticsearch

Distributed search and analytics engine. Full-text search, log aggregation, metrics.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Circuit breaker exceptions, disk watermark (index goes read-only)
- [[Type & Casting Errors]] — Mapping type mismatch (`mapper_parsing_exception`)
- [[Concurrency & Race Condition Errors]] — Version conflict on optimistic concurrency
- [[Configuration & Environment Errors]] — Index not found, fielddata disabled on text fields

## Common Gotchas
- Can't change field mapping on existing index — must reindex
- `text` vs `keyword` — text is analyzed (search), keyword is exact (sort/aggregate)
- Deep pagination (`from + size > 10000`) fails — use `search_after`
- Too many fields = mapping explosion — set `total_fields.limit`

## Related
- [[Kafka]] — often feeds data into Elasticsearch
- [[MOC — Databases & Data]]

## My Notes

