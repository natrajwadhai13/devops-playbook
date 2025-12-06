---
title: "• Non-RDS"
nav_order: 2
parent: "• Databases"
grand_parent: "• AWS"
grand_grand_parent: "4. Cloud Platforms"
---

Here are **A-to-Z Complete Notes on AWS Non-Relational Database Services** — 100% GitHub-ready, interview-oriented, simple to understand, and complete for DevOps, Cloud Engineers & Architects.

This covers **DynamoDB, Amazon Keyspaces, DocumentDB, Neptune, MemoryDB, and ElastiCache**.

---

# ⭐ **AWS Non-Relational Database Services – Complete Notes (A to Z)**

*(GitHub Markdown Format — clean & structured)*

---

# # **📌 1. What Are Non-Relational Databases?**

Non-relational databases (NoSQL) store data **without requiring fixed tables or strict schemas**.

AWS provides managed NoSQL services for:

* Key-Value
* Document
* Graph
* Columnar
* In-memory caching

These databases are best for:
✔ high scalability
✔ low latency
✔ flexible data
✔ big workloads
✔ serverless & distributed apps

---

# # **📌 2. AWS Non-Relational (NoSQL) Database Services List**

| AWS Service                              | Type                          | Use Case                       |
| ---------------------------------------- | ----------------------------- | ------------------------------ |
| **Amazon DynamoDB**                      | Key-Value & Document          | Serverless apps, microservices |
| **Amazon DocumentDB**                    | Document (MongoDB compatible) | JSON document apps             |
| **Amazon Keyspaces (Cassandra)**         | Wide Column Store             | Time-series, logging           |
| **Amazon Neptune**                       | Graph Database                | Social networks, relationships |
| **Amazon ElastiCache (Redis/Memcached)** | In-Memory                     | Caching, speed, session store  |
| **MemoryDB for Redis**                   | Durable In-Memory             | Enterprise Redis database      |

---

# # **📌 3. Amazon DynamoDB (Most Important NoSQL in AWS)**

Fully serverless **key-value + document** database.

---

## **🎯 DynamoDB Key Features**

* Fully Managed
* No servers — auto-scales
* Millisecond latency
* Unlimited storage
* Built-in **replication across 3 AZs**
* **On-Demand backup & PITR**
* DynamoDB Streams (event sourcing)
* IAM-based security
* Global Tables (multi-region active-active)

---

## **🎯 DynamoDB Core Components**

| Component                        | Meaning                             |
| -------------------------------- | ----------------------------------- |
| **Table**                        | Collection of items                 |
| **Item**                         | One row (but schema-less)           |
| **Attribute**                    | Value (field)                       |
| **Partition Key**                | Mandatory primary key               |
| **Sort Key**                     | Optional for ordering               |
| **GSI (Global Secondary Index)** | Index across partitions for queries |
| **LSI (Local Secondary Index)**  | Index within same partition key     |

---

## **🎯 DynamoDB Capacity Modes**

| Mode            | Best for              |
| --------------- | --------------------- |
| **On-Demand**   | Unpredictable traffic |
| **Provisioned** | Predictable traffic   |

---

## **🎯 DynamoDB Billing**

Billing depends on:

* Read Capacity Units (RCU)
* Write Capacity Units (WCU)
* Storage
* Streams
* Global Tables

---

## **🎯 DynamoDB Accelerator (DAX)**

* In-memory cache
* 10x performance boost
* Microsecond latency

---

## **🎯 DynamoDB Global Tables**

* Multi-region replication
* Active-Active architecture
* Zero downtime disaster recovery

---

## **🎯 DynamoDB Streams**

Captures table changes in real-time
Used for:

* Lambda triggers
* Event-driven architecture
* Cross-region replication

---

## **🎯 DynamoDB Security**

* Encryption at rest (KMS)
* IAM Policies
* VPC Endpoints
* CloudWatch metrics

---

## **🎯 DynamoDB Backup Options**

| Type                              | Description                   |
| --------------------------------- | ----------------------------- |
| **On-Demand Backup**              | Manual snapshot               |
| **Point-In-Time Recovery (PITR)** | Restore table to last 35 days |

---

## **🎯 Example DynamoDB Item**

```json
{
  "UserId": "101",
  "Name": "Natraj",
  "Role": "DevOps",
  "Skills": ["AWS", "Docker", "K8s"]
}
```

---

# # **📌 4. Amazon DocumentDB (MongoDB-Compatible)**

A fully managed **document database** designed for JSON workloads.

---

## **🎯 When to Use DocumentDB?**

* Applications using MongoDB
* JSON and semi-structured data
* REST APIs storing documents
* Catalogs, user profiles

---

## **🎯 DocumentDB Key Features**

* MongoDB API compatibility
* Auto-scaling storage (up to 64 TB)
* Automatic replication (6 copies across 3 AZs)
* Fast read performance with read replicas
* High availability

---

## **🎯 DocumentDB Architecture**

* Writer node
* Reader nodes (up to 15)
* Shared storage volume

---

## **🎯 DocumentDB Security**

* VPC only
* KMS encryption
* TLS in-transit
* IAM authentication proxy

---

# # **📌 5. Amazon Keyspaces (Apache Cassandra)**

A fully managed **Column-Family** NoSQL database compatible with **Cassandra Query Language (CQL)**.

---

## **🎯 Keyspaces Features**

* Serverless
* Highly scalable distributed database
* Multi-AZ replication
* Fully compatible with Cassandra drivers
* No clusters to manage

---

## **🎯 Best Use Cases**

* IoT time series
* Logging & metrics
* Recommendation engines
* Session management

---

# # **📌 6. Amazon Neptune (Graph Database)**

A **fully managed graph database** designed for:

