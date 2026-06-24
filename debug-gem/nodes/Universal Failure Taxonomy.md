---
tags: [meta-pattern, taxonomy, core]
aliases: [universal-failure-patterns, master-logic, error-taxonomy]
---
# Universal Failure Taxonomy

> Every software error, in every language, on every platform, maps to one of these 12 patterns.

This is the core classification system of the Debug Gem. When you encounter an error, classify it first — the pattern tells you where to look and what to fix.

## The 12 Universal Failure Patterns

### 1. [[Offset & Boundary Errors]]
Went past the edge. Array index out of bounds, buffer overflow, off-by-one, constraint violations, shape mismatches.

### 2. [[Null & Undefined Reference Errors]]
Tried to use nothing. Variable is null/nil/undefined/None and code tries to access a property or call a method on it.

### 3. [[Type & Casting Errors]]
Wrong type. Expected one type, got another. Implicit coercion, failed casts, schema mismatches.

### 4. [[Permission & Authorization Errors]]
Access denied. Authentication (who are you?) or authorization (are you allowed?) failed. 401, 403, EACCES.

### 5. [[Connection & Network Errors]]
Can't reach the other side. Network path broken, DNS failure, timeout, TLS handshake failure, service down.

### 6. [[Resource Exhaustion Errors]]
Ran out of something. Memory, CPU, disk, connections, time, quota, rate limit exceeded.

### 7. [[Concurrency & Race Condition Errors]]
Timing-dependent failure. Multiple operations on shared state without synchronization. Deadlocks, lost updates, inconsistency.

### 8. [[Dependency & Import Errors]]
Can't find what you need. Module not found, package version conflict, missing library, circular dependency.

### 9. [[State & Lifecycle Errors]]
Wrong state at wrong time. Object disposed, token expired, not initialized yet, sequence violation.

### 10. [[Configuration & Environment Errors]]
Right code, wrong settings. Missing env var, wrong region, dev config in production, version mismatch between environments.

### 11. [[Syntax & Parse Errors]]
Can't understand the structure. Code doesn't compile, config file malformed, data format invalid.

### 12. [[Serialization & Encoding Errors]]
Data corrupted in translation. Format conversion failed — JSON, protobuf, character encoding, schema evolution.

## How to Use This Taxonomy

### When You See an Error:
1. **Classify** — which of the 12 patterns does this match?
2. **Locate** — what technology/domain is it in?
3. **Diagnose** — use the pattern's decision tree
4. **Fix** — apply the pattern's fix strategies, adapted to your technology

### When Multiple Patterns Apply:
Some errors span multiple patterns. For example:
- `CUDA out of memory` = [[Resource Exhaustion Errors]] + CUDA-specific context
- `CORS error` = [[Permission & Authorization Errors]] + browser enforcement
- `Connection refused` in K8s = [[Connection & Network Errors]] + K8s networking

### Meta-Patterns (How to Think):
- [[The Assumption Trap]] — every bug is a violated assumption
- [[The Boundary Problem]] — most errors happen at interfaces between systems
- [[The Environment Delta]] — "works on my machine" means environments differ
- [[The Silent Failure]] — the most dangerous errors don't crash
- [[The Leaky Abstraction]] — look one layer below when stuck

## Related
- All 12 pattern nodes linked above
- All MOC (Map of Content) files for domain-specific details
- [[Gem_Instructions]] — how the Debug Gem uses this taxonomy

## My Notes

