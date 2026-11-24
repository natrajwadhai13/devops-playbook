---
title: "1. important-interview-questions"
parent: "• Troubleshooting Questions"
grand_parent: "• Ansible"
grand_grand_parent: "7. Configuration Management Tools"
nav_order: 1
has_children: true
---


Ansible – Top 30 Important Troubleshooting Questions

## Ansible – Top 30 Important Troubleshooting Questions

- [1. Ansible is unable to connect to a remote server. How will you troubleshoot?](#ansible-ssh-connectivity)
- [2. Ansible shows `UNREACHABLE!`. What are the possible reasons?](#ansible-unreachable-error)
- [3. SSH works manually but fails through Ansible. How will you fix it?](#ssh-works-manually-but-fails-through-ansible)
- [4. Ansible reports `Permission denied (publickey)`. How will you troubleshoot?](#permission-denied-publickey)
- [5. Ansible connects but fails with sudo permission errors. How will you resolve them?](#sudo-permission-errors)
- [6. Ansible is not recognizing hosts from the inventory file. How will you troubleshoot?](#inventory-not-recognizing-hosts)
- [7. Ansible is using the wrong SSH username or private key. How will you fix it?](#wrong-ssh-username-or-private-key)
- [8. Ansible variables are not loading correctly. How will you troubleshoot variable precedence?](#variable-precedence-issues)
- [9. Ansible playbook fails with YAML syntax errors. How will you debug it?](#yaml-syntax-errors)
- [10. Ansible reports `couldn't resolve module/action`. What will you check?](#module-or-action-not-found)
- [11. A task is skipped unexpectedly. How will you troubleshoot it?](#tasks-skipped-unexpectedly)
- [12. Ansible playbook fails because of an undefined variable. How will you fix it?](#undefined-variable-errors)
- [13. Ansible facts are not available. How will you troubleshoot fact gathering?](#ansible-facts-not-available)
- [14. Ansible fails to install packages on Ubuntu or RHEL. How will you investigate?](#package-installation-failures)
- [15. Ansible installs Nginx successfully but fails to start the service. What will you check?](#service-startup-failures)
- [16. Ansible restarts services on every execution. How will you prevent unnecessary restarts?](#unnecessary-service-restarts)
- [17. Ansible fails to copy files or configure permissions. How will you troubleshoot?](#file-copy-and-permission-issues)
- [18. Jinja2 template generates incorrect configuration. How will you debug it?](#jinja2-template-issues)
- [19. Ansible handler is not executing after configuration changes. Why?](#handler-not-executing)
- [20. Ansible Vault decryption fails. How will you troubleshoot it?](#ansible-vault-decryption-failures)
- [21. Ansible playbook takes too long to execute on 200+ servers. How will you optimize it?](#slow-playbook-execution)
- [22. How will you troubleshoot high CPU and memory usage on the Ansible controller?](#ansible-controller-high-cpu-and-memory)
- [23. Ansible dynamic inventory is not displaying AWS EC2 instances. How will you fix it?](#aws-dynamic-inventory-issues)
- [24. A playbook works in Dev but fails in Production. How will you investigate?](#playbook-works-in-dev-but-fails-in-production)
- [25. Deployment fails on 20 out of 100 production servers. How will you handle it?](#partial-production-deployment-failure)
- [26. How will you perform rolling deployments without production downtime?](#zero-downtime-rolling-deployment)
- [27. How will you implement rollback when an Ansible deployment fails?](#application-deployment-rollback)
- [28. How will you troubleshoot configuration drift across 500+ servers?](#configuration-drift-across-servers)
- [29. Ansible playbook execution is interrupted midway. How will you safely resume it?](#playbook-interrupted-midway)
- [30. Two engineers execute the same Ansible playbook simultaneously. How will you prevent conflicts?](#simultaneous-playbook-execution-conflicts)


===================


<a id="ansible-ssh-connectivity"></a>
### 1. Ansible is unable to connect to a remote server. How will you troubleshoot?

<a id="ansible-unreachable-error"></a>
### 2. Ansible shows `UNREACHABLE!`. What are the possible reasons?

<a id="ssh-works-manually-but-fails-through-ansible"></a>
### 3. SSH works manually but fails through Ansible. How will you fix it?

<a id="permission-denied-publickey"></a>
### 4. Ansible reports `Permission denied (publickey)`. How will you troubleshoot?

<a id="sudo-permission-errors"></a>
### 5. Ansible connects but fails with sudo permission errors. How will you resolve them?

<a id="inventory-not-recognizing-hosts"></a>
### 6. Ansible is not recognizing hosts from the inventory file. How will you troubleshoot?

<a id="wrong-ssh-username-or-private-key"></a>
### 7. Ansible is using the wrong SSH username or private key. How will you fix it?

<a id="variable-precedence-issues"></a>
### 8. Ansible variables are not loading correctly. How will you troubleshoot variable precedence?

<a id="yaml-syntax-errors"></a>
### 9. Ansible playbook fails with YAML syntax errors. How will you debug it?

<a id="module-or-action-not-found"></a>
### 10. Ansible reports `couldn't resolve module/action`. What will you check?

<a id="tasks-skipped-unexpectedly"></a>
### 11. A task is skipped unexpectedly. How will you troubleshoot it?

<a id="undefined-variable-errors"></a>
### 12. Ansible playbook fails because of an undefined variable. How will you fix it?

<a id="ansible-facts-not-available"></a>
### 13. Ansible facts are not available. How will you troubleshoot fact gathering?

<a id="package-installation-failures"></a>
### 14. Ansible fails to install packages on Ubuntu or RHEL. How will you investigate?

<a id="service-startup-failures"></a>
### 15. Ansible installs Nginx successfully but fails to start the service. What will you check?

<a id="unnecessary-service-restarts"></a>
### 16. Ansible restarts services on every execution. How will you prevent unnecessary restarts?

<a id="file-copy-and-permission-issues"></a>
### 17. Ansible fails to copy files or configure permissions. How will you troubleshoot?

<a id="jinja2-template-issues"></a>
### 18. Jinja2 template generates incorrect configuration. How will you debug it?

<a id="handler-not-executing"></a>
### 19. Ansible handler is not executing after configuration changes. Why?

<a id="ansible-vault-decryption-failures"></a>
### 20. Ansible Vault decryption fails. How will you troubleshoot it?

<a id="slow-playbook-execution"></a>
### 21. Ansible playbook takes too long to execute on 200+ servers. How will you optimize it?

<a id="ansible-controller-high-cpu-and-memory"></a>
### 22. How will you troubleshoot high CPU and memory usage on the Ansible controller?

<a id="aws-dynamic-inventory-issues"></a>
### 23. Ansible dynamic inventory is not displaying AWS EC2 instances. How will you fix it?

<a id="playbook-works-in-dev-but-fails-in-production"></a>
### 24. A playbook works in Dev but fails in Production. How will you investigate?

<a id="partial-production-deployment-failure"></a>
### 25. Deployment fails on 20 out of 100 production servers. How will you handle it?

<a id="zero-downtime-rolling-deployment"></a>
### 26. How will you perform rolling deployments without production downtime?

<a id="application-deployment-rollback"></a>
### 27. How will you implement rollback when an Ansible deployment fails?

<a id="configuration-drift-across-servers"></a>
### 28. How will you troubleshoot configuration drift across 500+ servers?

<a id="playbook-interrupted-midway"></a>
### 29. Ansible playbook execution is interrupted midway. How will you safely resume it?

<a id="simultaneous-playbook-execution-conflicts"></a>
### 30. Two engineers execute the same Ansible playbook simultaneously. How will you prevent conflicts?

===============================================

