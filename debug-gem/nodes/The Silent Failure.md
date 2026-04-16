---
tags: [meta-pattern, debugging-philosophy]
---
# The Silent Failure

> The most dangerous errors are the ones that don't crash.

Data corruption, logic errors, security holes, performance degradation — they pass silently. The system appears to work but produces wrong results.

## Examples
- SQL query returns empty set instead of error
- API returns 200 with error in body
- Float precision silently corrupts financial calculations
- Race condition silently loses data
- LLM hallucination — confident but wrong output
- Security vulnerability — no error, just exposed data
- Memory leak — works fine until it doesn't

## Prevention
- Add assertions and invariant checks
- Monitor not just errors, but correctness (data validation, checksums)
- Test that code produces correct output, not just that it runs
- Use property-based testing to find edge cases
- Log and alert on unexpected-but-not-crashing conditions

## Related
- [[The Assumption Trap]], [[The Boundary Problem]]
- [[LLM]] — hallucination is a silent failure
- [[Concurrency & Race Condition Errors]] — lost updates are silent

## My Notes