* Social networks
* Fraud detection
* Knowledge graphs
* Recommendation engines

---

## **🎯 Neptune Supports Two Graph Models**

| Model              | Query Language |
| ------------------ | -------------- |
| **Property Graph** | Gremlin        |
| **RDF Graph**      | SPARQL         |

---

## **🎯 Neptune Features**

* High-performance graph queries
* ACID transactions
* 6-way replication
* Multi-AZ
* Encrypted storage

---

# # **📌 7. Amazon ElastiCache (Redis & Memcached)**

In-memory databases for caching, distributed locking, and ultra-fast data.

---

## **🎯 Supports Two Engines**

| Engine        | Use Case                                       |
| ------------- | ---------------------------------------------- |
| **Redis**     | Sessions, queues, caching, real-time analytics |
| **Memcached** | Simple cache layer                             |

---

## **🎯 ElastiCache Features**

* <1 ms latency
* Multi-AZ with failover
* Cluster mode for sharding
* In-memory replication
* Backup & restore

---

## **🎯 ElastiCache Use Cases**

* Caching frequently-accessed data
* Application performance boost
* Leaderboards (gaming)
* Real-time dashboards

---

# # **📌 8. MemoryDB for Redis**

Durable, scalable Redis service.

---

## **MemoryDB Features**

* 100% Redis compatible
* Durable with multi-AZ
* In-memory + long-term persistence
* Ideal for microservices

---

# # **📌 9. Comparison of All NoSQL AWS Databases**

| Service               | Type                 | Strong Areas                          |
| --------------------- | -------------------- | ------------------------------------- |
| **DynamoDB**          | Key-Value / Document | Serverless, high scale, microservices |
| **DocumentDB**        | Document             | MongoDB workloads                     |
| **Keyspaces**         | Columnar (Cassandra) | Time-series, massive scale            |
| **Neptune**           | Graph                | Relationships, social networks        |
| **ElastiCache Redis** | In-memory            | Caching, session store                |
| **MemoryDB**          | Durable Redis        | Enterprise Redis + persistence        |

---

# # **📌 10. When to Use Which NoSQL DB?**

| Use Case                        | Best Choice |
| ------------------------------- | ----------- |
| Microservices                   | DynamoDB    |
| Document-based JSON             | DocumentDB  |
| Social networks / Graph queries | Neptune     |
| High-speed caching              | Redis       |
| Durable Redis                   | MemoryDB    |
| Analytics & time-series         | Keyspaces   |

---

# # **📌 11. Non-Relational vs Relational (Important Table)**

| Relational (RDS) | Non-Relational (NoSQL)   |
| ---------------- | ------------------------ |
| Tables, rows     | Flexible JSON, KV, graph |
| Fixed schema     | Schema-less              |
| Joins            | No joins                 |
| Vertical scaling | Horizontal scaling       |
| Traditional apps | Modern distributed apps  |

---

# # **📌 12. Real-Time Architectures Using NoSQL**

### ⭐ **Microservices With DynamoDB**

```
API Gateway → Lambda → DynamoDB → SNS → S3
```

### ⭐ **Caching Layer With Redis**

```
Application → ElastiCache Redis → RDS
```

### ⭐ **Neptune Graph for Recommendation**

```
Data Lake → Neptune → API → Frontend
```

---

# # **📌 13. Security for AWS NoSQL Services**

* KMS encryption
* IAM authentication
* VPC Endpoints
* Security groups & subnet placement
* TLS in-transit
* CloudTrail logging

---

# # **📌 14. Monitoring Tools**

| Tool                 | Purpose              |
| -------------------- | -------------------- |
| CloudWatch           | Metrics & alarms     |
| X-Ray                | Tracing for DynamoDB |
| CloudTrail           | Audits               |
| Performance Insights | DocumentDB           |

---

# # **📌 15. Backup & Restore**

| Service     | Backup Type                         |
| ----------- | ----------------------------------- |
| DynamoDB    | PITR + On-Demand Snapshot           |
| DocumentDB  | Automatic + Manual                  |
| Keyspaces   | Continuous backups (Cassandra-like) |
| ElastiCache | Snapshot backups                    |
| Neptune     | Snapshot + automated backups        |

---

# # **📌 16. AWS NoSQL Pricing (Summary)**

Pricing based on:

* Read/Write requests
* Storage size
* Data transfer
* Backup storage
* Global tables (if used)
* Caching nodes (for Redis/Memcached)

---

# # **📌 17. Interview Questions (Most Common)**

### 🔸 Q1. When to use DynamoDB vs RDS?

* DynamoDB for **high scale**, serverless, non-relational data.
* RDS for structured relational data.

### 🔸 Q2. What is DynamoDB partition key?

Unique identifier used for partitioning storage.

### 🔸 Q3. Difference between GSI and LSI?

| GSI                             | LSI                         |
| ------------------------------- | --------------------------- |
| Different partition key allowed | Same partition key          |
| Eventual consistency            | Strong consistency possible |

### 🔸 Q4. What is DynamoDB DAX?

In-memory cache for DynamoDB.

### 🔸 Q5. What is AWS Keyspaces?

Cassandra-compatible NoSQL database.

### 🔸 Q6. When to use Redis vs MemoryDB?

* Redis = cache
* MemoryDB = durable Redis database

---

# ⭐ **DONE! Non-Relational AWS Database Notes Completed (A to Z)**

These notes are ready for your **GitHub DevOps Study Repo**.

---

# 👉 Want me to generate:

✅ Architecture Diagrams (PNG/SVG)
✅ Full GitHub `.md` file
✅ Practical labs for DynamoDB, Redis, DocumentDB
✅ Comparison charts between RDS vs DynamoDB
?

Just tell me!
