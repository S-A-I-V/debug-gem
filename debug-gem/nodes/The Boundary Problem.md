---
tags: [meta-pattern, debugging-philosophy]
---
# The Boundary Problem

> Most errors happen at boundaries.

Between systems, between layers, between environments, between time zones, between teams, between languages, between sync and async, between user space and kernel space, between client and server.

**If you're debugging, find the boundary first.** The error is almost always at the interface, not deep inside a component.

## Examples of Boundaries
- API request/response (client ↔ server)
- Database query (application ↔ database)
- File I/O (process ↔ filesystem)
- Network call (service ↔ service)
- Serialization (in-memory ↔ wire format)
- Process boundary (parent ↔ child)
- Trust boundary (user input ↔ system)
- Environment boundary (dev ↔ prod)

## Related Patterns
- [[Serialization & Encoding Errors]] — data crossing format boundaries
- [[Type & Casting Errors]] — data crossing type boundaries
- [[Connection & Network Errors]] — crossing network boundaries
- [[Permission & Authorization Errors]] — crossing trust boundaries
- [[The Assumption Trap]], [[The Environment Delta]]

## My Notes

