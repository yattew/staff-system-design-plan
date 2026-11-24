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
* **Readings:** *Understanding Distributed Systems (UDS)* — Ch. 1–4; Google Paper: *Maglev: A Fast and Reliable Software Network Load Balancer*.
* **Deep Dive Topics:**
  * Transport: TCP handshake, TCP slow start, head-of-line blocking, UDP, QUIC (HTTP/3).
  * Layer 4 vs. Layer 7 Load Balancing: IP/Port hashing vs. HTTP parsing.
  * Load balancing tools: NGINX, HAProxy, Envoy Proxy architecture (control plane vs. data plane, thread models).
  * Consistent Hashing on load balancers, connection draining, TLS termination, Session persistence trade-offs.
* **Staff Drill:** Architect a multi-tiered ingress network handling 2M requests/sec using Maglev (L4 ECMP) routing to Envoy (L7) with zero-downtime certificate rotation.

### Week 2: Nov 30 – Dec 06, 2026 | DNS, Edge Caching, API Gateways & Reverse Proxies
* **Readings:** *UDS* — Ch. 5–7; Cloudflare Architecture Blog: *How Cloudflare Works at the Edge*.
* **Deep Dive Topics:**
  * DNS resolution: Anycast routing, GeoDNS, BGP routing, TTL caching anomalies.
  * CDN internals: Cache eviction (LRU, LFU, 2-Tier), Cache-Control headers, byte-range requests for video.
  * API Gateway vs. Reverse Proxy: Authentication offloading, rate limiting, request collapsing/deduplication.
* **Staff Drill:** Design a globally distributed Edge API Gateway that routes users to the nearest regional data center with sub-20ms latency and handles sudden BGP route leaks.

### Week 3: Dec 07 – Dec 13, 2026 | Site Reliability Engineering (SRE): Metrics, SLOs & Error Budgets
* **Readings:** *Site Reliability Engineering (The Google SRE Book)* — Chapters 3, 4, 5 (SLAs, SLOs, SLIs, Eliminating Toil).
* **Deep Dive Topics:**
  * SLI vs. SLO vs. SLA: How to pick meaningful SLIs (Latency, Traffic, Errors, Saturation - The 4 Golden Signals).
  * Error Budget Math: Calculating outage burn rates (1h burn, 6h burn, multi-window multi-burn-rate alerts).
  * Capacity Planning: Predicting resource exhaustion using utilization curves and headroom forecasting.
* **Staff Drill:** Define the complete SRE contract (SLIs, SLOs, Error Budget Policy) for a tier-0 Payment Ingestion Service targeting 99.99% availability.

### Week 4: Dec 14 – Dec 20, 2026 | Resilience Engineering: Chaos, Circuit Breakers & Backpressure
* **Readings:** *UDS* — Ch. 8–10; Netflix Technology Blog: *Chaos Engineering Upgraded*.
* **Deep Dive Topics:**
  * Cascading failures: Thread pool exhaustion, retry storms with exponential backoff & jitter.
  * Circuit Breakers (Hystrix / Resilience4j pattern): Closed, Open, Half-Open states.
  * Backpressure handling: Reactive streams, TCP flow control window, queue-drop strategies (CoDel algorithm).
  * Chaos Engineering: Fault injection, Latency injection, Chaos Monkey at scale.
* **Staff Drill:** Design a resilient microservice communication mesh that prevents cascading grid failures when a core downstream database's latency spikes by 500ms.

---

## Phase 2: Distributed Systems Core & Storage Engine Internals (Weeks 5 – 9)

### Week 5: Dec 21 – Dec 27, 2026 | Clocks, Time & Distributed Coordination
* **Readings:** *Designing Data-Intensive Applications (DDIA)* — Chapters 8; Google Paper: *Spanner: Google’s Globally-Distributed Database* (TrueTime section).
* **Deep Dive Topics:**
  * Physical clocks: NTP drift, leap seconds, monotonically increasing clocks (`CLOCK_MONOTONIC`).
  * Logical clocks: Lamport Timestamps, Vector Clocks (tracking causality and concurrency).
  * Google TrueTime API: Atomic clocks + GPS receivers, bounded uncertainty window ($\epsilon$), wait-out-the-uncertainty.
