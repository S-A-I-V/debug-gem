---
tags: [error-pattern, universal-failure-pattern]
aliases: [resource-exhaustion, oom, out-of-memory, timeout, rate-limit]
---
# Resource Exhaustion Errors

> The system ran out of something: memory, CPU, disk, connections, time, or quota.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The system has a finite resource, and demand exceeded supply.

## The Pattern
Something consumed more of a resource than was available. The resource could be:
- **Memory** — heap, stack, GPU VRAM
- **CPU/Time** — timeouts, infinite loops, blocking operations
- **Disk** — storage full, inode exhaustion
- **Connections** — database pool, sockets, file descriptors
- **Quota/Rate** — API rate limits, cloud service quotas, bandwidth

## Universal Symptoms
- `OutOfMemoryError`, `OOMKilled`, `MemoryError`
- `TimeoutError`, `DEADLINE_EXCEEDED`, `504 Gateway Timeout`
- `Too many open files`, `EMFILE`
- `429 Too Many Requests`, `ThrottlingException`
- `No space left on device`, `ENOSPC`
- Exit code 137 (OOM killed by kernel)
- Process killed by OS without application-level error

## Diagnostic Decision Tree
1. **WHAT resource?** Memory, CPU, disk, connections, rate limit, quota?
2. **WHERE?** Client, server, database, third-party API, infrastructure?
3. **WHY?** Leak (gradual), spike (sudden), or undersized (always too small)?
4. **IS IT A LEAK?** Does usage grow over time without releasing? (memory leak, connection leak, file handle leak)
5. **IS IT A SPIKE?** Did load suddenly increase? (traffic spike, large request, batch job)
6. **IS IT UNDERSIZED?** Was the limit always too low for normal operation?

## Common Causes by Resource

### Memory
- Memory leak — objects allocated but never freed/GC'd
- Loading entire dataset into memory instead of streaming
- Unbounded caches without eviction
- Recursive calls without base case (stack overflow)
- GPU: Model too large for VRAM

### Time/CPU
- Infinite loops, unbounded recursion
- Blocking operation on main thread
- N+1 query patterns (thousands of DB calls)
- Unoptimized algorithm on large input (O(n²) or worse)

### Connections
- Connection pool exhaustion (not returning connections)
- Socket leak (not closing connections)
- Too many concurrent requests to downstream service

### Disk
- Log files growing unbounded
- Temp files not cleaned up
- Database WAL/binlog accumulation

### Rate/Quota
- API rate limit exceeded (throttling)
- Cloud service quota reached
- License limit exceeded

## Fix Strategies
1. **Increase the limit** (short-term) — more memory, higher timeout, bigger quota
2. **Reduce consumption** — optimize algorithm, compress data, batch requests
3. **Fix the leak** — find what's not being released and release it
4. **Add backpressure** — rate limiting, queue with bounded size, circuit breaker
5. **Stream instead of buffer** — process data in chunks, not all at once
6. **Cache with eviction** — bounded cache with LRU/TTL eviction policy

## Technologies That Commonly Produce This
- [[Docker]], [[Kubernetes]] — OOMKilled (exit 137), CPU throttling, eviction
- [[Java]] — `OutOfMemoryError` (heap, metaspace, GC overhead)
- [[Python]] — `MemoryError`, `RecursionError`
- [[JavaScript]] — Heap out of memory, event loop blocking
- [[CUDA]], [[PyTorch]], [[TensorFlow]] — GPU OOM
- [[Redis]], [[PostgreSQL]], [[MySQL]] — Connection exhaustion, memory limits
- [[AWS]], [[GCP]], [[Azure]] — Service quotas, throttling
- [[LLM]] — Context window limits, rate limits
- [[Kafka]], [[Elasticsearch]] — Throughput exceeded, circuit breakers
- [[React]] — Infinite re-render loops

## Related Patterns
- [[The Silent Failure]] — resource exhaustion can degrade silently before crashing
- [[Connection & Network Errors]] — timeouts are sometimes resource exhaustion
- [[Configuration & Environment Errors]] — wrong limits configured

## My Notes

