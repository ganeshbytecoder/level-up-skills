

# PART 1 — SOFTWARE & PROGRAMMING FUNDAMENTALS

### 1–20

| #  | Term               | Simple meaning                                                 |
| -- | ------------------ | -------------------------------------------------------------- |
| 1  | Software           | Instructions executed by a computer                            |
| 2  | Application        | Software built to perform a specific purpose                   |
| 3  | Program            | A set of executable instructions                               |
| 4  | Process            | A running instance of a program                                |
| 5  | Thread             | A unit of execution inside a process                           |
| 6  | CPU                | Executes program instructions                                  |
| 7  | Memory / RAM       | Temporary working memory                                       |
| 8  | Storage            | Persistent data storage                                        |
| 9  | Binary             | Data represented using 0s and 1s                               |
| 10 | Source Code        | Human-readable program code                                    |
| 11 | Compiler           | Converts source code into executable form                      |
| 12 | Interpreter        | Executes code without traditional compilation to native binary |
| 13 | Runtime            | Environment where software executes                            |
| 14 | Runtime Error      | Error occurring while program executes                         |
| 15 | Compile-time Error | Error detected during compilation                              |
| 16 | Dependency         | External code/library your application requires                |
| 17 | Library            | Reusable collection of code                                    |
| 18 | Framework          | Structure and conventions for building applications            |
| 19 | SDK                | Tools/libraries provided for developing against a platform     |
| 20 | API                | Contract through which software components communicate         |

**Mental model:**

```text
Source Code
    ↓
Compiler / Interpreter
    ↓
Program
    ↓
Process
    ↓
Threads
    ↓
CPU + Memory
```

---

# PART 2 — OBJECT-ORIENTED & SOFTWARE DESIGN

### 21–40

| #  | Term                   | Simple meaning                                              |
| -- | ---------------------- | ----------------------------------------------------------- |
| 21 | Class                  | Blueprint for objects                                       |
| 22 | Object                 | Instance of a class                                         |
| 23 | Encapsulation          | Keeping data and behavior together/protected                |
| 24 | Abstraction            | Hiding unnecessary implementation details                   |
| 25 | Inheritance            | Reusing behavior from another class                         |
| 26 | Polymorphism           | Same interface, different implementations                   |
| 27 | Interface              | Contract defining expected behavior                         |
| 28 | Composition            | Building objects from other objects                         |
| 29 | Coupling               | Degree to which components depend on each other             |
| 30 | Cohesion               | How closely related responsibilities within a component are |
| 31 | Dependency Injection   | Supplying dependencies from outside                         |
| 32 | Inversion of Control   | Framework/container controls object creation/flow           |
| 33 | SOLID                  | Five principles for maintainable OO design                  |
| 34 | DRY                    | Don't Repeat Yourself                                       |
| 35 | KISS                   | Keep It Simple                                              |
| 36 | YAGNI                  | Don't build functionality until needed                      |
| 37 | Separation of Concerns | Keep different responsibilities separate                    |
| 38 | Design Pattern         | Reusable solution to a common design problem                |
| 39 | Anti-pattern           | Common approach that causes problems                        |
| 40 | Technical Debt         | Future cost created by shortcuts/design compromises         |

---

# PART 3 — DATA STRUCTURES & ALGORITHMS

### 41–60

| #  | Term             | Simple meaning                                         |
| -- | ---------------- | ------------------------------------------------------ |
| 41 | Array            | Ordered collection stored in contiguous memory         |
| 42 | List             | Ordered collection                                     |
| 43 | Linked List      | Nodes connected through references                     |
| 44 | Stack            | LIFO data structure                                    |
| 45 | Queue            | FIFO data structure                                    |
| 46 | Deque            | Queue supporting both ends                             |
| 47 | Hash Table       | Key-value structure using hashing                      |
| 48 | Hash Function    | Converts input into a hash value                       |
| 49 | Tree             | Hierarchical data structure                            |
| 50 | Binary Tree      | Tree where nodes have at most two children             |
| 51 | Heap             | Tree-based structure commonly used for priority queues |
| 52 | Graph            | Nodes connected by edges                               |
| 53 | BFS              | Breadth-first search                                   |
| 54 | DFS              | Depth-first search                                     |
| 55 | Recursion        | Function calling itself                                |
| 56 | Iteration        | Repeating through loops                                |
| 57 | Big O            | Describes algorithm growth/complexity                  |
| 58 | Time Complexity  | Computational time required as input grows             |
| 59 | Space Complexity | Memory required as input grows                         |
| 60 | Sorting          | Arranging data according to an ordering                |

