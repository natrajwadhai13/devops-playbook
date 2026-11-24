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

## **1. Ansible is unable to connect to a remote server. How will you troubleshoot?**

**1. Scenario:** Ansible fails to connect to `web01` and reports `Connection timed out`. Other servers are working.

**2. What will you check?**

- Server availability and network connectivity.
- SSH service and port 22.
- Firewall / AWS Security Group.
- SSH username, key and inventory.

**3. Commands:**

```
ping -c 4 SERVER_IP
nc -zv SERVER_IP 22
ssh -i ~/.ssh/id_rsa ubuntu@SERVER_IP
sudo systemctl status ssh
sudo ss -tulnp | grep :22
sudo ufw status
ansible web01 -i inventory -m ping -vvv
```

**4. Root Cause:** SSH service stopped, port blocked, incorrect IP, firewall rules or SSH authentication issue.

**5. Solution:**

```
sudo systemctl start ssh
sudo systemctl enable ssh
sudo ufw allow 22/tcp
chmod 600 ~/.ssh/id_rsa
ansible all -i inventory -m ping
```

Also correct inventory and security group rules where required.

**6. Interview Answer:** "I check network connectivity, SSH service, port 22, firewall, security groups and authentication. Using SSH verbose mode and Ansible `-vvv`, I identify the root cause, fix it and verify connectivity using the Ansible ping module."

---

<a id="ansible-unreachable-error"></a>

## **2. Ansible shows&#x20;**`UNREACHABLE!`**. What are the possible reasons?**

**1. Scenario:** Out of 100 production servers, Ansible connects to 80 servers but reports `UNREACHABLE!` for 20 servers.

**2. What will you check?**

- Identify affected servers.
- Network and SSH connectivity.
- DNS resolution.
- SSH authentication and inventory.
- Firewall and routing.

**3. Commands:**

```
ansible all -i inventory -m ping
ansible-inventory -i inventory --list
ping -c 4 SERVER_IP
nc -zv SERVER_IP 22
nslookup SERVER_NAME
ssh -vvv ubuntu@SERVER_IP
ansible app01 -i inventory -m ping -vvv
```

**4. Root Cause:**

| Error                        | Possible cause        |
| ---------------------------- | --------------------- |
| Connection timed out         | Firewall / Network    |
| Connection refused           | SSH service stopped   |
| Permission denied            | SSH authentication    |
| No route to host             | Routing issue         |
| Name not known               | DNS issue             |
| Host key verification failed | SSH host key mismatch |

**5. Solution:**

```
sudo systemctl restart ssh
sudo ufw allow 22/tcp
ansible all -i inventory -m ping
```

Fix DNS, routing, SSH keys or inventory depending on the actual error. Verify host identity before updating known_hosts.

**6. Interview Answer:** "I identify unreachable hosts first, then check network, SSH, DNS, authentication and inventory. I use `ansible -vvv` to identify the exact failure and fix the corresponding issue."

---

<a id="ssh-works-manually-but-fails-through-ansible"></a>

## **3. SSH works manually but fails through Ansible. How will you fix it?**

**1. Scenario:** Manual SSH login works, but Ansible reports `Permission denied (publickey)`.

**2. What will you check?**

- SSH username used by Ansible.
- Private key path and permissions.
- SSH agent configuration.
- Remote `authorized_keys`.
- Inventory variables.

**3. Commands:**

```
ssh ubuntu@SERVER_IP
ssh -vvv ubuntu@SERVER_IP
ls -l ~/.ssh/
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
ssh-add -l
ansible web01 -i inventory -m ping -vvv
```
**4. Root Cause:** Manual SSH uses a different key, but Ansible uses an incorrect key, username or inventory configuration.

**5. Solution:**

Inventory:

```
[webservers]
web01 ansible_host=192.168.1.30 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/.ssh/id_rsa
```

Test:

```
ansible web01 -i inventory -m ping --private-key=/home/ubuntu/.ssh/id_rsa
```

**6. Interview Answer:** "I compare manual SSH and Ansible SSH configurations. I verify username, private key, permissions, SSH agent and authorized_keys. Then I configure the correct connection variables in inventory and test again."

<a id="permission-denied-publickey"></a>

## **4. Ansible reports&#x20;**`Permission denied (publickey)`**. How will you troubleshoot?**

**1. Scenario:** Server is reachable and SSH port 22 is open, but Ansible cannot authenticate using the SSH key.

**2. What will you check?**

- Correct SSH username and private key.
- Public key in remote `authorized_keys`.
- `.ssh` directory permissions and ownership.
- SSH server configuration.
- Authentication logs.

**3. Commands:**

```
ssh -i ~/.ssh/id_rsa ubuntu@SERVER_IP
ls -l ~/.ssh/id_rsa
chmod 600 ~/.ssh/id_rsa

# On remote server
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
sudo tail -100 /var/log/auth.log
```

**4. Root Cause:** Incorrect private key, missing public key, wrong ownership, invalid permissions or disabled public-key authentication.

**5. Solution:**

```
# From Ansible controller
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@SERVER_IP

# On remote server
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chown -R ubuntu:ubuntu ~/.ssh

# Test
ansible web01 -i inventory -m ping
```

**6. Interview Answer:** "I verify the SSH key pair, username, authorized_keys, ownership and permissions. If the public key is missing, I install the correct public key and validate authentication using Ansible ping."

<a id="sudo-permission-errors"></a>

## **5. Ansible connects but fails with sudo permission errors. How will you resolve them?**

**1. Scenario:** Ansible connects successfully using the `ubuntu` user but fails while installing Nginx with `Missing sudo password` or sudo permission denied.

**2. What will you check?**

- Remote user sudo privileges.
- `become: yes` configuration.
- Sudoers file and group membership.
- Whether sudo requires a password.

**3. Commands:**

```
ssh ubuntu@SERVER_IP
whoami
sudo -l
groups

# Validate sudoers
sudo visudo -c

# Test Ansible privilege escalation
ansible web01 -i inventory -m command -a "whoami" -b

# Prompt for become password
ansible web01 -i inventory -m ping -b -K
```

**4. Root Cause:** User lacks sudo privileges, passwordless sudo is not configured, or `become` is missing.

**5. Solution:**

Ubuntu:

```
sudo usermod -aG sudo ubuntu
```

RHEL:

```
sudo usermod -aG wheel ubuntu
```

Playbook:

```
---
- name: Install Nginx
  hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: yes
```

Execute:

```
ansible-playbook -i inventory nginx.yml -K
```

For production, configure least-privilege sudo rules and secure credential management.

**6. Interview Answer:** "I check sudo privileges using `sudo -l`, validate sudoers and verify Ansible become configuration. If required, I configure approved sudo permissions or use `-K` to provide the become password. Finally, I verify execution as root."

---



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