* **Staff Drill:** Explain how Google Spanner achieves external consistency (linearizability) across global regions without locking out reads.

### Week 6: Dec 28, 2026 – Jan 03, 2027 | Storage Engine Internals: B-Trees vs. LSM-Trees
* **Readings:** *DDIA* — Chapter 3; *Database Internals* (Alex Petrov) — Part I (B-Tree Internals).
* **Deep Dive Topics:**
  * B-Trees: Page layout, write-ahead logging (WAL), dirty pages, random I/O bottlenecks.
  * LSM-Trees: MemTable (SkipLists), Immutable SSTables, Write Amplification, Read Amplification, Space Amplification.
  * Compaction algorithms: Size-Tiered vs. Leveled Compaction (RocksDB internals). Bloom filters for read acceleration.
* **Staff Drill:** Deep dive into RocksDB. Explain why an LSM tree can ingest 100k writes/sec on NVMe while MySQL InnoDB B-Tree stalls on disk writes.

### Week 7: Jan 04 – Jan 10, 2027 | Columnar Storage Engines & Analytical Formats
* **Readings:** *DDIA* — Chapter 3 (Part 2); ClickHouse Architecture Documentation.
* **Deep Dive Topics:**
  * Columnar vs. Row-oriented: When to use which (OLTP vs. OLAP).
  * Formats: Apache Parquet (Dremel encoding, dictionary encoding, bit packing, run-length encoding), Apache ORC.
  * Vectorized Execution: SIMD instructions, processing data in column batches in CPU cache.
* **Staff Drill:** Design an analytical storage engine that ingests 1 Billion telemetry logs per day and runs sub-second aggregation queries over date ranges without indexes.

### Week 8: Jan 11 – Jan 17, 2027 | Serialization, Protocols & Zero-Copy Reads
* **Readings:** *DDIA* — Chapter 4; Linux Kernel Documentation: `sendfile(2)` and Zero-Copy I/O.
* **Deep Dive Topics:**
  * Binary serialization: Protocol Buffers wire format (Varints, Tag-Length-Value encoding), Apache Avro, FlatBuffers.
  * Schema Evolution: Forward vs. Backward compatibility, Confluent Schema Registry.
  * Operating System Zero-Copy: Kernel space vs. User space memory copies, DMA (Direct Memory Access), `sendfile` syscall.
* **Staff Drill:** Explain exactly how Apache Kafka utilizes OS Page Cache and `sendfile` to bypass Java heap garbage collection and saturate 10Gbps NICs.

### Week 9: Jan 18 – Jan 24, 2027 | Distributed Key-Value Engines & Dynamo Architecture
* **Readings:** Amazon Paper: *Dynamo: Amazon’s Highly Available Key-value Store* (DeCandia et al.).
* **Deep Dive Topics:**
  * Quorum math: $N, R, W$ (Strong consistency when $R + W > N$).
  * Sloppy Quorums and Hinted Handoff.
  * Read Repair vs. Anti-Entropy processes using Merkle Trees.
  * Gossip protocols for node membership and failure detection.
* **Staff Drill:** Design a globally distributed Key-Value store (Cassandra / Dynamo-style) tuned for AP (Availability + Partition Tolerance) with tunable consistency per query.

---

## Phase 3: Replication, Partitioning, Consensus & Decentralized Systems (Weeks 10 – 14)

### Week 10: Jan 25 – Jan 31, 2027 | Replication Topologies & CRDTs
* **Readings:** *DDIA* — Chapter 5; Shapiro et al. Paper: *Conflict-free Replicated Data Types (CRDTs)*.
* **Deep Dive Topics:**
  * Single-leader vs. Multi-leader vs. Leaderless replication.
  * Replication lag anomalies: Read-your-own-writes, Monotonic reads, Consistent prefix reads.
  * Conflict resolution: Last-Write-Wins (LWW) data loss risks.
  * CRDTs: Operation-based vs. State-based CRDTs, PN-Counters, G-Sets, OR-Sets for collaborative editing.
