---
title: "• GitLab"
parent: "• DevOps Interview"
grand_parent: "1. DevOps"
nav_order: 1
---

Superb ask 👍 Natraj! Since you’re preparing for **DevOps interviews**, here’s a **comprehensive list of GitLab interview questions (basic → advanced)**, grouped by topics.

---

# 🔹 **GitLab Interview Questions & Answers**

## 1. Basics of GitLab

1. What is GitLab and how is it different from GitHub and Bitbucket?
2. Explain GitLab CE vs GitLab EE.
3. What are GitLab Runners? Types of runners?
4. How does GitLab CI/CD work?
5. What is `.gitlab-ci.yml` file and where is it stored?
6. Explain stages, jobs, and pipelines in GitLab.
7. What is the difference between `cache` and `artifacts` in GitLab CI?
8. How do you restrict a job to run only on a particular branch?

---

## 2. GitLab CI/CD

9. Explain the workflow of a GitLab CI/CD pipeline.
10. How do you define environment variables (secrets) in GitLab?
11. What is the difference between protected and masked variables?
12. How do you handle secrets in GitLab pipelines securely?
13. Explain GitLab environments (staging, production).
14. How do you set up deployment rules based on branch or tag?
15. What is `before_script`, `script`, and `after_script` in GitLab CI?
16. How do you use `only`, `except`, and `rules` keywords in GitLab pipelines?
17. What is the difference between manual and scheduled pipelines?
18. How do you trigger one pipeline from another project?
19. What is the purpose of `needs` keyword in GitLab?
20. How do you implement conditional execution in GitLab CI/CD?

---

## 3. GitLab Runners

21. What are GitLab Runners?
22. Difference between Shared Runner and Specific Runner.
23. How do you install and register a GitLab Runner?
24. What executors are supported by GitLab Runner (e.g., Shell, Docker, Kubernetes)?
25. How do you configure Docker executor in GitLab Runner?
26. What is the difference between `tags` and `runner tags`?
27. How do you restrict certain jobs to run only on specific runners?

---

## 4. Advanced GitLab CI/CD

28. How do you integrate GitLab with Kubernetes (Auto DevOps)?
29. Explain how GitLab supports GitOps with ArgoCD/Flux.
30. How do you use GitLab with Helm charts?
31. How do you integrate GitLab CI/CD with AWS/GCP/Azure?
32. How do you implement blue/green or canary deployments in GitLab?
33. How do you configure multi-project pipelines in GitLab?
34. Explain parent-child pipelines in GitLab.
35. How do you handle monorepos in GitLab CI/CD?

---

## 5. GitLab Security & Compliance

36. What is GitLab Secure stage?
37. How does GitLab handle SAST, DAST, Dependency Scanning?
38. How do you enable code quality scans in GitLab?
39. Explain GitLab Container Scanning.
40. How do you integrate GitLab with HashiCorp Vault / AWS Secrets Manager?
41. How do you ensure compliance in GitLab pipelines?

---

## 6. GitLab Administration

42. How do you set up GitLab self-hosted?
43. What are GitLab backup & restore steps?
44. How do you scale GitLab runners?
45. How do you troubleshoot stuck pipelines in GitLab?
46. What is GitLab Registry? How to use it?
47. How do you upgrade GitLab version?
48. What monitoring/logging options does GitLab provide?

---

## 7. Scenario-Based Questions

49. You pushed a broken `.gitlab-ci.yml` and pipeline fails – how do you fix it?
50. A secret accidentally got exposed in GitLab logs – what will you do?
51. How do you optimize a slow GitLab pipeline?
52. Your job needs a dependency from another job – how will you pass it?
53. How do you configure GitLab to deploy to multiple environments (dev, QA, prod)?
54. You want to deploy only when code is merged into `main` branch – how will you configure it?
55. You have multiple microservices in one repo – how will you build pipelines efficiently?
56. GitLab runner keeps failing with “executor not found” – how do you debug?
57. Your deployment pipeline must run only on tags – how do you implement it?
58. How to integrate Jira or Slack with GitLab CI/CD?

