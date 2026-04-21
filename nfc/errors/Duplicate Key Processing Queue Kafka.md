---
tags: [nfc, error, backend, kafka, postgresql, concurrency]
date: 2026-04-21
severity: 🟠 High
status: open
---
# Duplicate Key Processing Queue — Kafka Consumer

## Error
```
ERROR: duplicate key value violates unique constraint "uq_processing_queue_active_key"
Detail: Key (entity_type, entity_name, data_date, sub_key)=(JOB, CDP Collection, 2026-04-20, ) already exists.

org.springframework.dao.DataIntegrityViolationException: could not execute statement
[insert into processing_queue (attempt_count,claimed_at,claimed_by,created_at,data_date,
entity_name,entity_type,last_error,next_attempt_at,status,sub_key,updated_at,queue_id)
values (?,?,?,?,?,?,?,?,?,?,?,?,?)]
constraint [uq_processing_queue_active_key]
```

Thrown by: `JobEventHistoryConsumer` processing a Kafka job status event batch.

## Pattern
**Concurrency / Race Condition** — Kafka at-least-once delivery causes duplicate INSERT when consumer reprocesses a message after offset commit failure or rebalance.

Also check: **Boundary / Data Issue** — the `sub_key` value is empty string `('')`. If it should be populated, the real bug is upstream (event missing data).

## Root Cause
The Kafka consumer receives a job status event and tries to INSERT into `processing_queue`. A row with the same composite key `(entity_type, entity_name, data_date, sub_key)` already exists because:

1. **Duplicate Kafka message (most likely)** — Consumer processed the message, inserted the row, but crashed/timed out before committing the offset. On retry, same INSERT hits the unique constraint.
2. **Concurrent consumers** — Multiple consumer threads/instances process the same event and race to insert the same key.

## Fix

### Option A: SQL-level upsert (preferred)
```java
@Query(value = """
    INSERT INTO processing_queue (...)
    VALUES (...)
    ON CONFLICT ON CONSTRAINT uq_processing_queue_active_key
    DO UPDATE SET updated_at = NOW(), status = :status
    """, nativeQuery = true)
```

### Option B: Application-level catch and update
```java
try {
    repository.save(entity);
} catch (DataIntegrityViolationException e) {
    var existing = repository.findByEntityTypeAndEntityNameAndDataDateAndSubKey(
        entityType, entityName, dataDate, subKey);
    existing.setStatus(newStatus);
    existing.setUpdatedAt(Instant.now());
    repository.save(existing);
}
```

### Also investigate
- Is `sub_key` supposed to be empty? If not, fix the upstream event producer.
- Check Kafka consumer group config — `enable.auto.commit`, partition assignment, `session.timeout.ms`.
- If using `ON CONFLICT DO UPDATE`, add a version/timestamp check to avoid overwriting newer status with older.

## Time to Fix
TBD — depends on chosen approach.

## Connected To
- [[NFC-Backend]]
