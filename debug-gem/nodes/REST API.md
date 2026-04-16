---
tags: [api, networking, http]
aliases: [rest, rest-api, restful]
---
# REST API

Architectural style for web APIs. Uses HTTP methods (GET, POST, PUT, DELETE) on resource URLs.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — 401/403, CORS errors
- [[Type & Casting Errors]] — 400/422 validation errors
- [[Connection & Network Errors]] — 502/504 gateway errors
- [[Resource Exhaustion Errors]] — 429 rate limiting
- [[Concurrency & Race Condition Errors]] — 409 conflicts, stale data

## Common Gotchas
- CORS is browser-enforced — fix on server side, not client
- Trailing slash inconsistency — `/users` vs `/users/` may differ
- POST is not idempotent — use idempotency keys for safety
- Content-Type header must match body format

## Related
- [[GraphQL]] — alternative query-based API
- [[gRPC]] — alternative for service-to-service
- [[MOC — Networking & APIs]]

## My Notes