---

# 🔹 **Pro Tips for Interview**

* Always explain with **real-world example** (e.g., "In my project at UBS, we used GitLab CI/CD with Docker executor runners to build and deploy Node.js and React containers to AWS EC2").
* If asked about **GitOps**, highlight GitLab’s ability to work with **ArgoCD**.
* For **security questions**, mention SAST, DAST, dependency scanning.
* For **secrets**, emphasize “masked + protected variables” & integration with secret managers.

---

######################################

 **compact list of the 50 most frequently asked GitLab interview Q\&A** 
---

# 🚀 GitLab Interview Q\&A (50 Quick Notes)

## 🔹 Basics

1. **Q: What is GitLab?**
   A: GitLab is a complete DevOps platform providing SCM, CI/CD, security, and project management in one application.
   👉 GitLab is a DevOps lifecycle platform that provides Git repository management, CI/CD pipelines, issue tracking, and monitoring in one application.

2. **Q: Difference between GitHub, GitLab, and Bitbucket?**
   A: GitHub → open-source focus, GitLab → all-in-one DevSecOps, Bitbucket → Jira integration.
   🔹GitHub mainly focuses on Git repo hosting and collaboration.
   🔹Bitbucket is Atlassian’s Git-based tool with Jira integration.
   🔹GitLab is unique because it has built-in CI/CD and DevOps features.

3. **Q: GitLab CE vs EE?**
   A: CE is free/open-source, EE is paid with enterprise features (security, compliance, support).

4. **Q: What is `.gitlab-ci.yml` and  why is it important??**
   A: A YAML file in repo root that defines GitLab CI/CD pipeline.
   
   .gitlab-ci.yml is a configuration file in the repo root. It defines jobs, stages, and pipeline behavior (build, test, deploy). Without this file, GitLab CI/CD pipelines cannot run.

5. **Q: What are GitLab stages?**
   A: Logical pipeline steps like `build`, `test`, `deploy`.

6. **Explain the role of GitLab CI/CD pipelines.**
  Ans- GitLab CI/CD automates build, test, and deployment. Pipelines ensure every code change is validated before merging. It reduces manual work and increases delivery speed.



---

## 🔹 Pipelines & Jobs

6. **Q: What is a GitLab pipeline?**
   A: A sequence of jobs defined in `.gitlab-ci.yml` executed in stages.

7. **Q: Difference between job and stage?**
   A: Jobs are tasks, stages group jobs. Jobs in a stage run in parallel; stages run sequentially.

8. **Q: What are `before_script` and `after_script`?**
   A: Commands executed before/after each job.

9. **Q: What is `cache` vs `artifacts`?**
   A: Cache speeds builds (reused), artifacts are outputs shared between jobs.

10. **Q: How to run a job only on main branch?**
    A: Use `only: [main]` in job config.

---

## 🔹 Variables & Secrets

11. **Q: How do you store secrets in GitLab?**
    A: Via **CI/CD variables** in GitLab settings.

12. **Q: Difference between protected & masked variables?**
    A: Protected → only on protected branches/tags, Masked → hidden in logs.

13. **Q: How to use variables in pipeline?**
    A: `$VAR_NAME` inside `.gitlab-ci.yml`.

14. **Q: How to scope secrets for different environments?**
    A: Use **Environment scope** when defining variables.

15. **Q: How to inject secrets from Vault?**
    A: Integrate GitLab CI/CD with HashiCorp Vault/AWS Secrets Manager.

---

## 🔹 GitLab Runners

16. **Q: What is a GitLab Runner?**
    A: An agent that executes jobs in a pipeline.

17. **Q: Types of Runners?**
    A: Shared (all projects) & Specific (one project/group).

