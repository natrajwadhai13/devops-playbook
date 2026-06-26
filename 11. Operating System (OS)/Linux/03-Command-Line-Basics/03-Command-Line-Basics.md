---
title: • 03-Command-Line-Basics
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 3
has_children: true
---

---

title: Command Line Basics
parent: Linux
grand_parent: 11. Operating System (OS)
nav_order: 3
has_children: true

---

# Linux Command Line Basics

## Overview

The Linux Command Line (CLI) allows users to interact with the operating system using commands. It is faster, more powerful, and widely used by **Linux Administrators**, **System Engineers**, and **DevOps Engineers**.

---

# What is CLI?

CLI (Command Line Interface) is a text-based interface where users execute commands to perform tasks.

**Example**

```bash
pwd
ls
mkdir demo
```

---

# Why Learn Linux Commands?

- Faster than GUI
- Easy Automation
- Remote Server Management
- Essential for DevOps & Cloud
- Used in Production Servers

---

# Command Syntax

```bash
command [options] [arguments]
```

**Example**

```bash
ls -l /home
```

- `ls` → Command
- `-l` → Option
- `/home` → Argument

---

# Important Categories

## 1. Navigation Commands

Used to move between directories.

```bash
pwd
ls
cd
```

---

## 2. File & Directory Commands

Used to create, copy, move, and delete files.

```bash
mkdir
touch
cp
mv
rm
rmdir
```

---

## 3. File Viewing Commands

Used to read file contents.

```bash
cat
less
more
head
tail
```

---

## 4. Search Commands

Used to search files or text.

```bash
find
locate
grep
```

---

## 5. Help Commands

Used to get command documentation.

```bash
man
--help
whatis
```

---

# Best Practices

- Use `man` to learn any command.
- Avoid using `rm -rf` without checking the path.
- Press **Tab** for auto-completion.
- Use **Up Arrow (↑)** to repeat previous commands.
- Clear the terminal using:

```bash
clear
```

or

```bash
Ctrl + L
```

---

# Hands-on Practice

- Open the Terminal.
- Check the current directory.
- Navigate between folders.
- Create and delete a test folder.
- View the help page of a command.

---

# Quick Interview Questions

### What is CLI?

A text-based interface used to interact with the Linux operating system.

### Why is CLI preferred in Linux?

It is faster, supports automation, and is ideal for remote server management.

### What is the syntax of a Linux command?

```text
command [options] [arguments]
```

### How do you get help for a command?

```bash
man <command>
```

or

```bash
<command> --help
```

---

# Key Takeaways

- CLI is the primary interface for Linux administration.
- Linux commands follow the format: `command [options] [arguments]`.
- Learning basic commands is the foundation for Linux, Cloud, and DevOps.
- Practice commands daily to build confidence before moving to advanced topics.



## File Commands

```bash
pwd
ls
cd
mkdir
touch
cp
mv
rm
find
locate
tree
```

## Searching

```bash
grep
egrep
awk
sed
sort
uniq
cut
tr
```

## Compression

```bash
zip
unzip
gzip
gunzip
tar
xz
```




========================================

```bash
mkdir -p \
01-Introduction \
02-Linux-Installation \
03-Command-Line-Basics \
04-Linux-File-System \
05-File-Permissions \
06-Users-and-Groups \
07-Process-Management \
08-Package-Management \
09-Disk-Management \
10-Systemd-and-Services \
11-Boot-Process \
12-Networking \
13-SSH-and-Remote-Access \
14-Logs-and-Monitoring \
15-Cron-and-Scheduling \
16-Bash-Scripting \
17-Linux-Security \
18-Performance-Monitoring \
19-Storage-and-NFS \
20-Linux-Troubleshooting \
21-Linux-for-DevOps \
22-Web-Servers \
23-Real-World-Projects \
24-Interview-Questions \
25-Final-Project && \
for dir in */; do
    name="${dir%/}"
    touch "$dir$name.md"
done
```
