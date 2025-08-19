---
title: • Jenkins
parent: 2. CICD Tools
nav_order: 2
has_children: true
---

Here’s a **DevOps-ready `Jenkinsfile`** example that you can use to build CI/CD pipelines for **Docker-based applications**. This sample works well for projects using **GitHub**, **Docker**, **Node.js/Java**, and even **Ansible or shell scripts**.

---

## ✅ Sample `Jenkinsfile` (Declarative Pipeline)

```groovy
pipeline {
    agent any

    environment {
        REGISTRY = "docker.io/natrajwadhai"   // Replace with your Docker Hub or private registry
        IMAGE_NAME = "myapp"
        TAG = "${env.BUILD_NUMBER}"
    }

    options {
        timestamps()
        ansiColor('xterm')
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-repo/app.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "🛠️ Building the application..."
                sh 'docker build -t $REGISTRY/$IMAGE_NAME:$TAG .'
            }
        }

        stage('Test') {
            steps {
                echo "✅ Running unit tests..."
                sh './scripts/run_tests.sh'  // Or npm test / mvn test etc.
            }
        }

        stage('Push to Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $REGISTRY/$IMAGE_NAME:$TAG
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying to server..."
                sh 'ansible-playbook deploy.yml -i inventory/production.ini'
            }
        }
    }

    post {
        success {
            echo "✅ Build #${env.BUILD_NUMBER} completed successfully!"
        }
        failure {
            echo "❌ Build failed. Check console logs."
        }
    }
}
```

---

## 🧰 Key Components

| Section            | Purpose                               |
| ------------------ | ------------------------------------- |
| `agent any`        | Run on any available Jenkins node     |
| `environment`      | Define reusable environment variables |
| `git`              | Pull code from GitHub                 |
| `docker build`     | Build Docker image                    |
| `docker push`      | Push to Docker Hub                    |
| `ansible-playbook` | Trigger deployment                    |
| `post`             | Success/failure notification block    |

---

## 🧪 Required Jenkins Setup

* **Docker** installed on the Jenkins agent
* **Git** access to your repo
* **Credentials** ID `docker-hub-creds` with Docker Hub username/password
* Optional: **Ansible** installed to run playbooks
* `scripts/run_tests.sh` or equivalent should exist

---

## 🔧 Customize For You


* A Jenkinsfile with **multi-branch support**?
* A **Node.js, Java, or Python-specific pipeline**?
* Deployment to **AWS EC2 / EKS / Lambda** or **Azure**?


