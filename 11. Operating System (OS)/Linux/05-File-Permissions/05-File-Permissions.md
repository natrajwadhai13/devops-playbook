---
title: • 05-File-Permissions
parent: • Linux
grand_parent: 11. Operating System (OS)
nav_order: 4
has_children: true
---

# Module 4 – File Permissions

- chmod
- chown
- chgrp
- umask
- Sticky Bit
- SGID
- SUID
- ACL

---

---

title: File Permissions
parent: Linux
grand_parent: 11. Operating System (OS)
nav_order: 5
has_children: true

---

# File Permissions

## Overview

Linux uses **file permissions** to control who can read, write, and execute files or directories. Proper permission management is essential for **system security** and **DevOps administration**.

---

# View File Permissions

```bash
ls -l
```

**Example**

```text
-rwxr-xr-- 1 user group 1024 Jun 27 demo.sh
```

**Permission Breakdown**

```text
-rwxr-xr--
│││ │││ │││
│││ │││ └── Others (r--)
│││ └──── Group (r-x)
│└──────── Owner (rwx)
└───────── File Type
```

### Permission Symbols

| Symbol  | Meaning       |
| ------- | ------------- |
| `r (4)` | Read          |
| `w (2)` | Write         |
| `x (1)` | Execute       |
| `- (0)` | No Permission |

---

✅ File Type Indicators (first character in ls -l output)
| Symbol | Meaning |
| --- | --- |
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device file |
| `b` | Block device file |
| `p` | Named pipe (FIFO) |
| `s` | Socket |

# chmod

Used to **change file or directory permissions**.

### Syntax

```bash
chmod <permission> <file>
```

### Examples

```bash
chmod 755 script.sh
chmod +x script.sh
chmod -w file.txt
```

---

# chmod Command (Change File Permissions)

The `chmod` command is used to **change the permissions** of files and directories in Linux.

## Syntax

```bash
chmod [permissions] <file_name>
```

---

# User Categories

| Symbol | Meaning         |
| ------ | --------------- |
| `u`    | User (Owner)    |
| `g`    | Group           |
| `o`    | Others          |
| `a`    | All (u + g + o) |

---

# Permission Values

| Permission | Value |
| ---------- | ----: |
| r          |     4 |
| w          |     2 |
| x          |     1 |

### Common Permission Numbers

| Number | Permission |
| ------ | ---------- |
| 7      | rwx        |
| 6      | rw-        |
| 5      | r-x        |
| 4      | r--        |
| 3      | -wx        |
| 2      | -w-        |
| 1      | --x        |
| 0      | ---        |

---

# Symbolic Method

## Add Permission (+)

Give execute permission to the owner.

```bash
chmod u+x script.sh
```

Give write permission to the group.

```bash
chmod g+w file.txt
```

Give read permission to others.

```bash
chmod o+r notes.txt
```

Give execute permission to everyone.

```bash
chmod a+x script.sh
```

---

## Remove Permission (-)

Remove write permission from the owner.

```bash
chmod u-w file.txt
```

Remove execute permission from the group.

```bash
chmod g-x script.sh
```

Remove read permission from others.

```bash
chmod o-r secret.txt
```

Remove execute permission from everyone.

```bash
chmod a-x script.sh
```

---

## Set Exact Permission (=)

Give the owner only read and write permissions.

```bash
chmod u=rw file.txt
```

Give the group only read permission.

```bash
chmod g=r file.txt
```

Remove all permissions from others.

```bash
chmod o= file.txt
```

---

# Multiple Users Together

Give read permission to both owner and group.

```bash
chmod ug+r file.txt
```

Give execute permission to owner and others.

```bash
chmod uo+x script.sh
```

Remove write permission from group and others.

```bash
chmod go-w file.txt
```

---

# Numeric (Octal) Method

## 777

```bash
chmod 777 file.txt
```

```text
Owner  : rwx
Group  : rwx
Others : rwx
```

⚠️ Full access to everyone (Not recommended).

---

## 755

```bash
chmod 755 script.sh
```

