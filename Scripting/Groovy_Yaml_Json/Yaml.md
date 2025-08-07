---
title: "Kubernetes Basics"
parent: "• Groovy_Yaml_Json_Roadmap"
grand_parent: "Scripting"
nav_order: 1
---

Here’s a **practical YAML Roadmap** specifically designed for **DevOps engineers like you**, working with tools such as **Ansible, Kubernetes, GitLab CI/CD, Docker, and Terraform**, where YAML is essential.

---

## 🧭 **YAML Roadmap for DevOps Engineers (2–3 Weeks)**

📘 **Goal**: Be confident writing and reading YAML used in real-world DevOps tools like Ansible, Kubernetes, GitHub Actions, GitLab CI, Docker Compose, etc.

---

## 🔰 **Week 1: YAML Fundamentals**

### 🎯 Outcomes:

* Learn YAML syntax, structure, and common mistakes

### Topics:

* What is YAML?
* YAML vs JSON
* Syntax: indentation, key-value pairs, lists, dictionaries
* Scalars, strings, booleans, nulls
* Anchors (`&`) and aliases (`*`)
* Comments (`#`), multi-line strings (`|`, `>`)

### Hands-on:

* Convert JSON to YAML (and vice versa)
* Write simple YAML examples:

  ```yaml
  name: natraj
  skills:
    - DevOps
    - Linux
    - Cloud
  location:
    city: Pune
    area: Kharadi
  ```

---

## ⚙️ **Week 2: Tool-Specific YAML Use**

### 🎯 Outcomes:

* Understand how YAML is used in popular DevOps tools

---

### 🔹 **Ansible YAML (Playbooks & Roles)**

```yaml
- name: Install Nginx
  hosts: web
  become: yes
  tasks:
    - name: Install package
      apt:
        name: nginx
        state: present
```

📌 Learn:

* `hosts`, `tasks`, `vars`, `roles`, `handlers`
* How indentation impacts execution

---

### 🔹 **Kubernetes YAML (Manifests)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

📌 Learn:

* `apiVersion`, `kind`, `metadata`, `spec`
* Deployment, Service, Ingress YAML files

---

### 🔹 **Docker Compose YAML**

```yaml
version: "3"
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

📌 Learn:

* `version`, `services`, `volumes`, `networks`

---

### 🔹 **GitLab CI/CD YAML**

```yaml
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - echo "Building"

deploy:
  stage: deploy
  script:
    - echo "Deploying"
```

📌 Learn:

* `stages`, `script`, `before_script`, `only`, `except`, `rules`

---

## 🚀 **Week 3: Practice Projects**

### 🎯 Outcomes:

* Be fluent writing YAML files from scratch

---

### Mini-Projects:

1. **Ansible playbook** to install Apache + copy index.html
2. **Kubernetes Deployment YAML** for Node.js app
3. **GitLab CI/CD YAML** to build Docker image and push to Docker Hub
4. **Docker Compose file** to launch NGINX + Redis stack

---

## ✅ Best Practices

* Always use **2 spaces**, never tabs
* Use **inline comments** to describe blocks
* Validate YAML using tools like:

  * [https://www.yamllint.com](https://www.yamllint.com)
  * `yamllint` CLI
  * VSCode extension: YAML by Red Hat

---

## 📘 YAML Cheat Sheet Summary

| Feature      | Example                                   |                                     |
| ------------ | ----------------------------------------- | ----------------------------------- |
| Key-Value    | `name: natraj`                            |                                     |
| List         | `skills: [aws, docker, git]`              |                                     |
| Nested List  | `skills: \n  - aws \n  - docker`          |                                     |
| Boolean      | `enabled: true`                           |                                     |
| Multiline    | \`desc:                                   | \n  This is a long \n  text block\` |
| Anchor/Alias | `default: &def \n  val: 1 \n reuse: *def` |                                     |

---

## 🔧 Tools You Can Use:

* **YAML Linter**: `yamllint`, VSCode plugin
* **YAML to JSON Converter**: [https://codebeautify.org/yaml-to-json-xml-csv](https://codebeautify.org/yaml-to-json-xml-csv)
* **YAML Formatter**: [https://jsonformatter.org/yaml-formatter](https://jsonformatter.org/yaml-formatter)

---

Would you like:

* Sample YAML files for Ansible, K8s, or GitLab?
* A short YAML cheat sheet (PDF)?
* A YAML project to test your knowledge?

Let me know!
