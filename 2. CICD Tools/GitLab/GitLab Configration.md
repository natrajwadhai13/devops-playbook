---
title: • GitLab Pipline
parent: • GitLab
grand_parent: 2. CICD Tools
nav_order: 3
has_children: true
---

 In **GitLab CI/CD**, sensitive values (passwords, API keys, AWS credentials, database passwords, etc.) must **never** be hardcoded in `.gitlab-ci.yml`. Instead, we use **GitLab CI/CD Variables (Secrets)**.

Here’s a **step-by-step guide**:

---

# 🔹 1. Adding Secrets in GitLab

### Method 1: GitLab UI (most common)

1. Go to your **project in GitLab**.
2. Navigate to → **Settings → CI/CD → Variables → Expand**.
3. Click **Add variable**.
4. Fill details:

   * **Key**: `DB_PASSWORD`
   * **Value**: `mySuperSecretPassword`
   * **Protect** → Only available in protected branches (like `main`).
   * **Masked** → Hides in job logs.
   * **Environment scope** → Limit to `production` or `staging`.

👉 Example:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `DB_HOST`
* `DB_USER`
* `DB_PASSWORD`

---

### Method 2: GitLab CLI / API

If you want automation, you can use GitLab’s API to add secrets:

```bash
curl --request POST "https://gitlab.com/api/v4/projects/PROJECT_ID/variables" \
     --header "PRIVATE-TOKEN: <your_token>" \
     --form "key=DB_PASSWORD" \
     --form "value=mySuperSecretPassword"
```

---

# 🔹 2. Using Secrets in `.gitlab-ci.yml`

Secrets stored in GitLab become **environment variables** inside the pipeline.

```yaml
deploy_job:
  stage: deploy
  script:
    - echo "Deploying with DB password..."
    - echo $DB_PASSWORD   # (not recommended to echo secrets, just for example)
    - npm run deploy
  only:
    - main
```

✅ GitLab automatically injects `DB_PASSWORD` from the GitLab settings.

---

# 🔹 3. Other Configuration Steps for GitLab Pipelines

### 1. **Runners**

* GitLab CI jobs run on **Runners**.
* Types:

  * **Shared runners** → provided by GitLab.
  * **Specific runners** → you install on your server/VM/EC2.

👉 Example: Install a GitLab runner on Ubuntu:

```bash
sudo apt-get install gitlab-runner
gitlab-runner register
```

Then link to your project with the GitLab token.

---

### 2. **Caching Dependencies**

Use **cache** to speed up builds (e.g., `npm`, `pip`, `maven`):

```yaml
cache:
  paths:
    - node_modules/
```

---

### 3. **Artifacts**

Artifacts pass build results between stages:

```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 day
```

---

### 4. **Branch/Tag Rules**

Control which pipelines run on which branch:

```yaml
only:
  - main
except:
  - develop
```

---

### 5. **Environment Configurations**

GitLab allows defining **environments** (staging, prod).

```yaml
deploy_prod:
  stage: deploy
  environment:
    name: production
    url: https://app.example.com
  script:
    - ./deploy.sh
  only:
    - main
```

---

# 🔹 4. Security Best Practices

* ✅ Always use **masked + protected** secrets.
* ✅ Never hardcode passwords/API keys in `.gitlab-ci.yml`.
* ✅ Use **separate secrets for staging/prod** with environment scope.
* ✅ Rotate secrets regularly (especially cloud keys).
* ✅ Use **HashiCorp Vault** or AWS Secrets Manager with GitLab if enterprise setup.

---

