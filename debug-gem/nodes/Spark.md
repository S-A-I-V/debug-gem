---
tags: [data, big-data, distributed, processing]
aliases: [spark, apache-spark, pyspark]
---
# Apache Spark

Distributed data processing engine. Handles massive datasets across clusters. PySpark for Python API.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — OOM on driver/executor, GC overhead
- [[Type & Casting Errors]] — Column resolution errors, schema mismatches
- [[Serialization & Encoding Errors]] — `Task not serializable` (closure captures non-serializable object)

## Common Gotchas
- Lazy evaluation — errors surface at action time, not transformation time
- Data skew — one partition much larger than others causes OOM/slowness
- `collect()` on large data brings everything to driver — OOM
- Python UDFs are slow — use Pandas UDFs or native Spark functions

## Related
- [[Pandas]] — single-machine alternative
- [[Kafka]] — common data source
- [[MOC — AI & ML]]

## My Notes

