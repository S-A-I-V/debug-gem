# MOC — Databases & Data

> Error patterns for relational databases, NoSQL, caches, message queues, and data systems. Databases are where [[Concurrency & Race Condition Errors]], [[Resource Exhaustion Errors]], and [[Permission & Authorization Errors]] converge.

---

## [[PostgreSQL]]

### Connection Errors
- `could not connect to server: Connection refused. Is the server running on host "X" and accepting TCP/IP connections on port 5432?` → [[Connection & Network Errors]]: Server not running, wrong host/port, or firewall blocking.
- `FATAL: password authentication failed for user "X"` → [[Permission & Authorization Errors]]: Wrong password.
- `FATAL: no pg_hba.conf entry for host "X", user "Y", database "Z"` → [[Permission & Authorization Errors]]: Client IP not allowed in pg_hba.conf.
- `FATAL: too many connections for role "X"` → [[Resource Exhaustion Errors]]: Connection limit per role exceeded.
- `FATAL: sorry, too many clients already` → [[Resource Exhaustion Errors]]: Global connection limit (`max_connections`) reached. Use connection pooling (PgBouncer).
- `SSL connection is required` → [[Configuration & Environment Errors]]: Server requires SSL but client didn't use it.
- `FATAL: database "X" does not exist` → [[Configuration & Environment Errors]]: Wrong database name.
- `FATAL: role "X" does not exist` → [[Configuration & Environment Errors]]: User/role not created.

### Query Errors
- `ERROR: syntax error at or near "X"` → [[Syntax & Parse Errors]]: SQL syntax error.
- `ERROR: column "X" does not exist` → [[Configuration & Environment Errors]]: Column name wrong or not quoted (case-sensitive if quoted).
- `ERROR: relation "X" does not exist` → [[Configuration & Environment Errors]]: Table/view doesn't exist or wrong schema.
- `ERROR: function X does not exist` → [[Configuration & Environment Errors]]: Function not created or wrong argument types.
- `ERROR: operator does not exist: X = Y` → [[Type & Casting Errors]]: Comparing incompatible types.
- `ERROR: invalid input syntax for type X: "Y"` → [[Type & Casting Errors]]: Can't convert string to target type.
- `ERROR: column "X" is of type Y but expression is of type Z` → [[Type & Casting Errors]]: Type mismatch in INSERT/UPDATE.
- `ERROR: value too long for type character varying(X)` → [[Offset & Boundary Errors]]: String exceeds column length.
- `ERROR: integer out of range` → [[Offset & Boundary Errors]]: Number exceeds integer type range.
- `ERROR: division by zero` → [[Offset & Boundary Errors]]: Dividing by zero in query.
- `ERROR: more than one row returned by a subquery used as an expression` → [[Offset & Boundary Errors]]: Subquery returns multiple rows where single value expected.
- `ERROR: duplicate key value violates unique constraint "X"` → [[Concurrency & Race Condition Errors]] or [[Offset & Boundary Errors]]: Unique constraint violation.
- `ERROR: insert or update on table "X" violates foreign key constraint "Y"` → [[Offset & Boundary Errors]]: Referenced row doesn't exist.
- `ERROR: null value in column "X" of relation "Y" violates not-null constraint` → [[Null & Undefined Reference Errors]]: Inserting NULL into NOT NULL column.
- `ERROR: update or delete on table "X" violates foreign key constraint "Y" on table "Z"` → [[State & Lifecycle Errors]]: Can't delete row that's referenced by other tables.
- `ERROR: permission denied for table X` → [[Permission & Authorization Errors]]: User lacks GRANT.
- `ERROR: permission denied for schema X` → [[Permission & Authorization Errors]]: User lacks USAGE on schema.
- `ERROR: must be owner of table X` → [[Permission & Authorization Errors]]: Only owner can ALTER/DROP.

### Transaction & Concurrency Errors
- `ERROR: deadlock detected` → [[Concurrency & Race Condition Errors]]: Two transactions waiting for each other's locks.
  - **Fix**: Ensure consistent lock ordering. Keep transactions short. Retry on deadlock.
