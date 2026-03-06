---
title: "• Rex_Ansible_roadmap"
nav_order: 2
parent: "• Ansible"
grand_parent: "7. Configuration Management Tools"
has_children: true
---

## 🧭 **Rex-Ansible Roadmap (4 Weeks)**

🔧 **Goal**: Master Rex-Ansible to automate Linux/infra tasks and integrate it into enterprise workflows (with GitLab, ECMS, Autosys, etc.)

---

### 🔰 **Week 1: Foundation & Environment Setup**

#### 🎯 Outcomes:

- Understand what Rex-Ansible is
- Get the environment ready

#### Topics:

- What is Rex-Ansible?
  → Hybrid automation combining **Rex (Remote Execution)** with **Ansible Playbooks**
  → Popular in enterprise environments like UBS, Infosys due to control + legacy support
- Architecture: Rex layer + Ansible backend
- Install Rex-Ansible environment
- SSH key setup for remote execution
- Rex job structure vs Ansible playbooks

#### Hands-on:

- Create a basic rex job to execute shell command on a remote Linux machine
- Ansible `ping` via rex
- Integrate GitLab repo with rex job

---

### ⚙️ **Week 2: Writing Playbooks + Rex Scripts**

#### 🎯 Outcomes:

- Use Rex to run Ansible playbooks
- Parameterized automation

#### Topics:

- Rex job YAML format vs native Ansible playbooks
- Using variables in Rex-Ansible
- Executing shell scripts + inline Bash in Rex
- Host groups and inventory (rex-style)
- Use `rex run` and `rex list` effectively

#### Hands-on:

- Deploy Apache/NGINX using Rex-Ansible playbook
- Create a playbook to patch multiple servers
- Run job with inputs (environment, hostname)

---

### ☁️ **Week 3: Enterprise Integration & Security**

#### 🎯 Outcomes:

- Integrate with Infra, ECMS, GitLab CI
- Secure automation using Vault/Secrets

#### Topics:

- GitLab + Rex CI/CD integration (pipeline triggers for Rex jobs)
- Logging and job output storage in ECMS or shared NFS
- Use secrets securely with **Ansible Vault** or encrypted vars in Rex
- Notifications via SMTP or SNOW integration

#### Hands-on:

- Automate git-pulled config deployment via Rex
- Push job logs to ECMS directory
- Secure secrets using vault + passphrase vars
- Trigger job via GitLab webhook or API

---

### 🚀 **Week 4: Real-World Use Cases + Best Practices**

#### 🎯 Outcomes:

- Deploy end-to-end enterprise-grade Rex-Ansible project
- Follow structure and compliance

#### Project Example:

- Deploy and configure Tomcat with SSL on multiple RedHat servers using Rex-Ansible
- Steps:
  - Provision servers
  - Install packages
  - Copy configs
  - Set up monitoring (Nagios client or Prometheus exporter)
  - Log into ECMS
  - Send job result to mail

#### Best Practices:

- Use naming convention: `rex-ansible-{env}-{component}`
- Central inventory and var reuse
- Break roles into reusable playbooks
- Ensure audit logs are captured

---

## 🗂 Folder Structure Example:

```
rex-ansible/
├── jobs/
│   ├── deploy_nginx.yml
│   ├── update_servers.yml
├── roles/
│   └── nginx/
│       ├── tasks/
│       ├── handlers/
├── inventory/
│   ├── dev
│   └── prod
├── secrets/
│   └── vault.yml
├── logs/
├── ansible.cfg
```

---

## 📘 Resources (Most will be internal/enterprise due to proprietary use)

| Type          | Resource                                                                                       |
| ------------- | ---------------------------------------------------------------------------------------------- |
| Internal Wiki | Check UBS/Infosys internal Confluence or ECMS for Rex-Ansible SOPs                             |
| GitLab        | Look for sample Rex YAML in GitLab repos                                                       |
| Tools         | GSNOW, ECMS, GitLab CI, SMTP, Ansible Vault, AppDynamics (monitoring)                          |
| External      | You can’t find much public info — learn Ansible deeply and bridge Rex scripting on top of that |

---

## 🧠 Focus Areas for You (with your role in mind):

- **SSH-based execution with central inventory**
- **Logs + SNOW ticket triggers via Rex**
- **Using Rex jobs to call Ansible playbooks**
- **CI/CD triggers from GitLab to Rex-Ansible jobs**

---

Would you like me to generate a **sample Rex-Ansible job** for:

- NGINX deployment?
- Patching multiple Linux servers?
- Running Ansible playbooks from GitLab CI?
