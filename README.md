# AWS Jenkins CI/CD Pipeline

> **FR** — Pipeline CI/CD complet avec Jenkins : incrémentation de version automatique, build Maven, image Docker, déploiement SSH sur EC2, et commit automatique de la version.
>
> **EN** — Full CI/CD pipeline with Jenkins: automatic version increment, Maven build, Docker image build & push, SSH deployment to EC2, and automatic version commit.

---

## Stack

![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-red?logo=jenkins)
![AWS](https://img.shields.io/badge/AWS-EC2-orange?logo=amazonaws)
![Docker](https://img.shields.io/badge/Docker-Hub-blue?logo=docker)
![Java](https://img.shields.io/badge/Java-17-blue?logo=openjdk)
![Maven](https://img.shields.io/badge/Maven-build-red?logo=apachemaven)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3-green?logo=springboot)

---

## FR — Description

Ce projet met en place un pipeline Jenkins complet pour une application Java Spring Boot. Le pipeline automatise l'ensemble du cycle de livraison, de l'incrémentation de version au déploiement sur une instance EC2.

**Étapes du pipeline :**
1. **Increment version** — Incrémente automatiquement la version patch dans le `pom.xml` via le plugin `build-helper:parse-version`
2. **Build app** — Compile et package le JAR via la librairie partagée Jenkins (`buildJar()`)
3. **Build image** — Construit l'image Docker, se connecte à Docker Hub et la pousse (`buildImage()`, `dockerLogin()`, `dockerPush()`)
4. **Deploy** — Copie les fichiers de déploiement sur EC2 via SCP et exécute le `docker-compose` via SSH
5. **Commit version update** — Commit et push la mise à jour de version dans le repo Git

## EN — Description

This project sets up a full Jenkins pipeline for a Java Spring Boot application. The pipeline automates the entire delivery cycle, from version incrementing to deployment on an EC2 instance.

**Pipeline stages:**
1. **Increment version** — Automatically increments the patch version in `pom.xml` using the `build-helper:parse-version` plugin
2. **Build app** — Compiles and packages the JAR via Jenkins Shared Library (`buildJar()`)
3. **Build image** — Builds the Docker image, logs in to Docker Hub and pushes it (`buildImage()`, `dockerLogin()`, `dockerPush()`)
4. **Deploy** — Copies deployment files to EC2 via SCP and runs `docker-compose` via SSH
5. **Commit version update** — Commits and pushes the version update back to the Git repository

---

## Architecture

```
Git Push
   │
   ▼
Jenkins Pipeline
   ├── 1. Increment version (pom.xml patch++)
   ├── 2. mvn clean package → app.jar
   ├── 3. docker build → Docker Hub (mb938/demo-app:<version>-<build>)
   ├── 4. SCP + SSH → EC2 Instance
   │         └── docker-compose up (Spring Boot + PostgreSQL)
   └── 5. git commit + push "ci: version bump"
```

---

## FR — Prérequis Jenkins

Les credentials suivants doivent être configurés dans Jenkins :

| ID | Type | Usage |
|---|---|---|
| `git-credentials` | Username/Password | Push vers GitHub |
| `ec2-server-key` | SSH key | Connexion à l'instance EC2 |
| `dockerhub-credentials` | Username/Password | Push vers Docker Hub |

La Jenkins Shared Library `jenkins-groovy-shared-library` doit également être configurée (voir [jenkins-groovy-shared-library](https://github.com/m-bengueddache/jenkins-groovy-shared-library)).

## EN — Jenkins Prerequisites

The following credentials must be configured in Jenkins:

| ID | Type | Usage |
|---|---|---|
| `git-credentials` | Username/Password | Push to GitHub |
| `ec2-server-key` | SSH key | Connect to EC2 instance |
| `dockerhub-credentials` | Username/Password | Push to Docker Hub |

The Jenkins Shared Library `jenkins-groovy-shared-library` must also be configured (see [jenkins-groovy-shared-library](https://github.com/m-bengueddache/jenkins-groovy-shared-library)).

---

## Project Structure

```
.
├── Jenkinsfile             # Full CI/CD pipeline definition
├── Dockerfile              # Java app image (Amazon Corretto 17)
├── docker-compose.yaml     # Spring Boot + PostgreSQL deployment
├── server-cmds.sh          # Deployment script executed on EC2
├── pom.xml                 # Maven project (Spring Boot 3)
└── src/                    # Java Spring Boot application source
```
