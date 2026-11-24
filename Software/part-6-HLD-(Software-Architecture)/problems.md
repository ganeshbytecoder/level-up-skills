# Distributed Data Systems — Interview & System Design Master Notes

## The master mental model

### **System problem = Workload + Data model + Distribution + Storage engine + Operations**

Whenever you design or troubleshoot a system, ask:

1. **What happens with huge writes?**
2. **What happens with huge reads?**
3. **What happens with deletes/updates?**
4. **What happens when data becomes skewed?**
5. **What happens when one key becomes extremely hot?**
6. **What happens when a database/node fails?**
7. **What happens when data grows 10×?**

Then investigate these dimensions:

### Storage

**WAL → SSTables/LSM → B-tree → segments → Parquet → compaction**

### Memory

**Cache → buffer pool → JVM heap → query memory**

### Concurrency

**Locks → MVCC → optimistic concurrency → conflicts**

### Distribution

**Partition → shard → replica → hotspot → rebalancing**

### Deletion

**Tombstones → vacuum → compaction → delete files → mutations**

### Consistency

**Strong → eventual → quorum → transactions → conflict resolution**

### Performance

**Indexes → partition pruning → data locality → caching → batching**

### Operations

**Backup → restore → replication → repair → failover → upgrades**

---

# 1. Cassandra

### Mental model

**Cassandra = Distributed + partition-oriented + LSM/SSTable + write optimized + query-driven data model**

The most important Cassandra concepts:

> **Partition key → replicas → SSTables → compaction → tombstones → consistency level**

---

## Cassandra — Problem → Happens when → Why → Mitigation

| Problem                      | Happens when                                 | Why happens                                                     | Mitigation                                                       |
| ---------------------------- | -------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------- |
| **Tombstones**               | Deletes or TTLs are used heavily             | Cassandra marks data deleted instead of immediately removing it | Tune TTL/delete strategy, compaction, repair, `gc_grace_seconds` |
| **Tombstone storm**          | Millions/billions of records expire together | Reads encounter huge numbers of tombstones                      | Time-bucket data, TWCS, stagger expiration                       |
| **Wide partitions**          | Too much data uses one partition key         | One partition becomes huge and expensive to read/repair/compact | Bucket partition keys                                            |
| **Hot partition**            | One/few keys receive most traffic            | Requests concentrate on the same replicas                       | Better partition-key design, bucketing/sharding                  |
| **Read amplification**       | Data exists across many SSTables             | Read may need to check multiple SSTables                        | Compaction + good data model                                     |
| **Write amplification**      | Heavy compaction                             | Data is repeatedly rewritten during compaction                  | Select appropriate compaction strategy                           |
| **Too many SSTables**        | Many small writes                            | Every write creates/flushes data into SSTables                  | Batch appropriately, tune memtables/compaction                   |
| **Compaction pressure**      | Heavy write workload                         | Background compaction competes for CPU/disk I/O                 | Capacity + correct compaction strategy                           |
| **GC pressure**              | Large partitions / excessive heap usage      | JVM spends more time reclaiming memory                          | Smaller partitions, heap tuning                                  |
| **Repair overhead**          | Large cluster / huge data                    | Replica consistency must be maintained                          | Regular repair, incremental repair                               |
| **Inconsistent replicas**    | Failed writes/network problems               | Replicas may temporarily diverge                                | Repair + appropriate consistency                                 |
| **Stale reads**              | Weak consistency level                       | Read may contact replica with older data                        | QUORUM/appropriate consistency                                   |
| **Coordinator hotspot**      | Poor request distribution                    | One node coordinates disproportionate traffic                   | Token-aware drivers                                              |
| **Cross-partition query**    | Query doesn't match partition key            | Request fans out across nodes                                   | Model table for access pattern                                   |
| **Secondary-index problems** | Very large/high-cardinality workload         | Query can require expensive distributed work                    | Denormalize/model query-specific tables                          |
| **Large deletes**            | Bulk deletion                                | Generates many tombstones                                       | Time-based data model/TTL/bucket deletion                        |
| **Node failure**             | Replica goes down                            | Remaining replicas must serve traffic                           | RF + consistency + repair                                        |

### Cassandra interview sentence

> **“With Cassandra, I first validate the partition key and partition size because most scalability problems eventually become partition hotspots, wide partitions, tombstones, or compaction pressure.”**

---

# 2. Kafka

### Mental model

**Kafka = Distributed append-only log + partitions + replication + consumer offsets**

Think:

> **Producer → Partition → Broker → Replica → Consumer → Offset**

---

## Kafka — Problem → Happens when → Why → Mitigation