```text
Owner  : rwx
Group  : r-x
Others : r-x
```

Commonly used for executable scripts and directories.

---

## 744

```bash
chmod 744 script.sh
```

```text
Owner  : rwx
Group  : r--
Others : r--
```

---

## 700

```bash
chmod 700 private.sh
```

```text
Owner  : rwx
Group  : ---
Others : ---
```

Only the owner can access.

---

## 644

```bash
chmod 644 file.txt
```

```text
Owner  : rw-
Group  : r--
Others : r--
```

Most common permission for files.

---

## 600

```bash
chmod 600 secret.txt
```

```text
Owner  : rw-
Group  : ---
Others : ---
```

Used for passwords, SSH keys, and confidential files.

---

# Check Permissions

```bash
ls -l
```

Example:

```text
-rwxr-xr-- file.txt
```

Meaning:

```text
Owner  : rwx
Group  : r-x
Others : r--
```

---

# Hands-on Practice

```bash
touch demo.txt

chmod u+x demo.txt

chmod g+w demo.txt

chmod o-r demo.txt

chmod 755 demo.txt

chmod 644 demo.txt

ls -l demo.txt
```

---

# Quick Interview Questions

### What does `chmod` do?

Changes file or directory permissions.

### What is the difference between `u`, `g`, and `o`?

- `u` → Owner
- `g` → Group
- `o` → Others

### What does `chmod 755` mean?

- Owner → `rwx`
- Group → `r-x`
- Others → `r-x`

### Which permission is commonly used for normal files?

`644`

### Which permission is commonly used for shell scripts?

`755`

---

# Key Takeaways

- `chmod` modifies file and directory permissions.
- Use **symbolic mode** (`u`, `g`, `o`, `a`) for specific permission changes.
- Use **numeric mode** (`755`, `644`, `600`) for quick permission assignment.
- Always follow the **principle of least privilege**—grant only the permissions that are necessary.

=======================

# chown

Changes the **owner** of a file or directory.

### Syntax

```bash
chown user file
```

### Example

```bash
# chown natraj file.txt

Both user and group

# chown username:groupname filename

```

---

# chgrp

Changes the **group ownership**.

### Syntax

```bash
chgrp group file
```

### Example

```bash
chgrp developers project.sh
```

---

# umask

Sets the **default permissions** for newly created files and directories.

### Check Current Value

```bash
umask
```

### Example

```bash
umask 022
```

---

# Sticky Bit

Used on **shared directories**.

Only the **file owner** or **root** can delete files.

### Example

```bash
chmod +t /shared
```

### Check

```bash
drwxrwxrwt
```

**Common Example**

```text
/tmp
```

---

# SGID (Set Group ID)

Files created inside the directory inherit the **directory's group**.

### Set SGID

```bash
chmod g+s project
```

### Check

```bash
drwxrwsr-x
```

---

# SUID (Set User ID)

When executed, the program runs with the **owner's permissions** instead of the current user's.

### Set SUID

```bash
chmod u+s file
```

### Check

```bash
-rwsr-xr-x
```

**Common Example**

```bash
/usr/bin/passwd
```

---

# ACL (Access Control List)

ACL provides **more flexible permissions** than the standard owner, group, and others model.

### Grant Permission

```bash
setfacl -m u:user:rwx file.txt
```

### View ACL

```bash
getfacl file.txt
```

---

# Hands-on Practice

View permissions:

```bash
ls -l
```

Make a file executable:

```bash
chmod +x script.sh
```

Change owner:

```bash
chown user file.txt
```

Change group:

```bash
chgrp developers file.txt
```

Check default permissions:

```bash
umask
```

View ACL:

```bash
getfacl file.txt
```

---

# Quick Interview Questions

### What does `chmod` do?

Changes file or directory permissions.

### What does `chown` do?

Changes the file owner.

### What does `chgrp` do?

Changes the group ownership.

### What is `umask`?

Defines default permissions for newly created files and directories.

### What is the Sticky Bit?

Only the file owner or root can delete files in a shared directory.

### What is SGID?

New files inherit the directory's group ownership.

