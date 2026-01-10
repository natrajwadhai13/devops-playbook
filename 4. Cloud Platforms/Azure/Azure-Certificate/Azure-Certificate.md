---
title: "• Azure Certificate"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 0
has_children: true
---



**certification roadmap**

The DevOps Engineer Expert cert used to recommend AZ-104 or AZ-204 before AZ-400, but I need to be careful not to overstate this as a strict prerequisite. I’ll say it’s "recommended" and avoid calling it mandatory unless supported by sources. The blogs will help with trends for 2026. I’ll keep the structure concise: Summary, Fundamentals, Cloud Engineer Path, DevOps Engineer Path, 8-Week Plan, Tips. I’ll include Hindi where needed and use bold labels for clarity, keeping everything direct and step-by-step. No excessive details, just clear and helpful info.

# Azure certification path for cloud engineer and DevOps (step by step)

Agar aap Cloud Engineer ya DevOps me jaana chahte ho, yeh step-by-step roadmap follow karein. DevOps Expert role me Azure par automation, CI/CD, monitoring, security aur collaboration par strong experience chahiye — typically cross‑functional teams ke saath kaam karte ho and both admin + dev skills helpful hote hain. 2026 me DevOps + cloud security demand aur accelerate ho rahi hai, so yeh path future‑proof hai.

---

## Fundamentals (start here)

- **AZ‑900: Azure Fundamentals**  
  - **What:** Core cloud + Azure concepts, pricing, SLAs, governance.  
  - **Why:** Quick foundation; exam language clarity build hoti hai.  
  - **Timeline:** 2–3 weeks (aap already target kar rahe ho—continue).  
  - Note: Fundamentals ke baad role‑based associate exams lena easy hota hai.  
  - This builds the base for both cloud engineer and DevOps tracks (industry‑standard start).

- **Optional fundamentals (as per interest)**  
  - **SC‑900 (Security Fundamentals):** Security + compliance basics.  
  - **DP‑900 (Data Fundamentals):** Data storage/analytics basics.  
  - **AI‑900 (AI Fundamentals):** AI services intro.  
  - These help if you plan security/data/AI specializations later.

---

## Cloud engineer path (administrator/architect focus)

1. **AZ‑104: Azure Administrator Associate**  
   - **Skills:** Compute, storage, networking, identities; daily ops and governance.  
   - **Why:** Core admin role for cloud engineer; strong base for infra + operations.  
   - **Timeline:** 4–6 weeks with hands‑on labs.  
   - Solid foundation if later you aim DevOps or architecture.

2. **AZ‑700: Azure Network Engineer Associate** (optional, if networking heavy)  
   - **Skills:** VNet design, routing, VPN/ExpressRoute, load balancers, security.  
   - **Timeline:** 3–4 weeks.

3. **AZ‑305: Azure Solutions Architect Expert** (after AZ‑104 + architecture practice)  
   - **Skills:** Design governance, reliability, performance, cost, security.  
   - **Timeline:** 6–8 weeks.  
   - Good for senior cloud engineer roles.

---

## DevOps engineer path (automation/CI/CD focus)

1. **AZ‑104 or AZ‑204 first (recommended foundation)**  
   - **AZ‑104:** Admin skills for infra automation.  
   - **AZ‑204:** Developer skills for pipelines, artifacts, and release engineering.  
   - Microsoft DevOps Engineer role blends admin + dev skills and collaboration across teams; having strong skills in at least one area is expected.

2. **AZ‑400: DevOps Engineer Expert**  
   - **Skills:** Plan/track work, Git strategies, CI/CD (Azure Pipelines/GitHub Actions), artifact management, IaC (ARM/Bicep/Terraform), release gates, monitoring/feedback, secure DevOps.  
   - **Timeline:** 5–7 weeks with project‑based practice.  
   - Role focuses on continuous security, integration, testing, delivery, deployment, monitoring, and feedback to enable continuous value delivery.  
   - 2026 roadmap: Secure DevOps + AI‑assisted workflows are rising; build skills in policy‑as‑code, supply chain security, and observability.

3. **Security add‑ons (optional, high value)**  
   - **SC‑200 (Security Operations Analyst):** Sentinel, Defender, incident response.  
   - **SC‑300 (Identity and Access Administrator):** Entra ID (Azure AD) identities.  
   - **SC‑100 (Cybersecurity Architect):** End‑to‑end security design.  
   - These boost DevOps + platform reliability in enterprise setups.

---

## 8‑week practical study plan (fast‑track for you, Pune IST schedule)

- **Weeks 1–2:** AZ‑900 complete  
  - **Focus:** Cloud models, pricing/SLA, governance, compliance.  
  - **Practice:** MCQs + Hindi‑English mixed notes for tricky wording.

- **Weeks 3–6:** AZ‑104 (cloud engineer base)  
  - **Hands‑on:**  
    - **Compute:** VM scale sets, images, disks.  
    - **Networking:** VNets, NSGs, Load Balancer, App Gateway.  
    - **Identity:** RBAC, PIM, least privilege.  
    - **Storage/Backup:** Blob lifecycle, Backup/ASR.  
  - **Automation:** Azure CLI, PowerShell, ARM/Bicep.

- **Weeks 7–8:** AZ‑400 sprint (DevOps focus)  
  - **Pipelines:** Build + release for a Java/Node app.  
  - **IaC:** Bicep/Terraform module for a small workload.  
  - **Quality gates:** Unit tests, code coverage, static analysis.  
  - **Observability:** Azure Monitor, Log Analytics, alerts.  
  - **Security:** Secrets in Key Vault, SBOM, repo policies.

This sequence aligns with the DevOps Engineer role expectations (blend of admin + dev, continuous delivery of value) and the 2026 trend towards secure, automated pipelines.

---

## Tips to lock it in

- **Learn by building:** Mini project—Front Door + WAF + App Gateway + AKS/VMSS, pipeline deploy, monitor alerts.  
- **Keywords for exams:** Governance (RBAC, Policy), Resilience (HA, Fault tolerance), Automation (ARM/Bicep/Terraform), Security (WAF, Key Vault, Defender).  
- **Notes style:** Short bilingual bullets; practice reading long questions calmly to reduce exam pressure.  
- **Portfolio:** Post labs on GitHub + LinkedIn with metrics (deploy time ↓, rollback MTTR ↓, cost optimized).  
- **Mock tests:** Target 75–80% before scheduling.