| Problem                         | Happens when                            | Why happens                                       | Mitigation                                                |
| ------------------------------- | --------------------------------------- | ------------------------------------------------- | --------------------------------------------------------- |
| **Consumer lag**                | Consumer processes slower than producer | Incoming rate > processing rate                   | Scale consumers, increase partitions, optimize processing |
| **Hot partition**               | Poor message key                        | Most messages go to one partition                 | Better/high-cardinality key                               |
| **Partition imbalance**         | Uneven key distribution                 | Some partitions receive much more traffic         | Redesign key / repartition                                |
| **Consumer rebalance**          | Consumers join/leave/fail               | Group must redistribute partitions                | Stable consumers, cooperative rebalancing                 |
| **Rebalance storm**             | Frequent consumer restarts              | Constant ownership changes                        | Fix health/restarts, tune session settings                |
| **Poison message**              | Message repeatedly fails processing     | Consumer cannot successfully advance              | Retry topic + DLQ                                         |
| **Duplicate processing**        | Retry/rebalance/crash                   | Offset and processing aren't one atomic operation | Idempotent consumer                                       |
| **Message loss**                | Weak producer/broker configuration      | Data acknowledged before sufficient replication   | Appropriate `acks`, replication                           |
| **Under-replicated partitions** | Broker/follower falls behind            | Replica cannot keep up                            | Capacity + investigate broker                             |
| **Disk exhaustion**             | High throughput + long retention        | Log grows continuously                            | Retention/partition/storage planning                      |
| **Large messages**              | Events contain huge payloads            | Network/memory/disk overhead                      | Smaller events + object storage reference                 |
| **Producer backpressure**       | Brokers cannot accept data fast enough  | Producer buffers fill                             | Scale brokers, tune producer                              |
| **Consumer backpressure**       | Downstream API/DB is slow               | Consumer cannot process fast enough               | Bounded concurrency + pause/resume + buffering            |
| **Too many partitions**         | Excessive partition count               | Metadata, file handles, rebalance overhead        | Size partitions deliberately                              |
| **Ordering problem**            | Related events use different partitions | Kafka ordering is partition-local                 | Same key for ordered events                               |
| **Schema incompatibility**      | Producer changes event structure        | Consumers break                                   | Schema registry + compatibility rules                     |
| **Replication lag**             | Followers slower than leader            | ISR shrinks                                       | Broker/network/disk tuning                                |

### Kafka golden rule

> **Kafka guarantees ordering within a partition, not globally across the topic.**

### Kafka interview sentence

> **“I size Kafka primarily around throughput, ordering requirements, consumer parallelism, retention, and replication—not simply message count.”**

---

# 3. PostgreSQL

### Mental model

**PostgreSQL = Relational + ACID + MVCC + B-tree/indexes + WAL**

Think:

> **Transaction → MVCC → WAL → Buffer pool → B-tree → VACUUM**

---

## PostgreSQL — Problem → Happens when → Why → Mitigation

| Problem                   | Happens when                                  | Why happens                                  | Mitigation                                         |
| ------------------------- | --------------------------------------------- | -------------------------------------------- | -------------------------------------------------- |
| **Table bloat**           | Heavy UPDATE/DELETE                           | MVCC keeps old row versions                  | VACUUM/autovacuum                                  |
| **Index bloat**           | Heavy updates/deletes                         | Dead index entries accumulate                | REINDEX/maintenance                                |
| **Autovacuum pressure**   | Very high write/delete rate                   | Cleanup cannot keep up                       | Tune autovacuum                                    |
| **Long transaction**      | Transaction remains open                      | Old row versions cannot be cleaned           | Keep transactions short                            |
| **Lock contention**       | Many concurrent writes                        | Transactions wait for locks                  | Short transactions, indexes, proper access pattern |
| **Deadlock**              | Transactions acquire locks in different order | Circular dependency                          | Consistent lock ordering + retry                   |
| **Connection exhaustion** | Too many connections                          | Each connection consumes memory/resources    | Connection pooling                                 |
| **Hot row**               | Many requests update same row                 | Row-level locking creates contention         | Atomic redesign/sharding/counters                  |
| **Slow query**            | Missing/wrong index                           | Full table scan or bad plan                  | Index/query optimization                           |
| **Bad query plan**        | Stale statistics/data skew                    | Optimizer estimates incorrectly              | ANALYZE/statistics                                 |
| **Replication lag**       | Replica cannot replay WAL fast enough         | Primary produces changes faster than replica | Replica capacity/tuning                            |
| **Primary bottleneck**    | Very high write workload                      | Writes generally funnel through primary      | Partition/shard/application redesign               |
| **Large transaction**     | Huge batch update                             | WAL/locks/undo-like MVCC pressure            | Smaller batches                                    |
| **WAL growth**            | Replica/downstream falls behind               | WAL cannot be safely recycled                | Fix replication/retention issue                    |
| **Transaction ID issues** | Extremely old transactions                    | VACUUM cannot advance cleanup                | Prevent long-running transactions                  |
| **Storage growth**        | Data + indexes + WAL + bloat                  | Multiple storage consumers                   | Lifecycle + vacuum + archival                      |

### PostgreSQL interview sentence

> **“PostgreSQL gives me strong transactional semantics, but under heavy write workloads I pay attention to MVCC bloat, vacuum, locks, WAL, connection count, and primary scalability.”**

---

# 4. DynamoDB

### Mental model

**DynamoDB = Access-pattern-driven distributed key-value/document database**

Think:

> **Partition key → physical partition → capacity → hot partition**

---

## DynamoDB — Problem → Happens when → Why → Mitigation

| Problem                          | Happens when                                   | Why happens                                    | Mitigation                                 |
| -------------------------------- | ---------------------------------------------- | ---------------------------------------------- | ------------------------------------------ |
| **Hot partition**                | One partition key receives most traffic        | Requests concentrate on one physical partition | High-cardinality key / write sharding      |
| **Poor partition key**           | Low cardinality                                | Data/traffic becomes concentrated              | Redesign key                               |
| **Throttling**                   | Traffic exceeds available capacity             | Partition/table capacity is exceeded           | Autoscaling/on-demand/capacity planning    |
| **Hot GSI**                      | GSI partition key is skewed                    | Index receives concentrated traffic            | Better GSI key                             |
| **GSI write amplification**      | Many GSIs                                      | Every write updates indexes                    | Minimize unnecessary GSIs                  |
| **Large item**                   | Item approaches size limit                     | DynamoDB has an item-size limit                | Split/reference data                       |
| **Scan problem**                 | Application scans table                        | Scan touches many items                        | Use Query with key conditions              |
| **Eventually consistent read**   | Standard eventually consistent read            | Replica propagation delay                      | Strongly consistent read where appropriate |
| **Access-pattern mismatch**      | Application needs arbitrary relational queries | DynamoDB isn't designed for arbitrary joins    | Model around known access patterns         |
| **Uneven workload**              | Some keys much hotter                          | Distributed capacity isn't evenly consumed     | Sharding/bucketing                         |
| **Conditional-write contention** | Same item updated heavily                      | Conditional writes conflict                    | Redesign item ownership                    |
| **Cost explosion**               | Excessive reads/scans/indexes                  | Capacity consumption grows                     | Query efficiently + right-size indexes     |

