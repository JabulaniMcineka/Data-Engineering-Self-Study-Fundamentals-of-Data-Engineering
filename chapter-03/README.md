
Chapter 03 readme · MD
Copy

# 📚 Chapter 3 - Designing Good Data Architecture
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026
 
---
 
## 🎯 What This Chapter Is About
 
Chapter 3 defines what makes **good data architecture** and why it matters. It covers the principles behind great architecture decisions, the major architecture patterns used in the industry today, and how data engineers should think about designing systems that evolve with the business.
 
> **Key idea:** Good architecture is never finished. It is a living, breathing thing that constantly evolves in response to business needs and new technology.
 
---
 
## 🏗️ What Is Data Architecture?
 
Data architecture sits inside **enterprise architecture** — the broader design of all systems across a business.
 
```
Enterprise Architecture
├── Business Architecture
├── Technical Architecture
├── Application Architecture
└── Data Architecture  ← This is where we live
```
 
### Two Types of Data Architecture:
 
| Type | What It Means | Example |
|---|---|---|
| **Operational** | WHAT needs to happen | Business goals, processes, people |
| **Technical** | HOW it will happen | Ingestion, storage, transformation, serving |
 
> **Our definition:** Data architecture is the design of systems to support the evolving data needs of an enterprise, achieved by flexible and reversible decisions reached through careful evaluation of trade-offs.
 
---
 
## ✅ What Makes "Good" Data Architecture?
 
Good data architecture:
- Serves business requirements with **reusable building blocks**
- Maintains **flexibility** and makes appropriate trade-offs
- Is **agile** — it evolves as the business and technology change
- Uses **loosely coupled** systems that can change independently
 
Bad data architecture:
- Is **tightly coupled** and rigid
- Is **overly centralized** — one size fits all
- Uses the **wrong tools** for the job
- Makes **irreversible decisions** that are costly to undo
 
---
 
## 📐 The 9 Principles of Good Data Architecture
 
### Principle 1 — Choose Common Components Wisely 🧩
- Use shared tools and systems that work across the whole organisation
- Common components include object storage, version control, orchestration, monitoring
- Avoid forcing everyone into one-size-fits-all solutions
- Balance shared standards with flexibility for domain-specific needs
 
### Principle 2 — Plan for Failure 💥
> "Everything fails, all the time." — Werner Vogels (Amazon CTO)
 
Key terms for designing around failure:
 
| Term | Meaning |
|---|---|
| **Availability** | Percentage of time a system is up and running |
| **Reliability** | Probability a system performs as expected |
| **RTO** | Recovery Time Objective — max acceptable downtime |
| **RPO** | Recovery Point Objective — max acceptable data loss |
 
### Principle 3 — Architect for Scalability 📈
- **Scale up** — handle massive amounts of data when needed
- **Scale down** — reduce capacity when load decreases to save costs
- **Scale to zero** — shut down completely when not in use (serverless)
- **Elasticity** — automatically scale up and down based on current workload
 
### Principle 4 — Architecture Is Leadership 👥
- Data architects mentor engineers — they don't just make decisions
- Leadership does NOT mean command and control
- The best architects raise the level of the whole team
- As a data engineer, seek mentorship from architects and practice architectural thinking
 
### Principle 5 — Always Be Architecting 🔄
- Architecture is never done — it constantly evolves
- Maintain a **current state** (baseline), a **target state** (where you want to go), and a **sequencing plan** (how to get there)
- Architecture should be collaborative and agile — not waterfall
 
### Principle 6 — Build Loosely Coupled Systems 🔗
Loosely coupled systems:
- Are broken into **many small independent components**
- Communicate through **APIs and abstraction layers**
- Can be **updated independently** without breaking other parts
- Allow **teams to work independently** without constant coordination
 
> **Amazon's Bezos API Mandate (2002):** All teams must expose data through service interfaces. No direct access to another team's data store. Everything through APIs. This single decision led to the creation of AWS.
 
### Principle 7 — Make Reversible Decisions 🚪
- **One-way door** = a decision that is nearly impossible to reverse (avoid these)
- **Two-way door** = a decision you can easily undo (prefer these always)
- The data landscape changes rapidly — decisions made today may be wrong tomorrow
- Always ask: "Can we undo this if it doesn't work?"
 
