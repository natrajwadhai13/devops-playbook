---
title: "Kubernetes Basics"
nav_order: 2
parent: "• Groovy_Yaml_Json_Roadmap"
grand_parent: "Scripting"
---


Here is a **DevOps-focused JSON Roadmap** designed for **hands-on engineers like you** who work with tools such as **Terraform, REST APIs, CloudFormation, Docker, Kubernetes, GitLab, and monitoring systems**.

---

## 🧭 **JSON Roadmap for DevOps Engineers (2 Weeks)**

📘 **Goal**: Read, write, validate, and manipulate JSON in the context of DevOps tools and automation scripts.

---

## 🔰 **Week 1: JSON Fundamentals**

### 🎯 Outcomes:

* Understand JSON structure and syntax
* Use JSON in real-world DevOps tools

### Topics:

* What is JSON?
* JSON vs YAML
* JSON Data Types:

  * String, Number, Boolean, Array, Object, null
* JSON Rules:

  * Double quotes for keys
  * No trailing commas

### Hands-on:

* Write a basic JSON object:

```json
{
  "name": "natraj",
  "role": "DevOps Engineer",
  "skills": ["AWS", "Docker", "Ansible"],
  "experience": 6.2,
  "available": true
}
```

* Validate using:

  * [https://jsonlint.com](https://jsonlint.com)
  * VSCode `Prettify JSON`

---

## ⚙️ **Week 2: JSON in DevOps Tools**

### 🎯 Outcomes:

* Use JSON in automation, config, and APIs

---

### 🔹 **1. Terraform Output (state and plan in JSON)**

Command:

```bash
terraform show -json
```

Use case:

* Parse JSON output for tools like Jenkins or custom monitoring

---

### 🔹 **2. AWS CLI Outputs in JSON**

Command:

```bash
aws ec2 describe-instances --output json
```

Use case:

* Parse instance info using `jq`

---

### 🔹 **3. REST API Requests & Responses (Postman / Curl / Python)**

Example response:

```json
{
  "id": "12345",
  "status": "success",
  "message": "Instance created"
}
```

Use case:

* Validate deployment results or automation actions

---

### 🔹 **4. Docker Inspect Output**

Command:

```bash
docker inspect <container_id>
```

* You'll get large JSON output — practice querying it using `jq`.

---

### 🔹 **5. Monitoring Tools (Prometheus, ELK)**

* Some tools expose or accept JSON configs.
* Alertmanager & Grafana support JSON templating.

---

## 🧪 Tools & Utilities

| Tool       | Purpose                                 |
| ---------- | --------------------------------------- |
| `jq`       | Command-line JSON parser (super useful) |
| Postman    | API request/response testing            |
| `curl`     | API + JSON integration                  |
| VSCode     | Pretty format JSON                      |
| `jsonlint` | Validation tool                         |

---

### ✅ `jq` Examples (Very Useful for DevOps)

```bash
# Extract all EC2 instance IDs
aws ec2 describe-instances --output json | jq '.Reservations[].Instances[].InstanceId'

# Pretty print JSON from file
cat file.json | jq
```

---

## 📘 JSON Mini-Project (Optional Practice)

🔧 **Use Case**: Automate AWS EC2 inventory using Bash + AWS CLI + jq

1. Use `aws ec2 describe-instances --output json`
2. Parse and extract:

   * Instance ID
   * Public IP
   * State
   * Tag: Name
3. Save into a CSV report

---

## 🧠 JSON Cheat Sheet

| Concept       | Example                         |
| ------------- | ------------------------------- |
| Object        | `{ "key": "value" }`            |
| Array         | `"skills": ["docker", "git"]`   |
| Nested Object | `"server": { "ip": "1.1.1.1" }` |
| Boolean       | `"enabled": true`               |
| Null          | `"data": null`                  |
| Numbers       | `"experience": 6.2`             |

---

Would you like:

* A **sample DevOps script using JSON and jq**?
* A **JSON to YAML converter tool example**?
* Or help creating a **real use case project with AWS + JSON + Bash**?

