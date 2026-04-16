---
tags: [api, query-language, networking]
aliases: [graphql]
---
# GraphQL

Query language for APIs. Client specifies exactly what data it needs. Single endpoint, typed schema.

## Known For These Error Patterns
- [[Syntax & Parse Errors]] — Query syntax errors, schema mismatches
- [[Null & Undefined Reference Errors]] — Required variables not provided, non-nullable field returned null
- [[Resource Exhaustion Errors]] — N+1 query problem, deeply nested queries

## Common Gotchas
- N+1 problem — use DataLoader for batching
- Partial errors — GraphQL can return data AND errors simultaneously
- Over-fetching in nested queries — set depth/complexity limits
- Disable introspection in production

## Related
- [[REST API]] — alternative API style
- [[gRPC]] — alternative for service-to-service
- [[MOC — Networking & APIs]]

## My Notes