### Principle 8 — Prioritize Security 🔒
Two key security models:
- **Hardened perimeter** — trust everything inside, block everything outside (outdated and risky)
- **Zero trust** — trust nothing by default, verify everything always (modern approach)
 
> **Shared Responsibility Model:** Cloud providers secure the infrastructure. YOU are responsible for securing your data and applications on top of it.
 
**Every data engineer is a security engineer.** Numerous data breaches have happened simply because someone misconfigured an Amazon S3 bucket with public access.
 
### Principle 9 — Embrace FinOps 💰
- **FinOps** = managing cloud financial costs the same way you manage performance
- In the cloud, you pay per query, per compute, per storage — costs can spike fast
- Monitor spending the same way you monitor CPU and memory
- Always ask: what is the most cost-effective way to run this job?
 
---
 
## 🔄 Tight vs Loose Coupling
 
| | Tight Coupling | Loose Coupling |
|---|---|---|
| **Dependencies** | Everything depends on everything | Services are independent |
| **Change** | Changing one thing breaks others | Each part changes independently |
| **Teams** | Teams must coordinate constantly | Teams work independently |
| **Risk** | High — one failure cascades | Low — failures are isolated |
| **Example** | Monolith | Microservices |
 
---
 
## 🏛️ Architecture Tiers
 
### Single Tier ❌
```
[Database + Application] ← all on one server
```
- Simple but dangerous — if one thing fails, everything fails
- OK for prototyping, NOT for production
 
### Multi Tier ✅
```
[Presentation Layer]
       ↓
[Application Logic Layer]
       ↓
[Data Layer]
```
- Each layer is separated and independent
- Changes in one layer don't break others
- Much more resilient and scalable
 
---
 
## 🏗️ Monolith vs Microservices
 
### Monolith
- One large codebase doing everything
- Simple to start — good for early stage
- Becomes a "big ball of mud" as it grows
- Hard to change, test, or scale individual parts
 
### Microservices
- Many small services each doing one specific job
- Each service is independent and loosely coupled
- If one service goes down the others keep running
- More complex to build but much easier to scale and maintain
 
> 💡 **Advice:** Start with a monolith if you need to move fast. Plan to break it apart as you grow. Don't get too comfortable with the monolith.
 
---
 
## 📦 Types of Data Architecture
 
### 🏭 Data Warehouse
- Central hub for **reporting and analytics**
- Data is **structured and formatted** before being stored
- Uses **ETL** (Extract Transform Load) or **ELT** (Extract Load Transform)
- Separates analytics from production databases
- Examples: **Snowflake, Google BigQuery, Amazon Redshift**
 
```
Source Systems → ETL/ELT → Data Warehouse → Data Marts → Analysts
```
 
**ETL vs ELT:**
| | ETL | ELT |
|---|---|---|
| **Transform when?** | Before loading | After loading |
| **Where transform?** | External system | Inside the warehouse |
| **Best for** | Traditional on-premises | Cloud data warehouses |
 
### 🌊 Data Lake
- Stores **ALL data** — structured and unstructured — in one place
- Originally built on Hadoop (HDFS), now on cloud object storage
- Very flexible but became a **data swamp** without good governance
- Problems: no schema management, poor data quality, hard to delete records
 
### 🏠 Data Lakehouse
- Combines the **best of data warehouses AND data lakes**
- Stores data in object storage (like a lake)
- Supports **ACID transactions** — can update and delete records
- Has data management and governance (like a warehouse)
- Examples: **Databricks Delta Lake, Apache Iceberg**
 
### 🔧 Modern Data Stack
- **Cloud-based, plug-and-play, modular** components
- Off-the-shelf tools that connect together easily
- Focus on self-service, agile data management, open source
- Reduces complexity — no need to build everything from scratch
- Examples: **dbt, Fivetran, Airbyte, Looker**
 
