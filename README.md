# Hardcore Senior/Staff System Design, SRE & AI Systems Master Plan
### 40-Week Date-by-Date Execution Blueprint (November 24, 2026 – August 31, 2027)
**Target Profile:** T-Shaped Staff Software Engineer (Infrastructure, SRE & Big Data Foundation + Deep Enterprise AI/LLM Infrastructure Specialization)
**Commitment:** 3 to 4 Hours / Day on Weekdays (~20 hrs) + 4 to 6 Hours on Weekends (~25–26 Hours / Week Total)

---

## The Staff/Principal Engineering Philosophy
At mid-level, you know the components. At senior, you know the failure modes. **At Staff level, you are evaluated on:**
1. **Trade-off Quantification:** Justifying *why NOT* to use a trendy solution (e.g. monolithic Postgres over distributed NoSQL; evaluating GPU memory bandwidth vs compute).
2. **Failure Dominance & SRE:** Designing systems that operate gracefully under partial network partitions, disk corruption, split-brain, and model degradation.
3. **Economics & Capacity:** Translating architectural choices into hard dollars (GPU FLOPS per dollar, RAM bounds for vector search, network egress costs).

---

## Daily Schedule Structure (3 to 4 Hours / Day)

```
 DAILY STUDY ALLOCATION (3.5 Hours Average)
 ─────────────────────────────────────────────────────────────────────────────
 • Block 1 (60 Mins - Morning / Focus):  Deep Theory Reading (Book / Whitepaper)
 • Block 2 (60 Mins - Evening):          Tool Architecture & Internals Dissection
 • Block 3 (60 Mins - Night):            Staff System Design Drill / Trade-off Math
 • Block 4 (30 Mins - Review):           Failure Mode & SRE Recovery Note Synthesis
 • Weekend Deep Dive (3–4 Hours):        Hands-on Lab Sandbox (Kafka, Spark, vLLM, Flink)
```

---

## Phase 1: Foundations, Networking, Load Balancing & SRE Fundamentals (Weeks 1 – 4)

### Week 1: Nov 24 – Nov 29, 2026 | Modern Networking, IPC & Load Balancing Internals
* **Verified Readings & Book Chapters:**
  * UDS Introduction: Anatomy of a Distributed System (PDF pp. 13–20 / Book pp. 12–19)
  * UDS Ch. 2: Reliable links — TCP 3-Way Handshake, Flow Control, Congestion Control (PDF pp. 25–30 / Book pp. 24–29)
  * UDS Ch. 3: Secure links — TLS Handshake, Symmetric/Asymmetric Encryption, HMAC Integrity (PDF pp. 32–36 / Book pp. 31–35)
  * UDS Ch. 14: Duplication -> Section: Network load balancing — VIPs, L4 vs L7, DNS & Geo Balancing (PDF pp. 137–146 / Book pp. 136–145)
  * Google Paper: Maglev: A Fast and Reliable Software Network Load Balancer (Eisenbud et al., 2016 — Sec 3 Architecture & Sec 5 Lookup Table)
* **Deep Dive Topics:**
  * Transport: TCP handshake, TCP slow start, head-of-line blocking, UDP, QUIC (HTTP/3).
  * Layer 4 vs. Layer 7 Load Balancing: IP/Port hashing vs. HTTP parsing.
  * Load balancing tools: NGINX, HAProxy, Envoy Proxy architecture (control plane vs. data plane, thread models).
  * Consistent Hashing on load balancers, connection draining, TLS termination, Session persistence trade-offs.
* **Staff Drill:** Architect a multi-tiered ingress network handling 2M requests/sec using Maglev (L4 ECMP) routing to Envoy (L7) with zero-downtime certificate rotation.

### Week 2: Nov 30 – Dec 06, 2026 | DNS, Edge Caching, API Gateways & Reverse Proxies
* **Verified Readings & Book Chapters:**
  * UDS Ch. 4: Discovery — DNS Resolution & Service Discovery (PDF pp. 37–40 / Book pp. 36–39)
  * UDS Ch. 5: APIs — HTTP, Request Methods, Idempotency, OpenAPI (PDF pp. 41–51 / Book pp. 40–50)
  * UDS Ch. 12: Functional decomposition -> Section: API gateway (PDF pp. 112–118 / Book pp. 111–117)
  * UDS Ch. 14: Duplication -> Section: Caching (PDF pp. 154–157 / Book pp. 153–156)
  * Cloudflare Architecture Blog: How Cloudflare Works at the Edge
* **Deep Dive Topics:**
  * DNS resolution: Anycast routing, GeoDNS, BGP routing, TTL caching anomalies.
  * CDN internals: Cache eviction (LRU, LFU, 2-Tier), Cache-Control headers, byte-range requests for video.
  * API Gateway vs. Reverse Proxy: Authentication offloading, rate limiting, request collapsing/deduplication.
* **Staff Drill:** Design a globally distributed Edge API Gateway that routes users to the nearest regional data center with sub-20ms latency and handles sudden BGP route leaks.

### Week 3: Dec 07 – Dec 13, 2026 | Site Reliability Engineering (SRE): Metrics, SLOs & Error Budgets
* **Verified Readings & Book Chapters:**
  * UDS Ch. 20: Monitoring — Metrics (Counters/Gauges/Histograms), SLIs, SLOs, Alerts (PDF pp. 209–225 / Book pp. 208–224)
  * UDS Ch. 21: Observability — Structured Logs, Distributed Tracing & Span IDs (PDF pp. 227–234 / Book pp. 226–233)
  * The Google SRE Book — Ch. 3: Monitoring Distributed Systems & The 4 Golden Signals
  * The Google SRE Book — Ch. 4: Service Level Objectives & Error Budget Math
  * The Google SRE Book — Ch. 5: Eliminating Toil
* **Deep Dive Topics:**
  * SLI vs. SLO vs. SLA: How to pick meaningful SLIs (Latency, Traffic, Errors, Saturation - The 4 Golden Signals).
  * Error Budget Math: Calculating outage burn rates (1h burn, 6h burn, multi-window multi-burn-rate alerts).
  * Capacity Planning: Predicting resource exhaustion using utilization curves and headroom forecasting.