---

# PART 4 — GIT, CODE QUALITY & SOFTWARE DEVELOPMENT

### 61–80

| #  | Term                   | Simple meaning                                    |
| -- | ---------------------- | ------------------------------------------------- |
| 61 | Git                    | Distributed version-control system                |
| 62 | Repository             | Storage location for source code/history          |
| 63 | Commit                 | Snapshot of code changes                          |
| 64 | Branch                 | Independent line of development                   |
| 65 | Merge                  | Combine branches                                  |
| 66 | Rebase                 | Replay commits onto another base                  |
| 67 | Pull Request           | Proposed code change for review                   |
| 68 | Code Review            | Reviewing code before integration                 |
| 69 | Merge Conflict         | Git cannot automatically combine changes          |
| 70 | Tag                    | Named Git reference, often used for releases      |
| 71 | Release                | Published version of software                     |
| 72 | Semantic Versioning    | Versioning convention such as 2.4.1               |
| 73 | Linter                 | Detects code-quality/style problems               |
| 74 | Formatter              | Automatically formats source code                 |
| 75 | Static Analysis        | Analyzing code without executing it               |
| 76 | Refactoring            | Improving internal code without changing behavior |
| 77 | Code Smell             | Sign of potentially problematic code/design       |
| 78 | Clean Code             | Code designed for readability/maintainability     |
| 79 | Backward Compatibility | New version continues supporting old clients      |
| 80 | Breaking Change        | Change that can break existing consumers          |

---

# PART 5 — TESTING

### 81–100

| #   | Term             | Simple meaning                                           |
| --- | ---------------- | -------------------------------------------------------- |
| 81  | Unit Test        | Tests a small unit of code                               |
| 82  | Integration Test | Tests multiple components together                       |
| 83  | System Test      | Tests the complete system                                |
| 84  | End-to-End Test  | Tests a complete user/business flow                      |
| 85  | Regression Test  | Ensures old functionality still works                    |
| 86  | Smoke Test       | Basic test confirming system works                       |
| 87  | Sanity Test      | Quick validation after a change                          |
| 88  | Test Case        | Specific scenario being tested                           |
| 89  | Test Suite       | Collection of tests                                      |
| 90  | Test Fixture     | Setup data/environment for tests                         |
| 91  | Mock             | Simulated dependency                                     |
| 92  | Stub             | Provides predetermined responses                         |
| 93  | Spy              | Records interactions with a dependency                   |
| 94  | Test Double      | General term for mock/stub/spy/etc.                      |
| 95  | Code Coverage    | Percentage of code exercised by tests                    |
| 96  | Mutation Testing | Tests whether tests detect intentionally introduced bugs |
| 97  | Contract Testing | Verifies communication contracts between services        |
| 98  | Load Testing     | Tests behavior under expected load                       |
| 99  | Stress Testing   | Tests behavior beyond normal capacity                    |
| 100 | Chaos Testing    | Intentionally introduces failures                        |

---

# PART 6 — WEB & HTTP

### 101–120

| #   | Term             | Simple meaning                                 |
| --- | ---------------- | ---------------------------------------------- |
| 101 | HTTP             | Protocol used for web communication            |
| 102 | HTTPS            | HTTP protected with TLS                        |
| 103 | Request          | Message sent to a server                       |
| 104 | Response         | Message returned by a server                   |
| 105 | HTTP Method      | GET, POST, PUT, PATCH, DELETE, etc.            |
| 106 | HTTP Header      | Metadata attached to HTTP messages             |
| 107 | HTTP Body        | Main content of request/response               |
| 108 | HTTP Status Code | Numeric result of an HTTP request              |
| 109 | 2xx              | Successful response                            |
| 110 | 3xx              | Redirection                                    |
| 111 | 4xx              | Client-side error                              |
| 112 | 5xx              | Server-side error                              |
| 113 | GET              | Retrieve resource                              |
| 114 | POST             | Create/process data                            |
| 115 | PUT              | Replace resource                               |
| 116 | PATCH            | Partially modify resource                      |
| 117 | DELETE           | Remove resource                                |
| 118 | Cookie           | Small piece of client-associated data          |
| 119 | Session          | Server-side representation of client state     |
| 120 | Stateless        | Server does not rely on previous request state |