### What is SUID?

A program runs with the file owner's privileges.

### What is ACL?

Provides fine-grained permissions beyond owner, group, and others.

---

# Key Takeaways

- `chmod` changes permissions.
- `chown` changes file ownership.
- `chgrp` changes group ownership.
- `umask` controls default permissions.
- Sticky Bit protects files in shared directories.
- SGID ensures consistent group ownership.
- SUID allows programs to run with the owner's privileges.
- ACL provides advanced and flexible permission management.

====================================

For a **Linux for DevOps** course, adding **Important Notes / Tips** is very useful because these are common interview questions and real-world administration concepts.

# Important Notes & Tips

## 1. Every File Has Only One Owner

A file can have **only one owner**.

Example:

```bash
ls -l demo.txt
```

Output:

```text
-rw-r--r-- 1 natraj developers 100 Jun 27 demo.txt
```

- Owner → `natraj`
- Group → `developers`

✅ Only one owner is allowed.

---

## 2. Every File Has Only One Group Owner

A file **cannot have two group owners**.

❌ Invalid

```text
demo.txt
Group = developers, devops
```

✅ Valid

```text
demo.txt
Group = developers
```

If multiple groups need access, use **ACL (Access Control Lists)** instead of assigning multiple groups.

---

## 3. One User Can Belong to Multiple Groups

A user can be a member of many groups.

Example:

```bash
groups natraj
```

Output

```text
natraj : natraj developers docker sudo
```

Here, the user belongs to **4 groups**.

---

## 4. Linux Does Not Support Nested Groups

Linux groups **cannot contain other groups**.

❌ Invalid

```text
developers
    ├── devops
    ├── docker
    └── admins
```

Groups can contain **users only**, not other groups.

---

## 5. Owner Permissions Are Independent

Changing group permissions does **not** affect the owner.

Example:

```bash
chmod g-w file.txt
```

Only the group's permissions change.

---

## 6. Root Can Access Everything

The **root** user bypasses most file permission restrictions.

Example:

```bash
sudo cat secret.txt
```

Even if another user cannot read the file, root usually can.

---

## 7. Directories and Files Have Different Meanings for Permissions

### File Permissions

- `r` → Read file
- `w` → Modify file
- `x` → Execute file

### Directory Permissions

- `r` → List directory contents
- `w` → Create/Delete/Rename files
- `x` → Enter (cd) the directory

Example:

```bash
chmod 755 project/
```

---

## 8. Execute Permission Is Required for Scripts

Without execute permission:

```bash
./backup.sh
```

Output

```text
Permission denied
```

Fix:

```bash
chmod +x backup.sh
```

---

## 9. `777` Is Dangerous

```bash
chmod 777 file.txt
```

Anyone can:

- Read
- Modify
- Execute

❌ Avoid using `777` in production.

---

## 10. Common Permission Standards

| Permission | Used For              |
| ---------- | --------------------- |
| 755        | Scripts & Directories |
| 644        | Normal Files          |
| 600        | Passwords, SSH Keys   |
| 700        | Private Scripts       |
| 777        | Avoid in Production   |

---

## 11. Ownership vs Permission

These are different concepts.

```text
Owner      → Who owns the file
Group      → Which group owns the file
Permission → What they can do
```

---

## 12. New Files Inherit Group (SGID)

If the SGID bit is set on a directory:

```bash
chmod g+s project/
```

Any new file created inside inherits the **directory's group** automatically.

---

## 13. Permission Check Order

When a user accesses a file, Linux checks permissions in this order:

1. Owner (`u`)
2. Group (`g`)
3. Others (`o`)

Linux stops at the **first matching category**.

---

## 14. ACL Extends Standard Permissions

Normally, Linux permissions are limited to:

- Owner
- Group
- Others

ACL allows granting permissions to specific additional users or groups.

Example:

```bash
setfacl -m u:john:rwx project.txt
```

---

## 15. Interview Tip

Always remember:

```text
Linux Permission Model

Owner  → One User
Group  → One Group
Others → Everyone Else
```

This is the foundation of Linux file security.