* **Staff Drill:** Define the complete SRE contract (SLIs, SLOs, Error Budget Policy) for a tier-0 Payment Ingestion Service targeting 99.99% availability.

### Week 4: Dec 14 – Dec 20, 2026 | Resilience Engineering: Chaos, Circuit Breakers & Backpressure
* **Verified Readings & Book Chapters:**
  * UDS Ch. 15: Common failure causes — SPOF, Network, Unexpected Load, Cascades (PDF pp. 162–168 / Book pp. 161–167)
  * UDS Ch. 16: Downstream resiliency — Timeouts, Exponential Backoff, Circuit Breakers (PDF pp. 169–177 / Book pp. 168–176)
  * UDS Ch. 17: Upstream resiliency — Load Shedding, Rate-Limiting, Bulkheads, Health Checks (PDF pp. 178–191 / Book pp. 177–190)
  * Netflix Technology Blog: Chaos Engineering Upgraded
* **Deep Dive Topics:**
  * Cascading failures: Thread pool exhaustion, retry storms with exponential backoff & jitter.
  * Circuit Breakers (Hystrix / Resilience4j pattern): Closed, Open, Half-Open states.
  * Backpressure handling: Reactive streams, TCP flow control window, queue-drop strategies (CoDel algorithm).
  * Chaos Engineering: Fault injection, Latency injection, Chaos Monkey at scale.
* **Staff Drill:** Design a resilient microservice communication mesh that prevents cascading grid failures when a core downstream database's latency spikes by 500ms.

---

## Phase 2: Distributed Systems Core & Storage Engine Internals (Weeks 5 – 9)

### Week 5: Dec 21 – Dec 27, 2026 | Clocks, Time & Distributed Coordination
* **Verified Readings & Book Chapters:**
  * UDS Ch. 6: System models — Timing Assumptions, Crash-stop vs Byzantine (PDF pp. 56–58 / Book pp. 55–57)
  * UDS Ch. 7: Failure detection — Heartbeats, Ping-ack & Suspicion Timers (PDF pp. 59–60 / Book pp. 58–59)
  * UDS Ch. 8: Time — NTP Drift, Lamport Timestamps, Vector Clocks (PDF pp. 61–65 / Book pp. 60–64)
  * UDS Ch. 9: Leader election — Raft Election & Heartbeats (PDF pp. 67–71 / Book pp. 66–70)
  * DDIA Ch. 8: The Trouble with Distributed Systems (Unreliable Networks & Clocks)
  * Google Paper: Spanner: Google Globally-Distributed Database (Section 5: TrueTime API)
* **Deep Dive Topics:**
  * Physical clocks: NTP drift, leap seconds, monotonically increasing clocks (`CLOCK_MONOTONIC`).
  * Logical clocks: Lamport Timestamps, Vector Clocks (tracking causality and concurrency).
  * Google TrueTime API: Atomic clocks + GPS receivers, bounded uncertainty window ($\epsilon$), wait-out-the-uncertainty.
* **Staff Drill:** Explain how Google Spanner achieves external consistency (linearizability) across global regions without locking out reads.

### Week 6: Dec 28, 2026 – Jan 03, 2027 | Storage Engine Internals: B-Trees vs. LSM-Trees
* **Verified Readings & Book Chapters:**
  * DDIA Ch. 3: Storage and Retrieval — Hash Indexes, SSTables, LSM-Trees, B-Trees
  * Database Internals (Alex Petrov) — Part I: Storage Engine Architecture & B-Tree Basics
* **Deep Dive Topics:**
  * B-Trees: Page layout, write-ahead logging (WAL), dirty pages, random I/O bottlenecks.
  * LSM-Trees: MemTable (SkipLists), Immutable SSTables, Write Amplification, Read Amplification, Space Amplification.
  * Compaction algorithms: Size-Tiered vs. Leveled Compaction (RocksDB internals). Bloom filters for read acceleration.
* **Staff Drill:** Deep dive into RocksDB. Explain why an LSM tree can ingest 100k writes/sec on NVMe while MySQL InnoDB B-Tree stalls on disk writes.

### Week 7: Jan 04 – Jan 10, 2027 | Columnar Storage Engines & Analytical Formats
* **Verified Readings & Book Chapters:**
  * DDIA Ch. 3: Storage and Retrieval — Column-Oriented Storage & Analytics (OLAP)
  * ClickHouse Architecture Documentation: MergeTree Engine Family & Vectorized Query Execution
* **Deep Dive Topics:**
  * Columnar vs. Row-oriented: When to use which (OLTP vs. OLAP).
  * Formats: Apache Parquet (Dremel encoding, dictionary encoding, bit packing, run-length encoding), Apache ORC.
  * Vectorized Execution: SIMD instructions, processing data in column batches in CPU cache.
* **Staff Drill:** Design an analytical storage engine that ingests 1 Billion telemetry logs per day and runs sub-second aggregation queries over date ranges without indexes.

### Week 8: Jan 11 – Jan 17, 2027 | Serialization, Protocols & Zero-Copy Reads
* **Verified Readings & Book Chapters:**
  * DDIA Ch. 4: Encoding and Evolution — Protocol Buffers, Thrift, Avro & Schema Evolution
  * Linux Kernel Docs: sendfile(2), splice(2) & Zero-Copy Network I/O
* **Deep Dive Topics:**
  * Binary serialization: Protocol Buffers wire format (Varints, Tag-Length-Value encoding), Apache Avro, FlatBuffers.
  * Schema Evolution: Forward vs. Backward compatibility, Confluent Schema Registry.
  * Operating System Zero-Copy: Kernel space vs. User space memory copies, DMA (Direct Memory Access), `sendfile` syscall.
* **Staff Drill:** Explain exactly how Apache Kafka utilizes OS Page Cache and `sendfile` to bypass Java heap garbage collection and saturate 10Gbps NICs.