---

# PART 7 — API & SERVICE COMMUNICATION

### 121–140

| #   | Term              | Simple meaning                                               |
| --- | ----------------- | ------------------------------------------------------------ |
| 121 | REST              | Resource-oriented HTTP API style                             |
| 122 | RESTful API       | API following REST principles                                |
| 123 | JSON              | Common structured data format                                |
| 124 | XML               | Markup-based data format                                     |
| 125 | API Contract      | Agreement defining API behavior/data                         |
| 126 | API Versioning    | Supporting different API versions                            |
| 127 | Pagination        | Returning large datasets in chunks                           |
| 128 | Cursor Pagination | Pagination using a position/cursor                           |
| 129 | Offset Pagination | Pagination using offset + limit                              |
| 130 | Idempotency       | Repeating operation produces same intended result            |
| 131 | Idempotency Key   | Identifier used to prevent duplicate operations              |
| 132 | Rate Limiting     | Restricting request frequency                                |
| 133 | Throttling        | Slowing/restricting clients under load                       |
| 134 | API Gateway       | Entry point routing requests to services                     |
| 135 | Reverse Proxy     | Server sitting in front of backend services                  |
| 136 | Forward Proxy     | Proxy acting on behalf of clients                            |
| 137 | Webhook           | Server sends event notification to another system            |
| 138 | WebSocket         | Persistent bidirectional connection                          |
| 139 | gRPC              | High-performance RPC framework                               |
| 140 | GraphQL           | API query language allowing clients to request specific data |

---

# PART 8 — ARCHITECTURE

### 141–160

| #   | Term                          | Simple meaning                                                 |
| --- | ----------------------------- | -------------------------------------------------------------- |
| 141 | Monolith                      | Application deployed as one unit                               |
| 142 | Modular Monolith              | Monolith internally divided into modules                       |
| 143 | Microservices                 | Independently deployable services                              |
| 144 | Service                       | Independently running application component                    |
| 145 | Service Boundary              | Boundary defining service responsibility                       |
| 146 | Domain                        | Business area represented by software                          |
| 147 | Bounded Context               | Explicit domain boundary from DDD                              |
| 148 | Domain-Driven Design          | Designing around business domains                              |
| 149 | Layered Architecture          | System organized into layers                                   |
| 150 | Hexagonal Architecture        | Core isolated from external adapters                           |
| 151 | Clean Architecture            | Dependency direction points toward business logic              |
| 152 | Event-Driven Architecture     | Components communicate using events                            |
| 153 | Client-Server                 | Clients request services from servers                          |
| 154 | Distributed System            | Components execute across multiple machines                    |
| 155 | Service-Oriented Architecture | Application composed of services                               |
| 156 | API Gateway Pattern           | Centralized API entry point                                    |
| 157 | Backend-for-Frontend          | Backend tailored to a particular client                        |
| 158 | Sidecar                       | Supporting component deployed alongside application            |
| 159 | Service Mesh                  | Infrastructure layer managing service-to-service communication |
| 160 | Process Isolation             | Separating processes so failures don't spread                  |

---

# PART 9 — SCALABILITY & PERFORMANCE

### 161–180

