# 📚 Chapter 4 - Choosing Technologies Across the Data Engineering Lifecycle
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 4 explains how to choose the right technologies to support your data architecture. With thousands of tools available, data engineers must make smart, deliberate choices based on real business needs — not hype, trends, or personal preference.

> **Key idea:** Architecture is strategic — it is the WHAT, WHY, and WHEN. Technology is tactical — it is the HOW. Always get your architecture right before choosing your tools.

---

## ⚠️ The Most Important Rule

```
Architecture First → Technology Second
```

Never choose tools before you have a clear architecture. Teams that do this end up with a "Dr. Seuss fantasy machine" — a mess of disconnected tools with no clear purpose.

---

## 📋 Key Considerations When Choosing Technology

### 1. 👥 Team Size and Capabilities
- Small teams should use **managed and SaaS tools** — avoid complexity
- Large teams can afford to manage more complex solutions
- Avoid **cargo-cult engineering** — copying what giant tech companies do without having their resources
- Stick with technologies your team already knows — learning new tools takes significant time

### 2. 🚀 Speed to Market
- Speed wins in technology — deliver value early and often
- **Perfect is the enemy of good** — don't deliberate for months
- Slow decisions kill data teams — you were hired to deliver value
- Choose tools that help you move quickly, reliably, and securely

### 3. 🔗 Interoperability
- Rarely will you use only one technology — systems must talk to each other
- Always ask: how easily does tool A integrate with tool B?
- Look for tools with built-in integrations or standard connectors (JDBC, ODBC, REST APIs)
- Design for modularity — make it easy to swap tools as better ones emerge

### 4. 💰 Cost Optimization and Business Value
Three lenses for evaluating cost:

| Lens | What It Means |
|---|---|
| **TCO** (Total Cost of Ownership) | All direct and indirect costs of a technology |
| **TOCO** (Total Opportunity Cost) | The cost of what you gave up by choosing this tool |
| **FinOps** | Actively managing cloud spending like an engineering discipline |

**Capex vs Opex:**
| | Capex | Opex |
|---|---|---|
| **What it is** | Large up-front investment | Pay as you go |
| **Example** | Buying on-premises servers | AWS monthly bill |
| **Flexibility** | Low — hard to change | High — scale up or down |
| **Recommendation** | Avoid where possible | Preferred approach |

> 💡 **Take an opex-first approach** — cloud and pay-as-you-go technologies give you the flexibility to adapt as the landscape changes.

### 5. ⏳ Today vs the Future: Immutable vs Transitory Technologies

| Type | What It Means | Examples |
|---|---|---|
| **Immutable** | Technologies that stand the test of time | SQL, Bash, Object Storage (S3), Linux |
| **Transitory** | Technologies that come and go | JavaScript frameworks, Hadoop, specific orchestration tools |

> 💡 **The Lindy Effect:** The longer a technology has been around, the longer it will continue to be used. Use this as a test for whether a technology is immutable.

**Our advice:** Evaluate your technology choices every **2 years**. Build transitory tools around immutable foundations.

### 6. 📍 Location: Where to Run Your Stack

| Option | What It Means | Best For |
|---|---|---|
| **On Premises** | Own your hardware in your data centre | Established companies, regulated industries |
| **Cloud** | Rent hardware and services from AWS/Azure/GCP | Startups, scalable workloads, flexibility |
| **Hybrid Cloud** | Mix of on-premises and cloud | Companies mid-migration |
| **Multicloud** | Use multiple cloud providers | Serving customers across clouds, best-of-breed services |

**Cloud Economics — Key Points:**
- Cloud is NOT the same as on-premises — different pricing model entirely
- **Lift and shift** (moving servers directly to cloud VMs) is expensive — optimise for cloud
- **Data gravity** — getting data INTO a cloud is cheap, getting it OUT is expensive
- Use autoscaling, spot instances, and serverless to optimise cloud costs

### 7. 🏗️ Build vs Buy

| Option | When to Choose It |
|---|---|
| **Build** | Only when it gives you a genuine competitive advantage |
| **Buy (OSS)** | Default choice — use what already exists |
| **Buy (Managed/SaaS)** | When you don't want to manage the infrastructure |

> 💡 **Rule of thumb:** "When you need new tires, do you build them from scratch or buy them?" Buy whenever possible. Build only where it creates real competitive advantage.

**Types of software:**

| Type | What It Means | Examples |
|---|---|---|
| **Community OSS** | Free, community maintained | Apache Airflow, dbt, Spark |
| **Commercial OSS (COSS)** | Managed version of OSS — you pay for convenience | Databricks (Spark), Confluent (Kafka), dbt Cloud |
| **Proprietary** | Closed source, vendor managed | Informatica, Fivetran |
| **Cloud Native** | Built by cloud providers | AWS Glue, Google BigQuery, Azure Data Factory |

### 8. 🧩 Monolith vs Modular

| | Monolith | Modular |
|---|---|---|
| **What it is** | One system doing everything | Many small tools each doing one job |
| **Pros** | Simple to reason about | Flexible, swappable, best-of-breed |
| **Cons** | Brittle, hard to change, vendor lock-in | More complex, harder to manage |
| **Best for** | Getting started quickly | Production systems at scale |

