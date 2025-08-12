---
title: "• commands"
parent: Git-Github
grand_parent: "8. Repository Management (VCS) Tools"
nav_order: 1
has_children: true
---

Here’s a complete and categorized list of **Git & GitHub commands** — essential for DevOps engineers, developers, and system admins. I've organized them from basic to advanced, so you can build up your skills step-by-step.

---

## 🟢 Basic Git Commands

| **Command**               | **Description**                                |
| ------------------------- | ---------------------------------------------- |
| `git init`                | Initialize a new Git repository                |
| `git clone <repo-url>`    | Clone a remote repo locally                    |
| `git status`              | Show current status of files (modified/staged) |
| `git add <file>`          | Add file(s) to staging area                    |
| `git add .`               | Add all changes to staging area                |
| `git commit -m "message"` | Commit staged changes with message             |
| `git push`                | Push commits to remote repo                    |
| `git pull`                | Pull latest changes from remote repo           |
| `git log`                 | View commit history                            |

---

## 🟡 Branching & Merging

| **Command**                     | **Description**                    |
| ------------------------------- | ---------------------------------- |
| `git branch`                    | List all branches                  |
| `git branch <branch-name>`      | Create a new branch                |
| `git checkout <branch-name>`    | Switch to a different branch       |
| `git checkout -b <branch-name>` | Create and switch to a new branch  |
| `git merge <branch-name>`       | Merge a branch into current branch |
| `git branch -d <branch-name>`   | Delete a local branch              |

---

## 🔄 Undo Changes & Recovery

| **Command**              | **Description**                          |
| ------------------------ | ---------------------------------------- |
| `git reset <file>`       | Unstage a file                           |
| `git reset --hard`       | Discard all changes in working directory |
| `git revert <commit>`    | Revert a commit by creating a new one    |
| `git checkout -- <file>` | Restore a file to last commit            |
| `git clean -fd`          | Remove untracked files and folders       |

---

## 🔗 Remote Repositories

| **Command**                        | **Description**                    |
| ---------------------------------- | ---------------------------------- |
| `git remote -v`                    | Show current remotes               |
| `git remote add origin <url>`      | Add a new remote                   |
| `git push -u origin <branch-name>` | Push branch and set upstream       |
| `git fetch`                        | Download new data from remote repo |
| `git remote remove <name>`         | Remove a remote                    |

---

## 🏷️ Tags & Releases

| **Command**                  | **Description**               |
| ---------------------------- | ----------------------------- |
| `git tag`                    | List all tags                 |
| `git tag <tag-name>`         | Create a tag                  |
| `git tag -d <tag-name>`      | Delete a tag                  |
| `git push origin <tag-name>` | Push tag to remote            |
| `git push origin --tags`     | Push all local tags to remote |

---

## 🛠️ Advanced Commands

| **Command**                | **Description**                             |
| -------------------------- | ------------------------------------------- |
| `git stash`                | Temporarily save changes without committing |
| `git stash apply`          | Reapply stashed changes                     |
| `git cherry-pick <commit>` | Apply a specific commit to another branch   |
| `git rebase <branch>`      | Reapply commits on top of another base tip  |
| `git reflog`               | Show history of HEAD changes                |
| `git shortlog`             | Summary of commits grouped by author        |
| `git blame <file>`         | Show who modified each line of a file       |

---

## 🧪 GitHub (Git CLI Integration)

| **Command**                       | **Description**          |
| --------------------------------- | ------------------------ |
| `gh auth login`                   | Authenticate with GitHub |
| `gh repo create`                  | Create a new GitHub repo |
| `gh repo clone <user>/<repo>`     | Clone a GitHub repo      |
| `gh pr create`                    | Create a pull request    |
| `gh issue list`                   | List GitHub issues       |
| `gh release create <tag> <files>` | Create a new release     |

> 💡 `gh` is the GitHub CLI — [install it from here](https://cli.github.com/)

---

## 🔐 Git Security & Auth

| **Concept**                     | **Tool / Use**                                                |
| ------------------------------- | ------------------------------------------------------------- |
| **SSH Key**                     | Secure Git authentication                                     |
| **Personal Access Token (PAT)** | Used to push/pull via HTTPS after GitHub password deprecation |
| **GPG Signing**                 | Verify commit authorship                                      |
| `.gitignore`                    | Prevent sensitive/irrelevant files from being committed       |

---

## 📝 Bonus: Helpful Flags

| **Flag**          | **Use**                        |
| ----------------- | ------------------------------ |
| `--amend`         | Modify the last commit         |
| `--no-ff`         | Force merge commit creation    |
| `--squash`        | Combine commits before merging |
| `--force` or `-f` | Force push or overwrite        |
| `--set-upstream`  | Link local and remote branches |

---