### DynamoDB interview sentence

> **“With DynamoDB, I design the access patterns first. The partition key is both a data-distribution decision and a throughput decision.”**

---

# 5. BigQuery

### Mental model

**BigQuery = Distributed columnar OLAP + separation of storage and compute**

Think:

> **Scan → Filter → Shuffle → Join → Aggregate**

---

## BigQuery — Problem → Happens when → Why happens → Mitigation

| Problem                   | Happens when                           | Why happens                           | Mitigation                              |
| ------------------------- | -------------------------------------- | ------------------------------------- | --------------------------------------- |
| **High scan cost**        | `SELECT *`, poor filtering             | Large amount of columnar data scanned | Select columns + partition pruning      |
| **Partition scan**        | Query doesn't filter partition column  | Many partitions scanned               | Require/use partition filters           |
| **Shuffle explosion**     | Huge JOIN/GROUP BY                     | Data must move between workers        | Filter/pre-aggregate before shuffle     |
| **Data skew**             | One key dominates                      | One worker gets disproportionate data | Salting/repartitioning                  |
| **Large JOIN**            | Two huge tables joined                 | Massive network/shuffle workload      | Reduce datasets first                   |
| **Slot contention**       | Many concurrent jobs                   | Compute resources compete             | Workload isolation/reservations         |
| **Small-file ingestion**  | Huge number of tiny files              | Ingestion/planning overhead           | Batch/compact                           |
| **High query cost**       | Repeated massive scans                 | Same data repeatedly processed        | Partitioning/clustering/materialization |
| **Slow query**            | Poor join/order/aggregation            | Excessive data movement               | Query optimization                      |
| **Poor partition design** | Wrong partition column/granularity     | Partition pruning ineffective         | Choose query-aligned partition          |
| **Poor clustering**       | High-cardinality filters not clustered | More blocks scanned                   | Clustering                              |
| **Duplicate data**        | Repeated ingestion                     | Same records loaded multiple times    | Idempotent ingestion/merge              |

### BigQuery interview sentence

> **“For BigQuery, I focus on bytes scanned, partition pruning, clustering, shuffle volume, join cardinality, and slot utilization.”**

---

# 6. ClickHouse

### Mental model

**ClickHouse = Columnar OLAP + MergeTree + immutable parts + background merges**

Think:

> **INSERT → Parts → Merge → ORDER BY → Scan/Aggregate**

---

## ClickHouse — Problem → Happens when → Why happens | Mitigation

| Problem                 | Happens when                               | Why happens                                     | Mitigation                                    |
| ----------------------- | ------------------------------------------ | ----------------------------------------------- | --------------------------------------------- |
| **Too many parts**      | Tiny/frequent INSERTs                      | Each batch creates a data part                  | Batch inserts                                 |
| **Merge pressure**      | Heavy ingestion                            | Background merges rewrite data                  | Larger batches + adequate resources           |
| **FINAL cost**          | `ReplacingMergeTree FINAL` on huge dataset | Deduplication is performed during query         | Avoid unnecessary FINAL; optimize model       |
| **Duplicate rows**      | ReplacingMergeTree before merge            | Old/new versions coexist physically             | Correct ORDER BY/versioning + eventual merges |
| **Memory explosion**    | Huge GROUP BY/JOIN/SORT                    | Large intermediate structures                   | Query limits + pre-aggregation                |
| **Too many partitions** | High-cardinality partition key             | Excessive metadata/parts                        | Coarse partitioning                           |
| **Bad ORDER BY**        | Queries don't filter/order by useful keys  | Large portions of table scanned                 | Design ORDER BY around queries                |
| **Mutation backlog**    | Frequent UPDATE/DELETE                     | Mutations rewrite data                          | Prefer append/replacing patterns              |
| **Disk pressure**       | Large data + merges                        | Multiple temporary/merged parts require storage | Capacity planning                             |
| **Replica lag**         | Heavy merges/network load                  | Replica can't process changes quickly           | Capacity/tuning                               |
| **Join explosion**      | Many-to-many joins                         | Intermediate result becomes huge                | Control cardinality/pre-aggregate             |
| **Small files/parts**   | Micro-batch ingestion                      | Merge overhead dominates                        | Batch ingestion                               |

### ClickHouse golden rule

> **Partition for lifecycle. ORDER BY for query performance.**

### ClickHouse interview sentence

> **“For ClickHouse, I watch part count, merge pressure, ORDER BY effectiveness, partition cardinality, mutation backlog, and query memory.”**

---

# 7. Redis

### Mental model

**Redis = In-memory data structures + extremely low latency + cache/state**

Think:

> **Memory → Key → Event loop → TTL → Eviction**

---

## Redis — Problem → Happens when → Why happens → Mitigation

