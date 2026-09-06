Top articles to be read yearly
- https://mvineetsharma.medium.com/11-kafka-design-patterns-for-every-backend-engineer-f053bdadd99e




kafka lags in consumer  and backpressure management
grafana monitoring 
redis cache
mongodb  
prometheus 
airflow pipeline 
gcloud cost graphs in grafana
zookeeper and temporal cluster 
beam , truino , flink 
duckdb ,  rockdb , iceberg 
parquet and avro
best book for flink, beam, spark , kafka, iceburge 



Designing Data-Intensive Applications — distributed systems/data foundations
Fundamentals of Software Architecture — architecture thinking
Release It! — production reliability and failure modes
Building Microservices — practical distributed architecture
Site Reliability Engineering — reliability at scale
Database Internals — understand storage engines deeply
Designing Distributed Systems — system patterns



| Technology               | Best primary resource                                                   | Internals level | My rating for you |
| ------------------------ | ----------------------------------------------------------------------- | --------------: | ----------------: |
| **Kafka**                | **Kafka: The Definitive Guide, 2nd Ed.**                                |           ⭐⭐⭐⭐⭐ |             10/10 |
| **Flink**                | **Stream Processing with Apache Flink**                                 |           ⭐⭐⭐⭐⭐ |             10/10 |
| **Spark**                | **Learning Spark + Spark papers/source**                                |            ⭐⭐⭐⭐ |              9/10 |
| **Beam**                 | **Building Big Data Pipelines with Apache Beam + Beam execution model** |            ⭐⭐⭐⭐ |            8.5/10 |
| **Iceberg**              | **Official Iceberg Spec + Architecture docs**                           |           ⭐⭐⭐⭐⭐ |             10/10 |
| **All systems together** | **Designing Data-Intensive Applications**                               |           ⭐⭐⭐⭐⭐ |             10/10 |



7. Saga + distributed transactions ⭐⭐⭐⭐⭐

Saga
Compensation
Outbox pattern
Inbox pattern
Idempotency
Retry
Deduplication
Reconciliation
Event replay


2. Distributed systems fundamentals ⭐⭐⭐⭐⭐

Network failures
Partial failures
Retries
Timeouts
Idempotency
Ordering
Duplicates
At-least-once delivery
At-most-once delivery
Exactly-once semantics
Consistency
Availability
Partition tolerance
Leader election
Replication
Quorum
Consensus
Backpressure
Load shedding
Circuit breakers
Bulkheads
Failure recovery

6. Event-driven architecture beyond Kafka ⭐⭐⭐⭐⭐
Event
Command
Message
Notification
Event sourcing
CQRS
Choreography
Orchestration
Pub/sub
Event streaming
Eventual consistency
Replay
Dead letter queues
Schema evolution
Consumer compatibility





Yes. This is the version I would actually follow if the goal is **Senior → FAANG Staff Engineer**, especially with your background in Kafka, Spark, Flink, ClickHouse, BigQuery, GCP/AWS, and large-scale data platforms.

The key change is that **every topic will follow the same learning loop**:

> **1. Book → 2. Foundational paper → 3. FAANG engineering article → 4. Implementation/docs → 5. Hands-on exercise → 6. Architecture exercise → 7. Staff-level interview questions**

This prevents you from becoming someone who has merely "read 100 papers." The goal is to develop **architecture judgment**.

---

# 🏆 12-Month FAANG Staff Engineer Curriculum

## The year at a glance

| Month | Topic                                   | Primary systems                            |
| ----- | --------------------------------------- | ------------------------------------------ |
| 1     | Distributed Systems                     | GFS, Bigtable, Dynamo, Spanner, Raft       |
| 2     | Database Internals                      | PostgreSQL, RocksDB, Cassandra, LSM        |
| 3     | Distributed Databases                   | Spanner, F1, TAO, caching                  |
| 4     | Kafka & Event-Driven Systems            | Kafka, KRaft, transactions                 |
| 5     | Streaming Systems                       | Flink, Beam, MillWheel                     |
| 6     | Spark Internals                         | Catalyst, AQE, shuffle, memory             |
| 7     | Lakehouse                               | Iceberg, Hudi, Delta                       |
| 8     | Analytical Databases                    | ClickHouse, BigQuery, Trino                |
| 9     | SRE & Reliability                       | SLO, p99, tracing, failure                 |
| 10    | Large-Scale Architecture                | Uber, Amazon, Google, Netflix, Meta        |
| 11    | Platform Engineering                    | Internal platforms, developer productivity |
| 12    | Staff Engineer / Architecture Synthesis | Strategy, influence, architecture          |

