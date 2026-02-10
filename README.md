# docker-pipeline
docker-pipeline task

# Docker Pipeline Project 🚀

## 📌 Project Overview

This project demonstrates a **simple Docker CI/CD pipeline using Jenkins**.  
It builds a Docker image for a static web application using **NGINX**, then **pushes the image to Docker Hub** automatically using a Jenkins pipeline.

The goal of this project is to understand:
- Docker image creation using a Dockerfile
- Jenkins pipeline automation
- Secure Docker Hub authentication using Jenkins credentials
- End-to-end Docker build & push workflow

---

## 📁 Repository Structure

```

docker-pipeline/
├── Dockerfile
├── Jenkinsfile
├── index.html
└── README.md

````

---

## 🧱 Application Description

- The application is a **static HTML web page**
- Served using **NGINX**
- Dockerized using a lightweight `nginx:alpine` base image
- Automated build & push via Jenkins pipeline

---

## 🐳 Dockerfile

The Dockerfile builds a custom NGINX image and copies the static HTML file into the web root.

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
````

### Explanation

* `nginx:alpine` → Lightweight NGINX base image
* `COPY` → Copies `index.html` into the default NGINX web directory
* NGINX serves the page on port **80** by default

---

## ⚙️ Jenkins Pipeline (CI/CD)

The Jenkins pipeline performs the following steps:

1. Build Docker image
2. Login to Docker Hub securely
3. Push Docker image to Docker Hub
4. Logout after completion

---

## 📄 Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shahbazz16/docker-pipeline"
        DOCKER_TAG   = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
        success {
            echo 'Docker image built and pushed successfully 🎉'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
```

---

## 🔐 Jenkins Credentials Setup

In Jenkins:

**Manage Jenkins → Credentials → Global → Add Credentials**

| Field    | Value                               |
| -------- | ----------------------------------- |
| Kind     | Username with password              |
| ID       | dockerhub-credentials               |
| Username | shahbazz16                          |
| Password | Docker Hub password or access token |

---

## ▶️ Run Locally (Optional)

### Build Docker Image

```bash
docker build -t docker-pipeline .
```

### Run Docker Container

```bash
docker run -d -p 8080:80 docker-pipeline
```

### Access Application

```
http://localhost:8080
```

---

## 🐙 Docker Hub Image

After a successful Jenkins pipeline run, the Docker image is available on Docker Hub:

```
https://hub.docker.com/r/shahbazz16/docker-pipeline
```

Image Tag:

```
latest
```

---

## ✅ Pipeline Workflow Summary

```
index.html
   ↓
Dockerfile
   ↓
Jenkins Pipeline
   ↓
Docker Build
   ↓
Docker Login
   ↓
Docker Push → Docker Hub
```

---

## 🧠 Key Learnings

* Docker image creation with NGINX
* Jenkins declarative pipelines
* Secure credential management
* Automated Docker image publishing
* CI/CD best practices

---

## 👨‍💻 Author

**Shaikh Shahbaz**
DevOps | Docker | Jenkins | AWS