| Problem                 | Happens when                               | Why happens                                 | Mitigation                         |
| ----------------------- | ------------------------------------------ | ------------------------------------------- | ---------------------------------- |
| **Out of memory**       | Dataset exceeds memory                     | Working data is memory-resident             | Scale/eviction/right-size          |
| **Hot key**             | One key receives huge traffic              | One Redis shard becomes bottleneck          | Key splitting/local cache/replicas |
| **Big key**             | Huge string/list/set/hash                  | Memory and operation cost become large      | Split key/data                     |
| **Cache stampede**      | Popular key expires                        | Thousands of requests hit DB simultaneously | Locking/jitter/proactive refresh   |
| **Cache avalanche**     | Many keys expire simultaneously            | Backend receives sudden traffic spike       | TTL jitter                         |
| **Cache penetration**   | Requests repeatedly query nonexistent keys | Cache cannot prevent DB lookup              | Negative caching/Bloom filter      |
| **Blocking command**    | Huge set operation/`KEYS`                  | Expensive operation blocks processing       | `SCAN`, incremental operations     |
| **Eviction**            | Memory limit reached                       | Keys must be removed                        | Appropriate eviction policy        |
| **Replication lag**     | Replica can't keep up                      | Change stream grows                         | Capacity/tuning                    |
| **Persistence latency** | Heavy RDB/AOF operations                   | Disk/fork overhead                          | Tune persistence                   |
| **Cache inconsistency** | DB and Redis updated separately            | Two copies diverge                          | Invalidation/TTL/write-through     |
| **Hot shard**           | Cluster key distribution is skewed         | One node receives disproportionate traffic  | Better key distribution            |

### Redis interview sentence

> **“Redis is excellent for latency-sensitive state, but I immediately check memory growth, hot keys, big keys, eviction behavior, cache stampede, and persistence/replication.”**

---

# 8. Apache Iceberg

### Mental model

**Iceberg = Table abstraction over immutable data files + metadata + snapshots**

Think:

> **Table → Snapshot → Manifest → Data files → Parquet**

---

## Iceberg — Problem → Happens when → Why happens → Mitigation

| Problem                            | Happens when                           | Why happens                                   | Mitigation                     |
| ---------------------------------- | -------------------------------------- | --------------------------------------------- | ------------------------------ |
| **Small files**                    | Streaming/micro-batch writes           | Many tiny Parquet files                       | Compaction                     |
| **Manifest explosion**             | Huge number of files                   | Metadata becomes large                        | Manifest rewrite               |
| **Snapshot accumulation**          | Frequent commits                       | Old metadata/snapshots remain                 | Snapshot expiration            |
| **Delete-file accumulation**       | Frequent deletes/updates               | Queries process delete metadata               | Compaction                     |
| **Slow planning**                  | Massive metadata                       | Engine spends time planning files             | Optimize manifests             |
| **Partition evolution complexity** | Partition spec changes                 | Multiple partition layouts coexist            | Controlled evolution           |
| **Poor partitioning**              | Queries don't prune files              | Excessive files scanned                       | Query-aligned partitioning     |
| **Data-file explosion**            | Excessive parallel writers             | Too many files created                        | Target file sizes + compaction |
| **Orphan files**                   | Failed/partial operations              | Files exist without valid metadata references | Orphan-file cleanup            |
| **Concurrent commit conflict**     | Multiple writers commit simultaneously | Optimistic concurrency conflict               | Retry/rebase transaction       |
| **Delete amplification**           | Frequent row-level deletes             | Additional delete files/rewrites              | Batch deletes/compaction       |

### Iceberg interview sentence

> **“With Iceberg, the table is more than the Parquet files—the metadata, manifests, snapshots, and delete files become operational concerns at scale.”**

---

# 9. Apache Spark

### Mental model

**Spark = Distributed compute engine**

Think:

> **Driver → Executors → Tasks → Shuffle → Storage**

---

## Spark — Problem → Happens when → Why happens → Mitigation

| Problem                         | Happens when                            | Why happens                             | Mitigation                               |
| ------------------------------- | --------------------------------------- | --------------------------------------- | ---------------------------------------- |
| **Data skew**                   | One key contains disproportionate data  | One/few tasks process much more data    | Salting/AQE/repartition                  |
| **Large shuffle**               | JOIN/GROUP BY/ORDER BY                  | Data moves between executors            | Filter/pre-aggregate/repartition         |
| **OOM**                         | Partition/task requires too much memory | Data doesn't fit executor memory        | Smaller partitions/increase memory       |
| **GC pressure**                 | Large JVM objects                       | Garbage collection consumes CPU/time    | Tune memory/object usage                 |
| **Too many small files**        | Excessive parallel output               | Many tiny files created                 | Coalesce/repartition/compaction          |
| **Too few partitions**          | Dataset huge but partition count low    | Tasks become huge/parallelism low       | Increase partitions                      |
| **Too many partitions**         | Tiny tasks                              | Scheduling overhead                     | Reduce partitions                        |
| **Straggler task**              | Skew/slow node/large partition          | Job waits for slowest task              | AQE/speculation/skew handling            |
| **Driver OOM**                  | `collect()`/large metadata              | Driver receives huge dataset            | Avoid collect; distributed processing    |
| **Executor OOM**                | Large joins/aggregations                | Executor cannot hold intermediate state | Broadcast/filter/repartition             |
| **Broadcast explosion**         | Broadcast dataset too large             | Every executor receives large copy      | Tune/disable broadcast                   |
| **Shuffle spill**               | Insufficient execution memory           | Intermediate data spills to disk        | More memory/better partitioning          |
| **Serialization overhead**      | Inefficient objects/UDFs                | CPU/network cost increases              | Efficient types/native functions         |
| **Slow UDFs**                   | Python UDF per row                      | Serialization/Python boundary overhead  | Spark SQL/native functions/vectorization |
| **Checkpoint/storage pressure** | Excessive checkpointing                 | Data written repeatedly                 | Strategic checkpointing                  |
| **Bad join strategy**           | Wrong join selection                    | Huge shuffle or broadcast               | Analyze sizes and join strategy          |

