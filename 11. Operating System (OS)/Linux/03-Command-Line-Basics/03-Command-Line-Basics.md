---
title: • 03-Command-Line-Basics
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 3
has_children: true
---

jnmklmlv



















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
