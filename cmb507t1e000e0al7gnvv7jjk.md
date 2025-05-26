---
title: "Netflix Clone Deployment | GitOps | ArgoCD
|  AWS | EKS | Docker | Jenkins  | Monitoring | Prometheus | Grafana | SonarQube | DevSecOps |"
datePublished: Mon May 26 2025 11:28:30 GMT+0000 (Coordinated Universal Time)
cuid: cmb507t1e000e0al7gnvv7jjk
slug: netflix-clone-deployment-gitops-argocd-aws-eks-docker-jenkins-monitoring-prometheus-grafana-sonarqube-devsecops
tags: devops

---

Hello DevOps enthusiast, This blog covers the **end-to-end deployment** of a Netflix-clone app on Amazon EKS using **GitOps**, with integrated **DevSecOps** best practices for security, automation, and monitoring. I Hope this detailed blog is useful.

[**CLICK HERE FOR GITHUB REPOSITORY**](https://github.com/JawaidAkhtar/Netflix-clone)

**Steps:-**

Step 1 — Launch an Ubuntu (Latest Version) T2 Large Instance.

Step 2 — Create a TMDB API Key (use VPN for creating API key).

Step 3 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

Step 4 — Docker Image Build, Run and Push To DockerHub

Step 5 — Install Plugins like JDK, Nodejs, Sonarqube Scanner, OWASP Dependency Check and Docker in Jenkins.

Step 6 — Email Integration With Jenkins and Plugin setup.

Step 7 — Create AWS EKS cluster

Step 8 — Install Helm

Step 9 — Install Prometheus and Grafana using Helm

Step 10 — Install ArgoCD using Helm

Step 11 — Create a Pipeline Project in Jenkins using a Declarative Pipeline

Step 12 — Access the Netflix app on the Browser.

Step 13 — Terminate the AWS EC2 Instances.

**Now, let’s get started and dig deeper into each of these steps:-**

## 🏗️ Step-by-Step Deployment

### ⚡ Step 1: Launch an EC2 Instance

Launch an AWS T2 Large Instance. Use the image as Ubuntu. You can create a new key pair or use an existing one. Enable HTTP and HTTPS settings in the Security Group and open all ports (not best case to open all ports but just for learning purposes it’s okay).

Connect to the instance via SSH:

```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```

### 🔑 Step 2: Generate TMDB API Key

Next, we will create a TMDB API key

Open a new tab in the Browser and search for TMDB

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739540402619/8b8f0572-28d7-4976-80f0-f8a954ed9ffe.png align="center")

Click on the first result, you will see this page

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739540765501/aadb26b9-3892-42f8-9e78-99e06f5d4fbf.png align="center")

Click on the Login on the top right.

You need to create an account here. click on click here.

once you create an account you will see this page.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739540914794/af8ed1e0-ccc5-440a-8671-55d0ad44b8d5.png align="center")

Let’s create an API key, By clicking on your profile and clicking settings.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739541027081/a1c11050-7daf-4db4-8e60-694873fba80f.png align="center")

Now click on API from the left side panel.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739541185402/d838165d-7e84-416c-98e3-ba62529c69a2.png align="center")

Now click on create

Click on Developer

Now you have to accept the terms and conditions.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696676601846/c2b4a1c7-e72a-405c-82a5-cfd5fc454403.png?auto=compress,format&format=webp align="left")

Provide basic details

Click on submit and you will get your API key.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739541360496/41b3f438-029c-4edf-bd0f-241867dcbc58.png align="center")

### **🔧 Step 3: Install Required Tools**

### 3A: Install **Jenkins**.

Run below commands to Install Jenkins, Docker, Trivy

```bash
#Installation of JAVA
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version

#Installation of jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update the packages
sudo apt-get update
sudo apt-get install jenkins
jenkins --version
```

Once Jenkins is installed, now grab your Public IP Address and paste it to your browser like below.  
&lt;EC2 Public\_IP\_Address:8080&gt;

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Unlock Jenkins using an administrative password and install the suggested plugins.

Jenkins will now get install all the libraries.

Create a user click on save and continue.

Jenkins Getting Started Screen.

### 3B : Install Docker.

```bash
# Run the following command to uninstall all conflicting packages:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# To install the latest version, run:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version
sudo usermod -aG docker $USER   #my case is ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
```

### 3C : Install Trivy.

