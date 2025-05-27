---
title: "🚀 Deploy a Python Weather App Using GitLab CI/CD"
datePublished: Tue May 27 2025 15:25:41 GMT+0000 (Coordinated Universal Time)
cuid: cmb6o4o2w000m09jy7okr0ljk
slug: deploy-a-python-weather-app-using-gitlab-cicd
tags: devops

---

✅ In this blog, you'll learn how to deploy a simple Python weather app using GitLab CI/CD, Docker, GitLab Container Registry, and a self-hosted GitLab Runner on an EC2 instance. This guide is ideal for DevOps beginners who want hands-on CI/CD experience using real tools.

## 🛠️ Tech Stack & Tools

* **GitLab** (Repo + Container Registry + CI/CD)
    
* **AWS EC2** (as the server & GitLab Runner host)
    
* **Docker** (for containerization)
    
* **Shell GitLab Runner** (running on EC2)
    
* **Trivy** (for image vulnerability scanning)
    
* **SonarQube** (for code quality scanning)
    

---

## ✅ Prerequisites

* GitLab account
    
* AWS account
    
* Basic Python & Docker knowledge
    
* Python Weather App (e.g., `app.py` with `Flask`)
    

---

## 🔧 Step 1: Launch an EC2 Instance

1. Go to [AWS Console](https://console.aws.amazon.com/).
    
2. Launch an EC2 instance:
    
    * Amazon Linux 2 or Ubuntu 22.04
        
    * t2.micro (Free Tier)
        
    * Open inbound ports **22 (SSH)** and **80 (HTTP)**.
        
3. Connect to EC2:
    
    ```bash
    ssh -i your-key.pem ubuntu@your-ec2-public-ip
    
    ```
    

---

## 🐳 Step 2: Install Docker on EC2

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker gitlab-runner && newgrp docker
```

---

## 🤖 Step 3: Install GitLab Runner on EC2

```bash
curl -L --output gitlab-runner <https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64>
sudo mv gitlab-runner /usr/local/bin/gitlab-runner
sudo chmod +x /usr/local/bin/gitlab-runner
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start

```

---

## 🔐 Step 4: Register Your GitLab Runner

In GitLab:

* Go to your project → Settings → CI/CD → Runners → Copy the **Registration Token**
    

On EC2:

```bash
sudo gitlab-runner register

```

Provide:

* GitLab instance URL: `https://gitlab.com`
    
* Token: \[paste the registration token\]
    
* Description: `ec2-runner`
    
* Tags: `ec2`
    
* Executor: `shell`
    

---

## 📦 Step 5: Create a Dockerfile in Your Python Project

```plaintext
# Dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]

```

---

## 📋 Step 6: Create `.gitlab-ci.yml`

```yaml
stages:
  - scan
  - build
  - security_scan
  - deploy

variables:
  IMAGE_NAME: $CI_REGISTRY_IMAGE

default:
  tags:
    - ec2

sonarqube-check:
  stage: scan
  script:
    - echo "Running SonarQube scan..."
    - sonar-scanner \\
        -Dsonar.projectKey=weather-app \\
        -Dsonar.sources=. \\
        -Dsonar.host.url=$SONAR_HOST_URL \\
        -Dsonar.login=$SONAR_TOKEN

build:
  stage: build
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $IMAGE_NAME:$CI_COMMIT_SHORT_SHA .
    - docker push $IMAGE_NAME:$CI_COMMIT_SHORT_SHA

trivy-scan:
  stage: security_scan
  script:
    - trivy image --exit-code 1 --severity CRITICAL,HIGH $IMAGE_NAME:$CI_COMMIT_SHORT_SHA || true

deploy:
  stage: deploy
  script:
    - sudo apt update && sudo apt install -y openssh-client
    - echo "$SSH_PRIVATE_KEY" > key.pem
    - chmod 600 key.pem
    - |
      ssh -o StrictHostKeyChecking=no -i key.pem $SSH_USER@$SSH_HOST "
        docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY &&
        docker pull $IMAGE_NAME:$CI_COMMIT_SHORT_SHA &&
        docker stop weather-container || true &&
        docker rm weather-container || true &&
        docker run -d --name weather-container -p 80:5000 $IMAGE_NAME:$CI_COMMIT_SHORT_SHA"

```

---

## 🔐 Step 7: Set GitLab CI/CD Variables

Go to **GitLab → Project → Settings → CI/CD → Variables** and add:

* `SSH_PRIVATE_KEY` = private key to access EC2
    
* `SSH_USER` = `ubuntu`
    
* `SSH_HOST` = EC2 public IP
    
* `SONAR_HOST_URL` = SonarQube server URL
    
* `SONAR_TOKEN` = SonarQube access token
    

---

## ✅ Step 8: Push Code to GitLab and Trigger the Pipeline

```bash
git add .
git commit -m "Initial CI/CD setup with Trivy and SonarQube"
git push origin main

```

Go to **GitLab → CI/CD → Pipelines** and watch your job run through all stages!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748359494085/18b0feff-4403-4133-b8ff-104595077f44.png align="center")

## 🧪 Step 9: Verify Deployment

Visit your EC2 public IP in the browser:

```plaintext
<http://your-ec2-public-ip>
```

You should see your weather app running!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748359519023/a39c425a-17ee-42aa-8bdb-8e1e2914e855.png align="center")

## ✅ Wrap-Up

### 🔁 What We Achieved:

* Set up infrastructure on AWS
    
* Created a Docker image for the Python app
    
* Used GitLab CI to automate build, scan, and deployment
    
* Integrated SonarQube and Trivy
    
* Used GitLab Container Registry
    
* Deployed app to EC2 with full automation