### Spark golden rule

> **Most Spark performance problems reduce to: partitioning + shuffle + skew + memory + file layout.**

### Spark interview sentence

> **“For Spark, I first look at partition count, data skew, shuffle volume, task size, executor memory, spill, and file count.”**

---

# 10. Apache Flink

### Mental model

**Flink = Stateful stream processing + event time + checkpoints + backpressure**

Think:

> **Kafka → Source → Operator → State → Checkpoint → Sink**

---

## Flink — Problem → Happens when → Why happens → Mitigation

| Problem                           | Happens when                        | Why happens                             | Mitigation                                       |
| --------------------------------- | ----------------------------------- | --------------------------------------- | ------------------------------------------------ |
| **Backpressure**                  | Downstream operator is slower       | Upstream cannot pass data fast enough   | Scale bottleneck/operator                        |
| **State explosion**               | Huge keyed state                    | State grows continuously                | TTL/windowing/state cleanup                      |
| **Checkpoint timeout**            | Huge state/slow sink                | Checkpoint cannot complete in time      | Incremental checkpoints/scale                    |
| **Checkpoint failure**            | Backpressure/network/storage issues | State snapshot cannot finish            | Fix bottleneck/storage                           |
| **TaskManager OOM**               | State or operators too large        | JVM/off-heap memory exhausted           | Tune memory/parallelism/state                    |
| **Hot key**                       | One key dominates                   | One subtask gets most state/work        | Key redesign/salting where semantically possible |
| **Watermark delay**               | Late/slow events                    | Event-time progress is delayed          | Watermark strategy                               |
| **Late events**                   | Events arrive after watermark       | Window may already have closed          | Allowed lateness/side output                     |
| **State backend pressure**        | Massive state                       | State storage becomes bottleneck        | RocksDB/managed memory/tuning                    |
| **Sink bottleneck**               | DB/API/Kafka sink too slow          | Entire pipeline backs up                | Async I/O/batching/scale sink                    |
| **Unaligned checkpoint overhead** | Severe backpressure                 | Checkpoint captures in-flight buffers   | Use strategically                                |
| **Reprocessing after failure**    | Job restarts                        | Data replayed from checkpoint/savepoint | Idempotent/exactly-once sink                     |
| **Parallelism mismatch**          | Operator parallelism too low        | Bottleneck limits throughput            | Increase parallelism                             |
| **Network shuffle pressure**      | High repartitioning                 | Records move between TaskManagers       | Reduce repartitioning/optimize keying            |

### Flink golden rule

> **Flink performance = throughput + state size + checkpoint health + backpressure + event-time behavior.**

### Flink interview sentence

> **“When Flink slows down, I identify the bottleneck operator first, then check backpressure, state size, checkpoint duration, key skew, and sink throughput.”**

---

# 11. Parquet

### Mental model

**Parquet = Columnar file format**

Think:

> **File → Row groups → Column chunks → Pages → Compression**

---

## Parquet — Problem → Happens when → Why happens → Mitigation

| Problem                     | Happens when                      | Why happens                                      | Mitigation                     |
| --------------------------- | --------------------------------- | ------------------------------------------------ | ------------------------------ |
| **Small files**             | Too many writers/micro-batches    | File metadata + object-store operations increase | Compact files                  |
| **Huge files**              | Files become extremely large      | Parallelism and selective access can suffer      | Target reasonable file size    |
| **Poor compression**        | Wrong codec/data type             | Storage and scan cost increase                   | Choose codec based on workload |
| **Poor column pruning**     | Query requests most columns       | Columnar advantage disappears                    | Select only required columns   |
| **Poor predicate pushdown** | Filters can't be pushed           | More row groups scanned                          | Appropriate schema/query       |
| **Bad row-group size**      | Row groups poorly sized           | Scan efficiency suffers                          | Tune row-group size            |
| **Schema evolution issues** | Columns/types change incompatibly | Readers may fail or misinterpret data            | Schema governance              |
| **Small row groups**        | Excessive fragmentation           | Metadata and seek overhead                       | Larger row groups              |
| **Skewed files**            | Uneven partition data             | Some files much larger                           | Repartition/balance            |
| **Partition explosion**     | Too many directory partitions     | Metadata/listing overhead                        | Coarser partitioning           |
| **Corrupt files**           | Failed writes/partial files       | Readers cannot process file                      | Atomic write/validation        |
| **Too many columns**        | Extremely wide schema             | Metadata/processing overhead                     | Normalize/model appropriately  |

### Parquet interview sentence

> **“For Parquet, I think about file size, row-group size, column pruning, predicate pushdown, compression, partition layout, and schema evolution.”**

---

# 12. Spring Boot

Spring Boot is not a database or storage engine, but in your architecture it is often the **application/service layer connecting Kafka, databases, APIs, and caches**.

### Mental model

**Spring Boot = API/service + concurrency + connection pools + downstream dependencies**

Think:

> **Request → Thread/Async → Business logic → DB/Kafka/API → Response**

---

## Spring Boot — Problem → Happens when → Why happens → Mitigation