```bash
#Add repository to /etc/apt/sources.list.d
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

After the docker installation, we will create a sonarqube container.

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

Now our sonarqube is up and running, just take the ip adress and paste in browser as below:  
&lt;EC2 Public IP Address:9000&gt;

Enter username and password, click on login and change password

```plaintext
username admin
password admin
```

Update New password, This is Sonar Dashboard.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1694159887297/6e055b5c-13ea-405f-bc13-1234b05bf2ff.png?auto=compress,format&format=webp&auto=compress,format&format=webp&auto=compress,format&format=webp align="left")

### **📦 Step 4: Docker Image Build, Run and Push To DockerHub**

First of all, we will create image, push image and run netflix-clone manually to test our image is running properly. Go to netflix-clone repo and paste your TMDB\_Key in .env file and commit.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740144266495/efba2d0b-2b87-431b-8bc9-ad4776c00830.png align="center")

Run below command to build and run the container.

```bash
docker build --build-arg TMDB_V3_API_KEY=<Your-TMDB_Key> -t netflix-clone .

docker run --name netflix-clone-website --rm -d -p 80:80 netflix-clone

docker ps
```

Now take your public\_ip and paste to browser, you will see your application is running like below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740144584153/adc6404f-4918-45f1-9fb0-dd6f4d46d10b.png align="center")

Now we will push our image to DockerHub.  
Run the below command to login and image push

```bash
docker login -u jawaid365 -p <Access Token> #Replace jawaid365 with your dockerhub username
docker tag netflix-clone jawaid365/netflix-clone:latest  
docker push jawaid365/netflix-clone:latest
```

### **Step 5 — Install Plugins like JDK, NodeJs, Sonarqube Scanner, OWASP Dependency Check in jenkins**

### **5A — Install Plugins**

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins (Install with restart)

→ docker, Docker Commons, Docker Pipeline, Docker API, docker-build-step, stageview pipeline, prometheus metrics, email extention templates, Eclipse Temurin Installer, SonarQube Scanner, NodeJs Plugin, OWASP Dependency-Check, Kubernetes

After selecting all available plugin click on install.

### **5B — Configure Java and Nodejs in Global Tool Configuration**

Go to Manage Jenkins → Tools → Install JDK(17) → Click on Apply and Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740409520660/e756d80a-7fd3-406b-a1f4-befd6810edd8.png align="center")

Go to Manage Jenkins → Tools → Install NodeJs(23)→ Click on Apply and Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740409672515/50b3aaf4-7b22-4543-b199-fa1875c9a362.png align="center")

### **5c — Configure SonarQube, OWASP Depedency check and Docker in Global Tool Configuration**

Go to Manage Jenkins → Tools → Install SonarQube → Click on Apply and Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740410268523/67ec60fc-ee08-4820-ad66-35fb33b91ddb.png align="center")

Go to Manage Jenkins → Tools → Install OWASP Dependency check → Click on Apply and Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740410326208/dc0e6868-8cfb-4af3-b9f9-931e72dd979d.png align="center")

Go to Manage Jenkins → Tools → Install docker → Click on Apply and Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740410408556/a419f5ad-357a-416b-8b5c-8b47bc084e1a.png align="center")

### **Step 6 — Email Integration With Jenkins and Plugin setup.**

Install `Email Extension Plugin` in Jenkins

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1694164210365/4c78af7c-80a6-4442-b9cc-ee87b6956c1a.png?auto=compress,format&format=webp&auto=compress,format&format=webp align="left")

Go to your Gmail and click on your profile

Then click on Manage Your Google Account –&gt; click on the security tab on the left side panel you will get this page(provide mail password).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740411778395/9dcbe216-1fc3-46cb-92cd-60899b81ec30.png align="center")

2-step verification should be enabled.

Search for the app in the search bar you will get app passwords like the below image

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740412149118/58f54b1c-b269-499f-980f-ab5fe472162e.png align="center")

Click on other and provide your name and click on Generate and copy the password

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740412285569/242a1e02-3b09-47d5-8068-ca59b3023c1e.png align="center")

In the new update, you will get a password like this

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740412348601/b6abb415-ef97-4925-be2a-3b02acd5bad7.png align="center")

Once the plugin is installed in Jenkins, click on manage Jenkins –&gt; configure system there under the E-mail Notification section configure the details as shown in the below image

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740413387887/2ecb4573-5a72-445e-b6a2-42ff8d2a5cad.png align="center")

Click on Apply and save.

Click on Manage Jenkins–&gt; credentials and add your mail username and generated password

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740412969729/1d32a971-7f26-4e48-8932-d9ce11fe9882.png align="center")

### **Step 7 — Add credentials to jenkins**

### 7a- GitHub credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741016101083/b9dc34da-3e54-4c50-9b22-fa9fc331f376.png align="center")

### 7a- Dockerhub credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials

### 7a- SonarQube credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials

### 7a- Email credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials

### 7a- API Key credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials

### **Step 7 — Create AWS EKS cluster using eksctl**

Now we will create Amazon EKS cluster using EKSCTL. But for creating and managing EKS cluster we need AWS CLI, Kubectl and eksctl installed in our ec2.

### 7a- Setup AWS CLI

```bash
#!/bin/bash

