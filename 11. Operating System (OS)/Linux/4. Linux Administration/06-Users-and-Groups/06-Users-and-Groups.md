---
title: • 06-Users-and-Groups
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 4
has_children: true
---

- [1. Group Administration](#1. Group Administration)
- [2. User Administration](#1. User Administration)
- [3. Sudo Users](#3. Sudo Users)

---

<a id="1. Group Administration"></a>

# 1. Group Administration

## What is a Group?

A **Group** is a collection of users who share the same permissions to files, directories, and system resources.

Instead of assigning permissions to each user individually, Linux allows administrators to assign permissions to a group. Any user who belongs to that group automatically inherits the group's permissions.

### Real-world Example

Suppose your company has 10 DevOps Engineers.

Instead of giving permissions individually:

```text
John
Rahul
Amit
Ravi
Kiran
...
```

Create one group:

```text
devops
```

Add all users to that group.

Now, whenever you grant permission to the **devops** group, every member gets access automatically.

This makes administration easier and reduces errors.

---

# Why Groups are Required?

Without Groups

```text
Project Directory

John   → Permission
Rahul  → Permission
Amit   → Permission
Ravi   → Permission
Kiran  → Permission
```

Administrator has to manage permissions for every user.

---

With Groups

```text
Project Directory

Group : devops

Members

John
Rahul
Amit
Ravi
Kiran
```

Administrator manages only **one group**.

Advantages

- Easier Administration
- Better Security
- Centralized Permission Management
- Easy to Add/Remove Users
- Used in Enterprise Linux Servers

---

# Types of Groups

Linux supports two types of groups.

## 1. Primary Group

Every user has **exactly one Primary Group**.

Example

```text
User

john

Primary Group

developers
```

Check Primary Group

```bash
id john
```

Output

```text
uid=1001(john) gid=1002(developers)
```

---

## 2. Secondary Group

A user can belong to multiple Secondary Groups.

Example

```text
User

john

Primary Group
└── developers

Secondary Groups
├── docker
├── devops
├── sudo
└── nginx
```

Check

```bash
groups john
```

Output

```text
john : developers docker devops sudo nginx
```

---

# Important Notes

### Every User has

- One Primary Group
- Zero or More Secondary Groups

---

### Every File has

- One Owner
- One Group Owner

Example

```text
-rw-r--r--

Owner : john

Group : developers
```

A file **cannot have two group owners**.

---

### One User can belong to Multiple Groups

Example

```text
developers

John

Rahul

docker

John

Amit
```

John belongs to both groups.

---

### Linux does NOT support Nested Groups

❌ Invalid

```text
developers
    └── docker
```

Groups can contain **users only**.

Groups **cannot contain other groups**.

---

# Group Database Files

Linux stores group information in two files.

| File           | Purpose                  |
| -------------- | ------------------------ |
| `/etc/group`   | Group Information        |
| `/etc/gshadow` | Secure Group Information |

---

# /etc/group

Contains basic information about every group.

Location

```text
/etc/group
```

Format

```text
group_name:x:GID:user1,user2,user3
```

Example

```text
developers:x:1002:john,rahul,amit
```

Meaning

| Field           | Description          |
| --------------- | -------------------- |
| developers      | Group Name           |
| x               | Password Placeholder |
| 1002            | Group ID (GID)       |
| john,rahul,amit | Group Members        |

View File

```bash
cat /etc/group
```

---

# /etc/gshadow

Stores secure group information.

Location

```text
/etc/gshadow
```

Format

```text
group_name:encrypted_password:group_admins:members
```

Example

```text
developers:!:root:john,rahul,amit
```

Meaning

| Field           | Description         |
| --------------- | ------------------- |
| developers      | Group Name          |
| !               | Encrypted Password  |
| root            | Group Administrator |
| john,rahul,amit | Members             |

View File

```bash
sudo cat /etc/gshadow
```

---

# Group Commands

## Create Group

### Syntax

```bash
groupadd [options] group_name
```

Example

```bash
groupadd developers
```

Verify

```bash
grep developers /etc/group
```

---

## Common Options

### Create Group with Custom GID

```bash
groupadd -g 2000 developers
```

Verify

```bash
grep developers /etc/group
```

Output

```text
developers:x:2000:
```

---

### Create Duplicate GID (Rare)

```bash
groupadd -o -g 2000 backup
```

> Used only in special migration scenarios.

---

# Modify Group

### Syntax

```bash
groupmod [options] group_name
```

---

## Rename Group

```bash
groupmod -n devops developers
```

Before

```text
developers
```

After

```text
devops
```

---

## Change Group ID

```bash
groupmod -g 3000 devops
```

---

## Allow Duplicate GID

```bash
groupmod -o -g 3000 backup
```

---

# Delete Group

### Syntax

```bash
groupdel group_name
```

Example

```bash
groupdel devops
```

⚠ A group **cannot be deleted if it is the Primary Group of any user**.

Check

```bash
grep devops /etc/passwd
```

---

# Group Membership

Linux uses **gpasswd** to manage group members.

---

## Add User to Group

Syntax

```bash
gpasswd -a username group
```

Example

```bash
gpasswd -a john docker
```

Output

```text
Adding user john to group docker
```

---

## Remove User

```bash
gpasswd -d john docker
```

---

## Set Group Administrator

```bash
gpasswd -A john developers
```

John can now manage members of the group.

---

## Remove Group Password

```bash
gpasswd -r developers
```

---

# newgrp Command

Temporarily changes the current Primary Group.

Syntax

```bash
newgrp group_name
```

Example

```bash
newgrp docker
```

Verify

```bash
id
```

---

# Important Commands

View Current User Groups

```bash
groups
```

Check User Information

```bash
id john
```

Search Group

```bash
grep developers /etc/group
```

List All Groups

```bash
cat /etc/group
```

---

# Real-world Scenario 1

Create a DevOps Team

```bash
groupadd devops

useradd john

useradd rahul

gpasswd -a john devops

gpasswd -a rahul devops
```

Now give permission to the group.

```bash
chgrp devops /project

chmod 770 /project
```

Both users can access the project.

---

# Real-world Scenario 2

Docker Administration

Instead of giving Docker permission individually,

Create one group.

```bash
groupadd docker
```

Add users.

```bash
usermod -aG docker john

usermod -aG docker rahul
```

Users can now run Docker commands without using `sudo` after logging out and back in.

---

# Best Practices

✅ Use meaningful group names.

Examples

```text
developers

devops

docker

finance

hr

backup
```

---

✅ Give permissions to groups instead of individual users.

---

✅ Use Secondary Groups for application access.

Example

```text
docker

nginx

git

jenkins
```

---

✅ Never use duplicate GIDs unless absolutely necessary.

---

✅ Remove users from groups when they leave the project.

---

# Interview Questions

### What is a Group?

A collection of users sharing the same permissions.

---

### How many Primary Groups can a user have?

Only **one**.

---

### How many Secondary Groups can a user have?

Multiple.

---

### Can one file have multiple group owners?

No.

---

### Can Linux groups contain other groups?

No.

---

### Which file stores group information?

```text
/etc/group
```

---

### Which file stores secure group information?

```text
/etc/gshadow
```

---

### Difference between groupadd and groupmod?

- `groupadd` → Creates a new group.
- `groupmod` → Modifies an existing group.

---

### Which command adds a user to a group?

```bash
gpasswd -a user group
```

or

```bash
usermod -aG group user
```

---

# Summary

- A group is a collection of users with shared permissions.
- Every user has one Primary Group and may have multiple Secondary Groups.
- Group information is stored in `/etc/group`.
- Secure group information is stored in `/etc/gshadow`.
- Common commands: `groupadd`, `groupmod`, `groupdel`, `gpasswd`, and `newgrp`.
- Managing permissions through groups simplifies administration and is the standard practice in enterprise Linux environments.

==========================================

<a id="1. User Administration"></a>

# User Administration

---

# What is a User?

A **User** is an account that allows a person or a service to log in and use the Linux operating system.

Each user has a unique identity and owns files, processes, and system resources.

Linux is a **multi-user operating system**, meaning multiple users can work on the same server simultaneously without affecting each other.

---

# Why Users are Required?

Without user accounts:

- No authentication
- No security
- No ownership of files
- No permission control
- Anyone could access the system

With user accounts:

- Secure login
- Individual home directory
- Personal files
- Controlled permissions
- User activity tracking

---

# User Types

## 1. Root User

- Superuser
- UID = **0**
- Full administrative privileges
- Can perform any operation

Example

```bash
sudo -i
```

---

## 2. System Users

Used by Linux services and applications.

Examples

```text
apache
nginx
mysql
sshd
postfix
```

Characteristics

- Usually UID **1-999** (RHEL)
- No login shell
- No home directory (sometimes)

---

## 3. Regular Users

Normal users created by the administrator.

Examples

```text
john
natraj
rahul
ubuntu
```

Characteristics

- UID >=1000
- Home Directory
- Login Shell
- Password

---

# User Information

Every user has:

- Username
- UID (User ID)
- Primary Group (GID)
- Secondary Groups
- Home Directory
- Login Shell
- Password

Example

```text
Username : john

UID       : 1001

GID       : 1001

Home      : /home/john

Shell     : /bin/bash
```

---

# Default Files Created

When creating a user, Linux automatically creates:

✅ User Account

✅ UID

✅ Primary Group

✅ Home Directory

```text
/home/username
```

✅ Mail Spool

```text
/var/spool/mail/username
```

✅ Login Shell

```text
/bin/bash
```

---

# User Database Files

| File                 | Purpose                      |
| -------------------- | ---------------------------- |
| /etc/passwd          | User Information             |
| /etc/shadow          | Encrypted Passwords          |
| /etc/default/useradd | Default user settings        |
| /etc/login.defs      | Password & UID Policy        |
| /etc/skel            | Default Home Directory Files |

---

# /etc/passwd

Stores user account information.

Location

```text
/etc/passwd
```

Format

```text
username:x:UID:GID:comment:home:shell
```

Example

```text
john:x:1001:1001:John Doe:/home/john:/bin/bash
```

Field Description

| Field    | Description          |
| -------- | -------------------- |
| username | Login Name           |
| x        | Password Placeholder |
| UID      | User ID              |
| GID      | Primary Group ID     |
| Comment  | User Description     |
| Home     | Home Directory       |
| Shell    | Login Shell          |

View

```bash
cat /etc/passwd
```

---

# /etc/shadow

Stores encrypted passwords.

Location

```text
/etc/shadow
```

Format

```text
username:password:last:min:max:warn:inactive:expire
```

Example

```text
john:$6$abcxyz...:19800:0:99999:7:::
```

Meaning

| Field    | Description          |
| -------- | -------------------- |
| username | Login Name           |
| password | Encrypted Password   |
| last     | Last Password Change |
| min      | Minimum Days         |
| max      | Maximum Days         |
| warn     | Warning Days         |
| inactive | Disable After Expiry |
| expire   | Account Expiry       |

View

```bash
sudo cat /etc/shadow
```

---

# User Commands

## Create User

### Syntax

```bash
useradd [options] username
```

Example

```bash
useradd john
```

Verify

```bash
id john
```

---

## Create User with Home Directory

```bash
useradd -m john
```

---

## Create User with Custom Home

```bash
useradd -d /project/john john
```

---

## Create User with UID

```bash
useradd -u 2001 john
```

---

## Create User with Primary Group

```bash
useradd -g developers john
```

---

## Create User with Secondary Groups

```bash
useradd -G docker,devops john
```

---

## Create User with Shell

```bash
useradd -s /bin/bash john
```

---

# useradd Important Options

| Option | Description           |
| ------ | --------------------- |
| -m     | Create Home Directory |
| -d     | Home Directory        |
| -u     | UID                   |
| -g     | Primary Group         |
| -G     | Secondary Groups      |
| -s     | Login Shell           |
| -c     | Comment               |
| -e     | Expiry Date           |

---

# Modify User

Syntax

```bash
usermod [options] username
```

---

## Change Username

```bash
usermod -l john_new john
```

---

## Change Home Directory

```bash
usermod -d /home/newjohn -m john
```

---

## Change Login Shell

```bash
usermod -s /bin/bash john
```

---

## Add Secondary Group

```bash
usermod -aG docker john
```

> **Always use `-aG`.** Using only `-G` replaces all existing secondary groups.

---

## Lock User

```bash
usermod -L john
```

---

## Unlock User

```bash
usermod -U john
```

---

# Delete User

Syntax

```bash
userdel username
```

Delete user only

```bash
userdel john
```

Delete user and home directory

```bash
userdel -r john
```

---

# Password Management

Set Password

```bash
passwd john
```

Lock Password

```bash
passwd -l john
```

Unlock Password

```bash
passwd -u john
```

Expire Password

```bash
passwd -e john
```

---

# User Information Commands

Current User

```bash
whoami
```

Current UID

```bash
id
```

User Groups

```bash
groups john
```

Logged-in Users

```bash
who
```

Detailed Login Information

```bash
w
```

Login History

```bash
last
```

---

# Switch User

Switch to another user

```bash
su - john
```

Switch to root

```bash
sudo -i
```

---

# Default User Settings

View Default Settings

```bash
useradd -D
```

Configuration File

```text
/etc/default/useradd
```

Password Policy

```text
/etc/login.defs
```

Default Files

```text
/etc/skel
```

---

# Important Commands

Safely Edit passwd

```bash
vipw
```

Safely Edit group

```bash
vigr
```

Check passwd File

```bash
pwck
```

Check group File

```bash
grpck
```

Password Aging

```bash
chage -l john
```

---

# Real-world Scenario 1

Create a new DevOps Engineer.

```bash
groupadd devops

useradd -m -g devops john

passwd john

usermod -aG docker,sudo john
```

Now John can

- Login
- Access DevOps Project
- Run Docker
- Use sudo

---

# Real-world Scenario 2

An employee leaves the company.

Lock account immediately.

```bash
passwd -l john
```

Backup data.

Delete account.

```bash
userdel -r john
```

---

# Best Practices

- Use meaningful usernames.
- Never share user accounts.
- Avoid logging in directly as root.
- Use `sudo` for administrative tasks.
- Always create a home directory (`-m`).
- Assign the correct primary group.
- Use secondary groups for application access.
- Remove unused accounts promptly.
- Enable password aging for security.

---

# Interview Questions

### What is UID?

A unique numeric identifier for a user.

---

### Which UID belongs to root?

`0`

---

### Which file stores user information?

```text
/etc/passwd
```

---

### Which file stores encrypted passwords?

```text
/etc/shadow
```

---

### Which command creates a new user?

```bash
useradd username
```

---

### Difference between `useradd` and `adduser`?

- `useradd` is a low-level command available on most Linux distributions.
- `adduser` is a user-friendly wrapper (common on Debian/Ubuntu) that guides you through user creation.

---

### Why use `usermod -aG`?

To add a user to a secondary group **without removing existing group memberships**.

---

### Which command displays user details?

```bash
id username
```

---

### Which command removes a user and the home directory?

```bash
userdel -r username
```

---

# Summary

- Linux uses user accounts for authentication and resource management.
- Every user has a unique UID, primary group, home directory, and login shell.
- User information is stored in `/etc/passwd`, while encrypted passwords are stored in `/etc/shadow`.
- Common administration commands include `useradd`, `usermod`, `userdel`, `passwd`, `id`, `groups`, `who`, and `last`.
- Proper user management is essential for security, access control, and day-to-day Linux system administration.

=============================

---

# Login Shell

A **Login Shell** is the program that starts after a user successfully logs in. It provides the command-line environment where users execute commands.

Check your current shell:

```bash
echo $SHELL
```

View a user's login shell:

```bash
grep john /etc/passwd
```

Example:

```text
john:x:1001:1001:John:/home/john:/bin/bash
```

The last field (`/bin/bash`) is the login shell.

## Common Login Shells

| Shell         | Description               |
| ------------- | ------------------------- |
| /bin/bash     | Default shell             |
| /bin/sh       | POSIX Shell               |
| /bin/zsh      | Advanced shell            |
| /bin/ksh      | Korn Shell                |
| /bin/csh      | C Shell                   |
| /sbin/nologin | Prevent interactive login |
| /bin/false    | Immediately exits         |

### Change User Shell

```bash
usermod -s /bin/bash john
```

or

```bash
chsh -s /bin/bash john
```

---

## /sbin/nologin vs /bin/false

| /sbin/nologin                             | /bin/false            |
| ----------------------------------------- | --------------------- |
| Displays "This account is not available." | Immediately exits     |
| Used for service accounts                 | Used to disable login |
| Recommended for services                  | Less informative      |

---

# /etc/skel

The **Skeleton Directory** contains default files that are copied into a new user's home directory during account creation.

Location

```text
/etc/skel
```

View

```bash
ls -la /etc/skel
```

Example

```text
.bashrc
.bash_profile
.bash_logout
```

When creating a user:

```bash
useradd -m john
```

Linux copies

```text
/etc/skel/*
```

to

```text
/home/john
```

### Real-world Example

Customize `/etc/skel/.bashrc` with company aliases.

Every new user automatically receives the same configuration.

---

# useradd -D

Displays or modifies the default settings used by `useradd`.

View defaults

```bash
useradd -D
```

Example Output

```text
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

### Change Default Shell

```bash
useradd -D -s /bin/bash
```

### Change Default Home Directory

```bash
useradd -D -b /data/home
```

Related Configuration File

```text
/etc/default/useradd
```

---

# lastlog Command

Displays the last successful login for users.

Syntax

```bash
lastlog
```

Example

```text
Username      Port     From           Latest
root          pts/0    192.168.1.10   Mon Jun 30
john          pts/1    192.168.1.20   Tue Jul 01
```

Specific user

```bash
lastlog -u john
```

### Use Cases

- Audit user logins
- Find inactive users
- Security checks

---

# finger Command (Optional)

Displays detailed information about a user.

Syntax

```bash
finger john
```

Example

```text
Login: john
Name: John Doe
Directory: /home/john
Shell: /bin/bash
Last Login: Tue Jul 01
```

> **Note:** `finger` is not installed by default on many modern Linux distributions because of security concerns.

---

# Troubleshooting

## User Cannot Login

Possible causes:

- Wrong password
- Account locked
- Password expired
- Invalid login shell
- Home directory missing

Check

```bash
passwd -S john

id john

grep john /etc/passwd
```

---

## Account Locked

Check

```bash
passwd -S john
```

Unlock

```bash
passwd -u john
```

or

```bash
usermod -U john
```

---

## Password Expired

Check

```bash
chage -l john
```

Reset

```bash
passwd john
```

---

## Home Directory Missing

Check

```bash
ls /home/john
```

Create

```bash
mkdir /home/john

chown john:john /home/john
```

---

## Incorrect Login Shell

Check

```bash
grep john /etc/passwd
```

Change

```bash
usermod -s /bin/bash john
```

---

## UID Conflict

Check user

```bash
id john
```

Find duplicate UIDs

```bash
awk -F: '{print $3}' /etc/passwd | sort | uniq -d
```

---

## GID Conflict

Find duplicate GIDs

```bash
awk -F: '{print $3}' /etc/group | sort | uniq -d
```

---

# Interview Questions

### What is the purpose of `/etc/skel`?

It stores default files that are copied to a new user's home directory during account creation.

### What does `useradd -D` do?

Displays or modifies the default settings used when creating new users.

### What is the difference between `/sbin/nologin` and `/bin/false`?

- `/sbin/nologin` displays a message and denies login.
- `/bin/false` immediately exits without displaying a message.

### What does `lastlog` display?

It shows the last successful login information for user accounts.

### Is the `finger` command installed by default?

No. Many modern Linux distributions do not install it by default due to security considerations.

---

===================================

<a id="3. Sudo Users"></a>

# 1. Introduction

## What is sudo?

**sudo (Super User Do)** allows a normal user to execute commands with the privileges of another user (usually **root**) without logging in as the root user.

Instead of sharing the root password, administrators can grant limited administrative access using `sudo`.

### Example

```bash
sudo systemctl restart nginx
```

Here, the normal user temporarily gets root privileges only for this command.

---

# Why Use sudo?

Without sudo

```text
User
   │
   ▼
Need Root Password
```

With sudo

```text
User
   │
sudo
   │
   ▼
Execute Administrative Commands
```

### Advantages

- Better Security
- No need to share the root password
- Command logging
- Temporary privilege escalation
- Fine-grained access control

---

# Root vs sudo

| Root                     | sudo                      |
| ------------------------ | ------------------------- |
| Full access all the time | Temporary elevated access |
| Requires root login      | Normal user login         |
| Harder to audit          | Commands are logged       |
| Less secure              | Recommended               |

---

# How sudo Works

```text
User Login
      │
      ▼
Runs sudo Command
      │
      ▼
sudo checks /etc/sudoers
      │
      ▼
Permission Granted
      │
      ▼
Command Executes as Root
```

---

# Sudo Configuration Files

| File            | Purpose                 |
| --------------- | ----------------------- |
| /etc/sudoers    | Main sudo configuration |
| /etc/sudoers.d/ | Additional sudo rules   |

---

# Edit sudo Configuration

Always use

```bash
visudo
```

Never edit

```bash
vi /etc/sudoers
```

### Why?

`visudo` checks syntax before saving.

---

# Grant sudo Access (RHEL)

## Step 1

Create User

```bash
useradd john

passwd john
```

---

## Step 2

Open sudoers

```bash
visudo
```

---

## Step 3

Find

```text
# %wheel ALL=(ALL) ALL
```

Uncomment

```text
%wheel ALL=(ALL) ALL
```

---

## Step 4

Add user to wheel group

```bash
usermod -aG wheel john
```

Verify

```bash
groups john
```

Output

```text
john : john wheel
```

---

## Step 5

Login

```bash
su - john
```

---

## Step 6

Test

```bash
sudo whoami
```

Output

```text
root
```

---

# Ubuntu

Ubuntu uses

```text
sudo
```

group instead of

```text
wheel
```

Add user

```bash
usermod -aG sudo john
```

---

# Passwordless sudo

Open

```bash
visudo
```

Add

```text
john ALL=(ALL) NOPASSWD: ALL
```

Now

```bash
sudo reboot
```

No password required.

---

# Give sudo to Multiple Users

Example

```text
User_Alias ADMINS = john,rahul,amit

ADMINS ALL=(ALL) ALL
```

---

# Allow Only Specific Commands

Instead of

```text
ALL
```

Use

```text
john ALL=(ALL) /usr/bin/systemctl
```

User can execute only

```bash
sudo systemctl
```

---

# Important Commands

Check sudo access

```bash
sudo -l
```

Current user

```bash
whoami
```

Become root

```bash
sudo -i
```

Run command as another user

```bash
sudo -u nginx whoami
```

---

# Real-world Scenario 1

Developer needs to restart Tomcat.

Instead of giving root access:

```bash
usermod -aG wheel developer
```

Developer executes

```bash
sudo systemctl restart tomcat
```

---

# Real-world Scenario 2

Database Administrator

Allow only MySQL service.

```text
dbadmin ALL=(ALL) /usr/bin/systemctl restart mysqld
```

Cannot execute any other root commands.

---

# Best Practices

- Never enable direct root login.
- Always use `sudo`.
- Use `visudo` to edit `/etc/sudoers`.
- Grant only the minimum required privileges.
- Use groups (`wheel` or `sudo`) instead of assigning permissions individually.
- Use passwordless sudo only when necessary.
- Audit sudo usage regularly.

---

# Troubleshooting

## sudo: command not found

Install sudo package.

---

## User is not in sudoers file

Check group membership.

```bash
groups john
```

Add to the correct group.

```bash
usermod -aG wheel john
```

or

```bash
usermod -aG sudo john
```

Log out and log back in.

---

## Permission denied

Check

```bash
sudo -l
```

Verify `/etc/sudoers` using

```bash
visudo
```

---

## User Added to wheel but sudo Doesn't Work

The user must log out and log in again for the new group membership to take effect.

---

# Interview Questions

### What is sudo?

Allows a normal user to execute commands with elevated privileges.

---

### Difference between root and sudo?

- Root has permanent administrative privileges.
- `sudo` provides temporary administrative privileges.

---

### Which file controls sudo permissions?

```text
/etc/sudoers
```

---

### Why use `visudo`?

It validates the syntax before saving and prevents configuration errors.

---

### Which group provides sudo access in RHEL?

```text
wheel
```

---

### Which group provides sudo access in Ubuntu?

```text
sudo
```

---

### Which command checks a user's sudo permissions?

```bash
sudo -l
```

---

### Summary

- `sudo` provides secure, temporary administrative access.
- `visudo` should always be used to edit `/etc/sudoers`.
- RHEL uses the **wheel** group, while Ubuntu uses the **sudo** group.
- Use least-privilege access and grant only the permissions users need.
- `sudo` is the recommended method for privilege escalation in enterprise Linux environments.