* **Staff Drill:** Design the data sync engine for Google Docs / Figma that allows offline edits from 5 collaborators and automatically merges state without data loss.

### Week 11: Feb 01 – Feb 07, 2027 | Partitioning, Sharding & Rebalancing
* **Readings:** *DDIA* — Chapter 6.
* **Deep Dive Topics:**
  * Partitioning strategies: Key-range vs. Hash partitioning.
  * Secondary indexes: Document-partitioned (local index) vs. Term-partitioned (global index).
  * Shard rebalancing: Fixed partitions, dynamic partitioning (splitting/merging), proportional partitioning.
  * Mitigating hot-spot shards: Salted keys, dynamic load balancing.
* **Staff Drill:** Architect a database sharding strategy for an e-commerce platform handling Black Friday flash sales where 100,000 users query the exact same product SKU simultaneously.

### Week 12: Feb 08 – Feb 14, 2027 | Distributed Transactions & The Saga Pattern
* **Readings:** *Software Architecture: The Hard Parts* — Chapters on Distributed Transactions.
* **Deep Dive Topics:**
  * Two-Phase Commit (2PC): Prepare phase, Commit phase, Coordinator failure blocking mode.
  * Three-Phase Commit (3PC) limitations.
  * Saga Pattern: Orchestration-based vs. Choreography-based Sagas.
  * The Outbox Pattern + Change Data Capture (Debezium) for reliable event publishing.
* **Staff Drill:** Design an end-to-end trip booking transaction across Flight, Hotel, and Car Rental microservices using an Orchestrated Saga with idempotent compensation.

### Week 13: Feb 15 – Feb 21, 2027 | Distributed Consensus: Raft vs. Paxos Internals
* **Readings:** *DDIA* — Chapter 9; Ongaro & Ousterhout Paper: *In Search of an Understandable Consensus Algorithm (Raft)*.
* **Deep Dive Topics:**
  * Consensus requirements: Agreement, Integrity, Validity, Termination.
  * Raft protocol mechanics: Leader election, Randomized election timeouts, Log replication, Safety invariants, Joint Consensus cluster membership changes.
  * Paxos fundamentals: Multi-Paxos, Synod, Proposers, Acceptors, Learners.
* **Staff Drill:** Walk through a 5-node Raft cluster split into two partitions ($3$ nodes and $2$ nodes). Prove mathematically and mechanically why the minority partition can never commit a write.

### Week 14: Feb 22 – Feb 28, 2027 | Decentralized Systems, P2P Networks & BFT
* **Readings:** Maymounkov & Mazières Paper: *Kademlia: A Peer-to-Peer Information System Based on the XOR Metric*; Castro & Liskov Paper: *Practical Byzantine Fault Tolerance (PBFT)*.
* **Deep Dive Topics:**
  * Distributed Hash Tables (DHT): Kademlia XOR metric, routing tables ($k$-buckets), lookups ($O(\log N)$).
  * Cryptographic data structures: Merkle Trees, Merkle DAGs (IPFS, Git internals).
  * Byzantine Fault Tolerance: Handling malicious or arbitrary node failures ($3f + 1$ nodes required to tolerate $f$ Byzantine nodes).
* **Staff Drill:** Design a decentralized peer-to-peer file distribution network (BitTorrent style) that distributes a 20GB OS image to 100,000 servers in under 5 minutes without crushing central servers.

---

## Phase 4: Big Data Infrastructure & Offline Data Pipelines (Weeks 15 – 19)

### Week 15: Mar 01 – Mar 07, 2027 | Hadoop, HDFS & Distributed Storage Foundations
* **Readings:** Whitepaper: *The Google File System (GFS)*; Apache HDFS Architecture Guide.
* **Deep Dive Topics:**
  * HDFS Architecture: NameNode (in-memory namespace, EditLog, FsImage) vs. DataNodes.
  * Block placement policy (Rack awareness, 128MB/256MB blocks, replication pipelines).
  * NameNode High Availability: Active/Standby NameNodes using QJM (Quorum Journal Manager) and ZooKeeper.
  * MapReduce paradigm: Map, Shuffle & Sort, Reduce phase mechanics.