| #   | Term                     | Simple meaning                                |
| --- | ------------------------ | --------------------------------------------- |
| 161 | Scalability              | Ability to handle increasing workload         |
| 162 | Horizontal Scaling       | Add more instances                            |
| 163 | Vertical Scaling         | Add CPU/RAM to existing instance              |
| 164 | Auto Scaling             | Automatically change capacity                 |
| 165 | Load Balancing           | Distribute requests across instances          |
| 166 | Throughput               | Amount of work processed per unit time        |
| 167 | Latency                  | Time required for an operation                |
| 168 | P50                      | Median latency                                |
| 169 | P90                      | 90th percentile latency                       |
| 170 | P95                      | 95th percentile latency                       |
| 171 | P99                      | 99th percentile latency                       |
| 172 | P99.9                    | 99.9th percentile latency                     |
| 173 | Bottleneck               | Component limiting overall performance        |
| 174 | Concurrency              | Number of operations executing simultaneously |
| 175 | Parallelism              | Work executed simultaneously                  |
| 176 | Saturation               | Resource approaching/exceeding capacity       |
| 177 | Capacity                 | Maximum workload system can handle            |
| 178 | Benchmark                | Controlled performance measurement            |
| 179 | Profiling                | Finding where application spends resources    |
| 180 | Performance Optimization | Improving resource usage/response time        |

---

# PART 10 — CACHING & CONTENT DELIVERY

### 181–200

| #   | Term               | Simple meaning                                    |
| --- | ------------------ | ------------------------------------------------- |
| 181 | Cache              | Fast temporary storage                            |
| 182 | Cache Hit          | Requested data exists in cache                    |
| 183 | Cache Miss         | Data not found in cache                           |
| 184 | Cache Hit Ratio    | Percentage of requests served from cache          |
| 185 | TTL                | Time before cached data expires                   |
| 186 | Cache Invalidation | Removing/updating stale cache                     |
| 187 | Cache Aside        | Application manages cache reads/writes            |
| 188 | Write Through      | Write cache and backing store together            |
| 189 | Write Behind       | Cache writes asynchronously to storage            |
| 190 | Read Through       | Cache loads data automatically                    |
| 191 | Redis              | In-memory data store/cache                        |
| 192 | Memcached          | Distributed memory caching system                 |
| 193 | CDN                | Geographically distributed content cache          |
| 194 | Edge Location      | Location close to end users                       |
| 195 | Cache Stampede     | Many clients simultaneously refresh expired cache |
| 196 | Cache Warming      | Pre-populating cache                              |
| 197 | Distributed Cache  | Cache shared across machines                      |
| 198 | Local Cache        | Cache inside application process                  |
| 199 | Eviction           | Removing items from cache                         |
| 200 | LRU                | Least Recently Used eviction policy               |

---

# PART 11 — DATABASE FUNDAMENTALS

### 201–220

| #   | Term                | Simple meaning                                 |
| --- | ------------------- | ---------------------------------------------- |
| 201 | Database            | Persistent structured data store               |
| 202 | Relational Database | Database based on tables/relations             |
| 203 | SQL                 | Language for relational databases              |
| 204 | NoSQL               | Broad category of non-relational databases     |
| 205 | Table               | Collection of relational records               |
| 206 | Row                 | Individual record                              |
| 207 | Column              | Attribute of a record                          |
| 208 | Primary Key         | Unique identifier for a row                    |
| 209 | Foreign Key         | Reference to another table                     |
| 210 | Unique Constraint   | Ensures values are unique                      |
| 211 | Index               | Data structure accelerating lookups            |
| 212 | Composite Index     | Index involving multiple columns               |
| 213 | Query               | Request for database data                      |
| 214 | Query Plan          | Database's execution strategy                  |
| 215 | Full Table Scan     | Reading an entire table                        |
| 216 | Join                | Combining records from tables                  |
| 217 | Normalization       | Structuring data to reduce duplication         |
| 218 | Denormalization     | Intentionally duplicating data for performance |
| 219 | Transaction         | Atomic unit of database work                   |
| 220 | Connection Pool     | Reusable database connections                  |

---

# PART 12 — DATABASE SCALING & DISTRIBUTED DATA

### 221–240

