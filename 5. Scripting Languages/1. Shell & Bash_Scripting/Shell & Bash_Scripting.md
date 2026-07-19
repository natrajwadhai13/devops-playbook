---
title: "• Shell & Bash_Scripting"
parent: "5. Scripting Languages"
nav_order: 1
has_children: true
---

**Shell Scripting and Bash Scripting together**. In practice, most Linux systems use **Bash**, so the terms are often used interchangeably. Bash is simply the most popular shell used for shell scripting. ([Linuxize][1])

## Complete Shell Scripting / Bash Scripting Syllabus (DevOps Focus)

### Module 1: Introduction

- What is Shell?
- What is Bash?
- Types of Shells (sh, bash, ksh, zsh)
- Shell vs Bash
- Why Shell Scripting?
- Real-world automation examples

---

### Module 2: Script Basics

- First Shell Script
- Shebang (`#!/bin/bash`)
- Comments
- Execute Script
- chmod +x
- bash script.sh vs ./script.sh
- Exit Status (`$?`)

---

### Module 3: Variables

- User Variables
- Environment Variables
- Read-only Variables
- Export Variables
- Special Variables
  - `$0`
  - `$1-$9`
  - `$#`
  - `$*`
  - `$@`
  - `$$`
  - `$?`
  - `$!`

---

### Module 4: User Input

- read command
- Prompt message
- Hidden Password (`read -s`)
- Multiple Inputs
- Default Values

---

### Module 5: Operators

- Arithmetic
- Comparison
- String
- Logical
- File Test Operators

---

### Module 6: Conditional Statements

- if
- if-else
- elif
- Nested if
- test command
- `[ ]`
- `[[ ]]`
- case statement

---

### Module 7: Loops

- for loop
- while loop
- until loop
- Infinite Loop
- break
- continue

---

### Module 8: Functions

- Function Declaration
- Return Values
- Local Variables
- Passing Arguments
- Recursive Functions

---

### Module 9: Arrays

- Indexed Arrays
- Associative Arrays
- Array Operations
- Loop Through Arrays

---

### Module 10: Strings

- String Length
- Concatenation
- Substring
- Replace Text
- Uppercase
- Lowercase
- Pattern Matching

---

### Module 11: File Handling

- Check File Exists
- Create/Delete Files
- Read File
- Write File
- Append File
- Permissions
- File Size
- Backup Script

---

### Module 12: Command Line Arguments

- Positional Parameters
- shift
- getopts
- Option Parsing

---

### Module 13: Input / Output

- stdin
- stdout
- stderr
- Redirection (`>`, `>>`, `<`, `2>`)
- Pipe (`|`)
- tee

---

### Module 14: Text Processing

- grep
- egrep
- cut
- awk
- sed
- sort
- uniq
- tr
- wc
- head
- tail
- xargs
- find

---

### Module 15: Process Management

- ps
- top
- kill
- killall
- jobs
- bg
- fg
- nohup
- nice

---

### Module 16: System Administration Scripts

- User Creation
- Password Reset
- User Deletion
- Group Management
- Disk Usage Report
- Memory Report
- CPU Monitoring
- Service Status

---

### Module 17: Logging & Debugging

- set -x
- set -e
- set -v
- trap
- Logger
- Error Handling

---

### Module 18: Scheduling

- cron
- crontab
- at command
- Automated Jobs

---

### Module 19: Regular Expressions

- Regex Basics
- grep Regex
- sed Regex
- awk Regex

---

### Module 20: Networking Scripts

- ping
- ssh
- scp
- curl
- wget
- netstat
- ss
- nc (Netcat)

---

### Module 21: DevOps Automation

- Git Automation
- Docker Automation
- Kubernetes Automation
- Jenkins Script
- Backup Script
- Health Check Script
- Deployment Script

---

### Module 22: Advanced Bash

- Command Substitution
- Arithmetic Expansion
- Brace Expansion
- Process Substitution
- Here Document
- Here String
- Source Command
- Alias
- Shell Options
- Trap Signals
- Named Pipes

---

### Module 23: Best Practices

- Coding Standards
- Script Structure
- Error Handling
- Logging
- Reusable Functions
- Secure Scripting
- Performance Optimization

---

## Hands-on Projects

1. System Health Check Script
2. User Creation Automation
3. Log File Analyzer
4. Automatic Backup Script
5. Disk Cleanup Script
6. SSL Certificate Expiry Checker
7. Docker Container Health Checker
8. Website Monitoring Script
9. Service Restart Automation
10. AWS EC2 Health Check Script
11. Jenkins Build Automation
12. Kubernetes Pod Monitoring Script
13. Nginx Log Analyzer
14. CPU & Memory Alert Script
15. Daily Server Report Generator

## Interview Topics

- Shell vs Bash
- Shebang
- Special Variables
- Difference between `$*` and `$@`
- Difference between `[` and `[[`
- if vs case
- for vs while
- Arrays
- Functions
- getopts
- sed vs awk
- grep vs egrep
- Exit Codes
- Cron Jobs
- Debugging
- Trap Command
- File Descriptors
- Redirection
- Pipes
- Process Management
- Shell Script Best Practices ([Linuxize][1])

For your **DevOps GitHub roadmap**, this syllabus is enough to cover **95% of the Bash/Shell scripting knowledge** expected in Linux Administrator, DevOps Engineer, SRE, and Cloud Engineer interviews.

[1]: https://linuxize.com/series/bash-scripting-fundamentals/?utm_source=chatgpt.com "Bash Scripting Fundamentals | Linuxize"
