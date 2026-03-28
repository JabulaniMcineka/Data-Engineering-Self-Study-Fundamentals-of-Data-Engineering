# 📚 Chapter 6 - Storage
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 6 covers **storage** — the cornerstone of the entire data engineering lifecycle. Storage underlies every stage: ingestion, transformation, and serving. Data gets stored many times as it moves through the lifecycle.

> **Key idea:** To paraphrase an old saying — it's storage all the way down. Knowing the use case of your data and how you will retrieve it in the future is the first step to choosing the right storage solution.

---

## 🧱 Raw Ingredients of Data Storage

Before choosing a storage system, you must understand the physical components that make up storage.

### Storage Performance Comparison

| Storage Type | Latency | Bandwidth | Cost | Best For |
|---|---|---|---|---|
| **CPU Cache** | 1 nanosecond | ~1 TB/s | Very expensive | Active CPU processing |
| **RAM** | 0.1 microseconds | ~100 GB/s | ~$10/GB | Fast in-memory processing |
| **SSD** | 0.1 milliseconds | ~4 GB/s | ~$0.20/GB | OLTP databases, fast queries |
| **HDD** | 4 milliseconds | ~300 MB/s | ~$0.03/GB | Bulk storage, data lakes |
| **Object Storage** | 100 milliseconds | ~3 GB/s per instance | ~$0.02/GB/month | Data lakes, cloud warehouses |
| **Archival Storage** | 12 hours | Same as object once available | ~$0.004/GB/month | Compliance, long-term backup |

---

### 💿 Magnetic Disk Drive (HDD)

- Uses spinning platters and a read/write head to store binary data
- Very cheap — roughly **$0.03/GB** of capacity
- Transfer speed: 200–300 MB/s maximum
- Random access is slow due to physical seek time and rotational latency
- IOPS: 50–500 — very low compared to SSDs
- Still the **backbone of bulk data storage** because of low cost
- Cloud object storage uses thousands of HDDs in parallel for high throughput

**Key limitation:** Physics. You cannot make a spinning disk as fast as flash memory.

---

### ⚡ Solid State Drive (SSD)

- Stores data as electrical charges in flash memory cells
- No moving parts — purely electronic reads
- Random access latency: less than **0.1 ms** (100 microseconds)
- Transfer speeds: multiple GB/s
- IOPS: tens of thousands
- Cost: ~**$0.20/GB** — roughly 10x more expensive than HDD
- The **standard for OLTP systems** (transactional databases)
- Used in OLAP systems for caching frequently accessed data

---

### 🧠 Random Access Memory (RAM)

- Attached directly to the CPU — the fastest storage available
- Latency: ~100 nanoseconds — **1,000x faster than SSD**
- Bandwidth: ~100 GB/s per CPU
- Cost: ~**$10/GB**
- **Volatile** — data is lost when power is cut
- Used for: caching, active data processing, database indexes
- CPU also has its own cache (L1, L2, L3) which is even faster than RAM

---

### 🌐 Networking and CPU

- Distributed storage requires fast networks to move data between nodes
- CPUs handle request routing, aggregation, load balancing
- Storage in the cloud is essentially a **web application** with an API
- Network performance determines the effective throughput of distributed storage
- Engineers must balance **geographic spread** (durability) vs **proximity** (performance)

---

### 📦 Serialization

Serialization is converting data from in-memory format into a standard format that can be stored on disk or sent over a network.

**Common serialization formats:**

| Format | Type | Best For |
|---|---|---|
| CSV | Row-based, text | Simple data exchange |
| JSON | Semi-structured, text | APIs, web apps |
| Parquet | Columnar, binary | Analytics, data lakes |
| Avro | Row-based, binary | Streaming, Kafka |
| ORC | Columnar, binary | Hive, analytics |
| Apache Arrow | In-memory, columnar | Fast in-memory processing |

> 💡 **Learn Apache Parquet deeply** — it is the most important serialization format in modern data engineering.

---

### 🗜️ Compression

Compression makes data smaller. In storage systems, this has three major advantages:

1. **Less disk space** — data takes up less room
2. **Faster scan speeds** — a 10:1 compression ratio turns 200 MB/s into effective 2 GB/s
3. **Better network performance** — less data to transfer across the network

**Trade-off:** Compression and decompression require CPU time and resources.

---

### 💾 Caching

Caching stores **frequently or recently accessed data** in faster storage so it does not need to be fetched from slower storage each time.