| Problem                        | Happens when                         | Why happens                            | Mitigation                                   |
| ------------------------------ | ------------------------------------ | -------------------------------------- | -------------------------------------------- |
| **Thread pool exhaustion**     | Requests take too long               | Threads remain occupied                | Tune pool + async/nonblocking design         |
| **Connection pool exhaustion** | DB calls are slow/high concurrency   | Connections remain occupied            | Tune pool + query optimization               |
| **Downstream bottleneck**      | DB/API/Kafka is slow                 | Application threads pile up            | Timeouts + backpressure + bulkheads          |
| **Cascading failure**          | One dependency fails                 | Requests retry/pile up across services | Circuit breaker + timeout + bulkhead         |
| **Retry storm**                | Dependency is already overloaded     | Retries multiply traffic               | Exponential backoff + jitter                 |
| **Memory pressure**            | Huge payloads/concurrent requests    | Heap usage grows                       | Streaming + limits + bounded concurrency     |
| **GC pressure**                | Excessive object allocation          | JVM spends time collecting             | Reduce allocations/tune heap                 |
| **Kafka consumer lag**         | Processing slower than incoming rate | Consumer throughput insufficient       | Parallelism + batching + optimize processing |
| **Duplicate processing**       | Consumer restart/retry               | Same event processed again             | Idempotency                                  |
| **DB connection exhaustion**   | Too many service instances           | Total connections exceed DB capacity   | Pool limits + connection budget              |
| **Slow API**                   | Downstream latency                   | End-to-end latency accumulates         | Timeout + caching + parallel calls           |
| **Large payload**              | Request/response huge                | Network + serialization overhead       | Pagination/compression/streaming             |
| **Synchronous chain**          | Service A → B → C → D                | Latencies add and failures propagate   | Async/events where appropriate               |
| **Unbounded queues**           | Producer faster than consumer        | Memory grows indefinitely              | Bounded queue/backpressure                   |
| **No graceful shutdown**       | Deployment/restart                   | In-flight work lost                    | Graceful shutdown/draining                   |

### Spring Boot interview sentence

> **“For a Spring Boot service, I size around request rate, concurrency, latency, downstream throughput, connection pools, thread pools, memory, and failure behavior.”**

---

# 13. Cross-system problem patterns

These are the patterns you should recognize immediately in system design.

## Pattern 1 — Hot key

### Formula

**Hot key = uneven traffic distribution**

Examples:

* Cassandra → hot partition
* Kafka → hot partition
* DynamoDB → hot partition
* Redis → hot key
* Flink → hot keyed state
* Bigtable-like systems → hot row/tablet
* Spark → skewed partition

### General solution

> **Distribute the workload.**

Techniques:

**Bucketing → salting → sharding → better partition key → caching → replication**

---

# Pattern 2 — Data skew

### Formula

**Data skew = one key/partition contains disproportionately more data**

Common in:

* Spark
* Flink
* BigQuery
* Cassandra
* DynamoDB
* ClickHouse
* Kafka

### Example

Suppose:

```text
100 million records

Customer A = 50 million
Other customers = 50 million
```

One partition/task becomes enormous.

### Solutions

**Salting → repartitioning → bucketing → pre-aggregation → split hot keys**

---

# Pattern 3 — Small files

This appears everywhere in data platforms.

### Happens when

```text
Too many small writes
        ↓
Too many files
        ↓
Metadata overhead
        ↓
Slow planning/scanning
```

Especially important for:

**Parquet → Iceberg → Spark → ClickHouse → data lakes**

### Solution

> **Compaction + controlled file sizing + batch writes**

---

# Pattern 4 — Too much deletion

Different systems solve deletion differently.

| System     | Deletion problem                         |
| ---------- | ---------------------------------------- |
| Cassandra  | Tombstones                               |
| PostgreSQL | Dead tuples / bloat                      |
| ClickHouse | Mutations                                |
| Iceberg    | Delete files                             |
| Kafka      | Retention                                |
| Redis      | Eviction/expiration                      |
| Parquet    | Usually immutable → rewrite/delete files |
| Spark      | Reprocessing/rewrite                     |
| BigQuery   | DML/storage processing                   |
| DynamoDB   | Deletes consume capacity                 |

### Mental model

> **Delete does not necessarily mean “physically remove immediately.”**

Often:

**Logical delete → metadata → background cleanup → compaction/rewrite**

---

# Pattern 5 — Compaction

Compaction exists because many storage engines prefer:

> **Fast writes now → cleanup later**

Examples:

### Cassandra

```text
Memtable
   ↓
SSTable
   ↓
SSTable
   ↓
Compaction
   ↓
Fewer optimized SSTables
```

### ClickHouse

```text
INSERT
 ↓
Part
 ↓
Part
 ↓
Merge
 ↓
Larger optimized part
```

### Iceberg

```text
Many Parquet files
 ↓
Compaction
 ↓
Fewer larger files
```

### Kafka

Kafka doesn't use the same SSTable compaction model, but **log compaction** removes obsolete records for the same key.

---

# Pattern 6 — Backpressure

### Universal formula

```text
Producer rate > Consumer rate
        ↓
Backlog
        ↓
Backpressure
```

Examples:

### Kafka

```text
Producer
   ↓ 100K/sec
Kafka
   ↓ 60K/sec
Consumer

Consumer lag ↑
```

### Flink

```text
Source
  ↓
Operator A
  ↓
Operator B ← bottleneck
  ↓
Sink

Backpressure propagates upstream
```

### Spring Boot

```text
HTTP requests
      ↓
Spring service
      ↓
Slow DB
      ↓
Thread pool fills
      ↓
Requests queue
      ↓
Latency ↑
```

### General mitigation

**Scale bottleneck → increase parallelism → batching → buffering → rate limiting → backpressure**

---

# Pattern 7 — Replication lag

### Formula

```text
Primary write rate
       >
Replica processing rate

        ↓

Replication lag
```

Appears in:

**PostgreSQL → Cassandra → DynamoDB-like systems → Redis → Kafka → ClickHouse**

Always ask:

> **How much lag can the business tolerate?**

---

# Pattern 8 — Memory explosion

### Formula

