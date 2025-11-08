---
title: • About GitLab  
parent: • GitLab  
grand_parent: 2. CICD Tools  
nav_order: 1  
has_children: true  
---

# 🚀 About GitLab

## 🔸 IMP Questions

1. **What is GitLab?**
2. **How is it different from GitHub/Bitbucket?**
3. **How to use GitLab?**
4. **How to create/manage Projects/Groups?**
5. **How to create Pipelines in GitLab?**
6. **Variables - Secrets, Artifacts?**
7. **Self-hosted Runners vs SaaS Runners?**
8. **Project Deployment via CI/CD Pipelines in GitLab?**

---

## 🔹 What Is GitLab?

- GitLab is a **web-based DevOps lifecycle platform** offering tools for **SCM, CI/CD, security, and project management** in one application.
- It supports **end-to-end DevOps workflows**, unlike GitHub and Bitbucket which often require external integrations.
- GitLab can be **self-hosted** (e.g., via Docker), while GitHub cannot.
- GitLab uses **Projects** under **Groups**, whereas GitHub uses **Repositories** under **Usernames**.

| Platform | Structure | URL Format |
|----------|-----------|------------|
| GitLab   | Groups → Projects | `domain/groups/project` |
| GitHub   | Username → Repository | `domain/username/reponame` |

---

## 🔹 GitLab vs GitHub vs Bitbucket

### 🧭 Hosting & Deployment
- **GitHub**: Only SaaS; requires GitHub Enterprise for private hosting.
- **GitLab**: Can be self-hosted or used via GitLab.com.
- **Bitbucket**: Supports Jira integration and offers additional features.

### 🔗 Jira Integration
- GitLab and Bitbucket integrate with Jira.
- Bitbucket offers deeper Jira features like smart commits and issue linking.

