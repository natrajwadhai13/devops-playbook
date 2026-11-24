---
title: "• Troubleshooting Questions"
parent: "• Ansible"
grand_parent: "7. Configuration Management Tools"
nav_order: 0
has_children: true
---

# **Ansible – Troubleshooting & Production Scenarios Interview Questions**

This section covers real-world Ansible production troubleshooting scenarios for Linux Administrators, DevOps Engineers, and Senior DevOps Engineers.

The question bank is designed to cover Ansible from basic troubleshooting to advanced enterprise-level production issues.

**Recommended format for every scenario:**

1. Scenario – What happened in production?
2. What will you check?
3. Commands – How will you troubleshoot?
4. Root Cause – Why did the issue occur?
5. Solution – How will you fix it?
6. Interview Answer – How will you explain it in an interview?

## **Ansible Troubleshooting & Production Scenario Question Bank**

### **1. Ansible Connectivity & SSH Troubleshooting**

1. Ansible is unable to connect to a remote Linux server. How will you troubleshoot?
2. Ansible shows `UNREACHABLE!` while executing a playbook. What could be the reason?
3. SSH works manually, but Ansible SSH connection fails. How will you investigate?
4. Ansible reports `Permission denied (publickey)`. How will you fix it?
5. Ansible connection works for some servers but fails for others. How will you troubleshoot?
6. Ansible is unable to connect to a server on a custom SSH port. What configuration is required?
7. Ansible SSH connection takes too much time. How will you optimize it?
8. Ansible reports `Connection timed out`. What will you check?
9. Ansible cannot resolve the hostname of remote servers. How will you troubleshoot?
10. Ansible works with an IP address but fails with a hostname. What could be wrong?

### **2. Inventory & Configuration Troubleshooting**

11. Ansible is not recognizing hosts defined in the inventory file. How will you troubleshoot?
12. A host is present in the inventory but is not executing during playbook execution. Why?
13. Ansible is using the wrong SSH username. How will you correct it?
14. Variables defined in the inventory are not reflecting in the playbook. How will you troubleshoot?
15. Group variables are not applying to the target servers. What could be the issue?
16. Host variables are overriding group variables unexpectedly. How will you investigate?
17. Ansible is loading the wrong configuration file (`ansible.cfg`). How will you identify the active configuration?
18. How will you troubleshoot inventory issues in a large production environment with 500+ servers?

### **3. Privilege Escalation & Sudo Troubleshooting**

19. Ansible connects successfully but fails while executing commands requiring root privileges. How will you troubleshoot?
20. `become: yes` is configured, but Ansible still reports permission denied. What could be wrong?
21. Manual sudo works, but Ansible sudo execution fails. How will you fix it?
22. Ansible reports `Missing sudo password`. How will you resolve it?
23. How will you configure Ansible to execute tasks as a different user?
24. Ansible playbook fails because the remote user does not have passwordless sudo access. How will you troubleshoot?
25. How will you securely manage sudo passwords in production?

### **4. Playbook Execution & YAML Troubleshooting**

26. Ansible playbook fails with a YAML syntax error. How will you identify the issue?
27. Ansible reports `ERROR! couldn't resolve module/action`. How will you troubleshoot?
28. A playbook executes successfully but does not perform the expected task. What will you check?
29. Ansible executes tasks in the wrong sequence. How will you investigate?
30. A task is skipped unexpectedly. How will you troubleshoot conditions and tags?
31. Ansible playbook stops after the first failed task. How can you handle failures?
32. How will you troubleshoot variable precedence issues?
33. A variable is showing as undefined during playbook execution. How will you fix it?
34. Ansible reports an error while accessing a registered variable. What could be wrong?
35. How will you debug a complex playbook with multiple roles and conditions?

### **5. Package Management & Service Troubleshooting**

36. Ansible fails to install packages on Ubuntu servers. How will you troubleshoot?
37. Ansible package installation works on RHEL but fails on Ubuntu. What could be the issue?
38. Ansible reports that a package is already installed, but the application is not working. How will you investigate?
39. Ansible successfully installs Nginx but fails to start the service. What will you check?
40. Ansible restarts a service every time the playbook runs. How will you prevent unnecessary restarts?
41. Ansible reports `Could not find the requested service`. How will you troubleshoot?
42. Ansible is unable to update the package repository. How will you fix it?
43. How will you handle package installation across different Linux distributions?

### **6. File, Directory & Permission Troubleshooting**

44. Ansible fails to copy a file to a remote server. How will you troubleshoot?
45. The `copy` module reports success, but the destination file is missing. What will you investigate?
46. Ansible creates a directory with incorrect ownership and permissions. How will you fix it?
47. Ansible cannot modify a configuration file because of permission issues. How will you troubleshoot?
48. A Jinja2 template generates incorrect configuration on production servers. How will you debug it?
49. Ansible configuration management overwrites manual production changes. How will you prevent this?
50. Ansible is unable to download files using the `get_url` module. What will you check?

### **7. Variables, Facts & Templates Troubleshooting**