#Downloading aws-cli zip file
echo "Downloading AWS_CLI..."
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

#Installing unzip tool
echo "Installing unzip tool..."
sudo apt install unzip

#Unzip AWS_CLI zip file
unzip awscliv2.zip

#Installing AWS_CLI
echo "Installing AWS_CLI..."
sudo ./aws/install
```

Now configure aws cli using access key and secret key.  
now got to AWS console➡️IAM➡️Users➡️create user➡️follow the steps.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740665930376/4b767ffa-3645-4d9f-9d22-766fc3c97d0c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666042227/38a6794e-653f-42fc-a5a9-5a45cadfc544.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666086028/26e37fb7-52e9-418b-b8d8-0d417b0763d0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666170040/19da28ca-87e6-48bb-b691-61ccf8ce40b6.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666218944/bcf6a0f8-8243-405e-999c-e5953bc40b8d.png align="center")

1. Now create access key for newly created user.  
    Go to IAM➡️Users➡️Netflix-clone-admin➡️Create access key➡️follow the steps shown as below
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666242446/74544900-406d-4ca1-9d03-54af05c190e0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666277012/ccfc81f1-3654-41a3-a049-30a0ec0cb2ed.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740666518707/1525650d-0f91-4122-8e89-21cea95576f2.png align="center")

Now we will configure aws cli using access key and secret key

Run below command

```bash
aws configure
AWS Access Key ID [****************YEAC]: AKIARWPFIU5SPHW4NBO6 #Replace with your access key
AWS Secret Access Key [****************VTJY]:                  #Enter you secret access key
Default region name [us-east-2]: us-east-2
Default output format [json]: json
```

### 7b- [Setup Kubectl](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-2#/users/details/Netflix-clone-admin)

```bash
#!/bin/bash

#Download the latest release with the command:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

#Download the kubectl checksum file:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

#Validate the kubectl binary against the checksum file:
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

#Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

#Test to ensure the version you installed is up-to-date:
kubectl version --client
```

### 7C — Setup eksctl

```bash
# for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

Now we have all required tools for creating EKS cluster on AWS.  
now run below command to create EKS cluster.

```bash
eksctl create cluster --name netflix-clone-cluster --region us-east-2
```

### **Step8 — Install Helm for kubernetes**

Now we will install helm for simplifying deployment process and it also reduce deployment time.  
For installing helm run below command on your ec2 instance.

```bash
#!/bin/bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

### **Step 9 — Install Prometheus and Grafana using Helm**

We will use helm for deploying monitoring tools like prometheus and grafana for dashboard.  
Run below command for monitoring tools setup.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
kubectl get deployment -n default
```

You will see prometheus anf grafaa deployment is running.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740838164207/9c2fd136-db95-43b4-bf89-1e7f03771fc5.png align="center")

Now Expose your Prometheus and Grafana service to access its UI

```bash
#Exposing prometheus service on port 9090
kubectl port-forward svc/prometheus-operated -n default 9090:9090 --address=0.0.0.0 &

#Exposing grafana service on port 8081
kubectl port-forward svc/prometheus-grafana -n default 8081:80 --address=0.0.0.0 &
```

Now go to your browser and paste you public\_ip following port number 9090 and you will be see login page of prometheus.

Initial username and password is admin.  
After successfully login you will be asked to change initial password.

Now go to your browser and paste you public\_ip following port number 8081 and you will see login page of Grafana. (Username= admin, Password= prom-operator)