### 🤖 GitLab Duo
- GitLab Duo is an **AI-powered DevOps assistant**.
- Learn more: [GitLab Duo](https://about.gitlab.com/gitlab-duo/)

---

## 🔹 GitLab Editions

- **GitLab CE (Community Edition)**: Free, open-source.
- **GitLab EE (Enterprise Edition)**: Paid, enterprise features.
- **GitLab.com**: SaaS tiers — Free, Premium, Ultimate.

---

## 🔹 Key Features of GitLab

1. **Source Code Management (SCM)**
   - Git-based version control.
   - Merge Requests (MRs), code reviews, branching.

2. **CI/CD Pipelines**
   - Defined via `.gitlab-ci.yml`.
   - Automates build → test → deploy.
   - Supports Docker, Kubernetes, cloud.

3. **DevOps Platform**
   - Planning → Coding → Testing → Security → Deployment → Monitoring.

4. **Security & Compliance**
   - SAST, DAST, container scanning, audit logs.

5. **Project & Issue Management**
   - Kanban boards, milestones, epics, labels.

6. **Container & Cloud Native Support**
   - Built-in Docker registry.
   - Kubernetes integration for auto-deploy.

7. **Self-Hosted or SaaS**
   - GitLab.com (SaaS) or GitLab CE/EE (self-hosted).

---

## 🔹 GitLab Workflow (High-Level)

1. **Code** → Push to GitLab repo.
2. **Review** → Merge Requests (MRs).
3. **CI/CD** → Automated pipeline runs.
4. **Security** → Code & dependency scans.
5. **Deploy** → To VM, Docker, Kubernetes, cloud.
6. **Monitor** → Performance tracking.
7. **Plan** → Manage issues and sprints.

---

## 🔹 CI/CD Concepts in GitLab

- **Pipeline**: Sequence of jobs.
- **Stages**: Logical job groups (build, test, deploy).
- **Jobs**: Tasks within stages.
- **Artifacts**: Store logs/output.
- **Tags**: Assign runners.
- **Scripts**: Shell commands in jobs.

---

## 🔹 GitLab Variables

- **Types**:
  - User-defined
  - Predefined
- **Attributes**:
  - Protected: Only for protected branches.
  - Masked: Hidden in logs.
  - Expanded: Can reference other variables.

---

## 🔹 GitLab Runners

- **SaaS Runners**: Provided by GitLab.com.
- **Self-hosted Runners**: You install and manage them on your infrastructure.

---

## 🔹 Deployment via GitLab CI/CD

- Define pipeline in `.gitlab-ci.yml`.
- Use stages like `build`, `test`, `deploy`.
- Deploy to cloud, VM, Docker, or Kubernetes.
- Use artifacts and variables for secrets and logs.

---

## 🔹 GitLab Tips

- Use `>` to overwrite a file.
- Use `>>` to append to a file.
- Use [GitLab Cheatsheet](https://docs.gitlab.com/ee/topics/gitlab_cheat_sheet.html).
- Leverage predefined variables for dynamic pipelines.

---



-----------------------------------------------------------------

IMP Que-

What is GtLab?

How is it different from GitHub/BitBucket

How to use GitLab?

How to create/manage Projects/Groups?

How to create Pipelines in GitLab?

Variables - Secrets, Artifacts?

Self hosted Runners, SaaS Runners?

Project Deployment via CICD Pipelines in GtLab?

## 🚀 What Is GitLab?

* GitLab is a **web-based DevOps lifecycle platform** that provides a complete set of tools for **source code management (SCM), CI/CD, security, and project management** in one application.

* It is an alternative to **GitHub** and **Bitbucket**, but GitLab focuses heavily on **end-to-end DevOps workflows**.

* Diffrent GitLab we can run in docker hub but Git Hub Not. GitLab is Open Source and GitHub is used by Microshoft.

* GitLab name is Project and Github is repository.

Github: username:repository
GitLab: groups:Project

---

## 🔹 Key Features of GitLab

1. **Source Code Management (SCM)**

   * Git-based version control system.
   * Supports branching, merging, pull requests (called **Merge Requests** in GitLab).
   * Code reviews, inline comments, approvals.

2. **CI/CD (Continuous Integration / Continuous Deployment)**

   * Built-in GitLab CI/CD pipelines (via `.gitlab-ci.yml`).
   * Automates build, test, deploy stages.
   * Supports Docker, Kubernetes, and cloud deployments.

3. **DevOps Platform**

   * Everything from planning → coding → testing → security → deployment → monitoring in one platform.
   * Reduces the need to integrate multiple tools.

4. **Security & Compliance**

   * Built-in SAST, DAST, dependency scanning, and container scanning.
   * Audit logs, role-based access control.

5. **Project & Issue Management**

   * Kanban boards, epics, milestones, and labels.
   * Integrated issue tracking and planning.

6. **Container & Cloud Native Support**

   * GitLab Container Registry (built-in private Docker registry).
   * Easy integration with **Kubernetes** for auto-deploy.

7. **Self-Hosted or SaaS**

   * GitLab.com → SaaS (hosted by GitLab).
   * GitLab CE/EE → Self-hosted (install on your own servers, cloud, or VM).

---

## 🔹 GitLab Editions

* **GitLab CE (Community Edition)** → Free, open-source.
* **GitLab EE (Enterprise Edition)** → Paid, extra features for enterprises.
* **GitLab.com Free/Premium/Ultimate** → SaaS versions with tiered pricing.

---

## 🔹 GitLab Workflow (High-Level)

1. **Code** → Developer pushes code to GitLab repo.
2. **Review** → Code is reviewed via Merge Requests (MRs).
3. **CI/CD** → GitLab runs automated tests/builds via pipeline.
4. **Security** → Scans code & dependencies.
5. **Deploy** → Deploys to staging/prod (VM, Docker, Kubernetes, cloud).
6. **Monitor** → Track performance and issues.
7. **Plan** → Manage issues, tasks, and sprints.

---

## 🔹 Why Companies Use GitLab

* **All-in-one platform** → No need for Jenkins, Jira, DockerHub, SonarQube separately.
* **Open-source friendly**.
* **Enterprise-ready** with security, compliance, and scalability.
* Strong **DevSecOps** capabilities.

---

Here's a clean and structured summary of the key points you shared about GitLab, GitHub, Bitbucket, and related DevOps concepts — rearranged for clarity and usefulness:

---

## 🚀 GitLab vs GitHub vs Bitbucket: Key Differences & Capabilities

### 🧭 Hosting & Deployment
- **GitHub**:
  - Cannot be hosted on your own server.
  - Must use [GitHub.com](https://github.com) or purchase **GitHub Enterprise** for private hosting.
- **GitLab**:
  - Can be self-hosted on your own server.
  - Offers flexibility for on-premise deployments.
- **Bitbucket**:
  - Also supports integration with Jira and offers additional features compared to GitLab.

---

### 🔗 Integration with Jira
- **GitLab** and **Bitbucket** both support integration with **Jira**.
- **Bitbucket** provides **extra features** for Jira integration, such as deeper issue linking and smart commits.

---

### 🤖 GitLab Duo
- GitLab has introduced **GitLab Duo**, an AI-powered DevOps assistant.
- Learn more: [GitLab Duo](https://about.gitlab.com/gitlab-duo/)

---

### 🏗️ Structure: Groups, Projects, Repositories
| Platform | Terminology | URL Format |
|----------|-------------|------------|
| GitLab   | Groups & Projects | `domain/groups/project` |
| GitHub   | Repositories      | `domain/username/reponame` |

---

### ⚙️ CI/CD Concepts in GitLab
- **Pipeline**: Sequence of automated steps.
- **Stages**: Logical groupings of jobs (e.g., build, test, deploy).
- **Jobs**: Individual tasks within stages.
- **Artifacts**: Store logs or output files from jobs.
- **Tags**: Used to assign specific runners.
- **Scripts**: Shell commands executed in jobs.

---

### 🔐 GitLab Variables
- **Types**:
  - **User-defined**
  - **Predefined**
- **Attributes**:
  - **Protected**: Only available in protected branches.
  - **Masked**: Hidden in job logs.
  - **Expanded**: Can reference other variables.

---

### 📝 GitLab Tips
- Use `>` to **create/overwrite** a file.
- Use `>>` to **append** to a file.
- GitLab provides a helpful [cheatsheet](https://docs.gitlab.com/ee/topics/gitlab_cheat_sheet.html).
- You can use **predefined variables** in your `.gitlab-ci.yml` for dynamic behavior.

