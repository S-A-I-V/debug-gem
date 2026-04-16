---
tags: [meta-pattern, debugging-philosophy]
---
# The Leaky Abstraction

> All non-trivial abstractions leak. — Joel Spolsky

The ORM doesn't hide SQL perfectly. The container doesn't isolate the host completely. The cloud doesn't eliminate hardware failures. When debugging, be willing to look one layer below the abstraction.

## Examples
- ORM generates inefficient SQL → look at the actual queries
- Container networking issue → check host network configuration
- "Serverless" function hits cold start → understand the underlying compute
- HTTP library hides connection pooling → pool exhaustion causes mysterious failures
- Promise/async hides execution order → race conditions emerge

## Related
- [[The Boundary Problem]] — abstractions create boundaries
- [[The Assumption Trap]] — abstractions create assumptions

## My Notes