![How can I customize login page? - Configuration - Grafana Labs Community  Forums](https://us1.discourse-cdn.com/grafana/original/3X/a/b/abb939a1ca7a26be18afd752ef40430632377b39.png align="center")

### **Step 10 — Install ArgoCD using Helm**

Now we will install ArgoCD using Helm to automatically deploy our application.

Run below command to install ArgoCD and access its UI.

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd
kubectl get svc -n default
kubectl port-forward svc/argocd-server -n default 8082:443 --address=0.0.0.0 &
```

Now go to your browser and paste you public\_ip following port number 8082 and you will see login page of ArgoCD. Username is **admin** and for password run below command to see initial password.

```bash
kubectl -n default get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

![Getting Started :: ArgoCD Tutorial](https://redhat-scholars.github.io/argocd-tutorial/argocd-tutorial/_images/argocd-login.png align="left")

### **Step 11 — Create a Pipeline Project in Jenkins using a Declarative Pipeline**

Now we will create a jenkins complete pipeline for deploying our application.

Go to jenkins dashboard and click on new item, give a name for your pipeline and select pipeline option and then click ok.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741013934396/9a9b7651-3007-4418-80fc-7eee26f3f2e3.png align="center")

Now go to pipeline option, paste below complete pipeline script into script section and click on save and then apply.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741014111952/b5f1b2fe-f2cb-4237-bc07-56fd3949f203.png align="center")

Complete pipeline.

```plaintext
JenkinsFile:

pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'SonarQube-Scanner'
        API_Key = credentials('API_Key')
        
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Clone Repo') {
           steps{
                git branch: 'main', url: 'https://github.com/JawaidAkhtar/Netflix-clone.git'
                sh ' ls -la '
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Scanner') {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=Netflix \
                        -Dsonar.projectName=Netflix \
                        -Dsonar.sources=.
                    """
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarCred'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
               sh """
                
                npm install
            
                """
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy File System Scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                   // Get the latest Git commit ID
                    def commitId = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
            
                    env.COMMIT_ID = commitId  // Store commit ID in environment variable
                withCredentials([usernamePassword(credentialsId: 'DockerHubCred', usernameVariable: 'DockerHub_User', passwordVariable: 'DockerHub_Password')]) {
                        
                    sh """
                     whoami
                     echo "Building Docker image with commit ID: $COMMIT_ID"
                     docker build --build-arg TMDB_V3_API_KEY=$API_Key -t $DockerHub_User/netflix-clone:$COMMIT_ID .
                     docker run -d -p 80:80 --name netflix-clone ${DockerHub_User}/netflix-clone:${COMMIT_ID}
                     echo "Docker container is running on port 80"
                     """
                    }
                }
            }
        }
        

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHubCred', usernameVariable: 'DockerHub_User', passwordVariable: 'DockerHub_Password')]) {
                    sh """
                        docker login -u ${DockerHub_User} -p ${DockerHub_Password}
                        docker push ${DockerHub_User}/netflix-clone:${COMMIT_ID}
                    """
                }
            }
        }

        stage('Update Helm Chart') {
            steps {
                sh """
                    sed -i 's|tag: .*|tag: ${COMMIT_ID}|' ./helm/netflix-clone-chart/values.yaml
                """
            }
        }

        stage('Push Changes to GitHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHubCred', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_ACCESS_TOKEN')]) {
                    sh """
                        git config --global user.name "Jawaid Akhtar"
                        git config --global user.email mdjawaidakhtaransari@gmail.com
                        git add ./helm/netflix-clone-chart/values.yaml
                        git commit -m "Updated tag in values.yaml to ${COMMIT_ID}"
                        git push https://${GITHUB_USERNAME}:${GITHUB_ACCESS_TOKEN}@github.com/JawaidAkhtar/Netflix-clone.git HEAD:main
                    """
                }
            }
        }
    }
}

```

Now click on build now and your pipeline will be running.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741014497140/eb73d4d2-7ffa-4c9b-abd3-d97413f788bf.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741014648657/fe67442f-70f7-47a0-b336-146d00ab0642.png align="center")

### **Step 12 — Access the Netflix app on the Browser**

Now Take load balancer DNS and paste on browser to access the application.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741014779661/87c25da7-d071-49a9-addc-aebbe76f0fc0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741014833560/7a1930e3-2795-4187-a8bd-4f85fb4a1a0e.png align="center")

### **Step 13 — Terminate the AWS EC2 Instances and EKS**

Now run below command to delete the EKS cluster

```bash
eksctl delete cluster --name netflix --region us-east-2
```

Now delete your EC2 instance (main server)

Go to aws console➡️ EC2 ➡️select EC2 ➡️instance state ➡️terminate(delete) instance.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741015148043/df9ef1ac-18b6-42db-8a24-c8002046b924.png align="center")