```text
Input data
   ↓
Intermediate state
   ↓
Memory requirement
   ↓
OOM
```

Examples:

* Spark → shuffle/aggregation
* Flink → state
* Redis → dataset
* PostgreSQL → buffer/cache/connections
* ClickHouse → GROUP BY/JOIN
* BigQuery → distributed query memory
* Spring Boot → heap/object allocation

### General solution

**Bound memory → partition → spill → stream → batch → scale**

---

# Pattern 9 — Connection/thread exhaustion

Especially important in Spring Boot + databases.

```text
Traffic ↑
   ↓
Concurrent requests ↑
   ↓
DB calls ↑
   ↓
Connection pool exhausted
   ↓
Requests wait
   ↓
Latency ↑
```

Never solve this blindly by simply increasing the pool.

You must ask:

> **Can the database actually handle the additional connections?**

---

# Pattern 10 — Shuffle

### Formula

```text
Data
 ↓
Need same key together
 ↓
Move data across machines
 ↓
Network + serialization + disk
 ↓
Shuffle
```

Most important in:

**Spark → BigQuery → Flink → distributed SQL systems**

### Shuffle is expensive because of:

**Network + serialization + memory + disk spill + skew**

---

# Pattern 11 — Query-driven data modeling

Different systems require different modeling philosophies.

### Cassandra

> Design table around query.

### DynamoDB

> Design partition/access pattern around query.

### Redis

> Design key/data structure around operation.

### ClickHouse

> Design `ORDER BY` around query patterns.

### BigQuery

> Design partitioning/clustering around analytical filtering.

### Iceberg

> Design partitioning/file layout around scan patterns and lifecycle.

### PostgreSQL

> More flexible because relational indexes/query planner support arbitrary queries, but indexes still matter.

---

# Pattern 12 — Strong vs eventual consistency

### Strong consistency

```text
Write
 ↓
Acknowledged
 ↓
Read immediately
 ↓
Expected current value
```

Useful when:

**Money → inventory correctness → critical state**

### Eventual consistency

```text
Write
 ↓
Replica propagation
 ↓
Temporary stale read possible
 ↓
Eventually converges
```

Useful when:

**Feeds → analytics → caches → derived views**

The system-design question is:

> **What is the business consistency requirement?**

Not:

> “Which database is fastest?”

---

# Pattern 13 — Idempotency

Extremely important for:

**Kafka + Spring Boot + Flink + databases**

### Problem

```text
Event received
 ↓
DB updated
 ↓
Consumer crashes before committing offset
 ↓
Event processed again
```

Now:

```text
Same event
     ↓
processed twice
```

### Solution

Use an idempotency key:

```text
eventId = 12345

if eventId already processed:
    skip

else:
    process
    record eventId
```

Common techniques:

**Unique constraint → dedup table → idempotent upsert → transactional outbox/inbox**

---

# Pattern 14 — Failure and retry

Never design:

```text
Failure → immediately retry forever
```

That can create a retry storm.

Use:

```text
Failure
 ↓
Retry
 ↓
Exponential backoff
 ↓
Jitter
 ↓
Maximum attempts
 ↓
DLQ / fallback
```

For APIs:

**Timeout → Retry → Circuit breaker → Bulkhead**

---

# Pattern 15 — Scaling strategy

When traffic increases:

### Vertical scaling

```text
Bigger machine
```

Simple, but eventually limited.

### Horizontal scaling

```text
More machines
```

Requires:

**Partitioning → replication → load balancing → coordination**

### Read scaling

```text
Primary
 ↓
Read replicas
```

### Write scaling

Usually harder:

```text
Partition/shard
 ↓
Multiple write nodes
```

---

# 16. Database selection cheat sheet

| Requirement                                           | Usually consider |
| ----------------------------------------------------- | ---------------- |
| Strong transactions + relational queries              | **PostgreSQL**   |
| Massive distributed key-value workload                | **Cassandra**    |
| Serverless key-value/document + known access patterns | **DynamoDB**     |
| Extremely fast cache/state                            | **Redis**        |
| Event streaming                                       | **Kafka**        |
| Massive OLAP SQL                                      | **BigQuery**     |
| High-performance self-managed OLAP                    | **ClickHouse**   |
| Data lake table format                                | **Iceberg**      |
| Distributed batch processing                          | **Spark**        |
| Stateful real-time processing                         | **Flink**        |
| Columnar storage                                      | **Parquet**      |
| Business/API service layer                            | **Spring Boot**  |

---

# 17. The 10× system-design test

Whenever someone proposes a technology, ask:

### 1. 10× writes

```text
100K/sec → 1M/sec
```

What breaks first?

**CPU? Disk? Network? Partitions? WAL? Compaction?**

---

### 2. 10× reads

```text
1M/day → 10M/day
```

What breaks?

**Indexes? Cache? Replicas? Query compute?**

---

### 3. 10× data

```text
10 TB → 100 TB
```

What happens?

**Storage cost? Query latency? Metadata? Compaction? Backup?**

---

### 4. 10× largest partition

```text
10 GB → 100 GB
```

What happens?

**Hot partition? Repair? Query latency? Memory?**

---

### 5. 10× traffic for one key

```text
Normal key = 1K/sec
Hot key = 100K/sec
```

What happens?

**Hot partition / hot shard / hot consumer / hot cache key**

---

### 6. Node failure

```text
N nodes
 ↓
N - 1 nodes
```

Ask:

**Can we still serve traffic?**

Then:

**At what latency?**

Then:

**With what consistency?**

---

### 7. Network partition

Ask:

> What happens when nodes can communicate with some machines but not others?

Think:

**Consistency → quorum → split brain → retries → failover**

---

