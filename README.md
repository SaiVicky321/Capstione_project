# Capstone Project - Guvi Final Project

**Author:** Sai Vignesh R  
**Version:** 1.0  
**Description:** This document outlines the process to achieve React application deployment on an AWS EC2 instance using Git (Source Control Management), Jenkins (CI/CD), Docker (Containerization), and Monitoring via AWS CloudWatch with notifications through AWS SNS.

---

## Table of Contents

- [Introduction](#introduction)
- [Step 1: Clone the Repo and Create Docker Configuration](#step-1-clone-the-repo-and-create-docker-configuration)
- [Step 2: Create Bash Scripts](#step-2-create-bash-scripts)
- [Step 3: Push the Local Git Repo to GitHub](#step-3-push-the-local-git-repo-to-github)
- [Step 4: Docker Hub Repositories](#step-4-docker-hub-repositories)
- [Step 5: Jenkins CI/CD Pipeline](#step-5-jenkins-cicd-pipeline)
- [Step 6: AWS EC2 Deployment](#step-6-aws-ec2-deployment)
- [Step 7: Monitoring with AWS CloudWatch & SNS](#step-7-monitoring-with-aws-cloudwatch--sns)
- [Conclusion](#conclusion)

---

## Introduction

This project is a demonstration of a full DevOps pipeline to deploy a React application on AWS EC2 using several tools and services. The pipeline includes the following stages:

- **Git** for source control management (SCM)
- **Jenkins** for continuous integration and continuous delivery (CI/CD)
- **Docker** for containerizing the application
- **AWS EC2** for hosting the application
- **AWS CloudWatch** and **SNS** for monitoring and notifications

---

## Step 1: Clone the Repo and Create Docker Configuration

1. Clone the repository from the provided GitHub URL.
2. Create the necessary `Dockerfile` and `docker-compose.yaml` files to containerize the React app.

### Dockerfile
```dockerfile
FROM nginx
COPY build/ /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
###docker-compose.yaml
```yaml
version: '3.8'

services:
  react-app:
    image: saivicky321/react-app:latest
    ports:
      - "80:80"
```