> ⚠️ **Distributed Monolith:** A common trap — building a distributed system that still has all the problems of a monolith because everything shares the same dependencies (e.g. traditional Hadoop clusters).

### 9. ☁️ Serverless vs Servers

| | Serverless | Servers |
|---|---|---|
| **What it is** | Run code without managing infrastructure | You manage the machines |
| **Pros** | Quick to start, pay per use, auto-scales | Full control, cheaper at scale |
| **Cons** | Expensive at high volume, limited runtime | Operational overhead |
| **Examples** | AWS Lambda, Google BigQuery, Cloud Functions | EC2, Kubernetes clusters |

**When to use serverless:**
- Simple, discrete tasks with low frequency
- Quick prototypes and experiments
- When you want to avoid managing infrastructure

**When to use servers:**
- High-volume, complex workloads
- When serverless costs exceed server costs
- When you need full control and customisation

> 💡 **Rule:** Start with serverless. Move to servers with containers when you outgrow it.

---

## ⚔️ Benchmark Wars — Buyer Beware

Vendors often use misleading benchmarks to make their products look better. Watch out for:

| Trick | What It Means |
|---|---|
| **Small test datasets** | Claiming "petabyte scale" but testing on tiny data |
| **Nonsensical cost comparisons** | Comparing ephemeral vs permanent systems on cost per second |
| **Asymmetric optimisation** | Tuning their system but not the competitor's |

> Always test with your **own real-world data and query patterns** before trusting vendor benchmarks.

---

## 🔄 How Undercurrents Impact Technology Choices

| Undercurrent | What To Ask When Evaluating Tools |
|---|---|
| **Security** | How does this tool protect data? Is it GDPR/CCPA compliant? |
| **Data Management** | Does it support data quality, lineage, and governance? |
| **DataOps** | How do I monitor it? What happens when it breaks? |
| **Architecture** | Is this decision reversible? Does it integrate with my stack? |
| **Orchestration** | How does Airflow or Dagster manage this tool? |
| **Software Engineering** | Can I deploy it as code? Does it support CI/CD? |

---

## 🎼 Orchestration Deep Dive: Apache Airflow

| Feature | Detail |
|---|---|
| **Created by** | Maxime Beauchemin at Airbnb in 2014 |
| **Type** | Open source — Apache Software Foundation |
| **Managed versions** | AWS MWAA, GCP Cloud Composer, Astronomer |
| **Strengths** | Massive community, highly extensible, Python-based |
| **Weaknesses** | Scheduler can be a bottleneck, distributed monolith pattern, limited native data constructs |
| **Competitors** | Prefect, Dagster, Metaflow, Argo |

---

## 🗺️ How Everything Fits Together

```
BUSINESS NEED
      ↓
ARCHITECTURE (strategic — what, why, when)
      ↓
TECHNOLOGY SELECTION (tactical — how)
      ↓
Consider: Team size → Speed → Interoperability →
          Cost → Immutability → Location →
          Build vs Buy → Monolith vs Modular →
          Serverless vs Servers
```

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **TCO** | Total Cost of Ownership — all costs of a technology |
| **TOCO** | Total Opportunity Cost — what you gave up by choosing this tool |
| **FinOps** | Managing cloud costs like an engineering discipline |
| **Capex** | Capital expenditure — large up-front investment |
| **Opex** | Operational expenditure — pay as you go |
| **Immutable technology** | Technology that stands the test of time (SQL, S3, Linux) |
| **Transitory technology** | Technology that comes and goes (frameworks, specific tools) |
| **Lindy Effect** | The longer a technology exists, the longer it will continue to exist |
| **Lift and shift** | Moving on-premises servers directly to cloud VMs without optimising |
| **Data gravity** | Once data is in a cloud, it is expensive and difficult to move out |
| **COSS** | Commercial Open Source Software — managed version of an OSS project |
| **Cargo-cult engineering** | Copying what big tech companies do without having their resources |
| **Serverless** | Running code without managing the underlying infrastructure |
| **Distributed monolith** | A distributed system that still has all the problems of a monolith |
| **DeWitt clause** | A clause that prevented vendors from publishing benchmarks — now mostly gone |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| Architecture first | Always understand the business need before picking tools |
| Team size | I am learning solo — focus on managed and SaaS tools first |
| Build vs Buy | Default to OSS and managed services — build only for competitive advantage |
| Immutable technologies | Double down on SQL, Python, and Bash — they will always be relevant |
| Cloud economics | Learn AWS pricing model deeply — study FinOps basics |
| Airflow | Continue learning Airflow — already using it in my SA Artists project |
| Serverless | Study AWS Lambda and serverless patterns more deeply |
| Monolith vs Modular | Aim for modular, loosely coupled pipelines from day one |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [Apache Airflow](https://airflow.apache.org)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [FinOps Foundation](https://www.finops.org)
- [dbt — Data Build Tool](https://www.getdbt.com)
- [Dagster — Modern Orchestration](https://dagster.io)
- [Prefect — Workflow Orchestration](https://www.prefect.io)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*