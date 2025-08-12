---
title: Git-Github
parent: "8. Repository Management (VCS) Tools"
nav_order: 1
has_children: true
---




## 🔧 Git & GitHub – Functionalities and Why They Matter in DevOps

| **Functionality**               | **What It Is**                                                             | **Why It’s Important in DevOps**                                                          |
| ------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Version Control**             | Track changes to code over time using commits.                             | Ensures rollback, history tracking, and collaboration across teams.                       |
| **Branching**                   | Create isolated copies of the main codebase (feature, dev, prod).          | Supports CI/CD by separating development, staging, and production workflows.              |
| **Pull Requests (PR)**          | Request to merge changes from one branch to another (usually into `main`). | Enables peer review, CI checks, and enforces clean, reviewed code before merging.         |
| **Merge Conflicts**             | Conflicts when two branches change the same part of a file.                | Helps understand collaborative code resolution — a key real-world team skill.             |
| **Tags / Releases**             | Specific points in history marked as releases (e.g., `v1.0.0`).            | Critical for deployments, rollbacks, and versioning packages or containers.               |
| **Git Hooks (Auto Triggers)**   | Scripts that run before/after Git events like commit or push.              | Used in automation (e.g., pre-commit linting, post-commit CI triggers).                   |
| **Webhooks (GitHub)**           | GitHub -> Sends a signal when something happens (e.g., push, PR).          | Triggers CI/CD pipelines in Jenkins/GitHub Actions/GitLab — core to DevOps automation.    |
| **GitHub Actions / CI/CD**      | Automate tests, build, deploy when code is pushed or PRs are created.      | Saves time, enforces quality checks, enables fully automated DevOps pipelines.            |
| **Secrets & Tokens**            | GitHub -> Store API keys/passwords securely in repo settings.              | Keeps credentials out of code. Essential for secure automation & deployments.             |
| **Personal Access Token (PAT)** | Replaces passwords for authentication with GitHub via HTTPS.               | Required for secure Git pushes (especially after GitHub removed password auth).           |
| **.gitignore**                  | Tells Git which files/folders **not** to track (e.g., logs, secrets).      | Keeps your repo clean and secure from unnecessary or sensitive files.                     |
| **Fork / Clone**                | Fork: copy a repo on GitHub. Clone: pull repo to your local system.        | Allows collaboration, customization, and contribution without affecting original project. |
| **GitHub Pages**                | Host static sites directly from GitHub repos.                              | Used for documentation, personal portfolios, or live demos of DevOps projects.            |
| **Commit Messages**             | Notes on what the commit changes.                                          | Good commit messages = clear audit trail, helpful for reviews and debugging.              |
| **Rebasing vs Merging**         | Rebasing: linear history. Merging: preserves branch history.               | Helps maintain clean project history and resolve conflicts more efficiently.              |
| **Revert / Reset / Checkout**   | Ways to go back in history or undo mistakes.                               | Crucial for disaster recovery, rollbacks, or undoing broken deployments.                  |

---

## 🔒 Git Security and Access Control

| **Security Feature**        | **Purpose**                                             |
| --------------------------- | ------------------------------------------------------- |
| **SSH Key Authentication**  | Safer than passwords for authenticating Git access.     |
| **GPG Signed Commits**      | Verifies the identity of the commit author.             |
| **Branch Protection Rules** | Prevents force-pushes or direct commits to main branch. |
| **2FA on GitHub**           | Adds extra security for login and repo access.          |

---

## 🧠 Real-World Use Cases

| **Scenario**                           | **GitHub Feature Used**                         |
| -------------------------------------- | ----------------------------------------------- |
| CI/CD for Node.js app                  | GitHub Actions + Secrets + Branch Protection    |
| Terraform IaC repo                     | Tags for versioning + GitHub Webhook to Jenkins |
| Docker-based Python app                | GitHub Actions + Docker Build/Push on release   |
| PR-based deployment for staging        | Branching + Auto-Deploy on PR Merge             |
| Secrets management for AWS credentials | GitHub Secrets + GitHub Actions                 |

---

## ✅ Bonus Tip

If you're targeting **DevOps interviews for 20+ LPA**, being able to **explain these Git features + demonstrate in your own GitHub repo** (with real workflows, hooks, GitHub Actions, etc.) makes a **huge difference**.

---

Would you like me to prepare this in a **Markdown file** for your DevOps notes GitHub site? I can create and format it as:

```
_devops-interview/git-functionalities.md
```

