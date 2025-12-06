---
title: "• RDS"
nav_order: 1
parent: "• Databases"
grand_parent: "• AWS"
grand_grand_parent: "4. Cloud Platforms"
---

Here are **A-to-Z complete RDS Notes** — crystal clear, interview-ready, and GitHub-friendly.
This is the **best possible full RDS documentation** you can use for DevOps, AWS Associate, Professional, and real-time projects.

---

# ⭐ **Amazon RDS (Relational Database Service) – Complete Notes (A to Z)**

*(Fully structured for GitHub README.md, DevOps, Cloud Engineers & Interviews)*

---

# **📌 1. What is Amazon RDS?**

Amazon RDS (Relational Database Service) is a **managed relational database service** that allows you to run SQL databases in AWS **without handling hardware, software, backups, or maintenance**.

You only focus on **database usage**, AWS handles:

* Provisioning
* Patching
* Backups
* Monitoring
* Failover (Multi-AZ)
* Scaling

---

# **📌 2. Databases Supported by RDS**

RDS supports **6 relational database engines**:

| Database Engine   | Description                                    |
| ----------------- | ---------------------------------------------- |
| **MySQL**         | Most used open-source database                 |
| **PostgreSQL**    | Advanced open-source DB with JSON support      |
| **MariaDB**       | MySQL compatible                               |
| **Oracle**        | Enterprise commercial DB                       |
| **SQL Server**    | Microsoft relational DB                        |
| **Amazon Aurora** | AWS-built MySQL/Postgres compatible, 5x faster |

---

# **📌 3. RDS Core Features**

### ✔ Managed Database

AWS handles installation, patching, upgrades.

### ✔ Automated Backups

Daily incremental backups stored in S3.

### ✔ Multi-AZ Failover

Automatic failover to standby database.

### ✔ Read Replicas

Used for **read-heavy workloads**.

### ✔ Encryption

KMS encryption for data at rest.

### ✔ Monitoring

CloudWatch, Enhanced Monitoring, Performance Insights.

### ✔ Scaling

Scale:

* Instance size (vertical scaling)
* Storage (up to 64 TB)
* Read Replicas (horizontal scaling)

---

# **📌 4. RDS Instance vs Database**

* **DB Instance:** The whole server
* **Database:** Schema inside the DB instance

One instance can contain **multiple databases**, depending on engine.

---

# **📌 5. RDS Components**

### 1️⃣ **DB Instance Class**

Specifies CPU, RAM
Examples:

* db.t3.micro (free tier)
* db.m5.large
* db.r6g.xlarge (memory optimized)

### 2️⃣ **DB Storage Types**

| Storage Type        | Use case            |
| ------------------- | ------------------- |
| **gp3 / gp2 (SSD)** | General workloads   |
| **IO-Optimized**    | High IOPS workloads |
| **Magnetic**        | Old, rarely used    |

### 3️⃣ **Backup Retention Period**

1–35 days (automated backups)

### 4️⃣ **Maintenance Window**

Scheduled weekly updates.

### 5️⃣ **Database Port**

* MySQL: 3306
* PostgreSQL: 5432
* MariaDB: 3306
* SQL Server: 1433
* Oracle: 1521

---

# **📌 6. RDS Deployment Models**

| Deployment Type    | Purpose                               |
| ------------------ | ------------------------------------- |
| **Single-AZ**      | Development/testing                   |
| **Multi-AZ**       | Production – automatic failover       |
| **Read Replica**   | Scaling read traffic                  |
| **Aurora Cluster** | Distributed, high-performance cluster |

---

# **📌 7. Multi-AZ RDS (High Availability)**

### How it works:

* Primary DB (AZ-A)
* Synchronous replication to standby DB (AZ-B)
* Failover happens automatically

### What triggers failover?

* Primary DB failure
* Storage failure
* AZ failure
* Manual reboot with failover

---

# **📌 8. Read Replicas (High Read Performance)**

**Purpose:**

* Offload read traffic
* Analytics, dashboards
* Geo-redundancy

**Features:**

* Up to **15 replicas**
* **Asynchronous** replication
* Cross-region read replicas supported

**Used for:**

* Scaling
* Reporting
* Disaster recovery

---

# **📌 9. Automated Backups**

* Daily snapshot
* Point-in-time recovery (PITR)
* Stored in S3 (encrypted)
* Retention: 1 to 35 days

---

# **📌 10. Manual Snapshots**

User-created backups:

* Do not expire
* Can be copied across regions
* Can restore a new DB instance

---

# **📌 11. Security in RDS**

### ✔ Network Security

* RDS runs in **private subnets**
* Access controlled with:

  * Security Groups
  * NACLs
  * VPC Peering / PrivateLink

### ✔ Data Encryption