18. **Q: Executors supported by GitLab Runner?**
    A: Shell, Docker, Kubernetes, SSH, VirtualBox, etc.

19. **Q: How to restrict job to a specific runner?**
    A: Use **tags** in `.gitlab-ci.yml`.

20. **Q: How do you install GitLab Runner?**
    A: `sudo apt install gitlab-runner` → `gitlab-runner register`.

---

## 🔹 Advanced Pipelines

21. **Q: What is `needs` in GitLab CI?**
    A: Defines job dependencies, enabling parallel execution.

22. **Q: What are manual jobs?**
    A: Jobs triggered manually using `when: manual`.

23. **Q: What are scheduled pipelines?**
    A: Pipelines triggered on schedule (cron-like).

24. **Q: How do you run pipelines on tags?**
    A: Use `only: [tags]`.

25. **Q: What are child pipelines?**
    A: Pipelines triggered within another pipeline for modularization.

26. **Q: What are multi-project pipelines?**
    A: Triggering pipelines across projects for microservices.

27. **Q: How do you skip a pipeline on commit?**
    A: Add `[ci skip]` in commit message.

28. **Q: How to retry a failed job?**
    A: From GitLab UI → "Retry" button.

29. **Q: How to cancel a running pipeline?**
    A: From GitLab UI or API.

30. **Q: How do you debug failing pipelines?**
    A: Use `CI_DEBUG_TRACE=true` in variables.

---

## 🔹 Deployment & GitOps

31. **Q: What are GitLab Environments?**
    A: Logical deploy targets (staging, production) with URLs.

32. **Q: What is GitLab Auto DevOps?**
    A: Predefined CI/CD templates that auto-detect app and deploy via Kubernetes.

33. **Q: How do you deploy using GitLab CI/CD?**
    A: Add deployment scripts in `.gitlab-ci.yml` under `deploy` stage.

34. **Q: How do you integrate GitLab with Kubernetes?**
    A: Connect K8s cluster via GitLab → Operations → Kubernetes → Deploy with CI/CD.

35. **Q: How does GitLab support GitOps?**
    A: By storing infra as code in Git and syncing via ArgoCD/Flux.

---

## 🔹 Security

36. **Q: What is GitLab SAST?**
    A: Static Application Security Testing (checks code vulnerabilities).

37. **Q: What is DAST?**
    A: Dynamic Application Security Testing (scans running app).

38. **Q: What is dependency scanning?**
    A: Finds vulnerable libraries in dependencies.

39. **Q: What is container scanning?**
    A: Scans Docker images for vulnerabilities.

40. **Q: How do you secure secrets in GitLab CI/CD?**
    A: Use masked + protected variables, external secret managers.

---

## 🔹 Admin & Maintenance

41. **Q: How to backup GitLab?**
    A: `gitlab-backup create` command (for self-hosted GitLab).

42. **Q: How to restore GitLab from backup?**
    A: `gitlab-backup restore`.

43. **Q: How to upgrade GitLab?**
    A: Follow GitLab upgrade documentation, often via Omnibus packages.

44. **Q: What is GitLab Registry?**
    A: Built-in container registry to store Docker images.

45. **Q: How to monitor GitLab?**
    A: Use GitLab Monitoring (Prometheus, Grafana integration).

---

## 🔹 Scenario-Based

46. **Q: Your pipeline is very slow – how do you optimize?**
    A: Use caching, parallel jobs, `needs`, and optimized Docker images.

47. **Q: A secret leaked in logs – what do you do?**
    A: Revoke secret immediately, rotate keys, update masked variable.

48. **Q: How to deploy microservices with GitLab?**
    A: Use multi-project pipelines + Kubernetes.

49. **Q: How do you roll back deployment in GitLab?**
    A: Use GitOps (revert commit) or keep rollback job in pipeline.

50. **Q: How do you integrate GitLab with Jira/Slack?**
    A: Via GitLab integrations in project settings.

---