| #   | Term                 | Simple meaning                                     |
| --- | -------------------- | -------------------------------------------------- |
| 221 | ACID                 | Atomicity, Consistency, Isolation, Durability      |
| 222 | Atomicity            | Transaction fully succeeds or fails                |
| 223 | Consistency          | Database remains valid according to rules          |
| 224 | Isolation            | Concurrent transactions don't improperly interfere |
| 225 | Durability           | Committed data survives failures                   |
| 226 | Isolation Level      | Degree of transaction isolation                    |
| 227 | Replication          | Maintaining copies of data                         |
| 228 | Primary/Leader       | Node accepting authoritative writes                |
| 229 | Replica/Follower     | Copy of primary data                               |
| 230 | Read Replica         | Replica primarily serving reads                    |
| 231 | Replication Lag      | Delay between primary and replica                  |
| 232 | Failover             | Switching to another instance after failure        |
| 233 | Sharding             | Splitting data across databases                    |
| 234 | Shard Key            | Field determining shard placement                  |
| 235 | Partitioning         | Splitting data into partitions                     |
| 236 | Data Partitioning    | Organizing data into manageable subsets            |
| 237 | Consistent Hashing   | Hashing technique minimizing data movement         |
| 238 | Distributed Database | Database distributed across machines               |
| 239 | Eventual Consistency | Replicas converge over time                        |
| 240 | Strong Consistency   | Reads reflect latest committed state               |

---

# PART 13 — KAFKA & EVENT STREAMING

### 241–260

These are particularly important for your Kafka/Flink work.

| #   | Term                 | Simple meaning                                   |
| --- | -------------------- | ------------------------------------------------ |
| 241 | Kafka                | Distributed event-streaming platform             |
| 242 | Broker               | Kafka server                                     |
| 243 | Cluster              | Group of Kafka brokers                           |
| 244 | Topic                | Named stream of events                           |
| 245 | Partition            | Ordered subdivision of a topic                   |
| 246 | Offset               | Position of record within partition              |
| 247 | Producer             | Application publishing records                   |
| 248 | Consumer             | Application reading records                      |
| 249 | Consumer Group       | Consumers cooperating to process a topic         |
| 250 | Partition Assignment | Mapping partitions to consumers                  |
| 251 | Consumer Lag         | Difference between produced and consumed offsets |
| 252 | Replication Factor   | Number of copies of a partition                  |
| 253 | Leader Replica       | Replica handling partition operations            |
| 254 | Follower Replica     | Replica copying leader data                      |
| 255 | ISR                  | In-Sync Replicas                                 |
| 256 | Retention            | How long Kafka keeps records                     |
| 257 | Replay               | Re-reading historical events                     |
| 258 | Event                | Record representing something that happened      |
| 259 | Event Schema         | Structure/contract of an event                   |
| 260 | Schema Registry      | Central system managing event schemas            |

---

# PART 14 — DISTRIBUTED SYSTEMS & MESSAGING

### 261–280

| #   | Term                  | Simple meaning                                   |
| --- | --------------------- | ------------------------------------------------ |
| 261 | Message Queue         | System for asynchronous messages                 |
| 262 | Pub/Sub               | Publishers send messages to subscribers          |
| 263 | Producer              | Component producing messages                     |
| 264 | Consumer              | Component consuming messages                     |
| 265 | Acknowledgment        | Confirmation that message was processed          |
| 266 | At-most-once          | Message processed zero or one time               |
| 267 | At-least-once         | Message delivered one or more times              |
| 268 | Exactly-once          | Intended processing semantics without duplicates |
| 269 | Duplicate Message     | Same message delivered multiple times            |
| 270 | Dead Letter Queue     | Storage for repeatedly failed messages           |
| 271 | Retry                 | Attempt operation again                          |
| 272 | Exponential Backoff   | Increasing delay between retries                 |
| 273 | Jitter                | Random variation added to retry delay            |
| 274 | Backpressure          | Slow producers when consumers can't keep up      |
| 275 | Async Processing      | Work executed without blocking caller            |
| 276 | Batch Processing      | Process data in groups                           |
| 277 | Stream Processing     | Process data continuously                        |
| 278 | Event Ordering        | Maintaining event sequence                       |
| 279 | Delivery Guarantee    | Rules governing message delivery                 |
| 280 | Message Deduplication | Detecting/removing duplicate messages            |

---

# PART 15 — RELIABILITY & FAILURE HANDLING

### 281–300