### 8. Consumer becomes slow

Ask:

```text
Producer = 100K/sec
Consumer = 50K/sec
```

What happens?

**Backlog → lag → storage growth → recovery problem**

---

### 9. Downstream database becomes slow

Ask:

```text
API
 ↓
DB latency 20ms → 2 seconds
```

What happens?

**Thread pool → connection pool → queue → timeout → retry storm → cascading failure**

---

### 10. Delete 1 billion records

This is an excellent Staff-level question.

Ask:

> **Does this database physically delete them immediately?**

Possible answers:

**Cassandra → tombstones**

**PostgreSQL → MVCC/dead tuples**

**ClickHouse → mutations**

**Iceberg → delete files/rewrite**

**Parquet → immutable files**

**Kafka → retention/log compaction**

This single question exposes a lot about the underlying storage engine.

---

# 18. The universal troubleshooting framework

When production is slow, don't randomly tune things.

Use:

## **1. Traffic**

```text
Requests/sec?
Messages/sec?
Rows/sec?
```

## **2. Latency**

```text
p50
p95
p99
```

## **3. Errors**

```text
Timeout?
5xx?
Throttling?
OOM?
```

## **4. Saturation**

```text
CPU
Memory
Disk
Network
Connections
Threads
```

## **5. Distribution**

```text
Hot partition?
Hot key?
Data skew?
```

## **6. Storage**

```text
SSTables?
Segments?
Parts?
Files?
WAL?
Compaction?
```

## **7. Queue**

```text
Kafka lag?
Thread queue?
Connection pool?
Flink backpressure?
```

## **8. Replication**

```text
Replica lag?
Under-replicated partitions?
Checkpoint failure?
```

## **9. Query**

```text
Scan?
Shuffle?
Join?
Aggregation?
Index?
Partition pruning?
```

## **10. Recovery**

```text
What happens if the bottleneck fails?
Can the system catch up?
How long does recovery take?
```

---

# 19. The ultimate memory map

You can reduce the entire subject to this:

```text
                    DISTRIBUTED DATA SYSTEM
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
       WORKLOAD            DATA MODEL        DISTRIBUTION
          │                   │                   │
     ┌────┼────┐         ┌────┼────┐         ┌────┼────┐
     │    │    │         │    │    │         │    │    │
   Read Write Delete    Key  Index State    Shard Replica Hotspot
     │    │    │
     └────┼────┘
          │
      STORAGE ENGINE
          │
   ┌──────┼────────┐
   │      │        │
  WAL   LSM/BTree  Columnar
   │      │        │
   │   SSTables   Segments
   │      │        │
   │ Compaction   Parquet
   │
   └──────────────┐
                  │
              OPERATIONS
                  │
       ┌──────────┼──────────┐
       │          │          │
   Replication  Backup    Failover
       │
    Consistency
       │
   Strong/Eventual
```

---

# 20. The interview answer template

When asked:

> **“How would you design this system?”**

Use this sequence:

### Step 1 — Workload

```text
QPS =
Read/write ratio =
Average payload =
Peak traffic =
Data growth =
Retention =
Latency SLA =
```

### Step 2 — Access pattern

```text
Point lookup?
Range query?
Aggregation?
Search?
Join?
Streaming?
Batch?
```

### Step 3 — Data model

```text
Partition key =
Sort/order key =
Indexes =
TTL =
Relationships =
```

### Step 4 — Distribution

```text
Partition count =
Replication factor =
Shard strategy =
Hot-key strategy =
```

### Step 5 — Storage

```text
WAL?
SSTables?
B-tree?
Columnar?
Parquet?
Compaction?
```

### Step 6 — Consistency

```text
Strong?
Eventual?
Quorum?
Exactly once?
At least once?
Idempotent?
```

### Step 7 — Failure

```text
Node failure?
Network failure?
Database failure?
Consumer failure?
Downstream failure?
```

### Step 8 — Backpressure

```text
Producer rate =
Consumer rate =
Buffer =
Maximum backlog =
Recovery rate =
```

### Step 9 — Observability

Track:

```text
Throughput
p50/p95/p99
CPU
Memory
Disk
Network
Queue depth
Kafka lag
Compaction
Replication lag
Error rate
```

### Step 10 — 10× test

Finally ask:

> **“If traffic and data grow 10×, what breaks first?”**

That question should drive the next architectural decision.

---

# Final mental model

If you remember only one thing, remember this:

## **Every distributed data problem eventually becomes one of these:**

```text
1. TOO MUCH DATA
       ↓
   Storage / metadata / compaction

2. TOO MUCH TRAFFIC
       ↓
   Scaling / partitioning / caching

3. BAD DISTRIBUTION
       ↓
   Hot key / hot partition / skew

4. TOO MUCH STATE
       ↓
   Memory / checkpoint / cache / index

5. TOO MUCH DATA MOVEMENT
       ↓
   Shuffle / network / serialization

6. TOO MANY CHANGES
       ↓
   WAL / tombstones / mutations / delete files

7. TOO MUCH CONTENTION
       ↓
   Locks / connections / threads / conflicts

8. TOO MUCH LAG
       ↓
   Replication / Kafka consumers / Flink checkpoints

9. TOO MANY FAILURES
       ↓
   Retry / timeout / circuit breaker / idempotency

10. TOO MUCH GROWTH
       ↓
   Partitioning / sharding / lifecycle / archival
```

### The Staff Engineer question

Don't ask only:

> **“Which database should I use?”**

Ask:

> **“Given this workload, data model, scale, consistency requirement, failure model, and growth rate, what failure mode will this technology hit first, and how will I design around it?”**

That is the core of **database selection + capacity planning + distributed-system design**.
