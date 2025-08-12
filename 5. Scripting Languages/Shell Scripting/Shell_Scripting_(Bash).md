---
title: "Shell_Scripting_(Bash)"
nav_order: 1
parent: "• Linux_Roadmap"
grand_parent: "Scripting"
---
Here’s a **Bash Scripting Roadmap** specially designed for DevOps engineers like you, with a solid Linux and data center background, and a goal of scaling to 20 LPA packages in top roles.

---

## 🧭 Bash Roadmap for DevOps Engineers (3–4 Weeks)

📘 **Goal**: Write efficient Bash scripts to automate server administration, CI/CD, file processing, cron jobs, cloud automation, log parsing, and more.

---

## 🔰 **Week 1: Core Bash Fundamentals**

### 🎯 Outcomes:

* Understand Bash syntax, variables, conditionals, and loops

### Topics:

* Shebang (`#!/bin/bash`)
* Variables (`name="natraj"`)
* Quoting: `" "$var" vs '$var'`
* `echo`, `read`, `printf`
* Conditional statements:

  * `if`, `else`, `elif`, `case`
* Loops:

  * `for`, `while`, `until`
* Exit codes and `$?`

### 🧪 Sample Script:

```bash
#!/bin/bash
echo "Enter your name:"
read name
echo "Hello, $name!"
```

---

## 🔧 Week 2: Intermediate Scripting + File/Process Management

### 🎯 Outcomes:

* Write reusable scripts for tasks like monitoring, backups, automation

### Topics:

* Functions in Bash
* Arguments: `$1`, `$2`, `$@`, `$#`
* File test operators (`-f`, `-d`, `-e`)
* String and arithmetic operations
* Loops with files
* Scheduling with `cron`
* Working with `find`, `grep`, `awk`, `sed`, `cut`, `xargs`

### 🧪 Sample Script (Backup):

```bash
#!/bin/bash
src="/etc/nginx/"
dest="/backup/nginx_$(date +%F).tar.gz"

tar -czf $dest $src
echo "Backup saved to $dest"
```

---

## ⚙️ Week 3: DevOps + Automation Use Cases

### 🎯 Outcomes:

* Use Bash in real DevOps environments

### Topics:

* Automation with `scp`, `ssh`, `rsync`
* Parse logs and monitor services
* Service status check scripts
* Bash with AWS CLI (`aws s3`, `aws ec2`, etc.)
* Bash with Docker (`docker ps`, `docker inspect`)
* Bash with Git (`git pull`, `git clone` automation)

### 🧪 Sample: Service Health Check

```bash
#!/bin/bash
services=("nginx" "docker" "sshd")

for s in "${services[@]}"; do
  if systemctl is-active --quiet $s; then
    echo "$s is running"
  else
    echo "$s is NOT running"
  fi
done
```

---

## 📦 Week 4: Advanced Bash + Integration with DevOps Tools

### 🎯 Outcomes:

* Master Bash for complex pipelines, hooks, and jobs

### Topics:

* Bash in Jenkins `sh` step
* Bash in Git hooks (pre-commit, post-merge)
* Bash in Terraform & Ansible (external scripts)
* Error handling with `trap`, `set -e`, `set -x`
* Logging, color outputs, exit traps

### 🧪 Sample: Jenkins Build Step

```bash
pipeline {
  agent any
  stages {
    stage('Run Bash') {
      steps {
        sh '''
          echo "Running deployment..."
          ./deploy.sh
        '''
      }
    }
  }
}
```

---

## 📘 Must-Learn Bash Commands for DevOps

| Command | Use Case                    |
| ------- | --------------------------- |
| `grep`  | Search logs, errors         |
| `awk`   | Field extraction, reporting |
| `sed`   | Stream editing              |
| `cut`   | Column extraction           |
| `xargs` | Process lists efficiently   |
| `curl`  | REST APIs and integrations  |
| `jq`    | JSON parsing with Bash      |

---

## 🧪 Mini Projects for Practice

1. **Disk space monitor script with alert email**
2. **Automate log archive rotation**
3. **Docker container health check via cron**
4. **S3 backup automation using AWS CLI + cron**
5. **Create Jenkins pipeline with custom Bash scripts**

---

## 🧠 Bash Best Practices

* Use `set -e` to fail fast
* Validate all inputs (`if [[ -z $var ]]`)
* Modularize using functions
* Avoid hardcoding paths — use variables
* Always test scripts with `bash -x script.sh`

---

## 📚 Useful Resources

* [Explainshell.com](https://explainshell.com/) – explains bash commands
* [ShellCheck](https://www.shellcheck.net/) – bash script linter
* [tldr.sh](https://tldr.sh) – simplified bash command docs

---

Would you like:

* A **GitHub repo template with Bash examples**?
* **PDF cheat sheet** for Bash syntax?
* Or help building a **real CI/CD pipeline using Bash + Jenkins + Docker**?