51. Ansible facts are not available during playbook execution. How will you troubleshoot?
52. The `ansible_default_ipv4` variable is undefined. What could be the reason?
53. Ansible fact gathering is taking too much time. How will you optimize it?
54. Jinja2 template variables are not rendering correctly. How will you troubleshoot?
55. Ansible is using an unexpected variable value from `group_vars` or `host_vars`. How will you identify the source?
56. How will you troubleshoot errors involving nested dictionaries and lists?
57. A template works for one server but fails for another. How will you investigate?

### **8. Roles, Handlers & Task Execution**

58. Ansible role is not executing even though it is included in the playbook. What will you check?
59. A handler is not triggered after a configuration file changes. How will you troubleshoot?
60. A handler is executing multiple times unexpectedly. How will you optimize it?
61. Ansible role dependencies are not working correctly. How will you investigate?
62. How will you troubleshoot issues when multiple roles modify the same configuration file?
63. How will you debug a task that works independently but fails when executed through a role?

### **9. Ansible Performance & Scalability**

64. Ansible playbook takes 45 minutes to configure 200 Linux servers. How will you reduce execution time?
65. Ansible is executing tasks sequentially instead of parallel execution. How will you troubleshoot?
66. How will you optimize Ansible forks for 500+ servers?
67. Ansible controller CPU and memory usage are very high during playbook execution. What will you check?
68. SSH connections are repeatedly established for every task. How can you optimize connections?
69. How will you use `serial`, `throttle`, and `forks` in a production environment?
70. How will you troubleshoot performance issues caused by fact gathering?

### **10. Ansible Vault & Security Troubleshooting**

71. Ansible Vault password is incorrect. How will you troubleshoot?
72. A playbook fails with `Decryption failed`. What could be the reason?
73. How will you manage different Vault passwords for Dev, QA, and Production?
74. How will you troubleshoot encrypted variables that are not being decrypted?
75. How will you prevent passwords and secrets from appearing in Ansible logs?
76. How will you securely manage SSH keys, API tokens, and database credentials?
77. How will you integrate Ansible Vault with an enterprise secret-management solution?

### **11. Ansible Dynamic Inventory & Cloud Troubleshooting**

78. AWS EC2 instances are not appearing in Ansible dynamic inventory. How will you troubleshoot?
79. Ansible cannot authenticate with AWS APIs. What will you check?
80. Dynamic inventory returns duplicate hosts. How will you investigate?
81. Ansible cannot identify newly created EC2 instances. How will you troubleshoot?
82. How will you manage dynamic inventories across AWS regions?
83. Ansible is unable to connect to Azure virtual machines. What will you investigate?
84. How will you troubleshoot inventory synchronization issues in a cloud environment?

### **12. Ansible Production Deployment Scenarios**

85. You need to deploy an application on 100 production servers without downtime. How will you design the playbook?
86. During deployment, 20 out of 100 servers fail. How will you handle the deployment?
87. Ansible deployment succeeds, but the application is not accessible. What will you troubleshoot?
88. A configuration change breaks the production application. How will you roll back?
89. How will you implement rolling deployments using Ansible?
90. How will you perform application health checks after deployment?
91. How will you ensure that only healthy servers receive production traffic?
92. How will you prevent simultaneous restarts of all production servers?
93. How will you deploy different application versions to different environments?
94. How will you handle application deployment when the database migration fails?

### **13. Ansible Advanced Production Scenarios**

95. Two engineers execute the same playbook simultaneously. What problems can occur, and how will you handle them?
96. Ansible playbook execution is interrupted midway. How will you safely resume it?
97. A playbook runs successfully in Dev but fails in Production. How will you investigate?
98. How will you make a playbook idempotent?
99. How will you handle configuration drift across 500+ servers?
100. How will you implement compliance checks using Ansible?
101. How will you troubleshoot intermittent failures during large-scale execution?
102. How will you execute a playbook against only failed hosts?
103. How will you handle unreachable servers without stopping the entire deployment?
104. How will you implement centralized Ansible logging and auditing?
105. How will you troubleshoot Ansible Automation Platform / AWX job failures?
106. How will you manage Ansible execution across multiple teams and environments?

## **Important Ansible Troubleshooting Commands**

These commands should be covered alongside the scenario questions.

| Command                                                 | Purpose                      |
| ------------------------------------------------------- | ---------------------------- |
| `ansible --version`                                     | Check Ansible version        |
| `ansible-config dump --only-changed`                    | Check modified configuration |
| `ansible-inventory --list`                              | Display inventory            |
| `ansible-inventory --graph`                             | Display inventory groups     |
| `ansible all -m ping`                                   | Test connectivity            |
| `ansible-playbook --syntax-check site.yml`              | Check syntax                 |
| `ansible-playbook --check site.yml`                     | Dry-run execution            |
| `ansible-playbook -i inventory site.yml -v`             | Verbose execution            |
| `ansible-playbook site.yml -vvv`                        | Detailed debugging           |
| `ansible-playbook site.yml --list-hosts`                | List target hosts            |
| `ansible-playbook site.yml --list-tasks`                | List tasks                   |
| `ansible-playbook site.yml --start-at-task="Task Name"` | Start from a specific task   |
| `ansible-playbook site.yml --limit webservers`          | Limit execution              |
| `ansible-playbook site.yml --tags nginx`                | Execute specific tags        |
| `ansible-doc copy`                                      | Module documentation         |

