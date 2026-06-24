---
tags: [error-pattern, universal-failure-pattern]
aliases: [state-errors, lifecycle-errors, stale-state, expired]
---
# State & Lifecycle Errors

> The system is in the wrong state for the requested operation, or the operation happened at the wrong time in the lifecycle.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. Something expired, something wasn't initialized yet, something was already disposed, or the sequence of operations was wrong.

## The Pattern
Every system has a lifecycle (created → initialized → running → stopped → destroyed). Operations are only valid in certain states. This pattern fires when:
- Operation attempted on wrong state (object disposed, connection closed, transaction aborted)
- Something expired (token, certificate, session, cache entry)
- Something isn't ready yet (not initialized, still loading, pending)
- Sequence violated (step 2 before step 1, writing after close)

## Universal Symptoms
- `InvalidStateError`, `IllegalStateException`
- `ObjectDisposedException`, `ClosedChannelException`
- `Token expired`, `Certificate expired`, `Session timeout`
- `Not initialized`, `Already closed`, `Already started`
- `CrashLoopBackOff`, `Pending` (Kubernetes pod states)
- `ExpressionChangedAfterItHasBeenCheckedError` (Angular)
- `setState() called after dispose()` (Flutter/React)
- `current transaction is aborted` (PostgreSQL)

## Diagnostic Decision Tree
1. **WHAT state is it in?** Log/inspect the current state of the object/system.
2. **WHAT state should it be in?** What does the operation require?
3. **WHY is it in the wrong state?** Did something expire? Was initialization skipped? Did cleanup happen too early?
4. **IS IT A TIMING ISSUE?** Race condition between state transitions?
5. **IS IT A SEQUENCE ISSUE?** Operations called in wrong order?

## Common Causes

### Expiration
- JWT/OAuth token expired — implement refresh logic
- TLS certificate expired — set up auto-renewal
- Session timeout — handle re-authentication gracefully
- DNS TTL — stale cached records after migration
- Cache entry expired during use

### Premature Cleanup
- Object disposed/closed before operation completes
- Component unmounted before async operation returns (React, Flutter)
- Database connection returned to pool while query still running
- File handle closed in finally block before async write completes

### Initialization Order
- Accessing resource before initialization completes
- Dependency not ready when dependent starts
- Circular initialization dependencies
- Static initialization order (C++ fiasco)

### Wrong Sequence
- Writing to read-only replica
- Committing after transaction abort (PostgreSQL)
- Calling methods after `close()`/`dispose()`
- State machine transition not allowed from current state

## Fix Strategies
1. **Guard state** — check state before operation, throw meaningful error
2. **Handle expiration** — implement refresh/retry logic with exponential backoff
3. **Lifecycle hooks** — use proper initialization/cleanup hooks (componentDidMount/componentWillUnmount, constructor/dispose)
4. **State machines** — model valid transitions explicitly, reject invalid ones
5. **Cancellation** — cancel pending operations when lifecycle ends
6. **Idempotency** — make operations safe to retry regardless of state

## Technologies That Commonly Produce This
- [[React]], [[Angular]], [[Vue]], [[Flutter]] — Component lifecycle issues
- [[OAuth]], [[JWT]], [[OIDC]] — Token/session expiration
- [[TLS-SSL]], [[SAML]] — Certificate expiration, assertion timing
- [[Kubernetes]] — Pod state issues (CrashLoopBackOff, Pending, Terminating)
- [[PostgreSQL]] — Aborted transaction state
- [[Kafka]] — Consumer group rebalancing, partition leader changes
- [[Redis]] — Writing to replica, loading dataset
- [[Ethereum]] — Nonce issues, pending transactions
- [[Git]] — Detached HEAD, branch state conflicts
- [[Terraform]] — State drift, resource already exists
- [[PyTorch]] — Training mode vs eval mode, autograd graph

## Related Patterns
- [[Concurrency & Race Condition Errors]] — race conditions can cause unexpected state
- [[Configuration & Environment Errors]] — wrong config can prevent proper initialization
- [[The Assumption Trap]] — assuming something is in the right state

## My Notes