---

# MONTH 1 — Distributed Systems

### Goal

You should be able to look at any distributed architecture and immediately ask:

```text
What is partitioned?
What is replicated?
What happens when a node dies?
What consistency do we need?
Who is the leader?
How is consensus achieved?
What happens during network partition?
How do we recover?
```

---

## Reading sequence

### 1. Book

**Designing Data-Intensive Applications — Martin Kleppmann**

Read:

* Ch 1 — Reliable, Scalable and Maintainable Applications
* Ch 5 — Replication
* Ch 6 — Partitioning
* Ch 8 — Distributed Systems
* Ch 9 — Consistency and Consensus

This is your **book of the year**. Keep coming back to it.

---

### 2. Foundational papers

#### #1 — Google File System

[Google — The Google File System](https://research.google/pubs/the-google-file-system/?utm_source=chatgpt.com)

Focus:

* chunk servers
* master
* replication
* failure
* heartbeats

#### #2 — MapReduce

[Google — MapReduce](https://research.google/pubs/mapreduce-simplified-data-processing-on-large-clusters/?utm_source=chatgpt.com)

Focus:

* partition
* shuffle
* worker failure
* scheduling

#### #3 — Bigtable

[Google — Bigtable](https://research.google/pubs/bigtable-a-distributed-storage-system-for-structured-data/?utm_source=chatgpt.com)

Bigtable is especially important because it connects distributed storage, tablets, SSTables, partitioning and high-scale serving. ([Google Research][1])

#### #4 — Dynamo

[Amazon Dynamo paper/article](https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html?utm_source=chatgpt.com)

Learn:

```text
Consistent hashing
Quorum
Vector clocks
Eventual consistency
Hinted handoff
Read repair
```

#### #5 — Spanner

[Google Spanner](https://research.google/pubs/spanner-googles-globally-distributed-database-2/?utm_source=chatgpt.com)

#### #6 — Raft

[Raft paper](https://raft.github.io/raft.pdf?utm_source=chatgpt.com)

Read this **very carefully**.

You should be able to draw:

```text
Leader
   ↓
Log replication
   ↓
Followers
   ↓
Commit index
```

---

## 3. FAANG engineering articles

### Amazon

[Amazon Builders' Library](https://aws.amazon.com/builders-library/?utm_source=chatgpt.com)

Start with:

* Challenges with Distributed Systems
* Timeouts, retries and backoff
* Avoiding fallback
* Static stability

Amazon describes the Builders' Library as articles written by senior technical leaders about how Amazon architects, releases and operates systems. ([Amazon Web Services, Inc.][2])

### Google

[Google Research](https://research.google/?utm_source=chatgpt.com)

Read distributed-systems papers from the Systems & Networking area.

### Meta

[Meta Engineering — TAO](https://engineering.fb.com/2013/06/25/core-infra/tao-the-power-of-the-graph/?utm_source=chatgpt.com)

TAO is excellent for learning sharding + caching + persistent storage. Meta describes TAO as geographically distributed clusters with separate persistent and caching layers and hundreds of thousands of shards. ([Engineering at Meta][3])

---

## 4. Implementation

Study:

[etcd architecture](https://etcd.io/docs/?utm_source=chatgpt.com)

[Raft implementation](https://raft.github.io/?utm_source=chatgpt.com)

---

## 5. Hands-on exercise

Build a **mini Raft implementation**.

Don't build a production implementation.

Implement:

```text
3 nodes

Leader election
Heartbeat
Log replication
Leader failure
New leader
Commit index
```

---

## 6. Architecture exercise

### Design: Global Inventory Service

Requirements:

```text
10B records
100K writes/sec
1M reads/sec
<20ms p99
99.99% availability
multi-region
```

You must choose:

```text
Partition key
Replication
Consistency
Database
Cache
API
Failure strategy
```

---

## 7. Interview questions

You should be able to answer these without notes:

1. Explain CAP using a real production example.
2. Strong vs eventual consistency?
3. Why does Dynamo use quorum?
4. How does Raft elect a leader?
5. What happens when the leader crashes?
6. Why is consensus difficult?
7. How does Bigtable scale?
8. Why is consistent hashing useful?
9. How do you handle hot partitions?
10. Design a globally distributed inventory system.

### Staff-level question

> **Why did you choose this consistency model, and what business requirement justified the additional operational complexity?**

---

# MONTH 2 — Database Internals

## Goal

Move from:

> "I know Cassandra/ClickHouse/Postgres."

to:

> "I understand why their storage engines behave differently."

---

## 1. Books

### ⭐ Database Internals — Alex Petrov

Read:

* B-Trees
* LSM Trees
* WAL
* SSTables
* compaction
* distributed storage

### DDIA

Revisit:

* storage engines
* encoding
* replication

---

# 2. Foundational papers

### #7 — LSM Tree

[The Log-Structured Merge-Tree paper](https://www.cs.umb.edu/~poneil/lsmtree.pdf?utm_source=chatgpt.com)

### #8 — Bigtable

Re-read relevant storage sections.

### #9 — RocksDB

Study the architecture.

---

# 3. FAANG blogs

### Meta — RocksDB

[Meta — Under the Hood: Building RocksDB](https://engineering.fb.com/2013/11/21/core-infra/under-the-hood-building-and-open-sourcing-rocksdb/?utm_source=chatgpt.com)

This is especially relevant to you because RocksDB demonstrates how an embedded LSM-based engine can provide high-throughput local storage; Meta describes it as an embeddable persistent key-value store built for fast storage. ([Engineering at Meta][4])

### Amazon

[Amazon Builders' Library](https://aws.amazon.com/builders-library/?utm_source=chatgpt.com)

Study database/storage reliability articles.

---

# 4. Implementation docs

Study:

[RocksDB documentation](https://github.com/facebook/rocksdb/wiki?utm_source=chatgpt.com)

[PostgreSQL Internals](https://www.postgresql.org/docs/current/internals.html?utm_source=chatgpt.com)

[Apache Cassandra Architecture](https://cassandra.apache.org/doc/latest/cassandra/architecture/overview.html?utm_source=chatgpt.com)

---

# 5. Hands-on

Build:

```text
Mini LSM database

PUT
GET
DELETE

Memtable
 ↓
WAL
 ↓
SSTable
 ↓
Compaction
```

Then benchmark:

```text
100K writes
1M reads
different key distributions
```

---

# 6. Architecture exercise

Design:

> **4B-row serving database**

Compare:

```text
Postgres
Cassandra
RocksDB
ClickHouse
BigQuery
```

Create a decision matrix.

---

# 7. Interview questions

1. B-tree vs LSM?
2. Why are LSM writes fast?
3. Why does compaction matter?
4. What is write amplification?
5. Read amplification?
6. Space amplification?
7. WAL vs SSTable?
8. What causes write stalls?
9. How would you tune RocksDB?
10. Why isn't Cassandra a replacement for ClickHouse?

### Staff question

> **Your system needs 4B-row ingestion and 10ms reads. Why shouldn't you simply choose the database with the highest benchmark throughput?**

---

# MONTH 3 — Distributed Databases & Caching

## Books

### ⭐ DDIA

Focus:

* transactions
* distributed transactions
* consensus

### ⭐ Database Internals

Distributed storage chapters.

---

## Foundational papers

### #11 — Spanner

[Google Spanner](https://research.google/pubs/spanner-googles-globally-distributed-database-2/?utm_source=chatgpt.com)

### #12 — F1

[Google F1](https://research.google/pubs/f1-the-resilient-database-for-the-google-advertising-business/?utm_source=chatgpt.com)

### #13 — Percolator

[Google Percolator](https://research.google/pubs/large-scale-incremental-processing-using-distributed-transactions/?utm_source=chatgpt.com)

### #14 — TAO

[Meta TAO](https://engineering.fb.com/2013/06/25/core-infra/tao-the-power-of-the-graph/?utm_source=chatgpt.com)

### #15 — Scaling Memcache

Study Meta's caching architecture.

---

## FAANG blogs

### Meta

[Meta Engineering](https://engineering.fb.com/?utm_source=chatgpt.com)

Focus on:

```text
TAO
CacheLib
Memcache
RocksDB
Storage
```

### Amazon

[Amazon Builders' Library](https://aws.amazon.com/builders-library/?utm_source=chatgpt.com)

Focus on:

```text
distributed transactions
consistency
caching
load isolation
```

---

## Implementation

Study:

```text
Redis
Memcached
Spanner
CockroachDB
```

---

## Hands-on

Build:

```text
API
 ↓
Redis
 ↓
Postgres
```

Implement:

* cache-aside
* TTL
* invalidation
* stampede protection
* negative caching

---

## Architecture

### Design Facebook TAO-like system

```text
Client
 ↓
Cache
 ↓
Service
 ↓
Database
```

Support:

```text
10M QPS
multi-region
cache failures
database failures
hot keys
```

---

## Interview questions

1. Cache-aside vs write-through?
2. How do you prevent cache stampede?
3. How do you handle hot keys?
4. What happens when Redis fails?
5. Why is distributed caching difficult?
6. Strong consistency vs availability?
7. How do distributed transactions work?
8. Two-phase commit?
9. Saga?
10. How would you design a globally distributed metadata service?

---

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

# MONTH 9 — SRE / Reliability / Observability

## Books

### ⭐⭐⭐ Site Reliability Engineering

### ⭐⭐⭐ Site Reliability Workbook

### ⭐ Release It!

---

## Foundational papers

### #31 — Dapper

[Google Dapper](https://research.google/pubs/dapper-a-large-scale-distributed-systems-tracing-infrastructure/?utm_source=chatgpt.com)

Dapper is foundational for understanding tracing across large distributed systems; Google explicitly designed it for low overhead, transparent instrumentation and large-scale deployment. ([Google Research][8])

### #32 — The Tail at Scale

[Google — The Tail at Scale](https://research.google/pubs/the-tail-at-scale/?utm_source=chatgpt.com)

---

## Engineering

### Amazon

[Amazon Builders' Library](https://aws.amazon.com/builders-library/?utm_source=chatgpt.com)

Read:

* Challenges with Distributed Systems
* Avoiding Insurmountable Queue Backlogs
* Timeouts, Retries and Backoff
* Static Stability

### Netflix

[Netflix TechBlog](https://netflixtechblog.com/?utm_source=chatgpt.com)

Focus:

```text
Chaos Engineering
resilience
failure
observability
```

---

## Implementation

Study:

```text
OpenTelemetry
Prometheus
Grafana
Jaeger
```

---

## Hands-on

Create observability for your Flink/Kafka system:

```text
Kafka
 ├── throughput
 ├── lag
 └── errors

Flink
 ├── checkpoint duration
 ├── backpressure
 └── state size

ClickHouse
 ├── query latency
 ├── merges
 └── parts

API
 ├── p50
 ├── p95
 ├── p99
 └── errors
```

---

## Architecture

Define SLOs:

```text
API availability     99.99%
API p99               <50ms
Kafka lag              <30 sec
Data freshness         <5 min
Pipeline success       99.9%
Recovery               <30 min
```

---

## Interview

1. SLA vs SLO vs SLI?
2. Why p99?
3. What is tail latency?
4. How do retries cause outages?
5. What is retry storm?
6. Circuit breaker?
7. Backpressure?
8. Cascading failure?
9. How do you design for AZ failure?
10. How do you debug a distributed latency problem?

### Staff question

> **Your service has 99.9% average latency but terrible p99. What would you investigate?**

---

# MONTH 10 — FAANG-Scale Architecture

This is your **case-study month**.

Instead of learning another technology, study how large companies combine technologies.

---

## Books

### ⭐ Software Architecture: The Hard Parts

### ⭐ Building Microservices — 2nd Edition

### ⭐ Fundamentals of Software Architecture

---

# Case study 1 — Uber

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

Prioritize:

* data platforms
* Kafka
* Hudi
* databases
* rate limiting
* observability

Uber's current engineering archive is especially valuable because it continues publishing large-scale infrastructure work; recent examples include data replication, Hudi at trillion-record scale, database overload management, observability, and Kafka-related infrastructure. ([Uber][5])

---

# Case study 2 — Amazon

[Amazon Builders' Library](https://aws.amazon.com/builders-library/?utm_source=chatgpt.com)

Study:

```text
Retries
Timeouts
Backpressure
Queues
Shuffle sharding
Static stability
Failure isolation
```

---

# Case study 3 — Google

[Google Research](https://research.google/?utm_source=chatgpt.com)

Study:

```text
Spanner
Bigtable
Dapper
Borg
MapReduce
```

---

# Case study 4 — Meta

[Meta Engineering](https://engineering.fb.com/?utm_source=chatgpt.com)

Study:

```text
TAO
RocksDB
CacheLib
Memcache
```

---

# Case study 5 — Netflix

[Netflix TechBlog](https://netflixtechblog.com/?utm_source=chatgpt.com)

Study:

```text
microservices
resilience
platform engineering
chaos
deployment
```

---

## Architecture exercise

### Design Walmart Global Forecast Platform

```text
                       ┌── Kafka
                       │
Forecast Events ───────┼── Flink
                       │
                       └── Spark
                             │
                             ↓
                       Iceberg/GCS
                             │
                    ┌────────┴────────┐
                    ↓                 ↓
                BigQuery          ClickHouse
                                      │
                                    Redis
                                      │
                                     API
                                      │
                                     OMS
```

Now calculate:

```text
Throughput
Storage
Network
Latency
Cost
Availability
Recovery
```

---

# MONTH 11 — Platform Engineering + Staff Thinking

Now stop thinking only about individual systems.

Start thinking:

> **How do I create leverage for 100 engineering teams?**

---

## Books

### ⭐⭐⭐ Staff Engineer — Will Larson

### ⭐⭐⭐ An Elegant Puzzle

### ⭐⭐ The Manager's Path

### ⭐ Accelerate

---

## Company reading

### Google

[Google Engineering Practices](https://google.github.io/eng-practices/?utm_source=chatgpt.com)

Study:

```text
Design reviews
Code reviews
Engineering standards
Technical decision making
```

### Netflix

[Netflix TechBlog](https://netflixtechblog.com/?utm_source=chatgpt.com)

Search:

```text
platform
developer platform
developer productivity
infrastructure
```

### Uber

[Uber Engineering](https://www.uber.com/blog/engineering/?utm_source=chatgpt.com)

Study platform engineering.

---

## Hands-on

Build a conceptual:

# Internal Data Platform

```text
                    Data Platform
                         │
       ┌─────────────────┼─────────────────┐
       ↓                 ↓                 ↓
     Kafka             Flink             Spark
       │                 │                 │
       └─────────────────┼─────────────────┘
                         ↓
                      Iceberg
                         │
               ┌─────────┴────────┐
               ↓                  ↓
           BigQuery          ClickHouse
```

Then add:

```text
Self-service
Observability
Governance
Cost management
Data quality
Schema registry
Security
```

---

## Staff exercise

Write a 5-page proposal:

> **"Standardizing Walmart's streaming/data platform across 100 teams."**

Include:

```text
Problem
Current state
Business impact
Goals
Non-goals
Architecture
Migration
Adoption
Governance
Cost
Risks
Success metrics
```

---

## Interview questions

1. How do you influence without authority?
2. How do you resolve architecture disagreements?
3. When should you standardize?
4. When should teams have autonomy?
5. How do you migrate 100 services?
6. How do you create an internal platform?
7. How do you measure platform success?
8. How do you prevent platform teams becoming bottlenecks?

### Staff question

> **You have three teams with three different solutions. Do you standardize them or let them remain independent? Why?**

---

# MONTH 12 — Staff Engineer Synthesis

This month is different.

**No new technology.**

You now take everything you've learned and turn it into Staff-level architecture work.

---

# Week 1 — Distributed system design

Design:

### 1. Global inventory

```text
10B records
1M reads/sec
100K writes/sec
```

### 2. Global order system

```text
100K orders/sec
multi-region
exactly-once business effects
```

### 3. Distributed ID system

```text
1M IDs/sec
multi-region
no collisions
```

---

# Week 2 — Data platform

Design:

### 4. Walmart Forecast Platform

```text
Kafka
Flink
Spark
Iceberg 
parquet
ClickHouse
BigQuery
Airflow
Jupyter Notebook
python
```

### 5. 100PB Data Lake

Requirements:

```text
schema evolution
updates
deletes
backfills
time travel
data quality
governance
```

### 6. Database purge/archive

This one should be directly relevant to your current architecture work:

```text
OLTP
 ↓
CDC
 ↓
Kafka
 ↓
Iceberg
 ↓
GCS
 ↓
Retention
 ↓
Purge
```

---

# Week 3 — Reliability

Design:

### 7. Multi-region Kafka

### 8. Multi-region ClickHouse

### 9. Flink disaster recovery

### 10. Global API platform

For every system:

```text
Failure
Detection
Recovery
Data loss
RPO
RTO
SLO
Cost
```

---

# Week 4 — Staff architecture presentation

Pick your strongest system.

Prepare a **45-minute Staff-level architecture presentation**.

I'd recommend:

# "Designing a Walmart-Scale Unified Real-Time + Batch Data Platform"

Structure:

```text
1. Business problem
2. Current architecture
3. Scale
4. Constraints
5. Current bottlenecks
6. Requirements
7. Proposed architecture
8. Data flow
9. Storage
10. Streaming
11. Batch
12. Query layer
13. Reliability
14. Observability
15. Security
16. Cost
17. Migration
18. Alternatives
19. Trade-offs
20. Risks
21. Rollback
22. Success metrics
```

Then have someone challenge you.

Your job isn't to defend the architecture blindly.

Your job is to say:

> "Given constraint X, I would choose A. If constraint Y changes, I would choose B."

**That's Staff-level architecture thinking.**

---

# 🎯 Your Staff Interview Question Bank

By the end of the year, you should be able to answer these.

## Distributed systems

1. Design a distributed key-value store.
2. Design Dynamo.
3. Design Spanner.
4. Design distributed locking.
5. Design leader election.
6. Explain Raft.
7. Explain quorum.
8. Explain CAP.
9. Strong vs eventual consistency.
10. Design multi-region storage.

---

## Databases

11. B-tree vs LSM.
12. Explain WAL.
13. Explain compaction.
14. Explain write amplification.
15. Design Cassandra.
16. Design ClickHouse.
17. Design Postgres at scale.
18. Design a distributed cache.
19. Handle hot keys.
20. Handle hot partitions.

---

## Kafka

21. Design Kafka.
22. How does replication work?
23. What is ISR?
24. What happens when a broker dies?
25. How does KRaft work?
26. Exactly once?
27. Consumer rebalancing?
28. Partition strategy?
29. Kafka ordering guarantees?
30. How would you operate Kafka at 1PB/day?

---

## Flink

31. Event time?
32. Watermarks?
33. Checkpoints?
34. Savepoints?
35. Exactly once?
36. State management?
37. Backpressure?
38. Late events?
39. Hot keys?
40. State recovery?

---

## Spark

41. RDD?
42. Catalyst?
43. AQE?
44. Shuffle?
45. Stage?
46. Task?
47. Executor?
48. Data skew?
49. Memory spill?
50. Optimize a 10TB job.

---

## Lakehouse

51. Why Iceberg?
52. Iceberg vs Hudi?
53. Snapshot?
54. Manifest?
55. Partition evolution?
56. Schema evolution?
57. Deletes?
58. Compaction?
59. Small files?
60. Data lake transactions?

---

## SRE

61. SLA/SLO/SLI?
62. p99?
63. Tail latency?
64. Retry storms?
65. Cascading failures?
66. Backpressure?
67. Circuit breaker?
68. Load shedding?
69. Disaster recovery?
70. Multi-region failover?

---

# 🧠 The 30 Staff-Level Questions

These are more important than the 70 technology questions.

You should practice these repeatedly.

### Architecture

1. What is the simplest architecture that solves this problem?
2. What are the actual constraints?
3. What assumptions are you making?
4. What happens at 10× scale?
5. What is the bottleneck?
6. What happens when the database is unavailable?
7. What happens when Kafka is unavailable?
8. What happens when the network partitions?
9. Where is state stored?
10. Who owns that state?

### Trade-offs

11. Why this database?
12. Why not the simpler solution?
13. What are you sacrificing?
14. What happens if your assumption is wrong?
15. What is the operational cost?
16. What is the financial cost?
17. What is the migration cost?
18. What is the organizational cost?
19. What is the failure mode?
20. What is the rollback strategy?

### Staff leadership

21. Who needs to agree?
22. Who doesn't need to agree?
23. Who owns the decision?
24. How will you get adoption?
25. How do you measure success?
26. How do you prevent platform lock-in?
27. How do you handle disagreement?
28. What should become a platform?
29. What should remain team-owned?
30. What should you deliberately **not build**?

---

# 📓 Your weekly execution template

I strongly recommend making this your **Staff Engineer notebook template**.

For every topic:

```text
# Topic

## 1. Problem

What problem does this technology solve?

## 2. Scale

What scale was it designed for?

## 3. Book notes

5–10 important concepts.

## 4. Foundational paper

What was the original insight?

## 5. FAANG implementation

How did Google/Amazon/Meta/Uber/etc. solve it?

## 6. Implementation

How does the technology actually work?

## 7. Failure modes

What breaks?

## 8. Performance

What is the bottleneck?

## 9. Cost

What costs money?

## 10. Trade-offs

What does this architecture sacrifice?

## 11. Hands-on

What did I build?

## 12. Architecture

What would I design?

## 13. Interview questions

Can I answer them without notes?

## 14. Staff question

What architectural decision would I make differently?
```

---

# 🔥 The most important part for YOU

Given the systems you've been working on, I wouldn't spend equal time on everything.

Your priority should be:

```text
                    STAFF
                      │
              Architecture
                      │
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
 Distributed       Data          Reliability
 Systems          Platform          │
        │             │             │
        ↓             ↓             ↓
      Kafka        Iceberg        SRE
        │             │             │
        ↓             ↓             ↓
      Flink         Spark       Observability
        │             │
        └──────┬──────┘
               ↓
          ClickHouse
               │
               ↓
         API / Serving
```

And there is a very useful real-world case study sitting almost exactly at the intersection of your interests:

**Uber's current Hudi architecture.** Their 2026 engineering write-up describes Hudi operating across hundreds of petabytes, trillions of rows, Spark batch ingestion, Flink streaming ingestion, Presto/Spark query workloads, and extensive operational monitoring. ([Uber][6])

That is the kind of article you should not just read. You should **recreate the architecture on paper and then challenge every decision**.

---

# 🏆 Your ultimate goal after 12 months

Don't measure success as:

> ❌ "I read 100 papers."

Measure it as:

> ✅ "I can take a vague business problem, quantify its scale, identify constraints, choose an architecture, explain alternatives, reason about consistency and failure, estimate cost, define SLOs, create a migration plan, and convince multiple engineering teams to adopt it."

That is the transition from **Senior Engineer → Staff Engineer**.

And for your specific background, I would make **Kafka + Flink + Spark + Iceberg/Hudi + ClickHouse + distributed systems + SRE** your deepest technical specialization, while using **Software Architecture: The Hard Parts + Staff Engineer** to develop the architectural and organizational layer.

[1]: https://research.google/pubs/bigtable-a-distributed-storage-system-for-structured-data/?utm_source=chatgpt.com "Bigtable: A Distributed Storage System for Structured Data"
[2]: https://aws.amazon.com/builders-library/faqs/?utm_source=chatgpt.com "The Amazon Builders’ Library FAQs"
[3]: https://engineering.fb.com/2013/06/25/core-infra/tao-the-power-of-the-graph/?utm_source=chatgpt.com "TAO: The power of the graph - Engineering at Meta"
[4]: https://engineering.fb.com/2013/11/21/core-infra/under-the-hood-building-and-open-sourcing-rocksdb/?utm_source=chatgpt.com "Under the Hood: Building and open-sourcing RocksDB - Engineering at Meta"
[5]: https://www.uber.com/us/en/blog/engineering/?utm_source=chatgpt.com "Engineering | Uber Blog"
[6]: https://www.uber.com/us/en/blog/apache-hudi-at-uber/?utm_source=chatgpt.com "Apache Hudi™ at Uber: Engineering for Trillion-Record-Scale Data Lake Operations"
[7]: https://www.uber.com/us/en/blog/ubers-lakehouse-architecture/?utm_source=chatgpt.com "Setting Uber’s Transactional Data Lake in Motion with Incremental ETL Using Apache Hudi"
[8]: https://research.google/pubs/dapper-a-large-scale-distributed-systems-tracing-infrastructure/?utm_source=chatgpt.com "Dapper, a Large-Scale Distributed Systems Tracing Infrastructure"
