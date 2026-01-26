---
title: "• Virtual Machines "
parent: "📌 Compute Resources "
grand_parent: "• AZ-104"
grand_grand_parent: "• Azure Certificate"
grand_grand_grand_parent: "• Azure"
grand_grand_grand_grand_parent: "4. Cloud Platforms"
nav_order: 6
has_children: true
---

### 🖥 Virtual Machines

- Create virtual machines
- Configure Azure Disk Encryption
- Move VMs across:
  - Resource groups
  - Subscriptions
  - Regions

- Manage VM sizes and disks
- Deploy to:
  - Availability Zones
  - Availability Sets

- Configure **Virtual Machine Scale Sets**

---

## 🖥️ Azure Virtual Machine Types.

| VM Type                            | VM Series | Short Description                   | Common Use Case                            |
| ---------------------------------- | --------- | ----------------------------------- | ------------------------------------------ |
| **General Purpose**                | A, D      | Balanced CPU, memory & storage      | Web servers, app servers, dev/test         |
| **Compute Optimized**              | F         | High CPU, low memory                | Batch jobs, application servers            |
| **Memory Optimized**               | E, M      | High RAM capacity                   | Databases, in-memory analytics, SAP        |
| **Storage Optimized**              | L         | High disk throughput & IOPS         | Big data, NoSQL, data warehousing          |
| **GPU**                            | N         | GPU-based processing                | AI/ML, graphics rendering                  |
| **High Performance Compute (HPC)** | H         | Very high compute & fast networking | Scientific simulations, financial modeling |
| **Burstable**                      | B         | Low cost, CPU credits based         | Dev/test, low traffic workloads            |

---

## 🧠 AZ-104 Quick Memory Tips

- **Low cost + spikes** → **B-series**
- **Balanced workload** → **D-series**
- **CPU heavy** → **F-series**
- **RAM heavy** → **E-series**
- **AI / ML** → **N-series**

---

## 📌 One-Line Cheat Code

```
General → D | Compute → F | Memory → E | Storage → L | GPU → N | HPC → H | Burst → B
```

---
