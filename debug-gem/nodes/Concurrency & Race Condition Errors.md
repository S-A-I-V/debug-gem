---
tags: [error-pattern, universal-failure-pattern]
aliases: [concurrency-errors, race-condition, deadlock, thread-safety]
---
# Concurrency & Race Condition Errors

> Timing-dependent failure. Multiple operations interfere with each other because they're not properly synchronized.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. When multiple threads, processes, or requests access shared resources without proper coordination, the outcome depends on timing — and sometimes that timing produces incorrect results.

## The Pattern
Two or more concurrent operations access shared state, and at least one modifies it. Without synchronization:
- Read-modify-write is non-atomic — updates are lost
- Check-then-act has a gap — condition can change between check and act
- Operations interleave in unexpected orders

## Universal Symptoms
- Intermittent failures that can't be reproduced reliably
- Data corruption or inconsistency
- `ConcurrentModificationException` (Java)
- `SQLITE_BUSY: database is locked`
- Merge conflicts, `push rejected` (Git)
- Deadlocks (system hangs, no progress)
- `409 Conflict` (REST API)
- Lost updates — data written by one user silently overwritten by another
- Duplicate records when uniqueness expected

## Diagnostic Decision Tree
1. **IS IT INTERMITTENT?** Race conditions are timing-dependent — non-reproducibility is the clue
2. **IS THERE SHARED STATE?** What resource do multiple operations access?
3. **IS IT READ-MODIFY-WRITE?** Check → modify → write without atomicity?
4. **IS IT A DEADLOCK?** System hangs, no error message (threads waiting on each other)
5. **IS IT ORDERING?** Events arriving in unexpected order?

## Common Causes

### Race Conditions
- Read-modify-write without lock/transaction (`count = count + 1` from multiple threads)
- Check-then-act gap (TOCTOU: check file exists, then open — file deleted between)
- Double-submit from UI (user clicks button twice)
- Stale read — reading old value while another process updates

### Deadlocks
- Circular lock dependency (A locks X then Y, B locks Y then X)
- Database row locks in different orders
- Distributed deadlock across services
- Resource exhaustion disguised as deadlock (all connections locked)

### Lost Updates
- Two users edit same resource simultaneously — last write wins
- Optimistic locking not implemented
- Read-modify-write across multiple API calls without versioning

### Ordering Issues
- Events processed out of order
- Distributed system message reordering
- Promise/async operations completing in unexpected order
- Consumer group rebalancing in message queues

## Fix Strategies
1. **Make it atomic** — transactions, locks, CAS (Compare-And-Swap)
2. **Optimistic locking** — version numbers, ETags, conditional updates
3. **Idempotency** — make operations safe to repeat (idempotency keys)
4. **Queue serialization** — funnel concurrent operations through a single queue
5. **Immutability** — immutable data can't have race conditions
6. **Lock ordering** — always acquire locks in the same order to prevent deadlocks
7. **Conflict detection** — detect and resolve rather than prevent (CRDT, Last-Write-Wins)

## Technologies That Commonly Produce This
- [[Go]] — Goroutine races, concurrent map access
- [[Java]] — `ConcurrentModificationException`, synchronized blocks, deadlocks
- [[Python]] — GIL doesn't protect against all races (especially I/O)
- [[PostgreSQL]], [[MySQL]] — Deadlocks, serialization failures
- [[SQLite]] — `SQLITE_BUSY` (single writer)
- [[Redis]] — Distributed locks, WATCH/MULTI optimistic locking
- [[Kafka]] — Consumer group rebalancing, idempotent producer
- [[Git]] — Merge conflicts, push rejected
- [[Terraform]] — State lock conflicts
- [[Solidity]] — Reentrancy attacks, front-running
- [[REST API]] — 409 Conflict, stale data
- [[Kubernetes]] — Resource version conflicts on update

## Related Patterns
- [[State & Lifecycle Errors]] — races cause unexpected state
- [[The Silent Failure]] — lost updates happen silently
- [[Resource Exhaustion Errors]] — deadlocks can exhaust connection pools

## My Notes