- `ERROR: could not serialize access due to concurrent update` → [[Concurrency & Race Condition Errors]]: SERIALIZABLE isolation conflict.
  - **Fix**: Retry the transaction. This is expected behavior with SERIALIZABLE.
- `ERROR: could not serialize access due to read/write dependencies among transactions` → [[Concurrency & Race Condition Errors]]: Write skew detected.
- `ERROR: current transaction is aborted, commands ignored until end of transaction block` → [[State & Lifecycle Errors]]: Previous statement in transaction failed. Must ROLLBACK.
  - **Fix**: Always handle errors in transactions. ROLLBACK on error, then retry or report.
- `ERROR: canceling statement due to lock timeout` → [[Concurrency & Race Condition Errors]]: Couldn't acquire lock within `lock_timeout`.
- `ERROR: canceling statement due to statement timeout` → [[Resource Exhaustion Errors]]: Query exceeded `statement_timeout`.
- `ERROR: canceling statement due to user request` → [[State & Lifecycle Errors]]: Query cancelled by client.

### Performance & Resource Errors
- `ERROR: could not extend file "X": No space left on device` → [[Resource Exhaustion Errors]]: Disk full.
- `ERROR: temporary file size exceeds temp_file_limit` → [[Resource Exhaustion Errors]]: Query using too much temp space (large sort/hash).
- `WARNING: could not flush dirty data: No space left on device` → [[Resource Exhaustion Errors]]: WAL disk full.
- Slow queries → Check `EXPLAIN ANALYZE`. Look for sequential scans on large tables, missing indexes, bad join order.
- Table bloat → Dead tuples not vacuumed. Run `VACUUM ANALYZE`. Check autovacuum settings.
- WAL accumulation → Replication slot not advancing, or `wal_keep_size` too high. Check `pg_replication_slots`.
- Connection exhaustion → Use PgBouncer. Check for connection leaks in application.

### PostgreSQL Traps
- **NULL in unique constraints**: Multiple NULLs are allowed in unique columns (NULL != NULL).
- **Implicit casting**: PostgreSQL is stricter than MySQL but still has implicit casts that can surprise.
- **`VACUUM` not running**: If autovacuum is disabled or can't keep up, table bloat grows, queries slow down, and eventually transaction ID wraparound threatens data loss.
- **Transaction ID wraparound**: PostgreSQL uses 32-bit transaction IDs. If VACUUM doesn't run, you can run out. Emergency: `VACUUM FREEZE`.
- **`LIKE` vs `ILIKE`**: `LIKE` is case-sensitive, `ILIKE` is case-insensitive. Neither uses indexes by default (need `text_pattern_ops` or `pg_trgm`).
- **`OFFSET` performance**: `OFFSET 1000000` still scans 1M rows. Use keyset pagination instead.
- **`SELECT *` with many columns**: Fetches all columns including large text/bytea. Select only needed columns.
- **Missing `LIMIT` on DELETE/UPDATE**: Without WHERE or LIMIT, affects all rows. Always double-check.

---

## [[MySQL]]

