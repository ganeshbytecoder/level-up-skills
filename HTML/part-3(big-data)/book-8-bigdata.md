# 6-Month Big Data Mastery Curriculum

Given your current background—Java/Spring Boot, PySpark, Kafka, BigQuery, Dataproc, ClickHouse, and your recent focus on Flink/Iceberg—I would **not** build this as a beginner curriculum.

The goal should be:

> **In 6 months, become the engineer who can design, implement, optimize, debug, and explain a production-scale streaming + lakehouse + OLAP platform from first principles.**

Your target architecture by the end:

```text
                         ┌───────────────┐
                         │ Applications  │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │    Kafka      │
                         │ Event Backbone│
                         └───────┬───────┘
                                 │
                   ┌─────────────┴─────────────┐
                   ▼                           ▼
             ┌──────────┐                ┌──────────┐
             │  Flink   │                │  Spark   │
             │ Streaming│                │   Batch  │
             └────┬─────┘                └────┬─────┘
                  │                            │
                  └─────────────┬──────────────┘
                                ▼
                         ┌──────────────┐
                         │   Iceberg    │
                         │   Lakehouse  │
                         └──────┬───────┘
                                │
                  ┌─────────────┴────────────┐
                  ▼                          ▼
           ┌─────────────┐             ┌─────────────┐
           │   Trino/BQ  │             │ ClickHouse  │
           │ Analytics   │             │ Low latency │
           └─────────────┘             └─────────────┘
```

Kafka is particularly important because it is simultaneously a durable event store, pub/sub system, and stream-processing foundation. ([Kafka][1])

---

# First: Your Weekly Schedule

I recommend **12–15 hours/week**.

### Monday–Thursday

**1.5–2 hours/day**

* 45–60 min reading
* 45–60 min coding

### Friday

**2 hours**

* architecture
* internals
* debugging scenarios

### Saturday

**4–5 hours**

* project implementation

### Sunday

**2–3 hours**

* review
* write notes
* explain concepts without looking at notes
* interview/system-design questions

So approximately:

```text
Reading          30%
Coding           40%
Architecture     15%
Debugging        10%
Review            5%
```

**Do not spend 70% of your time reading.**

For Big Data, implementation + debugging is where the understanding becomes permanent.

---

# MONTH 1 — Distributed Systems + Kafka

## Objective

Before going deep into Flink and Spark, understand the distributed-systems concepts underneath them.

This month should make you comfortable with:

* partitioning
* replication
* ordering
* consistency
* availability
* fault tolerance
* leader election
* consensus
* distributed logs
* backpressure
* retries
* idempotency
* exactly-once vs at-least-once

---

# Week 1 — Distributed Systems Foundations

### Read

Your primary book:

**Designing Data-Intensive Applications — 2nd Edition**

The second edition was released in 2026 and is now the version I'd use going forward. ([O'Reilly Media][2])

Focus on:

* data systems architecture
* reliability
* scalability
* maintainability
* replication
* partitioning
* transactions
* distributed systems

DDIA is particularly valuable because it teaches **trade-offs**, rather than simply teaching a particular technology. ([Martin Kleppmann][3])

### Understand deeply

```text
Vertical scaling
      vs
Horizontal scaling

Replication
      vs
Partitioning

Strong consistency
      vs
Eventual consistency

Availability
      vs
Consistency

Latency
      vs
Throughput
```

### Build

Write a small Java application that simulates:

```text
Producer
   ↓
Partition 0
Partition 1
Partition 2
   ↓
Consumers
```

Implement:

* hashing
* partition assignment
* consumer assignment
* retry
* duplicate processing

---

# Week 2 — Kafka Fundamentals

### Read

Official Kafka documentation first.