| #   | Term                     | Simple meaning                                 |
| --- | ------------------------ | ---------------------------------------------- |
| 281 | Reliability              | Ability to perform correctly over time         |
| 282 | Availability             | Percentage of time system is usable            |
| 283 | High Availability        | Architecture designed to minimize downtime     |
| 284 | Fault Tolerance          | Continue operating despite failures            |
| 285 | Redundancy               | Multiple components performing same role       |
| 286 | Single Point of Failure  | Component whose failure can stop system        |
| 287 | Failover                 | Move operation to backup                       |
| 288 | Circuit Breaker          | Stops calls to unhealthy dependency            |
| 289 | Timeout                  | Maximum time allowed for operation             |
| 290 | Retry                    | Repeat failed operation                        |
| 291 | Retry Storm              | Excessive retries overload a system            |
| 292 | Bulkhead                 | Isolate resources/failure domains              |
| 293 | Graceful Degradation     | Provide reduced functionality during failure   |
| 294 | Graceful Shutdown        | Stop accepting work and finish existing work   |
| 295 | Health Check             | Endpoint/mechanism reporting service health    |
| 296 | Liveness Probe           | Checks whether process should be restarted     |
| 297 | Readiness Probe          | Checks whether instance should receive traffic |
| 298 | Fail-Fast                | Quickly stop when operation cannot succeed     |
| 299 | Disaster Recovery        | Recovering after major failure                 |
| 300 | Recovery Point Objective | Maximum acceptable data loss window            |

---

# PART 16 — DISTRIBUTED TRANSACTIONS & CONSISTENCY

### 301–320

| #   | Term                    | Simple meaning                                           |
| --- | ----------------------- | -------------------------------------------------------- |
| 301 | Recovery Time Objective | Maximum acceptable recovery time                         |
| 302 | CAP Theorem             | Tradeoff involving consistency, availability, partitions |
| 303 | Network Partition       | Communication failure between distributed components     |
| 304 | Consensus               | Nodes agreeing on a value/state                          |
| 305 | Consensus Algorithm     | Algorithm for distributed agreement                      |
| 306 | Raft                    | Consensus algorithm                                      |
| 307 | Paxos                   | Family of consensus algorithms                           |
| 308 | Leader Election         | Selecting one node as leader                             |
| 309 | Distributed Lock        | Lock shared across machines                              |
| 310 | Split Brain             | Multiple nodes incorrectly believe they are leader       |
| 311 | Quorum                  | Minimum number of nodes required for agreement           |
| 312 | Majority                | More than half of participating nodes                    |
| 313 | Two-Phase Commit        | Distributed transaction protocol                         |
| 314 | Coordinator             | Component coordinating distributed transaction           |
| 315 | Prepare Phase           | Participants prepare transaction                         |
| 316 | Commit Phase            | Participants commit transaction                          |
| 317 | Saga Pattern            | Distributed workflow using local transactions            |
| 318 | Saga Orchestration      | Central coordinator controls Saga                        |
| 319 | Saga Choreography       | Services coordinate through events                       |
| 320 | Compensation            | Action that semantically undoes previous work            |

---

# PART 17 — ADVANCED DATA & ARCHITECTURE PATTERNS

### 321–340

| #   | Term                     | Simple meaning                                   |
| --- | ------------------------ | ------------------------------------------------ |
| 321 | CQRS                     | Separate read and write models                   |
| 322 | Event Sourcing           | Store state changes as events                    |
| 323 | Command                  | Request to change state                          |
| 324 | Query                    | Request to retrieve state                        |
| 325 | Event Store              | Persistent storage of events                     |
| 326 | Projection               | Read model generated from events                 |
| 327 | Materialized View        | Precomputed query result                         |
| 328 | Outbox Pattern           | Reliably publish DB changes as events            |
| 329 | Inbox Pattern            | Track consumed messages to prevent duplicates    |
| 330 | Transactional Outbox     | DB transaction + outbox record                   |
| 331 | Change Data Capture      | Capture database changes as events               |
| 332 | CDC                      | Abbreviation for Change Data Capture             |
| 333 | Event Replay             | Reprocess historical events                      |
| 334 | Event Versioning         | Managing evolution of event formats              |
| 335 | Schema Evolution         | Changing schemas while maintaining compatibility |
| 336 | Consumer-Driven Contract | Consumer defines expected provider behavior      |
| 337 | Anti-Corruption Layer    | Protect one domain from another domain's model   |
| 338 | Strangler Pattern        | Gradually replace legacy system                  |
| 339 | Ambassador Pattern       | Proxy handling communication for an application  |
| 340 | Adapter Pattern          | Converts one interface into another              |