### Week 9: Jan 18 – Jan 24, 2027 | Distributed Key-Value Engines & Dynamo Architecture
* **Verified Readings & Book Chapters:**
  * Amazon Dynamo Paper: Dynamo: Amazon Highly Available Key-value Store (DeCandia et al., 2007 — Sec 4-6)
  * DDIA Ch. 5: Replication -> Leaderless Replication & Sloppy Quorums
* **Deep Dive Topics:**
  * Quorum math: $N, R, W$ (Strong consistency when $R + W > N$).
  * Sloppy Quorums and Hinted Handoff.
  * Read Repair vs. Anti-Entropy processes using Merkle Trees.
  * Gossip protocols for node membership and failure detection.
* **Staff Drill:** Design a globally distributed Key-Value store (Cassandra / Dynamo-style) tuned for AP (Availability + Partition Tolerance) with tunable consistency per query.

---

## Phase 3: Replication, Partitioning, Consensus & Decentralized Systems (Weeks 10 – 14)

### Week 10: Jan 25 – Jan 31, 2027 | Replication Topologies & CRDTs
* **Verified Readings & Book Chapters:**
  * UDS Ch. 10: Replication & Consistency Models (PDF pp. 73–84 / Book pp. 74–83)
  * UDS Ch. 14: Duplication -> Section: Replication (PDF pp. 147–153 / Book pp. 146–152)
  * DDIA Ch. 5: Replication — Single-Leader, Multi-Leader & Leaderless Replication
  * Marc Shapiro et al. Paper: Conflict-free Replicated Data Types (CRDTs)
* **Deep Dive Topics:**
  * Single-leader vs. Multi-leader vs. Leaderless replication.
  * Replication lag anomalies: Read-your-own-writes, Monotonic reads, Consistent prefix reads.
  * Conflict resolution: Last-Write-Wins (LWW) data loss risks.
  * CRDTs: Operation-based vs. State-based CRDTs, PN-Counters, G-Sets, OR-Sets for collaborative editing.
* **Staff Drill:** Design the data sync engine for Google Docs / Figma that allows offline edits from 5 collaborators and automatically merges state without data loss.

### Week 11: Feb 01 – Feb 07, 2027 | Partitioning, Sharding & Rebalancing
* **Verified Readings & Book Chapters:**
  * UDS Ch. 13: Partitioning — Sharding Strategies & Dynamic Rebalancing (PDF pp. 130–136 / Book pp. 129–135)
  * DDIA Ch. 6: Partitioning — Key Range vs Hash Partitioning, Request Routing
* **Deep Dive Topics:**
  * Partitioning strategies: Key-range vs. Hash partitioning.
  * Secondary indexes: Document-partitioned (local index) vs. Term-partitioned (global index).
  * Shard rebalancing: Fixed partitions, dynamic partitioning (splitting/merging), proportional partitioning.
  * Mitigating hot-spot shards: Salted keys, dynamic load balancing.
* **Staff Drill:** Architect a database sharding strategy for an e-commerce platform handling Black Friday flash sales where 100,000 users query the exact same product SKU simultaneously.

### Week 12: Feb 08 – Feb 14, 2027 | Distributed Transactions & The Saga Pattern
* **Verified Readings & Book Chapters:**
  * UDS Ch. 11: Transactions — ACID, Isolation, 2PC, Sagas (PDF pp. 85–100 / Book pp. 84–99)
  * DDIA Ch. 7: Transactions — Isolation Levels & Serializable Snapshot Isolation
  * Software Architecture: The Hard Parts (Ford et al.) — Distributed Transactions & Sagas
* **Deep Dive Topics:**
  * Two-Phase Commit (2PC): Prepare phase, Commit phase, Coordinator failure blocking mode.
  * Three-Phase Commit (3PC) limitations.
  * Saga Pattern: Orchestration-based vs. Choreography-based Sagas.
  * The Outbox Pattern + Change Data Capture (Debezium) for reliable event publishing.
* **Staff Drill:** Design an end-to-end trip booking transaction across Flight, Hotel, and Car Rental microservices using an Orchestrated Saga with idempotent compensation.

### Week 13: Feb 15 – Feb 21, 2027 | Distributed Consensus: Raft vs. Paxos Internals
* **Verified Readings & Book Chapters:**
  * UDS Ch. 9 & 10: Consensus & Leader Election (PDF pp. 67–84 / Book pp. 66–83)
  * DDIA Ch. 9: Consistency and Consensus — Linearizability, Total Order Broadcast, Raft vs Paxos
  * Ongaro & Ousterhout Paper: In Search of an Understandable Consensus Algorithm (Raft)
* **Deep Dive Topics:**
  * Consensus requirements: Agreement, Integrity, Validity, Termination.
  * Raft protocol mechanics: Leader election, Randomized election timeouts, Log replication, Safety invariants, Joint Consensus cluster membership changes.
  * Paxos fundamentals: Multi-Paxos, Synod, Proposers, Acceptors, Learners.
* **Staff Drill:** Walk through a 5-node Raft cluster split into two partitions ($3$ nodes and $2$ nodes). Prove mathematically and mechanically why the minority partition can never commit a write.

### Week 14: Feb 22 – Feb 28, 2027 | Decentralized Systems, P2P Networks & BFT
* **Verified Readings & Book Chapters:**
  * Maymounkov & Mazières Paper: Kademlia: A Peer-to-Peer Information System Based on the XOR Metric
  * Castro & Liskov Paper: Practical Byzantine Fault Tolerance (PBFT)
* **Deep Dive Topics:**
  * Distributed Hash Tables (DHT): Kademlia XOR metric, routing tables ($k$-buckets), lookups ($O(\log N)$).
  * Cryptographic data structures: Merkle Trees, Merkle DAGs (IPFS, Git internals).
  * Byzantine Fault Tolerance: Handling malicious or arbitrary node failures ($3f + 1$ nodes required to tolerate $f$ Byzantine nodes).
* **Staff Drill:** Design a decentralized peer-to-peer file distribution network (BitTorrent style) that distributes a 20GB OS image to 100,000 servers in under 5 minutes without crushing central servers.

---

## Phase 4: Big Data Infrastructure & Offline Data Pipelines (Weeks 15 – 19)

