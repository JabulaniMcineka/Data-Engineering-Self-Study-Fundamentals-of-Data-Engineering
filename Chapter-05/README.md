# 📚 Chapter 5 - Data Generation in Source Systems
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 5 covers the first stage of the data engineering lifecycle — **data generation in source systems**. Before you can build pipelines, you must understand where data comes from, how it is created, and the characteristics and quirks of the systems that produce it.

> **Key idea:** Source systems are usually outside your control — but they are still your responsibility. Bad upstream data causes problems everywhere downstream.

---

## 🌍 How Is Data Created?

```
ANALOG DATA                    DIGITAL DATA
Speech, writing, music    →    Converted to digital form
                               OR
                               Natively created by apps,
                               sensors, and transactions
```

Data exists everywhere:
- 📱 Mobile and web applications
- 🏦 Banking and financial transactions
- 📟 IoT sensors and devices
- 🔭 Scientific instruments
- 📊 Business systems and CRMs

> **Rule:** Get familiar with your source system. Read its documentation. Understand its patterns and quirks before you try to ingest from it.

---

## 📂 Main Types of Source Systems

### 1. 📄 Files and Unstructured Data

Files are a **universal medium of data exchange** — much of the world still sends data as files.

| Format | Type | Common Use |
|---|---|---|
| CSV | Structured/Semi | Most common tabular exchange |
| JSON | Semi-structured | APIs, web apps, configs |
| XML | Semi-structured | Legacy systems, enterprise apps |
| Excel | Structured | Business reporting, government data |
| TXT | Unstructured | Logs, raw text data |

---

### 2. 🏦 Application Databases (OLTP Systems)

OLTP = **Online Transaction Processing** — stores the current state of an application.

**Characteristics:**
- High speed reads and writes of individual records
- Supports thousands of concurrent users
- Low latency — sub-millisecond responses
- ACID compliant — guarantees data consistency
- NOT suited for large analytical queries

**Example:** A bank database that updates account balances in real time as transactions occur.

#### ACID Explained:

| Letter | Meaning | What It Guarantees |
|---|---|---|
| **A** | Atomicity | All or nothing — transactions fully complete or fully fail |
| **C** | Consistency | Every read returns the latest written value |
| **I** | Isolation | Concurrent updates don't interfere with each other |
| **D** | Durability | Committed data is never lost, even after power failure |

#### Atomic Transaction Example:
```
Bank Transfer: Account A → Account B

Step 1: Check Account A has enough funds
Step 2: Deduct from Account A
Step 3: Add to Account B

If ANY step fails → ENTIRE transaction is rolled back
Both accounts remain unchanged — no money is lost
```

---

### 3. 📊 OLAP Systems

OLAP = **Online Analytical Processing** — the opposite of OLTP.

| | OLTP | OLAP |
|---|---|---|
| **Optimised for** | Fast individual reads/writes | Large analytical queries |
| **Data size per query** | Single rows | Millions of rows |
| **Use case** | Application backend | Data warehouse, reporting |
| **Examples** | PostgreSQL, MySQL | Snowflake, BigQuery, Redshift |

> **Note:** Data engineers sometimes READ from OLAP systems too — for example, when serving data to ML models or reverse ETL workflows.

---

### 4. 🔄 Change Data Capture (CDC)

CDC captures **every change** that occurs in a database — inserts, updates, and deletes.

```
Source Database
      ↓
CDC captures every INSERT, UPDATE, DELETE
      ↓
Event stream sent to downstream systems
      ↓
Near real-time replication or analytics
```

**How CDC works:**
- Relational databases write changes to a **write-ahead log** before applying them
- CDC reads this log and converts changes into events
- Events are sent to a message queue or streaming platform

**Why it matters:** CDC is critical for building real-time data pipelines without overloading the source database.

---

### 5. 📋 Logs

Logs capture **who, what, and when** for every event in a system.

**Common log sources:**
- Operating systems
- Applications and servers
- Containers and Kubernetes
- Networks and firewalls
- IoT devices

**Log types:**

| Type | Format | Pros | Cons |
|---|---|---|---|
| **Binary** | Custom compact format | Very efficient, fast I/O | Not human readable |
| **Semistructured** | JSON | Portable, machine readable | Less efficient than binary |
| **Plain text** | Console output | Human readable | No standard format, hard to parse |

---

### 6. 🔄 CRUD vs Insert-Only Patterns

**CRUD** (Create, Read, Update, Delete):
- Standard pattern for most applications
- Records are updated in place
- Does not preserve history