* KMS encryption: data at rest
* SSL/TLS: data in transit

### ✔ IAM Authentication (Aurora/MySQL/Postgres)

Use IAM token instead of password.

---

# **📌 12. Monitoring RDS**

| Tool                     | Purpose                   |
| ------------------------ | ------------------------- |
| **CloudWatch**           | CPU, memory, IOPS         |
| **Enhanced Monitoring**  | OS-level metrics          |
| **Performance Insights** | Query-level analysis      |
| **RDS Events**           | Failover, backups, errors |

---

# **📌 13. Maintenance in RDS**

### Automated tasks:

* Minor version updates
* Patching OS & DB
* Backups
* Failover
* Storage scaling

Set **preferred maintenance window**.

---

# **📌 14. RDS Storage Auto Scaling**

Automatically increases storage when:

* Free space < threshold
* Storage-critical workload increases

Max limit is set during creation.

---

# **📌 15. Connecting EC2 to RDS**

Steps:

1. Launch EC2 in same VPC
2. Edit RDS security group → allow inbound MySQL/Postgres:

```
Type: MySQL/Aurora
Port: 3306
Source: EC2 Security Group
```

3. Connect:

```
mysql -h <rds-endpoint> -u admin -p
```

---

# **📌 16. Parameter & Option Groups**

### **Parameter Group**

* Controls DB engine configuration
* Example: max_connections, query timeout

### **Option Group**

* Adds extra features:

  * SQL Server features
  * Oracle packages
  * MySQL auditing

---

# **📌 17. RDS Endpoints**

| Endpoint Type                 | Example                     | Purpose                |
| ----------------------------- | --------------------------- | ---------------------- |
| **Primary Endpoint**          | mydb.xxxx.rds.amazonaws.com | Read + Write           |
| **Read Replica Endpoint**     | mydb-ro.rds.amazonaws.com   | Read Only              |
| **Custom Endpoints (Aurora)** | cluster-custom…             | Group certain replicas |

---

# **📌 18. Aurora vs RDS**

| Feature  | RDS      | Aurora                      |
| -------- | -------- | --------------------------- |
| Storage  | EBS      | Distributed cluster storage |
| Failover | 1–2 mins | < 35 sec                    |
| Speed    | Standard | 5x faster                   |
| Replicas | 5 max    | 15 max                      |
| Price    | Cheaper  | Higher                      |

---

# **📌 19. RDS Limitations**

* No SSH access
* No OS-level access
* Limited customization
* No cross-region automatic failover (except Aurora)
* Storage cannot be decreased once increased

---

# **📌 20. RDS Best Practices**

### ✔ Use Multi-AZ for production

### ✔ Use Read Replicas for heavy reads

### ✔ Enable Auto Backup + PITR

### ✔ Store credentials in Secret Manager

### ✔ Use Private Subnets

### ✔ Use Performance Insights for tuning

### ✔ Enable KMS encryption

---

# **📌 21. Practical: Create RDS MySQL**

### Steps:

1. Go to **RDS → Create Database**
2. Choose **MySQL**
3. Templates → Free Tier / Production
4. Choose:

   * Instance class: `db.t3.micro`
   * Storage: 20GB gp3
   * Multi-AZ: Optional
5. Set username/password
6. Select VPC, subnets
7. Choose **private subnet**
8. Set security group rules
9. Create DB

### Connect from EC2:

```
mysql -h <endpoint> -u admin -p
```

---

# **📌 22. When to Use RDS**

Use RDS when you need:

* Relational database
* Managed backups
* High availability
* Minimal admin overhead
* Auto scaling + replication

---

# **📌 23. When NOT to Use RDS**

Avoid RDS when:

* You need OS-level access
* Custom DB installation
* Very high write throughput (use DynamoDB)
* Xtreme workloads (Aurora preferred)

---

# **📌 24. Interview Questions**

### Q1. Difference between RDS Multi-AZ vs Read Replica?

**Multi-AZ = HA (failover)**
**Read Replica = performance (read scaling)**

### Q2. Can I write to Read Replica?

No, read-only.

### Q3. Can I SSH into RDS?

No.

### Q4. How to scale RDS?

Increase instance class or add read replicas.

### Q5. What is RDS endpoint?

DNS address to connect to DB.

### Q6. RDS backup types?

Automated + Manual snapshots.

### Q7. How failover happens in Multi-AZ?

Standby instance becomes primary.

---

# ⭐ **DONE! Full RDS Notes Ready**

If you want, I can also give:

✅ GitHub-ready Markdown file
✅ Architecture diagrams (PNG/SVG)
✅ Hands-on labs section
✅ Real-time RDS architecture design
✅ Comparison notes: RDS vs Aurora vs DynamoDB

Just tell me!