### Week 15: Mar 01 – Mar 07, 2027 | Hadoop, HDFS & Distributed Storage Foundations
* **Verified Readings & Book Chapters:**
  * Google Paper: The Google File System (GFS) (Ghemawat et al., 2003)
  * Apache HDFS Architecture Guide: NameNode, DataNode & Block Placement Policy
* **Deep Dive Topics:**
  * HDFS Architecture: NameNode (in-memory namespace, EditLog, FsImage) vs. DataNodes.
  * Block placement policy (Rack awareness, 128MB/256MB blocks, replication pipelines).
  * NameNode High Availability: Active/Standby NameNodes using QJM (Quorum Journal Manager) and ZooKeeper.
  * MapReduce paradigm: Map, Shuffle & Sort, Reduce phase mechanics.
* **Staff Drill:** Explain what happens when a DataNode fails during a client write pipeline in HDFS. How does the client and NameNode coordinate to ensure lease recovery and block integrity?

### Week 16: Mar 08 – Mar 14, 2027 | Apache Spark Core: Architecture & Catalyst Optimizer
* **Verified Readings & Book Chapters:**
  * Spark: The Definitive Guide (Chambers & Zaharia) — Ch. 1–5: Architecture & Catalyst Optimizer
  * Zaharia et al. Paper: Resilient Distributed Datasets (RDDs)
* **Deep Dive Topics:**
  * Spark Architecture: Driver, Cluster Manager (YARN/K8s), Executors, Cores.
  * RDD fundamentals: Lineage graphs, lazy evaluation, narrow vs. wide dependencies.
  * Catalyst Optimizer internals: Analysis, Logical Plan optimization, Physical Planning, Code generation (Whole-Stage Code Generation).
* **Staff Drill:** Write a detailed breakdown of a Spark job processing a 10TB dataset. Trace the query from SQL text to Catalyst Logical Plan, Physical Plan, and RDD DAG execution.

### Week 17: Mar 15 – Mar 21, 2027 | Spark Performance Tuning & Distributed Shuffles
* **Verified Readings & Book Chapters:**
  * Spark: The Definitive Guide — Ch. 12 & 13: Spark Tuning, Shuffles & Memory Management
* **Deep Dive Topics:**
  * The Spark Shuffle: Shuffle write, Shuffle fetch, spill to disk, memory pressure.
  * Memory Management: Unified Memory Manager (Storage Memory vs. Execution Memory fraction).
  * Mitigating Data Skew: Salting skewed keys, Broadcast Hash Join (`broadcast()`), Adaptive Query Execution (AQE).
* **Staff Drill:** Diagnose and resolve an out-of-memory (OOM) error caused by a severe 99:1 data skew on a join key in a 5TB Spark pipeline.

