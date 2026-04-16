---
tags: [api, networking, rpc, protobuf]
aliases: [grpc]
---
# gRPC

High-performance RPC framework by Google. Uses Protocol Buffers for serialization, HTTP/2 for transport. Supports streaming.

## Known For These Error Patterns
- [[Connection & Network Errors]] — `UNAVAILABLE`, `DEADLINE_EXCEEDED`
- [[Type & Casting Errors]] — `INVALID_ARGUMENT` (protobuf validation)
- [[Permission & Authorization Errors]] — `PERMISSION_DENIED`, `UNAUTHENTICATED`
- [[Serialization & Encoding Errors]] — Protobuf backward compatibility violations

## Common Gotchas
- Requires HTTP/2 — proxies/load balancers must support it
- Default max message size is 4MB — increase for large payloads
- Long-lived connections — need L7 load balancing, not L4
- Protobuf backward compatibility: never reuse field numbers, add fields as optional

## Related
- [[REST API]], [[GraphQL]] — alternative API styles
- [[MOC — Networking & APIs]]

## My Notes