### Common Errors
- `ERROR 1045 (28000): Access denied for user 'X'@'Y'` → [[Permission & Authorization Errors]]: Wrong password or user not allowed from this host.
- `ERROR 1049 (42000): Unknown database 'X'` → [[Configuration & Environment Errors]]: Database doesn't exist.
- `ERROR 1054 (42S22): Unknown column 'X' in 'Y'` → [[Configuration & Environment Errors]]: Column doesn't exist.
- `ERROR 1062 (23000): Duplicate entry 'X' for key 'Y'` → [[Concurrency & Race Condition Errors]] or [[Offset & Boundary Errors]]: Unique key violation.
- `ERROR 1064 (42000): You have an error in your SQL syntax` → [[Syntax & Parse Errors]]: SQL syntax error.
- `ERROR 1146 (42S02): Table 'X.Y' doesn't exist` → [[Configuration & Environment Errors]]: Table not found.
- `ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction` → [[Concurrency & Race Condition Errors]]: Deadlock. Retry.
- `ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction` → [[Concurrency & Race Condition Errors]]: Long-running transaction holding lock.
- `ERROR 1040 (HY000): Too many connections` → [[Resource Exhaustion Errors]]: Connection limit reached.
- `ERROR 1153 (08S01): Got a packet bigger than 'max_allowed_packet' bytes` → [[Resource Exhaustion Errors]]: Increase `max_allowed_packet`.
- `ERROR 1114 (HY000): The table 'X' is full` → [[Resource Exhaustion Errors]]: Table size limit or disk full.
- `ERROR 1366 (HY000): Incorrect string value: 'X' for column 'Y'` → [[Serialization & Encoding Errors]]: Character not supported by column charset.
  - **Fix**: Use `utf8mb4` charset, not `utf8` (which is only 3-byte UTF-8, can't store emoji).
- `ERROR 1406 (22001): Data too long for column 'X'` → [[Offset & Boundary Errors]]: String exceeds column length.
- `ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails` → [[Offset & Boundary Errors]]: Foreign key reference doesn't exist.
- `ERROR 2002 (HY000): Can't connect to local MySQL server through socket` → [[Connection & Network Errors]]: MySQL not running or socket path wrong.
- `ERROR 2003 (HY000): Can't connect to MySQL server on 'X'` → [[Connection & Network Errors]]: Server unreachable.
- `ERROR 2006 (HY000): MySQL server has gone away` → [[Connection & Network Errors]]: Connection dropped (timeout, server restart, packet too large).
- `ERROR 2013 (HY000): Lost connection to MySQL server during query` → [[Connection & Network Errors]]: Connection lost mid-query.

### MySQL Traps
- **`utf8` is NOT real UTF-8**: MySQL's `utf8` is 3-byte only (no emoji). Use `utf8mb4` for full UTF-8 support.
- **Implicit commits**: DDL statements (`ALTER TABLE`, `CREATE INDEX`) cause implicit commit. Can't be rolled back.
- **MyISAM vs InnoDB**: MyISAM doesn't support transactions or foreign keys. Always use InnoDB.
- **`GROUP BY` non-aggregated columns**: MySQL allows selecting non-aggregated columns not in GROUP BY (with `ONLY_FULL_GROUP_BY` disabled). Results are non-deterministic.
- **Silent truncation**: MySQL may silently truncate data that's too long (depending on `sql_mode`). Enable `STRICT_TRANS_TABLES`.
- **`ON UPDATE CURRENT_TIMESTAMP`**: Columns with this auto-update on any row update. Can be surprising.

---

## [[MongoDB]]

### Common Errors
- `MongoServerError: E11000 duplicate key error collection: X index: Y` → [[Offset & Boundary Errors]]: Unique index violation.
- `MongoServerError: not primary` / `MongoServerError: not master` → [[State & Lifecycle Errors]]: Writing to secondary in replica set.
- `MongoServerError: Authentication failed` → [[Permission & Authorization Errors]]: Wrong credentials or auth database.
- `MongoServerSelectionError: connect ECONNREFUSED` → [[Connection & Network Errors]]: Can't connect to MongoDB.
- `MongoServerError: document exceeds maximum size (16MB)` → [[Resource Exhaustion Errors]]: Document too large. Use GridFS.
- `MongoServerError: Executor error during find command: OperationFailed: Sort operation used more than the maximum X bytes of RAM` → [[Resource Exhaustion Errors]]: Sort exceeds memory limit. Add index or increase `allowDiskUse`.
- `MongoServerError: ns not found` → [[Configuration & Environment Errors]]: Collection doesn't exist.
- `MongoServerError: command X requires authentication` → [[Permission & Authorization Errors]]: Not authenticated.
- `MongoTimeoutError: Server selection timed out` → [[Connection & Network Errors]]: Can't find suitable server in replica set.
- `MongoWriteConcernError` → [[Concurrency & Race Condition Errors]]: Write concern not satisfied (not enough replicas acknowledged).

### MongoDB Traps
- **Schema-less doesn't mean schema-free**: Without validation, documents can have any shape. Use JSON Schema validation.
- **`$lookup` performance**: Joins in MongoDB are expensive. Denormalize data for read-heavy workloads.
- **ObjectId ordering**: ObjectIds are roughly time-ordered. Can be used for time-based queries but not precise.
- **Write concern defaults**: Default write concern is `w:1` (primary only). For durability, use `w:"majority"`.
- **Read preference**: `secondaryPreferred` can return stale data. Use `primary` for consistency-critical reads.
- **Index intersection**: MongoDB rarely uses multiple indexes for a single query. Design compound indexes.
- **`$regex` without index**: Regex queries without anchored prefix (`^`) can't use indexes. Full collection scan.

---

## [[Redis]]

### Common Errors
- `OOM command not allowed when used memory > 'maxmemory'` → [[Resource Exhaustion Errors]]: Memory limit reached.
  - **Fix**: Set eviction policy (`allkeys-lru`, `volatile-lru`), increase `maxmemory`, or reduce data.
- `WRONGTYPE Operation against a key holding the wrong kind of value` → [[Type & Casting Errors]]: Key exists as different data structure (e.g., GET on a hash key).
- `CLUSTERDOWN The cluster is down` → [[Connection & Network Errors]]: Cluster node failure, hash slots not covered.
- `MOVED X Y:Z` → [[Configuration & Environment Errors]]: Client not cluster-aware. Use cluster client.
- `ASK X Y:Z` → [[State & Lifecycle Errors]]: Slot being migrated. Client should redirect.
- `READONLY You can't write against a read only replica` → [[State & Lifecycle Errors]]: Writing to replica.
- `BUSY Redis is busy running a script` → [[Resource Exhaustion Errors]]: Lua script running too long.
- `LOADING Redis is loading the dataset in memory` → [[State & Lifecycle Errors]]: Redis starting up, loading RDB/AOF.
- `NOSCRIPT No matching script` → [[Configuration & Environment Errors]]: Lua script not loaded. Use `EVAL` instead of `EVALSHA`, or load script first.
- `ERR max number of clients reached` → [[Resource Exhaustion Errors]]: Connection limit reached.
- `EXECABORT Transaction discarded because of previous errors` → [[State & Lifecycle Errors]]: Error in MULTI/EXEC transaction.

### Redis Traps
- **Keys without TTL**: Keys without expiration grow forever. Always set TTL for cache data.
- **`KEYS *` in production**: Blocks Redis (single-threaded). Use `SCAN` instead.
- **Lua script timeout**: Long-running Lua scripts block all other operations. Keep scripts short.
- **Persistence trade-offs**: RDB = periodic snapshots (data loss on crash). AOF = append-only log (slower, larger). Use both for safety.
- **Pub/Sub message loss**: If subscriber disconnects, messages are lost. Use Streams for reliable messaging.
- **Single-threaded**: Redis is single-threaded for commands. One slow command blocks everything. Avoid `KEYS`, large `SORT`, `SMEMBERS` on huge sets.
- **Memory fragmentation**: `used_memory` vs `used_memory_rss`. High ratio means fragmentation. Restart or use `MEMORY PURGE`.
- **Cluster resharding**: Moving slots between nodes can cause brief unavailability for affected keys.

---

## [[Elasticsearch]]

### Common Errors
- `circuit_breaking_exception: [parent] Data too large` → [[Resource Exhaustion Errors]]: Query too memory-intensive.
- `mapper_parsing_exception` → [[Type & Casting Errors]]: Document field type doesn't match mapping.
- `index_not_found_exception` → [[Configuration & Environment Errors]]: Index doesn't exist.
- `resource_already_exists_exception` → [[State & Lifecycle Errors]]: Index already exists.
- `cluster_block_exception: index [X] blocked by: [FORBIDDEN/12/index read-only / allow delete]` → [[Resource Exhaustion Errors]]: Disk watermark reached. ES set index to read-only.
  - **Fix**: Free disk space, then: `PUT /index/_settings {"index.blocks.read_only_allow_delete": null}`.
- `search_phase_execution_exception` → Various: Check the root cause in the response.
- `version_conflict_engine_exception` → [[Concurrency & Race Condition Errors]]: Optimistic concurrency conflict.
- `illegal_argument_exception: Fielddata is disabled on text fields by default` → [[Configuration & Environment Errors]]: Can't aggregate/sort on text field. Use `.keyword` subfield.
- `too_many_clauses: maxClauseCount is set to X` → [[Resource Exhaustion Errors]]: Boolean query has too many clauses.

### Elasticsearch Traps
- **Mapping explosion**: Too many fields in an index. Set `index.mapping.total_fields.limit`.
- **Text vs Keyword**: `text` is analyzed (full-text search). `keyword` is exact match. Use `.keyword` for aggregations/sorting.
- **Near-real-time**: Documents are searchable after refresh (default 1 second). Not immediately after indexing.
- **Shard sizing**: Too many small shards = overhead. Too few large shards = can't distribute. Aim for 10-50GB per shard.
- **Deep pagination**: `from + size` > 10000 fails by default. Use `search_after` or scroll API.
- **Reindexing**: Can't change field mapping on existing index. Must create new index and reindex data.

---

## [[Kafka]]

### Common Errors
- `OffsetOutOfRangeException` → [[Offset & Boundary Errors]]: Consumer offset past retention period.
  - **Fix**: Reset offset with `kafka-consumer-groups --reset-offsets`.
- `NotLeaderForPartitionException` → [[State & Lifecycle Errors]]: Partition leader changed. Client should retry.
- `RecordTooLargeException` → [[Resource Exhaustion Errors]]: Message exceeds `max.message.bytes`.
- `SerializationException` → [[Serialization & Encoding Errors]]: Schema registry version conflict or serialization failure.
- `TimeoutException: Failed to update metadata` → [[Connection & Network Errors]]: Can't reach Kafka brokers.
- `TopicAuthorizationException` → [[Permission & Authorization Errors]]: ACL doesn't allow operation.
- `UnknownTopicOrPartitionException` → [[Configuration & Environment Errors]]: Topic doesn't exist.
- `GroupAuthorizationException` → [[Permission & Authorization Errors]]: Consumer group not authorized.
- `ProducerFencedException` → [[Concurrency & Race Condition Errors]]: Another producer with same transactional ID.
- `OutOfOrderSequenceException` → [[Concurrency & Race Condition Errors]]: Idempotent producer sequence number mismatch.
- `InvalidReplicationFactorException` → [[Configuration & Environment Errors]]: Replication factor > number of brokers.
- `RebalanceInProgressException` → [[State & Lifecycle Errors]]: Consumer group rebalancing.

### Kafka Traps
- **Consumer group rebalancing storms**: Frequent rebalances cause processing delays. Increase `session.timeout.ms`, use static group membership.
- **Exactly-once semantics**: Requires idempotent producer + transactional producer + read_committed consumers. Complex to set up correctly.
- **Topic partition count**: Can't decrease after creation. Plan partition count based on expected throughput.
- **Consumer lag**: Consumer falling behind producer. Monitor with `kafka-consumer-groups --describe`. Scale consumers (max = partition count).
- **Compacted topics**: Log compaction keeps latest value per key. But compaction is async — may see duplicates temporarily.
- **Message ordering**: Ordering guaranteed within a partition only. Use same key for related messages.
- **Retention vs compaction**: `cleanup.policy=delete` removes old messages. `cleanup.policy=compact` keeps latest per key. Can combine both.

---

## [[DynamoDB]]

### Common Errors
- `ProvisionedThroughputExceededException` → [[Resource Exhaustion Errors]]: RCU/WCU exceeded. Use on-demand or increase capacity.
- `ConditionalCheckFailedException` → [[Concurrency & Race Condition Errors]]: Condition expression not met.
- `TransactionConflictException` → [[Concurrency & Race Condition Errors]]: Transaction conflicts with another.
- `ValidationException: Item size has exceeded the maximum allowed size` → [[Resource Exhaustion Errors]]: Item > 400KB.
- `ValidationException: The provided key element does not match the schema` → [[Type & Casting Errors]]: Wrong key attribute name or type.
- `ResourceNotFoundException: Requested resource not found` → [[Configuration & Environment Errors]]: Table doesn't exist.
- `ItemCollectionSizeLimitExceededException` → [[Resource Exhaustion Errors]]: LSI partition > 10GB.
- `AccessDeniedException` → [[Permission & Authorization Errors]]: IAM policy doesn't allow DynamoDB operation.
- `ThrottlingException` → [[Resource Exhaustion Errors]]: Control plane API rate limit.

### DynamoDB Traps
- **Hot partition**: Uneven key distribution causes one partition to get all traffic. Design partition key for even distribution.
- **Scan is expensive**: Full table scan consumes RCU proportional to table size. Use Query with partition key.
- **GSI eventual consistency**: Global Secondary Indexes are eventually consistent. Writes to GSI are async.
- **Transaction limits**: Max 100 items per transaction. Max 4MB total.
- **No JOIN**: DynamoDB doesn't support joins. Denormalize data or use single-table design.
- **Attribute name limits**: Attribute names count toward item size. Use short names for frequently stored attributes.

---

## [[SQLite]]

### Common Errors
- `SQLITE_BUSY: database is locked` → [[Concurrency & Race Condition Errors]]: Another connection has a write lock.
  - **Fix**: Use WAL mode (`PRAGMA journal_mode=WAL`), increase busy timeout, serialize writes.
- `SQLITE_CONSTRAINT: UNIQUE constraint failed` → [[Offset & Boundary Errors]]: Unique constraint violation.
- `SQLITE_CONSTRAINT: NOT NULL constraint failed` → [[Null & Undefined Reference Errors]]: NULL in NOT NULL column.
- `SQLITE_CONSTRAINT: FOREIGN KEY constraint failed` → [[Offset & Boundary Errors]]: Foreign key reference invalid.
- `SQLITE_READONLY: attempt to write a readonly database` → [[Permission & Authorization Errors]]: File permissions or read-only filesystem.
- `SQLITE_CORRUPT: database disk image is malformed` → [[Serialization & Encoding Errors]]: Database file corrupted.
- `SQLITE_FULL: database or disk is full` → [[Resource Exhaustion Errors]]: Disk full.

### SQLite Traps
- **Single writer**: SQLite allows only one writer at a time. Use WAL mode for better concurrency.
- **No concurrent writes from multiple processes**: SQLite is designed for single-process use. Use PostgreSQL/MySQL for multi-process.
- **Type affinity**: SQLite uses type affinity, not strict types. You can store a string in an integer column.
- **No ALTER TABLE DROP COLUMN** (before 3.35.0): Older versions can't drop columns. Must recreate table.

---

## Universal Database Debug Checklist

1. **Can you connect?** → [[Connection & Network Errors]]: Check host, port, credentials, firewall, SSL.
2. **Are you authenticated?** → [[Permission & Authorization Errors]]: Check username, password, auth database.
3. **Are you authorized?** → [[Permission & Authorization Errors]]: Check GRANT, roles, policies.
4. **Does the object exist?** → [[Configuration & Environment Errors]]: Check table, column, index names. Case sensitivity.
5. **Is the query valid?** → [[Syntax & Parse Errors]]: Check SQL syntax, quoting, escaping.
6. **Does the data match the schema?** → [[Type & Casting Errors]]: Check types, constraints, nullable.
7. **Is there a constraint violation?** → [[Offset & Boundary Errors]]: Check unique, foreign key, check constraints.
8. **Is there a concurrency issue?** → [[Concurrency & Race Condition Errors]]: Check locks, deadlocks, isolation level.
9. **Are resources available?** → [[Resource Exhaustion Errors]]: Check connections, disk, memory, CPU.
10. **Is the query performant?** → Use EXPLAIN/EXPLAIN ANALYZE. Check indexes, query plan, statistics.