**Insert-Only:**
- Never update — always insert a new record with a timestamp
- Preserves full history of changes
- Useful for audit trails and banking applications

```
CRUD approach:
Customer address: "123 Main St" → updated to "456 Oak Ave"
(old address is GONE)

Insert-only approach:
Record 1: "123 Main St" | 2024-01-01
Record 2: "456 Oak Ave" | 2025-03-15
(full history is PRESERVED)
```

**Downside of insert-only:** Tables grow very large, and looking up the current state requires finding the latest record.

---

### 7. 💬 Messages vs Streams

| | Message Queue | Event Streaming Platform |
|---|---|---|
| **What it stores** | Individual discrete messages | Ordered append-only log of events |
| **Retention** | Deleted after delivery | Retained for weeks or months |
| **Use case** | Routing messages between services | Analytics, event history, replay |
| **Examples** | Amazon SQS, RabbitMQ | Apache Kafka, Amazon Kinesis |

**Message:** A single signal sent from one system to another. Once delivered, it is removed.

**Stream:** A permanent, ordered log of events. You can replay from any point in time.

---

### 8. ⏰ Types of Time

Always record **all three timestamps** in your pipelines:

| Time Type | What It Means |
|---|---|
| **Event time** | When the event actually occurred in the source system |
| **Ingestion time** | When the event arrived in your pipeline |
| **Process time** | When the event was transformed or processed |

> 💡 **Why this matters:** Data often arrives late. Without all three timestamps, you cannot tell when something happened vs when you received it. This is critical for accurate analytics.

---

## 🗄️ Database Types Reference

### Relational Databases (RDBMS)
- Data stored in tables with rows and columns
- Fixed schema enforced by the database
- ACID compliant — strong consistency guarantees
- Supports joins across multiple tables
- Best for: transactional applications, structured data
- Examples: PostgreSQL, MySQL, Oracle, SQL Server

### Key-Value Stores
- Retrieves records using a unique key — like a dictionary
- Extremely fast lookups
- In-memory versions used for caching (data disappears on shutdown)
- Persistent versions used for durable storage
- Best for: session data, caching, fast lookups
- Examples: Redis, DynamoDB

### Document Stores
- Stores nested JSON-like documents in collections
- No fixed schema — flexible and expressive
- Does NOT support joins — all related data must be in one document
- Generally NOT ACID compliant — often eventually consistent
- Best for: flexible schema apps, nested data
- Examples: MongoDB, Firestore, CouchDB

### Wide-Column Databases
- Optimised for massive scale and extremely high write rates
- Supports petabytes of data and millions of requests per second
- Single index (row key) — complex queries must be done elsewhere
- Best for: IoT, fintech, ad tech, real-time personalisation
- Examples: Apache Cassandra, Google Bigtable, HBase

### Graph Databases
- Stores data as nodes (entities) and edges (relationships)
- Optimised for traversing connections between entities
- Uses specialised query languages: Cypher, SPARQL, GQL
- Best for: social networks, fraud detection, recommendation engines
- Examples: Neo4j, Amazon Neptune

### Search Databases
- Optimised for text search and log analysis
- Supports fuzzy matching, semantic search, and keyword search
- Uses indexes for fast retrieval
- Best for: product search, log analysis, anomaly detection
- Examples: Elasticsearch, Apache Solr, Algolia

### Time Series Databases
- Optimised for data organised by time
- Write-heavy workloads — uses memory buffering
- Best for: IoT sensors, metrics, application logs, financial tick data
- Examples: InfluxDB, Apache Druid, TimescaleDB

---

## 🌐 API Types

### REST
- Most common API style
- Stateless — each call is independent
- Built around HTTP verbs: GET, POST, PUT, DELETE
- Widely supported — most SaaS platforms offer REST APIs
- Limitation: no universal standard — every REST API has quirks

### GraphQL
- Created at Facebook
- Allows flexible queries across multiple data models in one request
- Returns data in the exact shape you requested
- More expressive than REST but less widely adopted

### Webhooks
- Called **reverse APIs** — source pushes data TO you
- Triggered when specific events occur in the source system
- Useful for real-time event collection
- Common use: payment notifications, form submissions, app events

### gRPC
- Created by Google — now an open standard
- Uses Protocol Buffers for efficient binary data serialisation
- Supports bidirectional streaming
- Very efficient — lower CPU and bandwidth than REST
- Common in Google Cloud and internal microservices

---

## 🤝 Working with Source System Stakeholders

