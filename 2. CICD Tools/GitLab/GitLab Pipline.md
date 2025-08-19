---
title: • GitLab Pipline
parent: • GitLab
grand_parent: 2. CICD Tools
nav_order: 2
has_children: true
---

**sample GitLab CI/CD script** (`.gitlab-ci.yml`) with explanation.

Here’s a **basic working example** for a Node.js app (you can adapt for Java, Python, etc.):

---

# 📄 `.gitlab-ci.yml` (Sample Script)

```yaml
stages:
  - build
  - test
  - deploy

variables:
  NODE_ENV: "development"

before_script:
  - echo "Starting Pipeline..."
  - node -v
  - npm -v

build_job:
  stage: build
  script:
    - echo "Installing dependencies..."
    - npm install
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour

test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  needs:
    - build_job

deploy_job:
  stage: deploy
  script:
    - echo "Deploying application..."
    - npm run build
    - echo "Application deployed successfully!"
  only:
    - main   # run deploy only on main branch
  needs:
    - test_job
```

---

# 🔎 Explanation of Each Section

### 1. **stages**

```yaml
stages:
  - build
  - test
  - deploy
```

* Defines pipeline flow.
* Jobs are grouped into these stages, executed **in order**.

---

### 2. **variables**

```yaml
variables:
  NODE_ENV: "development"
```

* Global environment variables for jobs.
* Example: `NODE_ENV` helps in different configs for dev, test, prod.

---

### 3. **before\_script**

```yaml
before_script:
  - echo "Starting Pipeline..."
  - node -v
  - npm -v
```

* Runs **before each job**.
* Good for logging, checking versions, setting env.

---

### 4. **Jobs**

Each job has:

* **name** (`build_job`, `test_job`, `deploy_job`)
* **stage** → determines when it runs.
* **script** → commands to execute.

---

#### ✅ Build Job

```yaml
build_job:
  stage: build
  script:
    - echo "Installing dependencies..."
    - npm install
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour
```

* Installs dependencies.
* Saves `node_modules` as **artifacts** so next jobs can reuse.

---

#### ✅ Test Job

```yaml
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  needs:
    - build_job
```

* Runs unit tests.
* **needs** ensures it waits for `build_job` results.

---

#### ✅ Deploy Job

```yaml
deploy_job:
  stage: deploy
  script:
    - echo "Deploying application..."
    - npm run build
    - echo "Application deployed successfully!"
  only:
    - main
  needs:
    - test_job
```

* Builds and deploys the app.
* Runs **only on `main` branch**.
* Depends on successful `test_job`.

---

# 🚀 Workflow

1. **Developer pushes code → GitLab triggers pipeline.**
2. **Build stage** → Installs dependencies.
3. **Test stage** → Runs automated tests.
4. **Deploy stage** → Deploys if on `main`.

---