### Week 18: Mar 22 – Mar 28, 2027 | Modern Lakehouse Engines: Iceberg, Delta Lake & Hudi
* **Verified Readings & Book Chapters:**
  * Apache Iceberg: The Definitive Guide (O'Reilly) — Ch. 3 & 4: Table Format & Snapshot Isolation
  * Databricks Paper: Delta Lake: High-Performance ACID Table Storage over Cloud Object Stores
* **Deep Dive Topics:**
  * Why Hive Metastore failed at modern scale: Directory-based partition bottlenecks.
  * Apache Iceberg Architecture: Snapshot metadata tree (Iceberg Catalog → Metadata File → Manifest List → Manifest Files → Data Files).
  * ACID transactions on S3/GCS: Optimistic Concurrency Control (OCC), hidden partitioning, schema evolution, time-travel queries.
  * Copy-on-Write (CoW) vs. Merge-on-Read (MoR) performance trade-offs.
* **Staff Drill:** Architect a petabyte-scale Data Lakehouse migration from Hive to Apache Iceberg on AWS S3, guaranteeing zero downtime and supporting simultaneous streaming writes and batch reads.

### Week 19: Mar 29 – Apr 04, 2027 | Offline Data Orchestration & Data Quality
* **Verified Readings & Book Chapters:**
  * Fundamentals of Data Engineering (Reis & Housley) — Ch. 7 (Orchestration) & Ch. 8 (Data Quality)
  * Apache Airflow & Dagster Core Architecture Docs
* **Deep Dive Topics:**
  * Workflow orchestrators: Directed Acyclic Graphs (DAGs), Airflow task lifecycle vs. Dagster software-defined assets.
  * Idempotency in batch data: Partition overwriting vs. append-only, handling backfills for past 2 years.
  * Data Quality at Scale: Great Expectations, Soda Core, anomaly detection in pipeline row counts and distributions.
* **Staff Drill:** Design an automated batch backfilling pipeline that can re-process 24 months of raw clickstream data without exhausting compute budgets or producing duplicate records in downstream tables.

---

## Phase 5: Real-Time Streaming Systems & Online Pipelines (Weeks 20 – 24)

### Week 20: Apr 05 – Apr 11, 2027 | Apache Kafka Internals & Broker Storage
* **Verified Readings & Book Chapters:**
  * Kafka: The Definitive Guide (2nd Ed) — Ch. 3 (Producers), Ch. 4 (Consumers), Ch. 6 (Reliability)
  * Jay Kreps et al. Paper: Kafka: A Distributed Messaging System for Log Processing
* **Deep Dive Topics:**
  * Partition storage: Segment files (`.log`), Index files (`.index`), Time index files (`.timeindex`).
  * Producer internals: RecordAccumulator, Batching (`linger.ms`, `batch.size`), Compression (Snappy/zstd).
  * Consumer Group Rebalance Protocol: Eager rebalance vs. Cooperative Sticky assignor.
* **Staff Drill:** Design a Kafka deployment ingesting 500k messages/sec. Calculate broker disk I/O, network bandwidth, retention policies, and partition count strategy.

### Week 21: Apr 12 – Apr 18, 2027 | Kafka Exactly-Once Semantics (EOS) Deep Dive
* **Verified Readings & Book Chapters:**
  * Confluent Technical Paper: Transactions in Apache Kafka (Wang et al.)
  * Kafka KIP-98: Exactly Once Delivery and Transactional Messaging
* **Deep Dive Topics:**
  * Idempotent Producer: Producer ID (PID), Sequence numbers, deduplication at broker partition level.
  * Kafka Transactions: Transaction Coordinator, `__transaction_state` topic, Two-Phase Commit over log markers.
  * Read Committed isolation level: Last Stable Offset (LSO) vs. High Watermark (HW).
* **Staff Drill:** Walk through how Kafka EOS guarantees that a consume-transform-produce cycle across two different Kafka topics never processes a message twice during broker crashes.

### Week 22: Apr 19 – Apr 25, 2027 | Apache Flink: Stream Processing & Event Time
* **Verified Readings & Book Chapters:**
  * Streaming Systems (Tyler Akidau et al.) — Ch. 1, 2 & 3: Streaming 101 & The Dataflow Model
  * Apache Flink Architecture Docs: Timestamps, Watermarks & Windowing
* **Deep Dive Topics:**
  * Event Time vs. Processing Time vs. Ingestion Time.
  * Watermarks: Punctuated vs. Periodic watermarks, out-of-order event handling, allowed lateness.
  * Windowing mechanics: Tumbling, Sliding, Session windows.
* **Staff Drill:** Design a real-time sliding window aggregation (e.g. "Calculate 1-hour average transaction volume sliding every 10 seconds") that handles mobile network delays of up to 5 minutes correctly.

### Week 23: Apr 26 – May 02, 2027 | Flink State Management & Distributed Snapshots
* **Verified Readings & Book Chapters:**
  * Carbone et al. Paper: Lightweight Asynchronous Snapshots for Distributed Dataflows (Flink Checkpoints)
  * Apache Flink Docs: State Backends (RocksDB), Checkpoints & Savepoints
* **Deep Dive Topics:**
  * State primitives: ValueState, ListState, MapState.
  * State Backends: MemoryStateBackend vs. FileSystemStateBackend vs. EmbeddedRocksDBStateBackend.
  * Chandy-Lamport snapshotting: Checkpoint barriers injected into streams, asynchronous state persistence to S3, two-phase commit sink integration.
* **Staff Drill:** Architect a Flink stream processing cluster handling 200GB of state. Explain what happens when a task manager node dies and how it recovers to the exact microsecond state.

### Week 24: May 03 – May 09, 2027 | Streaming Architecture: Lambda vs. Kappa & Feature Stores
* **Verified Readings & Book Chapters:**
  * Jay Kreps: Questioning the Lambda Architecture (Confluent)
  * Feast / Tecton Architecture Whitepapers: Point-in-Time Correct Feature Serving
* **Deep Dive Topics:**
  * Lambda Architecture (Batch layer + Speed layer + Serving layer) vs. Kappa Architecture (Single stream-first engine).
  * Real-Time Feature Store (Feast / Hopsworks): Point-in-time joins, dual storage (Redis for online low-latency inference + Iceberg/Parquet for offline training).
* **Staff Drill:** Architect an online/offline unified Feature Store serving 10,000 requests/sec with < 8ms P99 latency while guaranteeing zero data leakage for offline model training.

---

## Phase 6: High-Scale Distributed Platforms (Weeks 25 – 28)

### Week 25: May 10 – May 16, 2027 | Distributed Caching: Stampedes, Eviction & Topologies
* **Verified Readings & Book Chapters:**
  * System Design Interview Vol 2 (Alex Xu) — Chapter 11: Distributed Message Queue / Cache
  * Redis Cluster Specification: Hash Slots, Gossip Protocol & Node Failover
* **Deep Dive Topics:**
  * Redis Cluster: 16,384 hash slots, gossip protocol, master-replica failover.
  * Cache Stampede / Thundering Herd solutions: Mutex locking, XFetch probabilistic early expiration algorithm.
  * Eviction policies: LRU, LFU, Volatile-TTL, Clock-Pro algorithm.
* **Staff Drill:** Design a distributed caching layer holding 40 TB of active data that guarantees immunity against cache stampedes when a viral post expires from cache.

### Week 26: May 17 – May 23, 2027 | Payment Ledgers & Financial Transaction Engines (PayPay Focus)
* **Verified Readings & Book Chapters:**
  * System Design Interview Vol 2 — Chapter 3: Payment System & Chapter 4: Digital Wallet
  * Martin Fowler: Accounting Patterns (Double-Entry Bookkeeping & Idempotent Ledgers)
* **Deep Dive Topics:**
  * Double-entry bookkeeping: Immutable journal, debit/credit balance invariance.
  * Payment gateway reconciliation: Asynchronous bank statement matching, handling pending/stuck transactions.
  * Idempotency keys at the network gateway and database levels.
* **Staff Drill:** Design PayPay’s QR code payment processing engine with guaranteed zero duplicate charges under flaky 4G connections and automatic multi-tier reconciliation.

### Week 27: May 24 – May 30, 2027 | Ultra-Low Latency Matching Engines & Lock-Free Design
* **Verified Readings & Book Chapters:**
  * System Design Interview Vol 2 — Chapter 2: Stock Exchange
  * Martin Thompson: LMAX Disruptor: High Performance Alternative to Bounded Queues
* **Deep Dive Topics:**
  * In-memory deterministic processing, ring buffers, lock-free queues (compare-and-swap primitives).
  * Order book matching: Limit orders, Market orders, Price-Time priority (FIFO).
  * Memory alignment, false sharing on CPU cache lines (L1/L2/L3 cache misses).
* **Staff Drill:** Design an in-memory matching engine that processes 200,000 orders/sec with deterministic P99 latency < 50 microseconds.

### Week 28: May 31 – Jun 06, 2027 | Multi-Region Disaster Recovery & Global Active-Active
* **Verified Readings & Book Chapters:**
  * AWS Whitepaper: Disaster Recovery of Workloads on AWS: Multi-Region Approaches
  * Stripe Engineering Blog: Designing Resilient Payment Networks Across Multi-Region Active-Active
* **Deep Dive Topics:**
  * Disaster recovery objectives: RPO (Recovery Point Objective) vs. RTO (Recovery Time Objective).
  * Patterns: Backup & Restore, Pilot Light, Warm Standby, Multi-Region Active-Active.
  * Split-brain mitigation during trans-Pacific fiber cuts (Tokyo to US-West).
* **Staff Drill:** Design a multi-region active-active user identity platform across Tokyo and Singapore with an RPO of 0 seconds and RTO < 30 seconds during a total regional cloud outage.

---

## Phase 7: Production Machine Learning Systems (Weeks 29 – 32)

### Week 29: Jun 07 – Jun 13, 2027 | Production ML Architecture & Training-Serving Skew
* **Verified Readings & Book Chapters:**
  * Designing Machine Learning Systems (Chip Huyen) — Ch. 1, 2 & 3: ML Systems Overview & Data Fundamentals
* **Deep Dive Topics:**
  * Training-serving skew: Point-in-time correctness, data leakage prevention, feature divergence.
  * Data Flywheel: Continuous feedback loops, weak supervision, active learning strategies.
* **Staff Drill:** Design an e-commerce personalized search architecture. Detail how user clicks, add-to-carts, and purchase signals flow from Kafka back into the offline training dataset.

### Week 30: Jun 14 – Jun 20, 2027 | Distributed Model Training Infrastructure
* **Verified Readings & Book Chapters:**
  * Designing Machine Learning Systems — Ch. 5 (Monitoring Shifts) & Ch. 6 (Scaling ML Training: DP, TP, PP)
  * Ray Core Architecture: Ray Tasks, Actors & Plasma Object Store
* **Deep Dive Topics:**
  * Distributed compute frameworks: Ray (Ray Core, Ray Train, Ray Data) vs. Slurm vs. Kubeflow.
  * Distributed training primitives: Data Parallelism (DDP), Model Parallelism, Pipeline Parallelism.
  * GPU networking: NCCL communication library, AllReduce algorithms, InfiniBand vs. RoCE (RDMA over Converged Ethernet).
* **Staff Drill:** Architect a distributed model training platform on Kubernetes running 100 concurrent distributed training jobs with GPU resource preemption and fault-tolerant checkpointing.

### Week 31: Jun 21 – Jun 27, 2027 | Distributed Fine-Tuning: DeepSpeed & FSDP
* **Verified Readings & Book Chapters:**
  * Microsoft Paper: ZeRO: Memory Optimizations Toward Training Trillion Parameter Models (DeepSpeed)
  * PyTorch Documentation: Fully Sharded Data Parallel (FSDP) Internals
* **Deep Dive Topics:**
  * Memory breakdown of model training: Model parameters, Gradients, Optimizer states (Adam: 16 bytes/param), Activations.
  * DeepSpeed ZeRO stages: ZeRO-1 (Optimizer state partitioning), ZeRO-2 (Gradient partitioning), ZeRO-3 (Parameter partitioning).
  * Fully Sharded Data Parallel (PyTorch FSDP) vs. ZeRO.
* **Staff Drill:** Calculate the exact VRAM footprint required to fine-tune a 70B parameter model using full fine-tuning vs. LoRA vs. ZeRO-3 offload across 8x A100 (80GB) GPUs.

### Week 32: Jun 28 – Jul 04, 2027 | Model Drift, Monitoring & Safe Rollouts
* **Verified Readings & Book Chapters:**
  * Designing Machine Learning Systems — Ch. 8: Continual Learning & Ch. 9: Monitoring and Observability
* **Deep Dive Topics:**
  * Detecting data drift: Kolmogorov-Smirnov test (KS-test), Population Stability Index (PSI), Wasserstein Distance.
  * Safe deployment strategies: Shadow deployments, Canary releases, Multi-Armed Bandits (Thompson Sampling / UCB) for dynamic model traffic routing.
* **Staff Drill:** Design an automated model monitoring system that detects performance degradation on live credit card fraud detection models and automatically rolls back traffic to a baseline model.

---

## Phase 8: Enterprise LLM Infrastructure & High-Performance Serving (Weeks 33 – 36)

### Week 33: Jul 05 – Jul 11, 2027 | LLM Serving Internals: vLLM & PagedAttention
* **Verified Readings & Book Chapters:**
  * Woosuk Kwon et al. Paper: Efficient Memory Management for Large Language Model Serving with PagedAttention (vLLM, SOSP 2023)
  * vLLM Architecture Docs: Continuous Batching, KV Cache Blocks & Paged Memory
* **Deep Dive Topics:**
  * The KV Cache memory crisis: Memory fragmentation and static allocation waste in autoregressive decoding.
  * PagedAttention architecture: Virtual memory paging for KV cache blocks, logical-to-physical block mapping.
  * Continuous Batching (Iteration-level scheduling) vs. Static Batching.
  * Chunked prefill to prevent long prompts from starving decoding tokens.
* **Staff Drill:** Design an enterprise LLM serving cluster serving 5,000 requests/sec across variable prompt lengths (100 to 32k tokens) maximizing GPU compute utilization.

### Week 34: Jul 12 – Jul 18, 2027 | Quantization Engines & High-Speed Inference
* **Verified Readings & Book Chapters:**
  * Ji Lin et al. Paper: AWQ: Activation-aware Weight Quantization for LLM Compression
  * Tri Dao Paper: FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning
* **Deep Dive Topics:**
  * Quantization mechanics: Post-Training Quantization (PTQ) vs. QAT. Weight-only vs. Weight-Activation (W8A8, W4A16).
  * AWQ vs. GPTQ vs. GGUF trade-offs.
  * Speculative Decoding: Small draft model verification pipelines.
  * TensorRT-LLM and Triton Inference Server architecture.
* **Staff Drill:** Calculate the exact hardware requirement (GPU count, networking, VRAM) to serve a 70B parameter model with a P99 Time-To-First-Token (TTFT) < 200ms and generation speed > 50 tokens/sec.

### Week 35: Jul 19 – Jul 25, 2027 | Enterprise AI Gateway & Multi-Tenant Routing
* **Verified Readings & Book Chapters:**
  * Portkey / LiteLLM Architecture Specifications: Model Fallbacks, Token Rate Limiting & Semantic Caching
* **Deep Dive Topics:**
  * AI Gateway architecture: Unified API abstraction, token-level streaming, provider fallbacks.
  * Semantic Caching: Using embedding similarity over vector DBs (Redis / Qdrant) to serve cached LLM completions.
  * Token-bucket rate limiting: Tracking tokens-per-minute (TPM) and requests-per-minute (RPM) per enterprise tenant.
* **Staff Drill:** Design an enterprise AI Gateway that handles 200 corporate clients, enforces strict token rate limits, caches 40% of queries via semantic cache, and guarantees 99.99% uptime with multi-provider failover.

### Week 36: Jul 26 – Aug 01, 2027 | Multi-LoRA Serving at Scale (S-LoRA & Punica)
* **Verified Readings & Book Chapters:**
  * Sheng et al. Paper: S-LoRA: Serving Thousands of Concurrent LoRA Adapters (UC Berkeley, 2023)
  * Punica Paper: Multi-Tenant LoRA Serving (Chen et al.)
* **Deep Dive Topics:**
  * The Multi-Tenant customization problem: Why loading separate models per tenant kills GPU memory.
  * Dynamic LoRA adapter swapping: Batching requests with different LoRA adapters on a single frozen base model.
  * Memory pool management for dynamic adapter weights in GPU RAM.
* **Staff Drill:** Design an enterprise multi-tenant fine-tuning platform where 1,000 enterprise customers can run custom LoRA adapters on top of Llama-3 with instant cold-start loading (< 50ms).

---

## Phase 9: Vector Databases, Billion-Scale RAG & Multimodal AI (Weeks 37 – 39)

### Week 37: Aug 02 – Aug 08, 2027 | Vector Database Internals (HNSW vs. IVF-PQ)
* **Verified Readings & Book Chapters:**
  * Malkov & Yashunin Paper: Efficient and robust approximate nearest neighbor search using HNSW (2018)
  * Microsoft Paper: DiskANN: Fast Accurate Billion-point Nearest Neighbor Search on a Single Node
* **Deep Dive Topics:**
  * Approximate Nearest Neighbor (ANN) search algorithms.
  * HNSW: Graph skip-lists, entry points, memory overhead (1.5x vector size in RAM).
  * IVF-PQ: Inverted file list, product quantization, compression ratios, disk-backed search.
  * Specialized Vector DBs (Milvus, Qdrant) vs. pgvector internals.
* **Staff Drill:** Architect a vector search index for 1 Billion 1536-dimensional vectors. Calculate the exact RAM requirement for raw HNSW vs. IVF-PQ. Defend when you would choose pgvector over a standalone Milvus cluster.

### Week 38: Aug 09 – Aug 15, 2027 | Billion-Scale Enterprise RAG Architecture
* **Verified Readings & Book Chapters:**
  * Machine Learning System Design Interview (Ali Aminian & Alex Xu) — Search and Retrieval
  * Pinecone / Milvus Technical Papers: Dense vs Sparse (BM25 + ColBERT) & Cross-Encoder Re-ranking
* **Deep Dive Topics:**
  * Chunking strategies: Semantic chunking, parent-document retrieval, sentence-window retrieval.
  * Hybrid Search: Combining Dense Vector retrieval with Sparse Lexical (BM25) using Reciprocal Rank Fusion (RRF).
  * Multi-stage retrieval: Fast ANN candidate generation (Top-100) followed by Cross-Encoder Re-Ranking (Top-5).
  * Document security: Enforcing role-based access control (RBAC / ACL) filtering at the index layer without latency degradation.
* **Staff Drill:** Design an enterprise knowledge retrieval system searching across 100 million internal corporate documents with strict multi-tenant access control and sub-300ms end-to-end latency.

### Week 39: Aug 16 – Aug 22, 2027 | Real-Time Fraud Detection & Multimodal Retrieval
* **Verified Readings & Book Chapters:**
  * Machine Learning System Design Interview — Chapter 3: Visual Search & Chapter 8: Fraud Detection
* **Deep Dive Topics:**
  * Dual-encoder multimodal architectures (CLIP).
  * Streaming feature extraction for fraud: Sliding window aggregations over Flink/Kafka (< 30ms decision SLA).
  * Automated RAG evaluations: RAGAS / TruLens metrics (Faithfulness, Answer Relevance, Context Precision).
* **Staff Drill:** Design PayPay’s real-time payment fraud detection system combining streaming aggregations on Flink with a low-latency model inference engine.

---

## Phase 10: Staff/Principal Interview Mastery & Defense (Week 40)

### Week 40: Aug 23 – Aug 31, 2027 | Staff Interview Simulations & Defense

* **Verified Readings & Book Chapters:**
  * Staff System Design Defense: Trade-Off Matrix Formulation, P99 Latency SLAs & CAP Nuances
  * Cross-Cutting Review: UDS Ch. 1–21, DDIA Ch. 1–9, Production Papers & Case Studies* **Deliverables & Activities:**
  * Conduct 5 full 45-minute timed system design sessions with recorded playback.
  * Practice cross-cutting Staff pushback drills:
    1. *"Our cloud bill is $2M/month, how do you cut compute and network egress costs by 50% without hurting SLAs?"*
    2. *"A trans-Pacific fiber cut just severed Tokyo from US-East, walk me through what fails and what stays up."*
    3. *"Explain why you chose Kafka + Flink over a simple Celery + Redis queue for this pipeline."*
  * Finalize resume highlighting enterprise AI platform achievements, scale, throughput, and reliability metrics.
  * Launch live applications on **TokyoDev** and **Japan Dev** to **Mercari, PayPay, Woven by Toyota, and Line Yahoo**.

---

## 🏛️ Legendary Production Case Studies (Integrated Across Curriculum)

A true Staff Engineer does not speak only in abstract theory—they reference hard-won engineering history from the industry's most legendary production systems. These exact case studies are baked directly into your 40 weeks:

### 1. The WhatsApp Erlang Architecture (Week 9 - Chat at Scale)
* **The Feat:** Only ~35 to 50 engineers supporting **900 Million (and later 1 Billion+) users** on ~few hundred servers.
* **The Architecture:** Erlang BEAM virtual machine on FreeBSD. Each user connection handled by a lightweight BEAM actor process (costing only ~2.5 KB of RAM vs 1MB+ OS threads).
* **Staff Deep Dive:** Rick Reed’s famous *"1 Million is so 2011 — 2 Million connections per box"* optimizations: tuning FreeBSD kernel network stack buffers (`kern.ipc.maxsockets`, `net.inet.tcp.recvspace`), offloading TLS, and hacking ejabberd for zero-copy message fanout.

### 2. Grammarly’s Common Lisp Engine (Week 26 - High-Speed Parsing)
* **The Feat:** Parsing tens of millions of grammatical rules per second with deterministic microsecond latency.
* **The Architecture:** Written in **Common Lisp** running natively compiled on SBCL (Steel Bank Common Lisp).
* **Staff Deep Dive:** Why Lisp for enterprise AI? Abstract Syntax Tree (AST) transformations are native to Lisp (code-as-data/homoiconicity). It delivered 10x lower memory overhead and vastly faster rule-tree evaluation than Java or Python would allow, wrapped in C++/Go service boundaries.

### 3. Why Discord Switched from Go to Rust (Week 4 - Tail Latency & GC Cliffs)
* **The Feat:** Eliminating recurrent P99 latency spikes in Discord’s Read States service (handling millions of messages).
* **The Architecture:** Re-architecting from Go to Rust.
* **Staff Deep Dive:** Go’s memory allocator and Garbage Collector ran every 2 minutes. Even with Go's fast concurrent collector, traversing millions of active chat channels in memory caused severe 2-second CPU cache thrashing. Moving to Rust eliminated the GC entirely, dropping P99 tail latency from **2,000ms to < 5ms**.

### 4. Figma’s Multiplayer Engine (Week 10 - Collaborative CRDTs)
* **The Feat:** Smooth 60fps real-time multiplayer design across thousands of concurrent users in a single canvas.
* **The Architecture:** Custom operational transformation/CRDT engine written in Rust and compiled to WebAssembly (Wasm) in the browser, synchronized over WebSockets with central server-side Rust coordinators.
* **Staff Deep Dive:** Client-side optimistic rendering, vector clock state reconciliation, and handling out-of-order network updates without locking the browser UI thread.

### 5. The LMAX Disruptor (Week 27 - Ultra-Low Latency Matching)
* **The Feat:** Processing **6 Million financial transactions per second** on a single thread on standard JVM hardware.
* **The Architecture:** Circular ring buffer with zero-lock concurrency, memory pre-allocation, and cache-line padding.
* **Staff Deep Dive:** Mechanical Sympathy—designing software to align with CPU hardware L1/L2/L3 cache line size (64 bytes) to eliminate False Sharing and lock contention.

### 6. Stripe’s Idempotency & Ledger Engine (Week 26 - Financial Correctness)
* **The Feat:** Zero duplicate transactions and mathematically immutable auditing across billions of API payments.
* **The Architecture:** Two-tiered idempotency: Gateway-level lease reservation on Redis + transactional database commits with double-entry bookkeeping ledgers.
* **Staff Deep Dive:** Replay attacks, atomic state transitions, and asynchronous bank statement clearing reconciliations.

### 7. Uber Michelangelo — The Birth of the Enterprise Feature Store (Weeks 23 & 24)
* **The Feat:** Scaling Uber from fragmented data science scripts into a unified platform serving millions of ML predictions/sec for dynamic pricing, ETAs, and fraud.
* **The Architecture:** The pioneer of the **Dual-Engine Feature Store**: Offline batch features computed via Spark into HDFS/Hive, and streaming real-time features computed via Flink into Cassandra/Redis.
* **Staff Deep Dive:** Eliminating **Training-Serving Skew** using unified feature definition pipelines. Guarantees that feature transformations (e.g. "driver's acceptance rate over last 10 minutes") use identical code and point-in-time snapshot logic in offline model training as in online microsecond inference.

### 8. Netflix Nearline ML Pipeline — Hybrid Offline/Online Personalization (Week 24)
* **The Feat:** Personalizing the homepage for 250M+ users with sub-second adaptation to what a user just clicked or watched.
* **The Architecture:** Three-tiered ML topology:
  1. **Offline Layer:** Heavy matrix factorization and deep neural networks trained overnight on Apache Spark.
  2. **Nearline Layer:** Streaming Kafka + Flink pipeline that processes viewing events, updates user session context, and computes real-time ranking adjustments in < 500ms.
  3. **Online Layer:** Ultra-low latency microservice combining pre-computed offline candidate sets with fresh nearline session state to render recommendations.
* **Staff Deep Dive:** Graceful degradation. If the nearline streaming pipeline lags or fails, the online layer automatically falls back to cached offline candidate sets with zero user disruption.

### 9. Pinterest PinSage — Billion-Scale Graph Neural Network Pipeline (Week 34)
* **The Feat:** Generating deep embeddings across 3 Billion visual pins and 18 Billion boards for real-time visual search and related item feeds.
* **The Architecture:** Graph Convolutional Networks (GCN) trained using MapReduce/Spark on multi-GPU clusters using localized random-walk neighborhood sampling.
* **Staff Deep Dive:** Scaling GCNs to billions of nodes without running out of GPU VRAM by sampling sub-graphs dynamically, exporting embeddings into high-throughput HNSW vector clusters.

### 10. ByteDance Monolith — Real-Time Streaming Recommendation Engine (Week 35)
* **The Feat:** Real-time recommendation loop for TikTok where user clicks, watch times, and skips update ranking models in sub-minute streaming cycles.
* **The Architecture:** Collisionless dynamic embedding hash tables that allocate memory on-the-fly for newly trending items, decoupled parameter servers, and asynchronous streaming checkpointing.
* **Staff Deep Dive:** Replacing traditional daily/hourly batch model retraining with continuous parameter synchronization over high-speed networks.