* **Staff Drill:** Explain what happens when a DataNode fails during a client write pipeline in HDFS. How does the client and NameNode coordinate to ensure lease recovery and block integrity?

### Week 16: Mar 08 – Mar 14, 2027 | Apache Spark Core: Architecture & Catalyst Optimizer
* **Readings:** *Spark: The Definitive Guide* — Chapters 1 to 5; Zaharia et al. Paper: *Resilient Distributed Datasets (RDDs)*.
* **Deep Dive Topics:**
  * Spark Architecture: Driver, Cluster Manager (YARN/K8s), Executors, Cores.
  * RDD fundamentals: Lineage graphs, lazy evaluation, narrow vs. wide dependencies.
  * Catalyst Optimizer internals: Analysis, Logical Plan optimization, Physical Planning, Code generation (Whole-Stage Code Generation).
* **Staff Drill:** Write a detailed breakdown of a Spark job processing a 10TB dataset. Trace the query from SQL text to Catalyst Logical Plan, Physical Plan, and RDD DAG execution.

### Week 17: Mar 15 – Mar 21, 2027 | Spark Performance Tuning & Distributed Shuffles
* **Readings:** *Spark: The Definitive Guide* — Chapters 12 & 13 (Tuning and Performance).
* **Deep Dive Topics:**
  * The Spark Shuffle: Shuffle write, Shuffle fetch, spill to disk, memory pressure.
  * Memory Management: Unified Memory Manager (Storage Memory vs. Execution Memory fraction).
  * Mitigating Data Skew: Salting skewed keys, Broadcast Hash Join (`broadcast()`), Adaptive Query Execution (AQE).
* **Staff Drill:** Diagnose and resolve an out-of-memory (OOM) error caused by a severe 99:1 data skew on a join key in a 5TB Spark pipeline.