**Cache hierarchy:**
```
CPU Cache (fastest, most expensive)
      ↓
RAM
      ↓
SSD
      ↓
HDD
      ↓
Object Storage
      ↓
Archival Storage (slowest, cheapest)
```

Every storage architecture uses multiple cache layers. Understanding the cache hierarchy helps you design faster and cheaper systems.

---

## 🗄️ Data Storage Systems

### Single Machine vs Distributed Storage

| | Single Machine | Distributed Storage |
|---|---|---|
| **Scale** | Limited by one server | Scales across many servers |
| **Reliability** | Single point of failure | Redundancy built in |
| **Performance** | Limited by one disk | Parallelism across many disks |
| **Use case** | Small datasets, prototyping | Production data engineering |

---

### ⚖️ Eventual vs Strong Consistency

A critical concept in distributed storage:

**Strong Consistency:**
- Every read returns the **latest written value**
- Slower — requires consensus across all nodes before responding
- Use when: accuracy is critical (financial transactions)

**Eventual Consistency (BASE):**
- Reads will **eventually** return consistent values — but not immediately
- Faster — nodes respond without waiting for full consensus
- Use when: scale and speed matter more than instant accuracy

| | ACID | BASE |
|---|---|---|
| **Stands for** | Atomicity, Consistency, Isolation, Durability | Basically Available, Soft-state, Eventual consistency |
| **Consistency** | Strong — always accurate | Eventual — accurate over time |
| **Best for** | Transactional databases (OLTP) | Large scale distributed NoSQL |

---

### 📁 File Storage

- Organised into a directory tree (folders and subfolders)
- Supports random reads AND writes
- Supports append operations
- Examples: local disk, NAS (Network Attached Storage), Amazon EFS

**Use in data engineering:** Use for temporary, one-time ingestion steps. Avoid for production pipelines — prefer object storage instead.

---

### 🧱 Block Storage

- Raw storage provided by SSDs and HDDs
- The operating system manages files on top of block storage
- Supports high random access performance
- Standard for VM operating systems and transactional databases

**Cloud examples:**
| Service | Provider | Use Case |
|---|---|---|
| Amazon EBS | AWS | VM disks, databases |
| Azure Managed Disks | Azure | VM disks, databases |
| Google Persistent Disk | GCP | VM disks, databases |

**RAID** (Redundant Array of Independent Disks) — combines multiple disks for redundancy and performance. Still used in on-premises systems.

**Instance Store Volumes** — physically attached to cloud VMs. Very fast and cheap but data is **lost when the VM shuts down**. Great for temporary processing caches.

---

### ☁️ Object Storage

Object storage is the **gold standard for data engineering storage** in the cloud.

**Key characteristics:**
- Objects are **immutable** — write once, cannot update or append in place
- To change data, rewrite the entire object
- Supports random reads through range requests
- Massively scalable — virtually limitless storage
- Highly durable — data replicated across multiple availability zones
- Cheap — ~$0.02/GB/month

**How it works:**
```
Bucket (top-level container)
      ↓
Object Key (unique identifier)
      ↓
Object Data (any binary content)
```

**Example S3 path:**
```
s3://my-data-lake/project/2026/03/data.parquet
```
Note: This LOOKS like a directory but it is NOT — it is just a key with slashes in it. Directory operations on object storage can be slow for large buckets.

**Object Storage Consistency:**
- Historically S3 was eventually consistent — now strongly consistent
- Can add a strongly consistent database layer for guaranteed consistency

**Object Versioning:**
- When you overwrite an object, the old version is retained
- Allows rollback to previous versions
- Costs more storage — set lifecycle policies to delete old versions

**Storage Classes (S3 example):**

| Class | Use Case | Access Cost | Storage Cost |
|---|---|---|---|
| S3 Standard | Frequently accessed | Low | High |
| S3 Standard-IA | Monthly access | Medium | Medium |
| S3 One Zone-IA | Monthly, less durable | Medium | Lower |
| S3 Glacier | Archival | High | Very low |
| S3 Glacier Deep Archive | Long-term compliance | Very high | Lowest |

---

### 🧮 Cache and Memory-Based Storage

**Memcached:**
- Simple key-value store
- Stores string and integer types
- Ultra-low latency — offloads pressure from backend databases
- No persistence — data lost on restart

**Redis:**
- Key-value store with richer data types (lists, sets, hashes)
- Supports persistence through snapshots and journaling
- Suitable for high-performance apps that can tolerate small data loss
- Also used as a message broker and pub/sub system