Two types of stakeholders you will work with:

| Stakeholder Type | Who They Are | What They Control |
|---|---|---|
| **Systems stakeholders** | Software engineers, app developers | The source systems themselves |
| **Data stakeholders** | IT, data governance, third parties | Access to the data |

### Data Contracts
A **data contract** is a written agreement between the source system owner and the data engineer:
- What data is being extracted
- How it is extracted (full or incremental)
- How often it is extracted
- Who the contacts are on both sides
- Where the contract is stored (GitHub, internal docs)

### SLA and SLO
- **SLA** (Service Level Agreement) — what you can expect from the source system
- **SLO** (Service Level Objective) — how performance is measured against the SLA
- Example SLA: "Source systems will be reliably available and of high quality"
- Example SLO: "Source systems will have 99% uptime"

---

## ⚡ Event Streaming Deep Dive

### Topics
A topic is a collection of related events in a streaming platform.

```
Producer (web orders app)
      ↓
Topic: "web-orders"
      ↓
Consumer 1: Fulfilment team
Consumer 2: Marketing analytics
```

### Stream Partitions
- Subdivisions of a stream into multiple parallel streams
- Like multiple lanes on a freeway — increases throughput
- Messages with the same partition key always go to the same partition
- Watch out for **hotspotting** — uneven distribution of messages across partitions

### Fault Tolerance
- Streaming platforms are distributed — data is replicated across nodes
- If one node fails, another takes over — no data loss
- This makes streaming platforms reliable for mission-critical event data

---

## 🔒 Undercurrents Applied to Source Systems

### Security
- Ensure data is encrypted at rest and in transit
- Use VPN instead of public internet where possible
- Store credentials in a secrets manager — never in code
- Always verify that source systems are legitimate

### Data Management
- Expect schemas to change — set up alerts for schema changes
- Work with source teams to establish data quality expectations
- Understand data retention and privacy policies (GDPR, CCPA)

### DataOps
- Set up monitoring for source system uptime
- Build data quality checks into your ingestion pipeline
- Have a backfill plan for when source systems go offline
- Create a clear communication chain between your team and source teams

### Software Engineering
- Always use HTTPS or VPN — never plain HTTP
- Store credentials securely — use IAM roles and secrets managers
- Handle retries and timeouts in your ingestion code
- Design for idempotency — processing the same message twice should be safe

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **OLTP** | Online Transaction Processing — fast individual reads and writes |
| **OLAP** | Online Analytical Processing — large analytical queries |
| **ACID** | Atomicity, Consistency, Isolation, Durability — database transaction guarantees |
| **CDC** | Change Data Capture — tracking every change in a source database |
| **CRUD** | Create, Read, Update, Delete — standard application data pattern |
| **Insert-only** | Never update — always insert new records with timestamps |
| **REST** | Most common API style — stateless HTTP-based data exchange |
| **Webhook** | Reverse API — source system pushes events to your endpoint |
| **gRPC** | Google's efficient remote procedure call framework |
| **Idempotent** | Processing a message multiple times produces the same result as once |
| **Data contract** | Written agreement between source system owner and data engineer |
| **SLA** | Service Level Agreement — expectations for system availability |
| **SLO** | Service Level Objective — measurable targets within an SLA |
| **Event time** | When an event actually occurred in the source system |
| **Ingestion time** | When an event arrived in your pipeline |
| **Process time** | When an event was transformed or processed |
| **Hotspotting** | When one stream partition receives too many messages |
| **Write-ahead log** | Database log that records changes before they are applied |
| **Eventual consistency** | System will become consistent over time but not instantly |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| CDC | Study CDC patterns deeply — critical for real-time pipelines |
| Data contracts | Set up contracts with AHRI source system owners |
| OLTP vs OLAP | Know the difference when designing ingestion strategies |
| API patterns | Practice REST and webhook ingestion in Python |
| Timestamps | Always record event, ingestion, and process time in pipelines |
| Schema changes | Set up monitoring for upstream schema changes at AHRI |
| Graph databases | Explore Neo4j for future learning |
| Streaming | Continue building on Kafka and Kinesis knowledge |
| Idempotency | Design all pipelines to be idempotent from the start |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Designing Data-Intensive Applications — Martin Kleppmann](https://dataintensive.net)
- [Elasticsearch Documentation](https://www.elastic.co/guide/index.html)
- [Neo4j Graph Database](https://neo4j.com)
- [REST API Design — Roy Fielding Dissertation](https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*