### Week 18: Mar 22 – Mar 28, 2027 | Modern Lakehouse Engines: Iceberg, Delta Lake & Hudi
* **Readings:** Whitepapers: *Apache Iceberg: The Definitive Guide* (O'Reilly); Databricks Paper: *Delta Lake: High-Performance ACID Table Storage over Cloud Object Stores*.
* **Deep Dive Topics:**
  * Why Hive Metastore failed at modern scale: Directory-based partition bottlenecks.
  * Apache Iceberg Architecture: Snapshot metadata tree (Iceberg Catalog → Metadata File → Manifest List → Manifest Files → Data Files).
  * ACID transactions on S3/GCS: Optimistic Concurrency Control (OCC), hidden partitioning, schema evolution, time-travel queries.
  * Copy-on-Write (CoW) vs. Merge-on-Read (MoR) performance trade-offs.
* **Staff Drill:** Architect a petabyte-scale Data Lakehouse migration from Hive to Apache Iceberg on AWS S3, guaranteeing zero downtime and supporting simultaneous streaming writes and batch reads.

### Week 19: Mar 29 – Apr 04, 2027 | Offline Data Orchestration & Data Quality
* **Readings:** Airflow & Dagster Documentation; *Fundamentals of Data Engineering* (Joe Reis & Matt Housley).
* **Deep Dive Topics:**
  * Workflow orchestrators: Directed Acyclic Graphs (DAGs), Airflow task lifecycle vs. Dagster software-defined assets.
  * Idempotency in batch data: Partition overwriting vs. append-only, handling backfills for past 2 years.
  * Data Quality at Scale: Great Expectations, Soda Core, anomaly detection in pipeline row counts and distributions.
* **Staff Drill:** Design an automated batch backfilling pipeline that can re-process 24 months of raw clickstream data without exhausting compute budgets or producing duplicate records in downstream tables.

---

## Phase 5: Real-Time Streaming Systems & Online Pipelines (Weeks 20 – 24)

### Week 20: Apr 05 – Apr 11, 2027 | Apache Kafka Internals & Broker Storage
* **Readings:** *Kafka: The Definitive Guide (2nd Ed)* — Chapters 3, 4 & 6; Kreps et al. Paper: *Kafka: a Distributed Messaging System for Log Processing*.
* **Deep Dive Topics:**
  * Partition storage: Segment files (`.log`), Index files (`.index`), Time index files (`.timeindex`).
  * Producer internals: RecordAccumulator, Batching (`linger.ms`, `batch.size`), Compression (Snappy/zstd).
  * Consumer Group Rebalance Protocol: Eager rebalance vs. Cooperative Sticky assignor.
* **Staff Drill:** Design a Kafka deployment ingesting 500k messages/sec. Calculate broker disk I/O, network bandwidth, retention policies, and partition count strategy.

### Week 21: Apr 12 – Apr 18, 2027 | Kafka Exactly-Once Semantics (EOS) Deep Dive
* **Readings:** Confluent Technical Paper: *Transactions in Apache Kafka*.
* **Deep Dive Topics:**
  * Idempotent Producer: Producer ID (PID), Sequence numbers, deduplication at broker partition level.
  * Kafka Transactions: Transaction Coordinator, `__transaction_state` topic, Two-Phase Commit over log markers.
  * Read Committed isolation level: Last Stable Offset (LSO) vs. High Watermark (HW).
* **Staff Drill:** Walk through how Kafka EOS guarantees that a consume-transform-produce cycle across two different Kafka topics never processes a message twice during broker crashes.

### Week 22: Apr 19 – Apr 25, 2027 | Apache Flink: Stream Processing & Event Time
* **Readings:** *Streaming Systems* (Tyler Akidau et al.) — Chapters 1, 2 & 3; Apache Flink Architecture Docs.
* **Deep Dive Topics:**
  * Event Time vs. Processing Time vs. Ingestion Time.
  * Watermarks: Punctuated vs. Periodic watermarks, out-of-order event handling, allowed lateness.
  * Windowing mechanics: Tumbling, Sliding, Session windows.
* **Staff Drill:** Design a real-time sliding window aggregation (e.g. "Calculate 1-hour average transaction volume sliding every 10 seconds") that handles mobile network delays of up to 5 minutes correctly.

### Week 23: Apr 26 – May 02, 2027 | Flink State Management & Distributed Snapshots
* **Readings:** Carbone et al. Paper: *Lightweight Asynchronous Snapshots for Distributed Dataflows* (Chandy-Lamport variant in Flink).
* **Deep Dive Topics:**
  * State primitives: ValueState, ListState, MapState.
  * State Backends: MemoryStateBackend vs. FileSystemStateBackend vs. EmbeddedRocksDBStateBackend.
  * Chandy-Lamport snapshotting: Checkpoint barriers injected into streams, asynchronous state persistence to S3, two-phase commit sink integration.
* **Staff Drill:** Architect a Flink stream processing cluster handling 200GB of state. Explain what happens when a task manager node dies and how it recovers to the exact microsecond state.

### Week 24: May 03 – May 09, 2027 | Streaming Architecture: Lambda vs. Kappa & Feature Stores
* **Readings:** Jay Kreps Blog: *Questioning the Lambda Architecture*; Feast / Tecton Architecture Whitepapers.
* **Deep Dive Topics:**
  * Lambda Architecture (Batch layer + Speed layer + Serving layer) vs. Kappa Architecture (Single stream-first engine).
  * Real-Time Feature Store (Feast / Hopsworks): Point-in-time joins, dual storage (Redis for online low-latency inference + Iceberg/Parquet for offline training).
* **Staff Drill:** Architect an online/offline unified Feature Store serving 10,000 requests/sec with < 8ms P99 latency while guaranteeing zero data leakage for offline model training.

---

## Phase 6: High-Scale Distributed Platforms (Weeks 25 – 28)

### Week 25: May 10 – May 16, 2027 | Distributed Caching: Stampedes, Eviction & Topologies
* **Readings:** *System Design Interview Vol 2 (SDI 2)* — Chapter 11; Redis Cluster Specification.
* **Deep Dive Topics:**
  * Redis Cluster: 16,384 hash slots, gossip protocol, master-replica failover.
  * Cache Stampede / Thundering Herd solutions: Mutex locking, XFetch probabilistic early expiration algorithm.
  * Eviction policies: LRU, LFU, Volatile-TTL, Clock-Pro algorithm.
* **Staff Drill:** Design a distributed caching layer holding 40 TB of active data that guarantees immunity against cache stampedes when a viral post expires from cache.

### Week 26: May 17 – May 23, 2027 | Payment Ledgers & Financial Transaction Engines (PayPay Focus)
* **Readings:** *SDI 2* — Chapter 3 & 4 (Payment System & Digital Wallet).
* **Deep Dive Topics:**
  * Double-entry bookkeeping: Immutable journal, debit/credit balance invariance.
  * Payment gateway reconciliation: Asynchronous bank statement matching, handling pending/stuck transactions.
  * Idempotency keys at the network gateway and database levels.
* **Staff Drill:** Design PayPay’s QR code payment processing engine with guaranteed zero duplicate charges under flaky 4G connections and automatic multi-tier reconciliation.

### Week 27: May 24 – May 30, 2027 | Ultra-Low Latency Matching Engines & Lock-Free Design
* **Readings:** *SDI 2* — Chapter 2 (Stock Exchange); LMAX Disruptor Technical Paper.
* **Deep Dive Topics:**
  * In-memory deterministic processing, ring buffers, lock-free queues (compare-and-swap primitives).
  * Order book matching: Limit orders, Market orders, Price-Time priority (FIFO).
  * Memory alignment, false sharing on CPU cache lines (L1/L2/L3 cache misses).
* **Staff Drill:** Design an in-memory matching engine that processes 200,000 orders/sec with deterministic P99 latency < 50 microseconds.

### Week 28: May 31 – Jun 06, 2027 | Multi-Region Disaster Recovery & Global Active-Active
* **Readings:** AWS Whitepaper: *Disaster Recovery of Workloads on AWS: Multi-Region Approaches*.
* **Deep Dive Topics:**
  * Disaster recovery objectives: RPO (Recovery Point Objective) vs. RTO (Recovery Time Objective).
  * Patterns: Backup & Restore, Pilot Light, Warm Standby, Multi-Region Active-Active.
  * Split-brain mitigation during trans-Pacific fiber cuts (Tokyo to US-West).
* **Staff Drill:** Design a multi-region active-active user identity platform across Tokyo and Singapore with an RPO of 0 seconds and RTO < 30 seconds during a total regional cloud outage.

---

## Phase 7: Production Machine Learning Systems (Weeks 29 – 32)

### Week 29: Jun 07 – Jun 13, 2027 | Production ML Architecture & Training-Serving Skew
* **Readings:** *Designing Machine Learning Systems (DMLS)* (Chip Huyen) — Chapters 1, 2 & 3.
* **Deep Dive Topics:**
  * Training-serving skew: Point-in-time correctness, data leakage prevention, feature divergence.
  * Data Flywheel: Continuous feedback loops, weak supervision, active learning strategies.
* **Staff Drill:** Design an e-commerce personalized search architecture. Detail how user clicks, add-to-carts, and purchase signals flow from Kafka back into the offline training dataset.

### Week 30: Jun 14 – Jun 20, 2027 | Distributed Model Training Infrastructure
* **Readings:** *DMLS* — Chapters 5 & 6; Ray Core Documentation.
* **Deep Dive Topics:**
  * Distributed compute frameworks: Ray (Ray Core, Ray Train, Ray Data) vs. Slurm vs. Kubeflow.
  * Distributed training primitives: Data Parallelism (DDP), Model Parallelism, Pipeline Parallelism.
  * GPU networking: NCCL communication library, AllReduce algorithms, InfiniBand vs. RoCE (RDMA over Converged Ethernet).
* **Staff Drill:** Architect a distributed model training platform on Kubernetes running 100 concurrent distributed training jobs with GPU resource preemption and fault-tolerant checkpointing.

### Week 31: Jun 21 – Jun 27, 2027 | Distributed Fine-Tuning: DeepSpeed & FSDP
* **Readings:** Microsoft Paper: *ZeRO: Memory Optimizations Toward Training Trillion Parameter Models* (Rajbhandari et al.).
* **Deep Dive Topics:**
  * Memory breakdown of model training: Model parameters, Gradients, Optimizer states (Adam: 16 bytes/param), Activations.
  * DeepSpeed ZeRO stages: ZeRO-1 (Optimizer state partitioning), ZeRO-2 (Gradient partitioning), ZeRO-3 (Parameter partitioning).
  * Fully Sharded Data Parallel (PyTorch FSDP) vs. ZeRO.
* **Staff Drill:** Calculate the exact VRAM footprint required to fine-tune a 70B parameter model using full fine-tuning vs. LoRA vs. ZeRO-3 offload across 8x A100 (80GB) GPUs.

### Week 32: Jun 28 – Jul 04, 2027 | Model Drift, Monitoring & Safe Rollouts
* **Readings:** *DMLS* — Chapters 8 & 9.
* **Deep Dive Topics:**
  * Detecting data drift: Kolmogorov-Smirnov test (KS-test), Population Stability Index (PSI), Wasserstein Distance.
  * Safe deployment strategies: Shadow deployments, Canary releases, Multi-Armed Bandits (Thompson Sampling / UCB) for dynamic model traffic routing.
* **Staff Drill:** Design an automated model monitoring system that detects performance degradation on live credit card fraud detection models and automatically rolls back traffic to a baseline model.

---

## Phase 8: Enterprise LLM Infrastructure & High-Performance Serving (Weeks 33 – 36)

### Week 33: Jul 05 – Jul 11, 2027 | LLM Serving Internals: vLLM & PagedAttention
* **Readings:** Kwon et al. Paper: *Efficient Memory Management for Large Language Model Serving with PagedAttention* (vLLM Paper).
* **Deep Dive Topics:**
  * The KV Cache memory crisis: Memory fragmentation and static allocation waste in autoregressive decoding.
  * PagedAttention architecture: Virtual memory paging for KV cache blocks, logical-to-physical block mapping.
  * Continuous Batching (Iteration-level scheduling) vs. Static Batching.
  * Chunked prefill to prevent long prompts from starving decoding tokens.
* **Staff Drill:** Design an enterprise LLM serving cluster serving 5,000 requests/sec across variable prompt lengths (100 to 32k tokens) maximizing GPU compute utilization.

### Week 34: Jul 12 – Jul 18, 2027 | Quantization Engines & High-Speed Inference
* **Readings:** Papers: *AWQ: Activation-aware Weight Quantization*; *FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning* (Tri Dao).
* **Deep Dive Topics:**
  * Quantization mechanics: Post-Training Quantization (PTQ) vs. QAT. Weight-only vs. Weight-Activation (W8A8, W4A16).
  * AWQ vs. GPTQ vs. GGUF trade-offs.
  * Speculative Decoding: Small draft model verification pipelines.
  * TensorRT-LLM and Triton Inference Server architecture.
* **Staff Drill:** Calculate the exact hardware requirement (GPU count, networking, VRAM) to serve a 70B parameter model with a P99 Time-To-First-Token (TTFT) < 200ms and generation speed > 50 tokens/sec.

### Week 35: Jul 19 – Jul 25, 2027 | Enterprise AI Gateway & Multi-Tenant Routing
* **Readings:** Portkey / LiteLLM Architecture Specs.
* **Deep Dive Topics:**
  * AI Gateway architecture: Unified API abstraction, token-level streaming, provider fallbacks.
  * Semantic Caching: Using embedding similarity over vector DBs (Redis / Qdrant) to serve cached LLM completions.
  * Token-bucket rate limiting: Tracking tokens-per-minute (TPM) and requests-per-minute (RPM) per enterprise tenant.
* **Staff Drill:** Design an enterprise AI Gateway that handles 200 corporate clients, enforces strict token rate limits, caches 40% of queries via semantic cache, and guarantees 99.99% uptime with multi-provider failover.

### Week 36: Jul 26 – Aug 01, 2027 | Multi-LoRA Serving at Scale (S-LoRA & Punica)
* **Readings:** Sheng et al. Paper: *S-LoRA: Serving Thousands of Concurrent LoRA Adapters* (UC Berkeley).
* **Deep Dive Topics:**
  * The Multi-Tenant customization problem: Why loading separate models per tenant kills GPU memory.
  * Dynamic LoRA adapter swapping: Batching requests with different LoRA adapters on a single frozen base model.
  * Memory pool management for dynamic adapter weights in GPU RAM.
* **Staff Drill:** Design an enterprise multi-tenant fine-tuning platform where 1,000 enterprise customers can run custom LoRA adapters on top of Llama-3 with instant cold-start loading (< 50ms).

---

## Phase 9: Vector Databases, Billion-Scale RAG & Multimodal AI (Weeks 37 – 39)

### Week 37: Aug 02 – Aug 08, 2027 | Vector Database Internals (HNSW vs. IVF-PQ)
* **Readings:** Malkov & Yashunin Paper: *Efficient and robust approximate nearest neighbor search using Hierarchical Navigable Small World graphs (HNSW)*; DiskANN Paper (Microsoft).
* **Deep Dive Topics:**
  * Approximate Nearest Neighbor (ANN) search algorithms.
  * HNSW: Graph skip-lists, entry points, memory overhead (1.5x vector size in RAM).
  * IVF-PQ: Inverted file list, product quantization, compression ratios, disk-backed search.
  * Specialized Vector DBs (Milvus, Qdrant) vs. pgvector internals.
* **Staff Drill:** Architect a vector search index for 1 Billion 1536-dimensional vectors. Calculate the exact RAM requirement for raw HNSW vs. IVF-PQ. Defend when you would choose pgvector over a standalone Milvus cluster.

### Week 38: Aug 09 – Aug 15, 2027 | Billion-Scale Enterprise RAG Architecture
* **Readings:** *Machine Learning System Design Interview (MLSDI)* — Ali Aminian & Alex Xu.
* **Deep Dive Topics:**
  * Chunking strategies: Semantic chunking, parent-document retrieval, sentence-window retrieval.
  * Hybrid Search: Combining Dense Vector retrieval with Sparse Lexical (BM25) using Reciprocal Rank Fusion (RRF).
  * Multi-stage retrieval: Fast ANN candidate generation (Top-100) followed by Cross-Encoder Re-Ranking (Top-5).
  * Document security: Enforcing role-based access control (RBAC / ACL) filtering at the index layer without latency degradation.
* **Staff Drill:** Design an enterprise knowledge retrieval system searching across 100 million internal corporate documents with strict multi-tenant access control and sub-300ms end-to-end latency.

### Week 39: Aug 16 – Aug 22, 2027 | Real-Time Fraud Detection & Multimodal Retrieval
* **Readings:** *MLSDI* — Chapters 3 & 8 (Visual Search & Fraud Detection).
* **Deep Dive Topics:**
  * Dual-encoder multimodal architectures (CLIP).
  * Streaming feature extraction for fraud: Sliding window aggregations over Flink/Kafka (< 30ms decision SLA).
  * Automated RAG evaluations: RAGAS / TruLens metrics (Faithfulness, Answer Relevance, Context Precision).
* **Staff Drill:** Design PayPay’s real-time payment fraud detection system combining streaming aggregations on Flink with a low-latency model inference engine.

---

## Phase 10: Staff/Principal Interview Mastery & Defense (Week 40)

### Week 40: Aug 23 – Aug 31, 2027 | Staff Interview Simulations & Defense
* **Deliverables & Activities:**
  * Conduct 5 full 45-minute timed system design sessions with recorded playback.
  * Practice cross-cutting Staff pushback drills:
    1. *"Our cloud bill is $2M/month, how do you cut compute and network egress costs by 50% without hurting SLAs?"*
    2. *"A trans-Pacific fiber cut just severed Tokyo from US-East, walk me through what fails and what stays up."*
    3. *"Explain why you chose Kafka + Flink over a simple Celery + Redis queue for this pipeline."*
  * Finalize resume highlighting enterprise AI platform achievements, scale, throughput, and reliability metrics.
  * Launch live applications on **TokyoDev** and **Japan Dev** to **Mercari, PayPay, Woven by Toyota, and Line Yahoo**.