---

### 🐘 HDFS (Hadoop Distributed File System)

- Based on Google File System (GFS)
- Originally built for MapReduce processing
- Combines compute and storage on the same nodes
- Files broken into blocks (~128 MB) replicated across 3 nodes
- NameNode manages directory metadata and block locations

**Current status:**
- No longer a hot technology but still widely used in legacy systems
- Large organisations with massive Hadoop clusters still run HDFS
- Most new systems use cloud object storage (S3) instead
- Apache Spark still commonly runs on HDFS in some environments

---

### 🌊 Streaming Storage

- Stores event streams with configurable retention
- Supports **replay** — reprocess historical data from any point in time
- Used as source systems AND data engineering systems

**Examples:**
| Platform | Provider | Type |
|---|---|---|
| Apache Kafka | Open source / Confluent | Self-managed or managed |
| Amazon Kinesis | AWS | Fully managed |
| Google Pub/Sub | GCP | Fully managed |
| Apache Pulsar | Open source | Self-managed |

---

## 📊 Indexes, Partitioning, and Clustering

### From Rows to Columns

**Row-oriented storage (OLTP):**
- Data stored row by row on disk
- Fast for individual record lookups
- Slow for scanning millions of rows

**Columnar storage (OLAP):**
- Data stored column by column on disk
- Only reads the columns needed for a query
- Extremely high compression ratios — similar values are packed together
- Fast for large scans and aggregations

### Partitioning

Splits a table into subtables based on a field value:
```
Table: orders
Partition by: year
      ↓
partition_2024/
partition_2025/
partition_2026/
```

A query filtering by year only scans the relevant partition — dramatically reducing data read.

**Most common partitioning strategies:**
- Date or timestamp (most common in analytics)
- Geographic region
- Customer ID or tenant ID

### Clustering

Sorts data WITHIN a partition by one or more fields:
- Colocates similar values together
- Improves performance for filtering, sorting, and joining

### Snowflake Micro-Partitioning

Snowflake uses an algorithmic approach called micro-partitions:
- Sets of rows between 50–500 MB uncompressed
- Automatically clusters similar values together
- Maintains metadata about each micro-partition (value ranges, row counts)
- Allows aggressive pruning — skips entire micro-partitions that don't match query filters

---

## 🏗️ Data Engineering Storage Abstractions

### Data Warehouse
- Structured, formatted data for analytics and reporting
- Optimised for complex SQL queries and aggregations
- Schema on write — data must conform to schema at write time
- Examples: Snowflake, Google BigQuery, Amazon Redshift

### Data Lake
- Stores ALL data — structured, semi-structured, and unstructured
- Built on cheap object storage (S3, GCS, ADLS)
- Originally write-once, read-many — no updates or deletes
- Can become a **data swamp** without proper governance
- Stores raw files in any format: Parquet, JSON, CSV, images, video

### Data Lakehouse
- Combines the best of data warehouses AND data lakes
- Stores data in open formats in object storage
- Adds ACID transaction support — update and delete records
- Supports table history and rollback
- Examples: Databricks Delta Lake, Apache Hudi, Apache Iceberg

**Comparison:**

| | Data Warehouse | Data Lake | Data Lakehouse |
|---|---|---|---|
| **Storage** | Proprietary format | Object storage | Object storage |
| **Schema** | Strict | Flexible | Flexible with governance |
| **ACID** | Yes | No | Yes |
| **Unstructured data** | No | Yes | Yes |
| **Cost** | Expensive | Cheap | Moderate |

### Data Catalog
- Centralised metadata store for all data in the organisation
- Makes data **discoverable** — users can search and find data
- Tracks data lineage — where data came from and how it changed
- Provides a social layer — users can add descriptions and wiki pages
- Critical for data governance and data mesh architectures
- Examples: Apache Atlas, AWS Glue Data Catalog, Databricks Unity Catalog

---

## 🔥 Data Temperature and Lifecycle

### Hot, Warm, and Cold Data

| Temperature | Access Pattern | Storage | Cost |
|---|---|---|---|
| 🔥 **Hot** | Constantly — real time | RAM or SSD | Most expensive |
| 🌤️ **Warm** | Monthly | Infrequent access tier | Medium |
| ❄️ **Cold** | Rarely — compliance only | Archival storage | Cheapest to store, expensive to retrieve |

### Data Retention Considerations

Ask these questions for every dataset:

1. **Value** — How important is this data? Can it be re-created?
2. **Time** — How long must we keep it? Does it lose value over time?
3. **Compliance** — Do regulations (HIPAA, GDPR, PCI) require retention?
4. **Cost** — What is the cost of storage vs the cost of retrieval?

**Best practice:** Set automated lifecycle policies to move data to cheaper storage tiers as it ages, and delete data when it is no longer needed.

---

## 🔑 Separation of Compute from Storage

This is one of the most important ideas in modern data engineering:

**Old approach (colocation):**
```
[Compute + Storage on same server]
```
- Data and processing on the same machine
- Fast but inflexible — must keep servers running permanently

**Modern approach (separation):**
```
[Object Storage] ← permanent, cheap, durable
      ↓
[Ephemeral Compute Cluster] ← spin up when needed, delete when done
```
- Store data cheaply in object storage
- Spin up compute clusters only when processing is needed
- Pay only for compute while it runs
- Scale up massively for big jobs — then delete the cluster

**Why this matters:**
- Dramatically reduces costs
- Allows massive scale on demand
- Data remains safe even if compute clusters fail
- The dominant pattern in all modern cloud data engineering

**Real world examples:**

| System | Storage | Compute |
|---|---|---|
| AWS EMR | S3 | Temporary Spark/Hadoop cluster |
| Google BigQuery | Colossus (GCS) | Serverless query engine |
| Snowflake | S3/Azure Blob/GCS | Virtual warehouses |
| Databricks | S3/ADLS/GCS | Spark clusters |

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **HDD** | Hard Disk Drive — cheap, slow, spinning magnetic disk |
| **SSD** | Solid State Drive — fast, expensive, flash memory |
| **RAM** | Random Access Memory — ultra-fast but volatile |
| **Serialization** | Converting data to a standard format for storage or transmission |
| **Parquet** | Most popular columnar file format for data engineering |
| **Compression** | Making data smaller to save space and improve scan speeds |
| **Object storage** | Key-value store for immutable objects — gold standard for data lakes |
| **HDFS** | Hadoop Distributed File System — legacy big data storage |
| **ACID** | Atomicity Consistency Isolation Durability — strong transaction guarantees |
| **BASE** | Basically Available Soft-state Eventual consistency |
| **Eventual consistency** | Reads become consistent over time but not immediately |
| **Strong consistency** | Every read returns the latest written data |
| **Data lakehouse** | Hybrid of data lake and data warehouse |
| **Data catalog** | Centralised metadata store for data discovery |
| **Partitioning** | Splitting a table into subtables by a field to speed up queries |
| **Clustering** | Sorting data within partitions to colocate similar values |
| **Zero copy cloning** | Creating a virtual copy of data without physically copying files |
| **TTL** | Time to Live — how long data is kept before expiring |
| **Schema on write** | Data must match schema at write time — strict |
| **Schema on read** | Schema applied when reading data — flexible |
| **Micro-partitioning** | Snowflake's automatic algorithmic approach to partitioning |
| **Replay** | Reprocessing historical data from a point in time in streaming systems |
| **IOPS** | Input/Output Operations Per Second — measure of disk performance |
| **RAID** | Redundant Array of Independent Disks — combines disks for redundancy |
| **EBS** | Amazon Elastic Block Store — cloud block storage for VMs |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| Object storage | Already using S3 at AHRI — deepen understanding of storage classes |
| Parquet | Already using Parquet in pipelines — understand compression better |
| Data lifecycle policies | Set up automated S3 lifecycle rules to move old data to cheaper tiers |
| Separation of compute and storage | Design all pipelines to use ephemeral compute with S3 as storage layer |
| Data catalog | Explore AWS Glue Data Catalog or Apache Atlas for AHRI |
| Eventual vs strong consistency | Understand DynamoDB consistency modes for future projects |
| Columnar storage | Study columnar databases like BigQuery and Snowflake more deeply |
| Data temperature | Design storage tiering strategy for clinical data at AHRI |
| Partitioning | Always partition large tables by date in my analytical pipelines |
| Data lakehouse | Study Delta Lake and Apache Iceberg for future architecture decisions |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/)
- [Apache Parquet Documentation](https://parquet.apache.org/docs/)
- [Delta Lake Documentation](https://docs.delta.io)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [Designing Data-Intensive Applications — Martin Kleppmann](https://dataintensive.net)
- [Redis Documentation](https://redis.io/docs/)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*