---

# PART 18 — OBSERVABILITY

### 341–360

| #   | Term                | Simple meaning                                     |
| --- | ------------------- | -------------------------------------------------- |
| 341 | Observability       | Ability to understand system behavior from outputs |
| 342 | Monitoring          | Tracking system health/performance                 |
| 343 | Metric              | Numeric measurement                                |
| 344 | Log                 | Recorded application/system event                  |
| 345 | Trace               | End-to-end request journey                         |
| 346 | Span                | Individual operation inside a trace                |
| 347 | Trace ID            | Identifier shared across a distributed request     |
| 348 | Correlation ID      | Identifier connecting related operations/logs      |
| 349 | Distributed Tracing | Tracing requests across services                   |
| 350 | Log Aggregation     | Collecting logs centrally                          |
| 351 | Dashboard           | Visual representation of system data               |
| 352 | Alert               | Notification triggered by condition                |
| 353 | SLI                 | Service Level Indicator                            |
| 354 | SLO                 | Service Level Objective                            |
| 355 | SLA                 | Service Level Agreement                            |
| 356 | Error Budget        | Allowed amount of unreliability                    |
| 357 | Incident            | Production event requiring response                |
| 358 | MTTR                | Mean Time To Recovery/Repair                       |
| 359 | MTTF                | Mean Time To Failure                               |
| 360 | OpenTelemetry       | Standard/tooling ecosystem for telemetry           |

---

# But we're not finished

The **360 terms above are your core master vocabulary**.

However, because you're working toward senior/staff-level distributed-data engineering, I would add another layer specifically for the technologies you're learning.

## Your "must-master" vocabulary

For **your particular engineering path**, I'd put these at the top of your study list:

### Distributed systems

```text
Distributed System
CAP
Consistency
Availability
Partition Tolerance
Quorum
Consensus
Raft
Leader Election
Split Brain
Replication
Failover
Fault Tolerance
Idempotency
Exactly Once
At Least Once
At Most Once
Backpressure
Retry
Timeout
Circuit Breaker
Bulkhead
Saga
Outbox
CDC
```

### Kafka

```text
Broker
Cluster
Topic
Partition
Offset
Producer
Consumer
Consumer Group
Partition Assignment
Consumer Lag
Replication Factor
Leader Replica
Follower Replica
ISR
Retention
Compaction
Replay
Ordering
Schema Registry
Schema Evolution
```

### Flink

You should next learn:

```text
Job
JobManager
TaskManager
Task Slot
Operator
Subtask
Parallelism
Operator Chain
Task Chain
Source
Sink
Transformation
Keyed Stream
State
Operator State
Keyed State
Checkpoint
Savepoint
Watermark
Event Time
Processing Time
Ingestion Time
Window
Tumbling Window
Sliding Window
Session Window
Timer
State Backend
Checkpoint Barrier
Barrier Alignment
Unaligned Checkpoint
Exactly-Once Processing
Two-Phase Commit Sink
Backpressure
Rescaling
Restart Strategy
Failover Region
```

### Spark

```text
Driver
Executor
Cluster Manager
Application
Job
Stage
Task
RDD
DataFrame
Dataset
Transformation
Action
Lazy Evaluation
DAG
Shuffle
Partition
Broadcast
Broadcast Join
Sort-Merge Join
Hash Join
Skew
Data Skew
Spill
Caching
Persistence
Checkpoint
Catalyst Optimizer
Whole-Stage Code Generation
Adaptive Query Execution
```

### Iceberg / Lakehouse