[Apache Kafka Documentation](https://kafka.apache.org/documentation/?utm_source=chatgpt.com)

Kafka's current documentation covers producers, consumers, topics, partitions, replication, Kafka Connect, Kafka Streams and event-processing architecture. ([Kafka][1])

### Learn

```text
Broker
Topic
Partition
Offset
Producer
Consumer
Consumer Group
Leader
Follower
ISR
Replication Factor
Controller
```

Then:

```text
Producer
   ↓
Topic
   ↓
Partition
   ↓
Consumer Group
   ↓
Consumer
```

### Critical questions

You should be able to answer:

**Why can't consumer parallelism exceed partition count?**

**Why does Kafka provide ordering only within a partition?**

**What happens when a consumer dies?**

**What happens when a broker dies?**

**How does Kafka achieve durability?**

**What is ISR?**

**Why does partitioning determine scalability?**

Kafka's partition model provides ordering within each partition while allowing parallel consumption across partitions. ([Kafka][4])

---

# Week 3 — Kafka Internals

Now go much deeper.

Study:

### Producer

* batching
* compression
* `acks`
* retries
* idempotent producer
* `linger.ms`
* `batch.size`
* delivery semantics

### Consumer

* polling
* offsets
* commits
* rebalance
* cooperative rebalancing
* consumer lag
* partition assignment

### Broker

* log segments
* page cache
* retention
* replication
* leader/follower
* ISR
* controller

### Important

Understand:

```text
Producer
   ↓
Memory buffer
   ↓
Batch
   ↓
Broker
   ↓
Log segment
   ↓
Replication
   ↓
Consumer
   ↓
Offset commit
```

---

# Week 4 — Kafka Production

Learn:

* Schema Registry
* Avro
* Protobuf
* schema evolution
* backward compatibility
* Kafka Connect
* Debezium
* CDC
* DLQ
* retries
* exactly-once
* transactions
* monitoring
* lag

### Build Project #1

## CDC Pipeline

```text
PostgreSQL
    ↓
Debezium
    ↓
Kafka
    ↓
Flink
    ↓
Iceberg
```

Capture:

```text
INSERT
UPDATE
DELETE
```

Then replay events from Kafka.

### Month 1 milestone

You should be able to design:

> "Replicate 50 PostgreSQL tables into a lakehouse with <1 minute freshness without impacting PostgreSQL."

---

# MONTH 2 — Apache Flink

This is your **most important month**.

You have already been asking the right questions about:

* TaskManagers
* Task slots
* parallelism
* subtasks
* Kafka partitions
* checkpoints
* Kubernetes deployment

Now connect all of it.

Flink is fundamentally a stateful stream-processing engine, with event-time processing, state, checkpoints, savepoints and exactly-once capabilities. ([Apache Flink][5])

---

# Week 5 — Flink Architecture

Read:

[Apache Flink Documentation](https://flink.apache.org/documentation/?utm_source=chatgpt.com)

Learn:

```text
JobManager
TaskManager
Task Slot
Operator
Subtask
Parallelism
Execution Graph
Job Graph
Task
Operator Chain
Network Shuffle
```

Then deeply understand:

```text
Kafka partitions
       ↓
Flink source parallelism
       ↓
Subtasks
       ↓
Task slots
       ↓
TaskManagers
```

You should be able to calculate:

> 24 Kafka partitions + Flink parallelism 12 + 3 TaskManagers × 4 slots

and explain exactly how records flow.

---

# Week 6 — Flink Data Processing

Learn:

* DataStream API
* operators
* map
* filter
* flatMap
* keyBy
* reduce
* process functions
* windows
* timers
* event time
* processing time
* watermarks
* late events

The most important concept:

```text
Event Time
     ↓
Watermark
     ↓
Window
     ↓
State
     ↓
Result
```

Build:

### Project #2 — Real-Time Order Aggregator

```text
Kafka
 ↓
Flink
 ↓
keyBy(customer_id)
 ↓
5-minute tumbling window
 ↓
sum(order_amount)
 ↓
Kafka
```

Then intentionally send **late events**.

Understand what happens.

---

# Week 7 — Flink State + Checkpointing

This is where you become advanced.

Learn:

* keyed state
* operator state
* state backend
* RocksDB/ForSt concepts
* checkpoint
* savepoint
* checkpoint barrier
* aligned checkpoint
* unaligned checkpoint
* incremental checkpoint
* state redistribution
* recovery

Flink uses checkpoints to capture state and stream positions so an application can recover consistently after failure. ([Apache Nightlies][6])

Understand this:

```text
Kafka
   │
   │ records
   ▼
Flink
   │
   ├── State
   │
   └── Checkpoint
          ↓
        S3/GCS
```

Then kill a TaskManager.

Watch recovery.

---

# Week 8 — Flink Production

Learn:

* backpressure
* checkpoint tuning
* state growth
* rescaling
* savepoints
* exactly-once sinks
* Kafka source
* Iceberg sink
* JDBC sink
* Kubernetes
* Flink Kubernetes Operator

### Build

```text
Kafka
  ↓
Flink
  ↓
Stateful enrichment
  ↓
Iceberg
```

Deploy it on Kubernetes.

Your Java JAR should become:

```text
Docker image
      ↓
Kubernetes
      ↓
Flink JobManager
      ↓
TaskManagers
```

### Month 2 milestone

You should be able to answer:

> "A Flink job is falling behind, checkpoint duration increased from 30 sec to 8 min, and Kafka lag is growing. Diagnose it."

That is a much better test of Flink knowledge than knowing API syntax.

---

# MONTH 3 — Apache Spark

You already use PySpark, so we're going **past API knowledge**.

Your objective:

> Understand what Spark actually does internally.

Spark SQL uses structural information to optimize execution, and its performance tooling includes partitioning, join strategies, statistics and AQE. ([Apache Spark][7])

---

# Week 9 — Spark Architecture

Learn:

```text
Driver
Cluster Manager
Executor
Task
Stage
Job
DAG
Shuffle
Partition
```

Understand:

```text
Spark Application
       ↓
     Job
       ↓
     Stage
       ↓
     Task
       ↓
   Executor
```

Then understand:

```text
Narrow transformation
        vs
Wide transformation
```

Examples:

```text
map()
filter()
     ↓
Narrow

groupBy()
join()
distinct()
     ↓
Shuffle / Wide
```

---

# Week 10 — Spark SQL Internals

Study:

```text
SQL
 ↓
Parsed Logical Plan
 ↓
Analyzed Logical Plan
 ↓
Optimized Logical Plan
 ↓
Physical Plan
 ↓
Code Generation
 ↓
Tasks
```

Learn:

* Catalyst
* Tungsten
* whole-stage code generation
* columnar processing
* predicate pushdown
* projection pruning
* statistics

---

# Week 11 — Spark Performance

This is extremely important for your work.

Master:

* partition sizing
* shuffle
* broadcast join
* sort merge join
* shuffle hash join
* AQE
* skew
* spill
* executor memory
* GC
* caching
* file sizes

Spark's current tuning documentation specifically emphasizes partition tuning, statistics, join strategy, caching and Adaptive Query Execution. ([Apache Spark][7])

### Build performance experiments

Generate:

```text
1B rows
```

Test:

```text
Broadcast Join
vs
Sort Merge Join
```

Then create:

```text
99% of records → customer_id = 1
1% → other IDs
```

Watch the skew.

Fix it.

---

# Week 12 — Spark Production

Learn:

* cluster sizing
* executor sizing
* dynamic allocation
* shuffle service
* checkpointing
* fault tolerance
* speculative execution
* partition pruning
* file compaction
* Spark UI

### Month 3 project

Build:

```text
5–10 TB simulated dataset
        ↓
Spark
        ↓
Join
        ↓
Aggregation
        ↓
Iceberg
```

Then optimize it.

Your deliverable should be:

```text
Version 1: 90 min

Version 2: 40 min

Version 3: 15 min
```

And explain exactly **why**.

---

# MONTH 4 — Parquet + Iceberg + Lakehouse

This month connects Spark and Flink.

---

# Week 13 — Parquet

Master:

```text
Row
Column
Row Group
Page
Dictionary Encoding
Statistics
Compression
Predicate Pushdown
Projection Pushdown
```

Understand:

```text
CSV

vs

Avro

vs

Parquet
```

Your existing study material correctly frames Parquet as the analytical columnar format and Avro as particularly suitable for streaming/message serialization. 

### Experiment

Create:

```text
1 TB equivalent dataset
```

Compare:

```text
CSV
JSON
Avro
Parquet
```

Measure:

* storage size
* write speed
* read speed
* CPU
* predicate filtering

---

# Week 14 — Iceberg Internals

This is one of the most important weeks of the entire curriculum.

Learn:

```text
Catalog
 ↓
Table Metadata
 ↓
Snapshot
 ↓
Manifest List
 ↓
Manifest
 ↓
Parquet Files
```

Iceberg snapshots represent table state, while manifests track data files and their statistics; manifest lists help prune manifests during planning. ([Apache Iceberg][8])

Understand:

* snapshots
* manifests
* manifest lists
* metadata JSON
* data files
* partition specs
* sequence numbers
* schema IDs
* field IDs

---

# Week 15 — Iceberg Advanced

Master:

* hidden partitioning
* partition evolution
* schema evolution
* time travel
* rollback
* MERGE
* UPDATE
* DELETE
* equality deletes
* position deletes
* optimistic concurrency
* isolation

Iceberg's hidden partitioning separates the logical query from the physical partition layout, allowing partition schemes to evolve without requiring consumers to rewrite queries. ([Apache Iceberg][9])

---

# Week 16 — Iceberg Production

Master:

* small-file problem
* compaction
* snapshot expiration
* orphan files
* manifest rewriting
* file sizing
* partition strategy
* write distribution

Iceberg recommends regular snapshot expiration and provides maintenance operations for compaction and manifest rewriting. ([Apache Iceberg][10])

### Build Project #3

```text
Kafka
 ↓
Flink
 ↓
Iceberg
 ↓
Spark
 ↓
Analytics
```

Then:

```text
PostgreSQL
 ↓
CDC
 ↓
Kafka
 ↓
Flink
 ↓
Iceberg
```

Perform:

```text
INSERT
UPDATE
DELETE
MERGE
TIME TRAVEL
ROLLBACK
```

### Month 4 milestone

You should be able to explain:

> "How can Iceberg provide ACID semantics on object storage?"

without hand-waving.

---

# MONTH 5 — ClickHouse + Serving Architecture

Now we're moving from processing/storage into **high-performance serving**.

This is especially relevant to your ARS/forecasting use cases.

---

# Week 17 — ClickHouse Architecture

Master:

```text
MergeTree
Parts
Primary Key
Order By
Partition
Granules
Marks
Compression
Merges
```

ClickHouse MergeTree tables write immutable parts and merge them in the background; the primary key determines sort order and indexes granules rather than individual rows. ([GitHub][11])

Understand:

```text
INSERT
 ↓
Part
 ↓
Sorted by ORDER BY
 ↓
Background Merge
 ↓
Larger Part
```

---

# Week 18 — ClickHouse Query Performance

Learn:

* primary key design
* ORDER BY
* partitioning
* data skipping
* granules
* PREWHERE
* compression
* materialized views
* projections
* aggregation

Then answer:

> Why can ClickHouse read billions of rows quickly?

Not:

> "Because ClickHouse is fast."

You should explain the physical mechanisms.

---

# Week 19 — ClickHouse Distributed Systems

Learn:

```text
Shard
Replica
Distributed Table
ReplicatedMergeTree
ZooKeeper / Keeper
Replication
Consistency
Failover
```

Understand:

```text
Client
  ↓
Distributed Table
  ↓
Shard 1       Shard 2       Shard 3
 ↓              ↓             ↓
Replica A     Replica A     Replica A
Replica B     Replica B     Replica B
```

---

# Week 20 — ClickHouse + Lakehouse

Now compare:

```text
BigQuery
Spark
Trino
ClickHouse
Iceberg
PostgreSQL
```

For each, answer:

| Requirement           | Best candidate |
| --------------------- | -------------- |
| OLTP                  | PostgreSQL     |
| Huge batch processing | Spark          |
| Streaming computation | Flink          |
| Lakehouse storage     | Iceberg        |
| Interactive OLAP      | ClickHouse     |
| Serverless analytics  | BigQuery       |
| Federated SQL         | Trino          |

But **never memorize this table**.

Learn the underlying trade-offs.

### Build Project #4

```text
Kafka
 ↓
Flink
 ├──────────────→ ClickHouse
 │
 └──────────────→ Iceberg
                       ↓
                     Spark
```

Then compare:

```text
ClickHouse query
vs
Iceberg + Spark
vs
BigQuery
```

for:

* latency
* throughput
* cost
* freshness
* concurrency

---

# MONTH 6 — Architecture + Production Mastery

This is where everything comes together.

---

# Week 21 — Data Architecture

Master:

### Lambda

```text
Batch Layer
+
Streaming Layer
+
Serving Layer
```

### Kappa

```text
Kafka
  ↓
Streaming computation
  ↓
Serving
```

Your existing material already highlights the major Kappa advantage: one streaming computation path with replay from Kafka, at the cost of potentially expensive large replays. 

Learn when each is appropriate.

---

# Week 22 — CDC + Lakehouse Architecture

Design:

```text
OLTP
 │
 └── PostgreSQL
        ↓
     Debezium
        ↓
      Kafka
        ↓
      Flink
        ↓
     Iceberg
        ↓
 ┌──────┴─────────┐
 ↓                ↓
Spark           Trino
 ↓
ML / Analytics
```

Then add:

```text
ClickHouse
```

for low-latency serving.

---

# Week 23 — Kubernetes + Production

You specifically asked previously how your Flink JARs become Kubernetes workloads.

Master:

```text
Docker
 ↓
Kubernetes
 ↓
Flink Kubernetes Operator
 ↓
JobManager
 ↓
TaskManagers
```

Learn:

* containerization
* CPU/memory
* requests/limits
* autoscaling
* health checks
* secrets
* config
* persistent storage
* networking
* monitoring
* deployment strategies

Then learn:

### Observability

```text
Metrics
Logs
Traces
Alerts
Dashboards
```

Monitor:

Kafka:

```text
Consumer Lag
Under Replicated Partitions
Throughput
Request Latency
```

Flink:

```text
Backpressure
Checkpoint Duration
Checkpoint Failures
Busy Time
Idle Time
Records/sec
State Size
```

Spark:

```text
Stage Duration
Shuffle Read
Shuffle Write
Spill
Task Skew
Executor GC
```

ClickHouse:

```text
Query Latency
Parts
Merges
Bytes Read
Rows Read
CPU
Memory
```

Iceberg:

```text
File Count
Average File Size
Snapshots
Manifest Count
Compaction
Metadata Growth
```

---

# Week 24 — Staff-Level System Design

This is your final exam.

You should be able to design all of these without looking anything up.

---

## Problem 1

### Real-time order platform

Requirements:

```text
10M events/hour
<5 sec processing latency
Exactly-once
7-day replay
5-year historical data
```

Design it.

---

## Problem 2

### CDC platform

```text
100 PostgreSQL tables
50 TB/day
<1 minute freshness
```

Design:

```text
Postgres
→ Debezium
→ Kafka
→ Flink
→ Iceberg
```

Explain:

* partitioning
* ordering
* schema evolution
* deletes
* retries
* exactly-once
* recovery

---

# Problem 3

### Walmart-scale forecasting

Given:

```text
4B records/day
500 GB–1 TB/day
```

Design:

```text
Kafka
 ↓
Flink
 ↓
Iceberg
 ↓
Spark
 ↓
ClickHouse
 ↓
REST API
```

Explain:

* partitioning
* parallelism
* capacity
* file size
* compaction
* storage
* query performance
* failure recovery
* cost

This one is particularly valuable for your current engineering domain.

---

# Problem 4 — The Big One

Design:

> "Build a platform that receives operational events through Kafka, processes them in real time with Flink, stores historical data in Iceberg, performs large-scale recomputation using Spark, and exposes low-latency analytical queries through ClickHouse."

You should be able to draw the entire architecture on a whiteboard.

---

# Your Reading List

Don't buy 30 books.

I would use this **core library**.

## 1. Distributed Systems

### MUST READ

**Designing Data-Intensive Applications — 2nd Edition**

This should be your foundational book. The new edition is 672 pages and covers modern cloud/data-system architecture as well as distributed systems fundamentals. ([O'Reilly Media][2])

Read it throughout Months 1–3.

---

# 2. Kafka

### MUST READ

**Kafka: The Definitive Guide**

Use it for:

* architecture
* producers
* consumers
* Kafka Connect
* Kafka Streams
* operations

Then use the official Kafka documentation as your reference source. ([Kafka][1])

---

# 3. Flink

For Flink, I would prioritize **official documentation + architecture papers/blogs + implementation** over a single book.

Start with:

[Apache Flink Documentation](https://flink.apache.org/documentation/?utm_source=chatgpt.com)

Especially:

* Stateful Stream Processing
* Checkpointing
* State
* Windows
* Event Time
* Deployment
* Kubernetes Operator

Flink's documentation explicitly covers state redistribution, checkpoints, savepoints and exactly-once semantics. ([Apache Nightlies][6])

Also read:

**Lightweight Asynchronous Snapshots for Distributed Dataflows**

This is worth understanding once you reach advanced checkpointing.

---

# 4. Spark

Use:

[Apache Spark SQL Documentation](https://spark.apache.org/docs/latest/sql-programming-guide.html?utm_source=chatgpt.com)

and:

[Spark Performance Tuning](https://spark.apache.org/docs/latest/sql-performance-tuning.html?utm_source=chatgpt.com)

The latter should become one of your frequently referenced documents because it covers partitioning, statistics, join strategies and AQE. ([Apache Spark][7])

---

# 5. Iceberg

Use the official docs as your primary reference:

[Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/?utm_source=chatgpt.com)

Read especially:

1. Concepts
2. Table format
3. Snapshots
4. Manifests
5. Partitioning
6. Schema evolution
7. Partition evolution
8. Transactions
9. Performance
10. Maintenance

The Iceberg performance documentation explains how manifest lists, manifests, partition data and column statistics reduce scan planning and file reads. ([Apache Iceberg][12])

---

# 6. ClickHouse

Use the official documentation heavily.

[ClickHouse Documentation](https://clickhouse.com/docs/?utm_source=chatgpt.com)

Start with:

* MergeTree
* primary keys
* ORDER BY
* partitions
* data skipping
* distributed tables
* replication
* materialized views
* projections
* query optimization

The MergeTree internals are especially important because its parts, sorting, granules and background merges explain much of ClickHouse's performance characteristics. ([GitHub][11])

---

# The Projects You Must Complete

Don't just read.

Complete these **five projects**.

### Project 1 — Kafka Event Platform

```text
Java Producer
       ↓
Kafka
       ↓
Java Consumer
       ↓
PostgreSQL
```

Features:

* schema registry
* retries
* DLQ
* idempotency
* consumer groups
* monitoring

---

### Project 2 — Real-Time Flink Platform

```text
Kafka
 ↓
Flink
 ↓
State
 ↓
Windows
 ↓
Kafka
```

Add:

* event time
* watermarks
* late events
* checkpoints
* savepoints
* backpressure

---

### Project 3 — Streaming Lakehouse

```text
Kafka
 ↓
Flink
 ↓
Iceberg
 ↓
Spark
```

Implement:

```text
INSERT
UPDATE
DELETE
MERGE
TIME TRAVEL
ROLLBACK
```

---

### Project 4 — Low-Latency Analytics

```text
Kafka
 ↓
Flink
 ↓
 ├── Iceberg
 └── ClickHouse
```

Compare query performance.

---

### Project 5 — Full Production Platform

```text
                  ┌─── ClickHouse
                  │
Kafka → Flink ────┼─── Iceberg
                  │      ↓
                  │    Spark
                  │
                  └─── Kafka
```

Deploy using:

```text
Docker
Kubernetes
Flink Kubernetes Operator
```

Add:

```text
Prometheus
Grafana
Logs
Alerts
```

---

# The 6-Month Timeline

Here is the version I would actually put on your calendar:

| Month | Main Focus                  | Deliverable                    |
| ----- | --------------------------- | ------------------------------ |
| **1** | Distributed Systems + Kafka | CDC → Kafka                    |
| **2** | Flink                       | Stateful streaming application |
| **3** | Spark                       | Multi-TB optimized pipeline    |
| **4** | Parquet + Iceberg           | Streaming lakehouse            |
| **5** | ClickHouse                  | Low-latency analytical serving |
| **6** | Architecture + K8s          | Production-grade platform      |

---

# Your Daily Study Method

This is important.

For every concept, use this sequence:

### 1. What?

Example:

> What is a Kafka partition?

### 2. Why?

> Why does Kafka need partitions?

### 3. How?

> How does partitioning enable parallelism?

### 4. Internals

> What happens inside the broker?

### 5. Failure

> What happens if the broker dies?

### 6. Performance

> What limits throughput?

### 7. Production

> How would I configure this at Walmart scale?

### 8. Trade-off

> What alternative could I use?

---

# The Standard I Want You to Reach

At the beginning:

> "Flink has checkpoints."

After 6 months:

> "Flink's checkpoint barriers flow through the operator graph and capture a consistent point across input streams and operator state. If the job fails, Flink restores operator state and source positions from the latest successful checkpoint and replays records from that point. Checkpoint interval trades runtime overhead against recovery/replay cost. Backpressure can delay barriers, so increasing checkpoint timeout without addressing the bottleneck may hide the actual problem."

That's the level you want.

Similarly, don't stop at:

> "Iceberg provides ACID."

You should be able to explain:

```text
Write data files
      ↓
Create manifest
      ↓
Create snapshot
      ↓
Update table metadata
      ↓
Atomic commit
      ↓
New snapshot visible
```

And understand optimistic concurrency, manifest pruning and partition evolution.

---

# Your Final Skill Matrix

At the end of six months, aim for:

| Skill                   | Target |
| ----------------------- | -----: |
| Distributed Systems     |  ⭐⭐⭐⭐⭐ |
| Kafka                   |  ⭐⭐⭐⭐⭐ |
| Flink                   |  ⭐⭐⭐⭐⭐ |
| Spark                   |  ⭐⭐⭐⭐⭐ |
| Iceberg                 |  ⭐⭐⭐⭐⭐ |
| ClickHouse              |  ⭐⭐⭐⭐⭐ |
| Parquet                 |   ⭐⭐⭐⭐ |
| CDC                     |  ⭐⭐⭐⭐⭐ |
| Kubernetes              |   ⭐⭐⭐⭐ |
| Data Architecture       |  ⭐⭐⭐⭐⭐ |
| Performance Engineering |  ⭐⭐⭐⭐⭐ |
| Production Debugging    |  ⭐⭐⭐⭐⭐ |

## And one final rule

**Do not study these as five independent technologies.**

Your real objective is to understand this:

```text
                   DISTRIBUTED SYSTEMS
                          │
                          ▼
                       KAFKA
                          │
                ┌─────────┴─────────┐
                ▼                   ▼
              FLINK               SPARK
          real-time             batch/replay
                │                   │
                └─────────┬─────────┘
                          ▼
                       ICEBERG
                    lakehouse storage
                          │
                ┌─────────┴─────────┐
                ▼                   ▼
            CLICKHOUSE            BQ
             serving             analytics
```

Once you understand **why data moves through each layer, what happens internally, how each system fails, and why you would choose one over another**, you are no longer just a Spark/Flink/Kafka developer—you are becoming a **Data Platform / Big Data Architect-level engineer**.

And based on the topics you've already been working through—Flink slots/parallelism, Kafka → Iceberg/BigQuery, ClickHouse at billions of rows, Kubernetes deployment, and large Spark pipelines—this is the direction I would take you rather than starting from beginner-level tutorials.

[1]: https://kafka.apache.org/documentation/?utm_source=chatgpt.com "Introduction | Apache Kafka"
[2]: https://www.oreilly.com/library/view/designing-data-intensive-applications/9781098119058/?utm_source=chatgpt.com "Designing Data-Intensive Applications, 2nd Edition [Book]"
[3]: https://martin.kleppmann.com/2017/03/27/designing-data-intensive-applications.html?utm_source=chatgpt.com "Designing Data-Intensive Applications — Martin Kleppmann’s publications"
[4]: https://kafka.apache.org/08/documentation.html?utm_source=chatgpt.com "Introduction | Apache Kafka"
[5]: https://flink.apache.org/?utm_source=chatgpt.com "Apache Flink® — Stateful Computations over Data Streams | Apache Flink"
[6]: https://nightlies.apache.org/flink/flink-docs-stable/docs/concepts/stateful-stream-processing/?utm_source=chatgpt.com "Stateful Stream Processing | Apache Flink"
[7]: https://spark.apache.org/docs/latest/sql-performance-tuning?utm_source=chatgpt.com "Performance Tuning - Spark 4.2.0 Documentation"
[8]: https://iceberg.apache.org/terms/?utm_source=chatgpt.com "Terms - Apache Iceberg™"
[9]: https://iceberg.apache.org/docs/latest/partitioning/?utm_source=chatgpt.com "Partitioning - Apache Iceberg™"
[10]: https://iceberg.apache.org/docs/latest/maintenance/?utm_source=chatgpt.com "Maintenance - Apache Iceberg™"
[11]: https://github.com/ClickHouse/ClickHouse/blob/master/docs/en/engines/table-engines/mergetree-family/mergetree.md?utm_source=chatgpt.com "ClickHouse/docs/en/engines/table-engines/mergetree-family/mergetree.md at master · ClickHouse/ClickHouse · GitHub"
[12]: https://iceberg.apache.org/docs/latest/performance/?utm_source=chatgpt.com "Performance - Apache Iceberg™"
