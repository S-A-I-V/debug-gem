---
tags: [database, nosql, aws, serverless]
aliases: [dynamodb, dynamo]
---
# DynamoDB

AWS managed NoSQL database. Key-value and document store. Serverless, auto-scaling.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Throughput exceeded, item size limit (400KB), hot partitions
- [[Concurrency & Race Condition Errors]] — Conditional check failures, transaction conflicts
- [[Configuration & Environment Errors]] — Table not found, key schema mismatch

## Common Gotchas
- Hot partition — uneven key distribution causes one partition to get all traffic
- No JOIN — denormalize data or use single-table design
- GSI is eventually consistent — writes are async
- `Scan` consumes RCU proportional to table size — use `Query` with partition key

## Related
- [[AWS]] — DynamoDB is AWS-native
- [[MongoDB]] — similar document model but different trade-offs
- [[MOC — Databases & Data]]

## My Notes

