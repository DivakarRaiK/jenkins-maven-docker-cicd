# Jenkins Maven Docker CI/CD Pipeline

![CI/CD](https://img.shields.io/badge/CI%2FCD-Jenkins-blue?logo=jenkins)
![Maven](https://img.shields.io/badge/Build-Maven-red?logo=apachemaven)
![Docker](https://img.shields.io/badge/Container-Docker-blue?logo=docker)
![DockerHub](https://img.shields.io/badge/Registry-DockerHub-blue?logo=docker)

A complete CI/CD pipeline that automates the build, test, containerization, and deployment of a Java Maven web application using Jenkins and Docker.

---

## 📋 Project Overview

This project demonstrates an end-to-end DevOps pipeline that:
- Pulls source code from GitHub
- Compiles and packages a Java Maven web application
- Builds a Docker image based on Tomcat
- Runs the containerized application
- Pushes the Docker image to DockerHub

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Jenkins | CI/CD Orchestration |
| Maven | Build & Dependency Management |
| Docker | Containerization |
| Tomcat | Application Server (base image) |
| GitHub | Source Code Management |
| DockerHub | Container Image Registry |

---

## 🏗️ Pipeline Architecture

```
GitHub Repo
    │
    ▼
┌─────────────────┐
│  Git Checkout   │  ← Pull source code from master branch
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Code Compile   │  ← mvn compile
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Code Package   │  ← mvn clean install (generates webapp.war)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Build & Tag     │  ← docker build -t divakarraik/project-2 .
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Containerise    │  ← docker run -p 9008:8080
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ DockerHub Login │  ← Secure credentials via Jenkins
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Push to Hub    │  ← docker push divakarraik/project-2
└─────────────────┘
```

---

## 📁 Project Structure

```
jenkins-maven-docker-cicd/
├── Jenkinsfile              # Declarative Jenkins pipeline script
├── Dockerfile               # Docker image definition
├── DockerTagupdate.sh       # Docker tag update helper script
├── pom.xml                  # Maven parent POM
├── server/                  # Server module (JAR)
│   └── src/
└── webapp/                  # Web application module (WAR)
    └── src/
```

---

## ⚙️ Pipeline Stages

### 1. Git Checkout
Pulls the latest code from the `master` branch of the web application repository.

### 2. Code Compile
Runs `mvn compile` — compiles all Java source files across the multi-module Maven project (Maven Project → Server → Webapp).

### 3. Code Package
Runs `mvn clean install`:
- Runs unit tests — **2 passed, 0 failures**
- Packages server module as `server.jar`
- Packages webapp module as `webapp.war`

### 4. Build & Tag Docker Image
Builds a Docker image using the Dockerfile:
```dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY webapp/target/webapp.war /usr/local/tomcat/webapps
```

### 5. Containerisation
Runs the Docker container exposing port `9008`:
```bash
docker run -it -d --name c9 -p 9008:8080 divakarraik/project-2
```

### 6. DockerHub Login
Authenticates with DockerHub using Jenkins credentials stored securely as `docker-hub-credentials`.

### 7. Push to DockerHub
Pushes the image to DockerHub:
```
docker.io/divakarraik/project-2:latest
```

---

## ✅ Build Result

```
[INFO] Reactor Summary for Maven Project 1.0-SNAPSHOT:
[INFO] Maven Project ......  SUCCESS [ 0.335 s]
[INFO] Server .............. SUCCESS [ 1.697 s]
[INFO] Webapp .............. SUCCESS [ 0.714 s]
[INFO] BUILD SUCCESS

Finished: SUCCESS
```

Tests run: **2** | Failures: **0** | Errors: **0**

---

## 🚀 How to Run

### Prerequisites
- Jenkins with Git, Maven Integration, and Docker Pipeline plugins
- Jenkins tools configured: JDK (`java-11`), Maven (`maven`)
- DockerHub credentials added in Jenkins as `docker-hub-credentials`

### Steps

1. Create a new **Jenkins Pipeline** job
2. Set pipeline definition to **Pipeline script from SCM** → Git → this repo URL
3. Click **Build Now**
4. Access the app at `http://<your-server-ip>:9008`

---

## 🐳 Pull from DockerHub

```bash
docker pull divakarraik/project-2
docker run -d -p 9008:8080 divakarraik/project-2
```

---

## 👤 Author

**Divakar Rai K**
- GitHub: [@DivakarRaiK](https://github.com/DivakarRaiK)
- DockerHub: [divakarraik](https://hub.docker.com/u/divakarraik)