### ⚡ Lambda Architecture
```
Source → Batch Layer (slow, accurate)      → Serving Layer
       → Speed Layer (fast, approximate)   → Serving Layer
```
- Tries to combine batch and streaming in one architecture
- Very complicated to manage — two codebases doing similar things
- Mostly **outdated** — better options exist today
 
### 🌊 Kappa Architecture
- Uses **streaming as the backbone** for everything
- Simpler than Lambda — one codebase
- Still complex and expensive in practice
- Not widely adopted
 
### 🕸️ Data Mesh
- **Decentralised** architecture — opposite of a central data lake
- Each business domain **owns and serves its own data**
- Treats **data as a product**
- Four principles:
  1. Domain-oriented decentralised data ownership
  2. Data as a product
  3. Self-serve data infrastructure as a platform
  4. Federated computational governance
 
---
 
## 🌱 Brownfield vs Greenfield Projects
 
| | Brownfield | Greenfield |
|---|---|---|
| **What it is** | Redesigning existing architecture | Building from scratch |
| **Challenge** | Constrained by past decisions | Risk of shiny object syndrome |
| **Approach** | Use the strangler pattern — replace piece by piece | Focus on requirements not cool technology |
| **Risk** | Big bang rewrites fail — be surgical | Resume-driven development — building to impress not to deliver |
 
> 💡 **Strangler Pattern:** Slowly replace a legacy system one piece at a time. Don't do a big bang rewrite — it almost always fails.
 
---
 
## 🌍 Key Architecture Concepts
 
### Distributed Systems
- Use **horizontal scaling** — add more machines instead of one bigger machine
- **Leader node** distributes work to **worker nodes**
- Data is **replicated** — if one machine dies, others take over
- Most cloud data tools use distributed systems under the hood
 
### Event-Driven Architecture
- Business events (new order, new customer, payment) trigger data flows
- Three components: **event production → routing → consumption**
- Services are loosely coupled — producer does not know who consumes the event
- Growing in popularity alongside streaming data
 
### Multitenancy
- Multiple teams or customers sharing the same system
- Must carefully manage **performance** (noisy neighbour problem) and **security** (data isolation)
- Use views and access controls to isolate tenant data
 
---
 
## 📝 Key Terms Learned
 
| Term | Simple Meaning |
|---|---|
| **ETL** | Extract Transform Load — transform before loading |
| **ELT** | Extract Load Transform — transform after loading |
| **ACID** | Atomicity Consistency Isolation Durability — data transaction standards |
| **FinOps** | Managing cloud costs like an engineering discipline |
| **RTO** | Recovery Time Objective — how fast must we recover? |
| **RPO** | Recovery Point Objective — how much data loss is acceptable? |
| **Loose Coupling** | Services are independent and talk through APIs |
| **Monolith** | One large system doing everything |
| **Microservices** | Many small independent services each doing one job |
| **Data Lakehouse** | Hybrid of data lake and data warehouse |
| **Data Mesh** | Decentralised domain-driven data architecture |
| **Strangler Pattern** | Gradually replacing a legacy system piece by piece |
| **Greenfield** | Building a new system from scratch |
| **Brownfield** | Redesigning or improving an existing system |
| **Elasticity** | Ability to automatically scale up and down based on load |
| **Zero Trust** | Trust nothing by default — verify everything always |
 
---
 
## 🙋 How This Applies to Me
 
| Chapter Concept | My Action Plan |
|---|---|
| Data Warehouse vs Data Lake | Understand when to use each one |
| ETL vs ELT | Learn SQL transformations — practice with dbt |
| Loose Coupling | Study how APIs and microservices work |
| FinOps | Learn how cloud pricing works on AWS and Azure |
| Data Mesh | Read more about domain-driven design |
| Security | Study IAM roles and zero trust principles |
| Modern Data Stack | Learn the tools — dbt, Fivetran, Snowflake |
| Scalability | Understand how distributed systems work |
 
---
 
## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Google Cloud Architecture Principles](https://cloud.google.com/architecture/framework)
- [Data Mesh — Zhamak Dehghani](https://martinfowler.com/articles/data-mesh-principles.html)
- [FinOps Foundation](https://www.finops.org)
- [dbt — Data Build Tool](https://www.getdbt.com)
 
---
 
*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*
 