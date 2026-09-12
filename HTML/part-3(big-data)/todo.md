# MONTH 4 — Kafka

This should be one of your **deepest months**.

## Book

### ⭐⭐⭐ Kafka: The Definitive Guide

Read deeply.

---

## Foundational papers/articles

### #16 — Kafka

[Kafka documentation and design](https://kafka.apache.org/documentation/?utm_source=chatgpt.com)

### #17 — The Log

[Jay Kreps — The Log](https://engineering.linkedin.com/kafka/log-what-every-software-engineer-should-know-about-real-time-datas-part-i?utm_source=chatgpt.com)

### #18 — Kafka replication

Study Kafka's replication design.

### #19 — Kafka transactions

Study exactly-once semantics.

### #20 — KRaft

Study the modern Kafka metadata architecture.

---

## FAANG blogs

### LinkedIn

[LinkedIn Engineering](https://engineering.linkedin.com/?utm_source=chatgpt.com)

Kafka originated at LinkedIn, so prioritize Kafka-related material.

### Uber

Uber currently has a large engineering archive and recent Kafka/data articles, including its 2026 work around uForwarder and Kafka async queuing. ([Uber][5])

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

---

## Implementation

Study:

```text
Partition
Leader
ISR
Controller
KRaft
Producer
Consumer
Consumer group
Offset
Rebalance
Transaction
Exactly once
```

---

## Hands-on

Build:

```text
Kafka
 ↓
Producer
 ↓
3 brokers
 ↓
3 partitions
 ↓
2 consumers
```

Experiment with:

* broker failure
* consumer failure
* partition failure
* duplicate messages
* rebalancing
* consumer lag

---

## Architecture exercise

### Design Walmart Event Platform

```text
100M events/sec
10PB/day
7-day retention
multi-region
99.99%
```

Calculate:

* partitions
* brokers
* disk
* network
* replication
* consumer throughput
* recovery

---

## Interview questions

1. How does Kafka achieve high throughput?
2. Partition vs topic?
3. What determines partition count?
4. What happens when a broker dies?
5. ISR?
6. Leader election?
7. How does consumer rebalancing work?
8. Exactly once?
9. How can duplicates happen?
10. How do you handle a hot partition?

### Staff question

> **A team asks for exactly-once semantics. What questions do you ask before agreeing to implement it?**

---

# MONTH 5 — Flink / Streaming

## Book

### ⭐⭐⭐ Streaming Systems

Read:

* event time
* processing time
* windows
* triggers
* watermarks
* state
* exactly-once

---

## Papers

### #21 — MillWheel

[Google MillWheel](https://research.google/pubs/millwheel-fault-tolerant-stream-processing-at-internet-scale/?utm_source=chatgpt.com)

### #22 — Dataflow Model

[Google Dataflow Model](https://research.google/pubs/the-dataflow-model-a-practical-approach-to-balancing-correctness-latency-and-cost-in-massive-scale-unbounded-out-of-order-data-processing/?utm_source=chatgpt.com)

### #23 — Beam model

[Apache Beam](https://beam.apache.org/documentation/programming-guide/?utm_source=chatgpt.com)

---

## FAANG/company engineering

### Uber

This is especially relevant now because Uber's current Hudi architecture uses both Spark batch ingestion and Flink streaming ingestion, including event-time ordering and watermarking. ([Uber][6])

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

### Google

[Google Research](https://research.google/?utm_source=chatgpt.com)

---

## Implementation docs

[Flink State](https://nightlies.apache.org/flink/flink-docs-stable/docs/concepts/stateful-stream-processing/?utm_source=chatgpt.com)

[Flink Checkpointing](https://nightlies.apache.org/flink/flink-docs-stable/docs/ops/state/checkpoints/?utm_source=chatgpt.com)

[Flink Time & Watermarks](https://nightlies.apache.org/flink/flink-docs-stable/docs/concepts/time/?utm_source=chatgpt.com)

---

## Hands-on

Build your project:

```text
Kafka
 ↓
Flink
 ↓
State
 ↓
Postgres
 ↓
Iceberg
 ↓
ClickHouse
```

Implement:

```text
event time
watermarks
windows
deduplication
checkpoint
restart
late events
```

---

## Architecture

### Design real-time order processing

```text
Kafka
 ↓
Flink
 ├── fraud
 ├── inventory
 ├── aggregation
 └── alerts
       ↓
   Iceberg
       ↓
 ClickHouse
```

---

## Interview

1. Event time vs processing time?
2. What is a watermark?
3. How does Flink recover state?
4. What is checkpointing?
5. Savepoint vs checkpoint?
6. Exactly-once?
7. How do you handle late events?
8. How does backpressure work?
9. How do you scale state?
10. How do you handle a hot key?

### Staff question

> **How would you guarantee data correctness when Kafka delivers duplicates and events arrive 30 minutes late?**

---

# MONTH 6 — Spark Internals

## Books

### ⭐⭐⭐ Learning Spark

### ⭐⭐⭐ High Performance Spark

---

## Papers

### #24 — RDD

[RDD paper](https://www.usenix.org/legacy/events/nsdi12/tech/full_papers/Zaharia_new.pdf?utm_source=chatgpt.com)

### #25 — Spark SQL

[Spark SQL paper](https://people.csail.mit.edu/matei/papers/2015/sigmod_spark_sql.pdf?utm_source=chatgpt.com)

---

## Engineering/blog

### Databricks

[Databricks Blog](https://www.databricks.com/blog?utm_source=chatgpt.com)

Prioritize:

* Spark performance
* Photon
* AQE
* Delta
* query optimization

### Uber

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

Search:

```text
Spark
data platform
Hudi
ETL
```

---

## Implementation

[Spark SQL performance tuning](https://spark.apache.org/docs/latest/sql-performance-tuning.html?utm_source=chatgpt.com)

Study:

```text
Catalyst
AQE
Broadcast join
Sort merge join
Shuffle hash join
Partitioning
Spill
Memory
```

---

## Hands-on

Take one of your real Spark jobs.

Produce:

```text
SQL
 ↓
Logical plan
 ↓
Catalyst
 ↓
Physical plan
 ↓
Stage
 ↓
Task
 ↓
Shuffle
 ↓
Executor
```

Then optimize it.

---

## Architecture

Design:

> **500GB → 2B rows/day Spark pipeline**

Calculate:

* executors
* cores
* memory
* partitions
* shuffle
* runtime
* cost

---

## Interview

1. Why does Spark shuffle?
2. What causes data skew?
3. What is a stage?
4. Stage vs task?
5. What is Catalyst?
6. What is AQE?
7. Broadcast join?
8. Why does Spark spill?
9. Why can adding executors make a job slower?
10. How do you optimize a 10TB Spark job?

---

# MONTH 7 — Iceberg / Hudi / Lakehouse

This month is **very high priority for your profile**.

## Books

### ⭐ Fundamentals of Data Engineering

### DDIA

Revisit storage architecture.

---

## Foundational reading

### #26 — Iceberg specification

[Apache Iceberg Specification](https://iceberg.apache.org/spec/?utm_source=chatgpt.com)

### #27 — Iceberg evolution

[Iceberg Evolution](https://iceberg.apache.org/docs/latest/evolution/?utm_source=chatgpt.com)

### #28 — Delta protocol

[Delta Lake Protocol](https://github.com/delta-io/delta/blob/master/PROTOCOL.md?utm_source=chatgpt.com)

---

## Company engineering

### Uber — Hudi

This is one of the **must-read articles of your entire curriculum**.

[Uber — Hudi at trillion-record scale](https://www.uber.com/us/en/blog/apache-hudi-at-uber/?utm_source=chatgpt.com)

Uber's January 2026 article describes a data lake with roughly 350 logical PB, 10 PB/day ingested, 350,000 commits/day, 70,000 table-service operations/day and tables exceeding 400B rows. ([Uber][6])

Then read:

[Uber — Transactional Data Lake with Hudi](https://www.uber.com/us/en/blog/ubers-lakehouse-architecture/?utm_source=chatgpt.com)

That architecture is especially relevant because it connects data freshness, incremental ETL, Spark, Hudi and a centralized data lake. ([Uber][7])

---

## Implementation

Study:

```text
Snapshot
Manifest
Manifest list
Metadata
Partition spec
Schema evolution
Compaction
Clustering
Deletes
Time travel
```

---

## Hands-on

Build:

```text
Kafka
 ↓
Flink
 ↓
Iceberg
 ↓
Spark
 ↓
Trino
```

Implement:

* INSERT
* UPDATE
* DELETE
* schema evolution
* partition evolution
* time travel

---

## Architecture

### Walmart archival system

Design:

```text
OLTP
 ↓
CDC
 ↓
Kafka
 ↓
Flink
 ↓
Iceberg
 ↓
GCS
```

Then:

```text
OLTP records
      ↓
Audit archive
      ↓
Purge
```

Define:

* retention
* legal hold
* recovery
* schema evolution
* audit
* replay

---

## Interview

1. Why Iceberg instead of Parquet?
2. What is a snapshot?
3. What is a manifest?
4. How does partition evolution work?
5. How does schema evolution work?
6. How do deletes work?
7. What causes small files?
8. Compaction vs clustering?
9. Iceberg vs Hudi?
10. Why does a data lake need transactions?

### Staff question

> **Why would you choose Iceberg over Hudi for Walmart's 100PB data platform?**

---

# MONTH 8 — ClickHouse / BigQuery / Trino

## Books

### DDIA

Analytical processing chapters.

### Database Internals

Query execution/storage concepts.

---

## Papers

### #29 — Dremel

[Google Dremel](https://research.google/pubs/dremel-interactive-analysis-of-web-scale-datasets-2/?utm_source=chatgpt.com)

### #30 — Presto

[Presto paper](https://trino.io/paper/?utm_source=chatgpt.com)

---

## Engineering

### ClickHouse

[ClickHouse](https://clickhouse.com/?utm_source=chatgpt.com)

Study:

* MergeTree
* distributed tables
* replication
* indexes
* compression

### Google

[Google Cloud BigQuery](https://cloud.google.com/blog/products/data-analytics?utm_source=chatgpt.com)

### Uber

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

Focus on Presto/data-platform articles.

---

## Implementation

[ClickHouse MergeTree](https://clickhouse.com/docs/engines/table-engines/mergetree-family/mergetree?utm_source=chatgpt.com)

[ClickHouse data skipping indexes](https://clickhouse.com/docs/optimize/skipping-indexes?utm_source=chatgpt.com)

[ClickHouse query optimization](https://clickhouse.com/docs/optimize/query-optimization?utm_source=chatgpt.com)

---

## Hands-on

Create:

```text
100M rows
1B rows
5B rows
```

Compare:

```text
ClickHouse
BigQuery
Postgres
DuckDB
```

Measure:

```text
Insert throughput
Query latency
Storage
Compression
Cost
```

---

## Architecture

Design your:

> **4B-row forecast serving system**

with:

```text
Kafka
 ↓
Spark/Flink
 ↓
ClickHouse
 ↓
Redis
 ↓
REST
```

---

## Interview

1. Why is ClickHouse fast?
2. What is MergeTree?
3. What is a part?
4. Why are merges required?
5. How does ClickHouse index data?
6. Primary key vs traditional B-tree?
7. What is a granule?
8. How does replication work?
9. How do you shard ClickHouse?
10. When should you NOT use ClickHouse?

### Staff question

> **Why would you put ClickHouse behind an API instead of directly querying BigQuery?**

---
