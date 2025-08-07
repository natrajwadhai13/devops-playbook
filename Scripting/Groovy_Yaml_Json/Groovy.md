---
title: "Groovy"
parent: "• Groovy_Yaml_Json_Roadmap"
grand_parent: "Scripting"
nav_order: 3
---

Here is a **Groovy Roadmap for DevOps Engineers**, tailored to your background (Linux, Jenkins, Git, Docker, AWS, Ansible, etc.). Groovy is essential for mastering **Jenkins pipelines**, especially when building complex CI/CD automation.

---

## 🧭 **Groovy Roadmap for DevOps Engineers (2–3 Weeks)**

📘 **Goal**: Understand Groovy well enough to write and debug **Jenkins scripted and declarative pipelines**, automate DevOps tasks, and work with dynamic scripting inside Jenkinsfiles.

---

## 🔰 Week 1: Groovy Basics

### 🎯 Outcomes:

* Understand Groovy syntax
* Write simple scripts and functions

---

### ✅ Topics to Learn:

* Variables & Data Types (`def`, `int`, `String`, `List`, `Map`)
* Conditionals: `if`, `else`
* Loops: `for`, `each`, `while`
* Functions (`def myFunc()`)
* String interpolation (`"Hello, $name"`)
* Closures (very common in Jenkins)

---

### 🧪 Practice Examples:

```groovy
def name = "Natraj"
def skills = ["Git", "Jenkins", "Docker"]

skills.each { skill ->
  println("Working with $skill")
}

def greet(name) {
  return "Hello, $name!"
}
println(greet("DevOps"))
```

---

## 🔧 Tools:

* Run Groovy in:

  * [https://groovyconsole.appspot.com](https://groovyconsole.appspot.com)
  * Jenkins "Script Console" (for testing inside Jenkins)
  * Local machine with `groovy` installed

---

## ⚙️ Week 2: Jenkins + Groovy (Declarative & Scripted Pipeline)

### 🎯 Outcomes:

* Write Groovy-based Jenkinsfiles
* Use shared libraries and custom functions

---

### ✅ Declarative Pipeline Example:

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Building...'
      }
    }
    stage('Test') {
      steps {
        echo 'Testing...'
      }
    }
  }
}
```

---

### ✅ Scripted Pipeline Example:

```groovy
node {
  stage('Build') {
    echo "Building app"
  }
  stage('Deploy') {
    def envList = ["dev", "qa", "prod"]
    envList.each {
      echo "Deploying to $it"
    }
  }
}
```

---

### ✅ Learn:

* `node`, `stage`, `steps`, `script`, `parallel`
* Defining and calling functions
* Using `params`, `environment` variables
* Handling user input (`input` step)

---

## 🧰 Week 3: Advanced Groovy + Jenkins Use Cases

### 🎯 Outcomes:

* Build real pipelines using loops, maps, conditionals
* Debug Jenkinsfile errors confidently

---

### ✅ Advanced Topics:

* Shared Libraries (create your own)
* DSL in Jenkins
* Parallel execution
* Try/catch for error handling
* Using JSON/YAML in Groovy

---

### 🛠️ Example: Dynamic Stage Creation

```groovy
def stages = ['Build', 'Test', 'Deploy']

pipeline {
  agent any
  stages {
    stages.each { stageName ->
      stage(stageName) {
        steps {
          echo "Running $stageName stage"
        }
      }
    }
  }
}
```

---

## 📘 Groovy vs Bash vs Python (for DevOps)

| Use Case          | Groovy | Bash   | Python        |
| ----------------- | ------ | ------ | ------------- |
| Jenkins pipelines | ✅ Best | ❌ Poor | ⚠️ Not native |
| Linux scripting   | ⚠️ Ok  | ✅ Best | ✅ Good        |
| APIs/JSON         | ✅ Good | ❌ Poor | ✅ Best        |

---

## 🧠 Best Practices

* Use `echo` for debugging pipelines
* Modularize pipeline logic with shared libs
* Validate Groovy using Jenkins Script Console
* Use `@Library` for shared pipelines in large teams

---

## 🧪 Mini Project Ideas:

1. Create Jenkins pipeline with dynamic stages from a list
2. Automate Docker build + push using Groovy pipeline
3. Trigger EC2 snapshot backup using Groovy + AWS CLI

---

Would you like:

* Sample **Jenkinsfiles** (scripted/declarative)?
* A **Groovy cheat sheet PDF**?
* Guidance on setting up a **shared library** in Jenkins?