```text
Data Lake
Data Warehouse
Data Lakehouse
Table Format
Iceberg
Snapshot
Manifest
Manifest List
Metadata File
Partition Spec
Hidden Partitioning
Schema Evolution
Partition Evolution
Time Travel
Snapshot Isolation
Compaction
Small Files
Delete File
Position Delete
Equality Delete
Merge-on-Read
Copy-on-Write
Catalog
Catalog Service
Table Snapshot
Vacuum
Retention
```

### ClickHouse

```text
MergeTree
ReplicatedMergeTree
Partition Key
Primary Key
ORDER BY
Data Part
Merge
Mutation
TTL
Materialized View
Distributed Table
Shard
Replica
Distributed Query
Columnar Storage
Compression
Granule
Skip Index
Projection
Partition Pruning
Predicate Pushdown
```

### Kubernetes

```text
Cluster
Node
Pod
Container
Deployment
ReplicaSet
StatefulSet
DaemonSet
Job
CronJob
Service
Ingress
ConfigMap
Secret
Namespace
Label
Selector
Annotation
Volume
PersistentVolume
PersistentVolumeClaim
StorageClass
Resource Request
Resource Limit
CPU Limit
Memory Limit
Liveness Probe
Readiness Probe
Startup Probe
Horizontal Pod Autoscaler
Vertical Pod Autoscaler
Rolling Update
Deployment Strategy
```

---

# The terminology hierarchy I recommend for you

Don't study these 360 terms randomly.

Use this hierarchy:

```text
                    SOFTWARE ENGINEER
                           │
          ┌────────────────┴────────────────┐
          │                                 │
      APPLICATION                       DATA
          │                                 │
    Java / Spring                    SQL / NoSQL
    REST / APIs                      Kafka
    Git / Testing                    Spark
          │                           Flink
          │                           Iceberg
          │                           ClickHouse
          │
          ▼
                  DISTRIBUTED SYSTEMS
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    Scalability       Reliability      Consistency
        │                 │                 │
   Load Balance       Retry             CAP
   Caching            Timeout           Consensus
   Sharding           Circuit Breaker   Replication
   Partitioning       Bulkhead          Quorum
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                    CLOUD / K8S
                          │
                    Containers
                    Kubernetes
                    Networking
                    Autoscaling
                    Observability
                    CI/CD
                          │
                          ▼
                 SYSTEM ARCHITECTURE
                          │
             ┌────────────┼────────────┐
             │            │            │
           CQRS         Saga       Event Sourcing
           Outbox        CDC        Event Driven
           DDD           Mesh       Microservices
             │            │            │
             └────────────┼────────────┘
                          ▼
                 STAFF / ARCHITECT
                          │
                  Trade-offs
                  Failure modes
                  Capacity
                  Cost
                  SLOs
                  Consistency
                  Architecture
                  Evolution
```

# What "knowing" a term should mean

Don't memorize:

> **Backpressure = mechanism that prevents overwhelming downstream systems.**

Instead, you should eventually be able to say:

> "Our Kafka consumers are processing slower than producers. Consumer lag is increasing. Flink is experiencing backpressure downstream, so increasing Kafka partitions alone may not solve the problem. I need to identify the bottleneck—CPU, network, state access, sink throughput, or checkpointing—and scale the appropriate operator."

That's the difference between **knowing terminology** and **thinking like a senior engineer**.

---

# Your target

I would make this your progression:

| Stage       | Goal                                     |
| ----------- | ---------------------------------------- |
| **Stage 1** | 100 fundamental terms                    |
| **Stage 2** | 200 application/backend terms            |
| **Stage 3** | 300 distributed-system terms             |
| **Stage 4** | 360+ architecture/data terms             |
| **Stage 5** | Explain every term using a real system   |
| **Stage 6** | Design systems using the terminology     |
| **Stage 7** | Discuss trade-offs like a Staff Engineer |

And for your current Kafka/Flink/Spark/Iceberg/ClickHouse direction, **Stages 3–7 are where the biggest career payoff will be**.

If we continue this as a course, I would take these **360 terms one by one**, but group related terms together—for example **Partition → Parallelism → Consumer Group → Task Slot → Subtask → Shuffle → Backpressure**—so you learn how the concepts connect rather than memorizing a glossary.
