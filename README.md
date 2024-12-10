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

### docker-compose.yaml
```yaml
version: '3.8'
services:
  react-app:
    image: saivicky321/react-app:latest
    ports:
      - "80:80"
```

## Step 2: Create Bash Scripts

Create two bash scripts to automate the Docker build and deployment process.

* build.sh: A script to build the Docker images.
    ```bash
    #!/bin/bash
    sudo docker build -t saivicky321/react-app .
    echo "Build Completed"
    ```
* deploy.sh: A script to deploy the Docker images to the server.
    ```bash
    #!/bin/bash
    sudo docker-compose up -d
    echo "deployed Successfully"
    ```

## Step 3: Push the Local Git Repo to GitHub
1. Create an empty repository in GitHub.
2. Push your local repository to GitHub, using the branch dev.
    ```bash
   git push origin dev
    ```

## Step 4: Docker Hub Repositories
1. Create two repositories on Docker Hub:
    * dev:Public repository for development images.
    * prod: Private repository for production images.

2. Push Docker images to these repositories during the CI/CD process.

## Step 5: Jenkins CI/CD Pipeline

1. Pre-requisite:
    * Create a prod branch in your GitHub repository, merge it with the dev branch, and push the changes to GitHub.
    * In Jenkins, configure a multibranch pipeline.

2. Jenkinsfile for the pipeline:
```groovy
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENDIALS = credentials('Dockerhub-sai')
    }
    stages {
        stage('GIT Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/prod']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/SaiVicky321/Capstione_project.git']])
            }
        }
        stage('listing the directory') {
            steps {
                sh 'ls -lrt'
            }
        }
        stage('listing the docker images') {
            steps {
                sh 'docker images'
            }
        }
        stage('Building the docker image') {
            steps {
                sh """docker build -t saivicky321/prod:V${env.BUILD_NUMBER} ."""
                sh 'docker images'
            }
        }
        stage('Running the container') {
            steps {
                sh """docker run --name my-react-app -d -p 80:80 saivicky321/prod:V${env.BUILD_NUMBER}"""
                sh 'docker ps -a'
            }
        }
        stage('docker login ') {
            steps {
                sh """echo $DOCKERHUB_CREDENDIALS_PSW | docker login -u $DOCKERHUB_CREDENDIALS_USR --password-stdin"""
            }
        }
        stage('Image push') {
            steps {
                sh """docker push saivicky321/prod:V${env.BUILD_NUMBER}"""
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
```
3. Jenkins Setup:
* Install Jenkins on your server and sign up.
* Install the necessary Jenkins plugins like Docker, Docker Pipeline, Docker Commons, Pipeline: Multibranch with defaults, Docker API.
* Connect Jenkins to GitHub with an auto-build trigger for both dev and prod branches.

4. Pipeline Configuration:
* Create a multibranch pipeline job in Jenkins.
* Set up Jenkins configuration for Docker credentials.
* Add a file in dev_repo to trigger the auto-build process and verify Docker image push to dockerhub/dev.

5. Test the Pipeline:
* Merge dev_repo to prod_repo and test the auto-trigger for the prod branch, which should trigger a Docker build and push to dockerhub/prod.

## Step 6: AWS EC2 Deployment
1. Set up an AWS EC2 instance to host the application.
2. Configure the Security Groups (SG) for access to the EC2 instance.
3. Deploy the React application using the Docker image.

## Step 7: Monitoring with AWS CloudWatch & SNS
1. CloudWatch Setup:
Use AWS CloudWatch to monitor the health of your application and gather logs.

2. SNS Setup:
Configure AWS SNS (Simple Notification Service) to send notifications based on CloudWatch metrics and alarms.


# Conclusion
This project showcases a complete DevOps pipeline for deploying a React application on AWS using Git, Jenkins, Docker, and AWS services. The system is automated for continuous integration, continuous deployment, and monitoring, ensuring a streamlined and efficient process.

For the full code and pipeline configuration, please refer to the GitHub repository.
