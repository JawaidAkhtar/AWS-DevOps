---
title: "Php-Todo-App"
datePublished: Mon May 26 2025 11:40:48 GMT+0000 (Coordinated Universal Time)
cuid: cmb50nmgh000m0al7em6s0fx2
slug: php-todo-app
tags: devops, devops-articles

---

Hello everyone, This document covers the **end-to-end deployment** of a PHP-APP on Amazon EKS using **GitOps**, with integrated **DevSecOps** best practices for security, automation, and monitoring. I Hope this detailed guide is useful.

### [CLICK HERE FOR GITLAB REPOSITORY](https://gitlab.com/mdjawaidakhtaransari/php-crud-api.git)

**Steps:-**

Step 1 — Launch 3 Ubuntu (Latest Version) Instance on AWS.

Step 2 — Install Jenkins, Docker. Create a Sonarqube Container using Docker on Jenkins server.

Step 3 — Install Plugins like gitlab, docker, Sonarqube Scanner, and more in Jenkins.

step 4 — Configure jenkins, integrate sonarqube, and add credentials.

Step 5 — setup k8s server as jenkins agent and connecting them.

Step 6 — Create a Pipeline Project in Jenkins using a Declarative Pipeline

step 7 — setup mysql database on second server.

Step 8 — Create AWS EKS cluster

Step 9 — Install Helm

Step 10 — Install Prometheus and Grafana using Helm

Step 11 — Install ArgoCD using Helm

Step 12 — Access the php-app on the Browser.

## 🏗️ Step-by-Step Deployment

### ⚡ Step 1: Launch EC2 Instances

Launch an AWS t2.medium Instance. Use the image as Ubuntu. You can create a new key pair or use an existing one. Enable HTTP and HTTPS settings in the Security Group and open 8080 and 9000 port no for jenkins and sonarqube.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253320512/e408c7c1-65dd-4791-addd-d554bccbef37.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253348857/64792db2-3b63-4c22-b457-7aa30cd059db.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253373091/cc1394da-3636-42e0-a7e8-a5dc80aa991f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253384678/30e85d1e-55c5-4df9-bccd-ff8c323004de.png align="center")

Now create one more ec2 instance with same configuration

Now again create one more ec2 instance but this time in private subnetand open port 3306 for mysql database.

### **Step 2 — Install Jenkins, Docker. Create a Sonarqube Container using Docker on first server.**

Connect to the instance via SSH:

`ssh -i your-key.pem ubuntu@your-ec2-ip`

### 2A: Install **Jenkins**.

Run below commands to Install Jenkins

```bash
#Installation of JAVA
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version

#Installation of jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \\
  <https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key>
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \\
  <https://pkg.jenkins.io/debian-stable> binary/ | sudo tee \\
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

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253745145/8dd9dfc7-2f5f-4aea-aa8c-b91f55acfab7.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253755270/3ca14b80-abad-4864-8afa-17a46ba5f09e.png align="center")

Jenkins will now get install all the libraries.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748253787695/0aa43817-8e52-489b-b34c-452ac44a387d.png align="center")

Create a user click on save and continue.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254017345/8485c5cf-797b-4cef-b7f6-4c03dce6ecdf.png align="center")

Jenkins Getting Started Screen.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254052614/0964e05b-ca54-44ea-9755-25f82245e3e4.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254064327/37c471ca-aa8b-4bd6-ade3-2447fa9d2f9f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254172168/89dff95c-e328-41ab-bce2-debbfe667eab.png align="center")

### 2B : Install Docker.

Run below command to install docker.

```bash
# Run the following command to uninstall all conflicting packages:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \\
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] <https://download.docker.com/linux/ubuntu> \\
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \\
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# To install the latest version, run:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version
sudo usermod -aG docker $USER && newgrp docker  #my case is ubuntu
```

After the docker installation, we will create a sonarqube container.

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

Now our sonarqube is up and running, just take the ip adress and paste in browser as below:

&lt;EC2 Public IP Address:9000&gt;

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254276971/834b9753-f41d-4934-8626-6cfee2a12095.png align="center")

Enter username and password, click on login and change password

```bash
username admin
password admin
```

Update New password, This is Sonar Dashboard.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254316782/bddedb42-c45c-4ed0-b851-4f16f5a9ac5c.png align="center")

### Step 3 — Install Plugins like gitlab, docker, Sonarqube Scanner, and more in Jenkins.

### **3A — Install Plugins**

Go to Manage Jenkins →Plugins → Available Plugins →

Install below plugins (Install with restart)

→ GitLab, Generic WebHook Trigger, Docker, Docker Commons, Docker Pipeline, Docker API, docker-build-step, stageview pipeline, prometheus metrics, email extention templates, SonarQube Scanner, Kubernetes

After selecting all available plugin click on install.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748254485303/d612be40-2fc3-402c-9f92-9bbf44cf9339.png align="center")

### step 4 — Configure jenkins, integrate sonarqube, and add credentials.

### 4a- GitLab credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials. Use username and access tocken.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741016101083/b9dc34da-3e54-4c50-9b22-fa9fc331f376.png align="left")

### 4b- Dockerhub credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials. Use username and access tocken.

### 4c- SonarQube credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials. Use username and access tocken.

### 4d- Email credentials setup

Now go to jenkins dashboard ➡️manage jenkins ➡️credentials ➡️system ➡️global credentials ➡️add credentials. Use username and access tocken/passwords.

### **4e — Configure SonarQube in Global Tool Configuration**

Go to Manage Jenkins → Tools → Install SonarQube → Click on Save

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740410268523/67ec60fc-ee08-4820-ad66-35fb33b91ddb.png align="left")

### Step 5 — setup k8s server as jenkins agent and connecting them

### 5a- Go to the k8s server using ssh:

SSH into the instance using the `.pem` file:

`ssh -i "your-key.pem" ubuntu@your-k8s-ec2-public-ip`

Install Java on the Agent:

`sudo apt update`

`sudo apt install openjdk-21-jdk -y`

`java -version`

### 5b- Establish SSH Connection Between Jenkins and k8s

**Go to jenkins server and generate SSH Key Pair**

`ssh-keygen`

Save the key pair in the default location (`~/.ssh/id_rsa`).

Copy the Public Key to the k8s server

You can copy the public-key and paste it into the `k8s server >.ssh>authorized_key`

Test the SSH connection from the Jenkins server to the k8s server:

`ssh -i <private_key> ubuntu@your-agent-ec2-public-ip`

you should be able to SSH into the agent without being prompted for a password.

### **5c- Add the k8s to the Jenkins**

Access Jenkins Dashboard:

Go to the Jenkins master web interface.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255299868/3510dba7-ae11-4f47-b61c-cc0f4d1d8a13.png align="center")

Navigate to Manage Jenkins &gt; Manage Nodes and Clouds &gt; New Node.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255317615/b03c53db-d81c-4ff6-82bb-74ff8e90d178.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255331616/13df635f-7a22-4548-8b7e-b64a6e43f35d.png align="center")

Provide a name for the agent, select Permanent Agent, and click OK.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255388333/0ec69b91-10c0-4735-8888-665b594bf088.png align="center")

Configure the node:

Number of Executors: Set as required.

Remote root directory: Set this to `/home/ubuntu` (or another directory on the agent).

Labels: Assign a label (e.g., `k8s`).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255416275/ca808368-df11-4b51-b23c-ef9aed006ed4.png align="center")

Launch Method: Choose Launch agent via SSH.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748255458155/3f8ae9ed-604e-4c77-aca6-fb4087ae78bd.png align="center")

Host: Enter the public IP of the agent EC2 instance.

Credentials: Add the SSH credentials you created earlier.(username and private\_key)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257630332/58ba37ae-728e-4e8d-a4cc-6f75637e10ad.png align="center")

Host Key Verification Strategy: Choose a strategy (e.g., Non-Verifying Verification Strategy).

Click Save.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257661501/9f9137d3-70f3-452a-b084-b7634c155d4c.png align="center")

The Jenkins master will attempt to connect to the agent. Check the status under Manage Jenkins ➡️ Manage Nodes and Clouds.

The agent should show as Connected.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257708768/bf1f6f38-16b0-405f-b1fc-882802f6383f.png align="center")

### **Step 6 — Create a Pipeline Project in Jenkins using a Declarative Pipeline**

Now we will create a jenkins complete pipeline for deploying our application.

Go to jenkins dashboard and click on new item, give a name for your pipeline and select pipeline option and then click ok.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257821287/0c58ac36-9f57-4ee9-bfce-525711287f8e.png align="center")

Now add below pipeline into script

```plaintext
pipeline {
agent { label 'k8s' }

environment {
GIT_REPO_SSH = "git@gitlab.com:mdjawaidakhtaransari/php-crud-api.git"
NAMESPACE = "staging"
SCANNER_HOME = tool 'sonar-scanner'
}
stages {
    stage('Checkout') {
        steps {
            checkout([$class: 'GitSCM',
                branches: [[name: 'staging']],
                userRemoteConfigs: [[
                url: "${env.GIT_REPO_SSH}",
                credentialsId: 'gitlab-cred'
                ]]
              ])
              sh 'git checkout -B staging origin/staging'
       }
   } 
     stage('SonarQube Analysis') {
         steps {
             withSonarQubeEnv('sonar-scanner') {
             sh '${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=php-crud-api -Dso
             r.sources=. -Dsonar.host.url=http://3.145.204.121:9000'}
             }
           }
           
     stage('Docker Build') {
         steps {
             script {
              sh "docker build -t php-crud-api:latest ."
              }
            }
         }
     stage('Trivy Image Scan') {
         steps {
             sh "trivy image php-crud-api:latest || true"
             }
          }
      stage('Docker Login & Push') {
          steps {
              withCredentials([usernamePassword(credentialsId: 'DockerHubCred', username
              ariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
              sh """
                echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
                docker tag php-crud-api:latest ${DOCKER_USER}/php-crud-api:latest
                docker push ${DOCKER_USER}/php-crud-api:latest
                """
                }
              }
           }
       stage('Deploy to Staging with Helm') {
           steps {
               sh """
                  helm upgrade --install php-crud-api ./helm -f ./values.yaml --namespace
                  $NAMESPACE
                  """
                 }
               }
       stage('Notify via Email') {
           steps {
               mail to: 'mdjawaidakhtaransari@gmail.com',
               subject: "✅ Jenkins Build #${env.BUILD_NUMBER} Successful - php-crud-api
               ",
               body: "Build ${env.BUILD_NUMBER} completed successfully.\\nImage: jawaid36
               5/php-crud-api:latest\\nNamespace: ${NAMESPACE}"
               }
            }
          }
      post {
        failure {
            mail to: 'mdjawaidakhtaransari@gmail.com',
            subject: "❌ Jenkins Build #${env.BUILD_NUMBER} Failed - php-crud-api",
            body: "Build failed at stage: ${env.STAGE_NAME}\\nCheck Jenkins logs for detai
            ls."
           }
         }
      }
```

### step 7 — setup mysql database on second server

### 7a- Connect to your mysql server but with private ip

```bash
ssh -i <your-key.pem> ubuntu@<private-ip>
```

Now run below command to install and configure mysql database

```bash
sudo apt update
sudo apt install mysql-server -y
sudo systemctl restart mysql
sudo mysql_secure_installation
```

### 7b- Create a new user and database

```sql
CREATE DATABASE todo;
CREATE USER 'admin' IDENTIFIED BY 'your-password';
GRANT ALL PRIVILEGES ON todo.* TO 'admin';
USE DATABASE todo;
CREATE TABLE tasks;
FLUSH PRIVILEGES;
EXIT;
```

```sql
INSERT INTO tasks (title, description, completed)
VALUES
('Complete Terraform Project', 'Finish writing the Terraform scripts', 0),
('Deploy Web App', 'Deploy the Python app on EC2 instance', 0),
('Write Blog Post', 'Document the Terraform workspace process', 1);
```

### 7c- Allow Remote Access

Edit MySQL config file:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.c
```

```bash
Change  bind-address = 127.0.0.1  to  bind-address = 0.0.0.0
```

Restart MySQL

```bash
sudo systemctl restart mysql
```

### Step 8 — Create aws EKS cluster

for creating EKS cluster we need below tools to be installed and confidured, So lets start installing one by one

**8a- Install Docker**

Run below command to install docker.

```bash
# Run the following command to uninstall all conflicting packages:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \\
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] <https://download.docker.com/linux/ubuntu> \\
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \\
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# To install the latest version, run:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version
sudo usermod -aG docker $USER && newgrp docker  #my case is ubuntu
```

**8b- Install Trivy**

```bash
#Add repository to /etc/apt/sources.list.d
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - <https://aquasecurity.github.io/trivy-repo/deb/public.key> | sudo apt-key add -
echo deb <https://aquasecurity.github.io/trivy-repo/deb> $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

**8c- Install java**

```bash
#Installation of JAVA
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
```

**8d- Install aws cli and configure it**

```bash
#!/bin/bash
#Downloading aws-cli zip file
echo "Downloading AWS_CLI..."
curl "<https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip>" -o "awscliv2.zip"
#Installing unzip tool
echo "Installing unzip tool..."
sudo apt install unzip
#Unzip AWS_CLI zip file
unzip awscliv2.zip
#Installing AWS_CLI
echo "Installing AWS_CLI..."
sudo ./aws/install
```

Now create access key for user. Go to IAM➡️Users➡️your user name➡️Create access key➡️follow the steps shown as below

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257914475/35bd17d7-6f71-47bf-9b4b-bca25a72f7ec.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257926499/9170ccd3-b0bf-4d77-99a7-2469151fda75.png align="center")

Now we will configure aws cli using access key and secret key

Run below command

```bash
aws configure
AWS Access Key ID [****************YEAC]: AKIARWPFIU5SPHW4NBO6 #Replace with your access key
AWS Secret Access Key [****************VTJY]:                  #Enter you secret access key
Default region name [us-east-2]: ap-south-1
Default output format [json]: json
```

### **8e- Install kubectl**

```bash
#!/bin/bash

#Download the latest release with the command:
curl -LO "<https://dl.k8s.io/release/$>(curl -L -s <https://dl.k8s.io/release/stable.txt>)/bin/linux/amd64/kubectl"

#Download the kubectl checksum file:
curl -LO "<https://dl.k8s.io/release/$>(curl -L -s <https://dl.k8s.io/release/stable.txt>)/bin/linux/amd64/kubectl.sha256"

#Validate the kubectl binary against the checksum file:
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

#Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

#Test to ensure the version you installed is up-to-date:
kubectl version --client
```

### **8f — install eksctl**

```bash
curl -sLO "<https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz>"

# (Optional) Verify checksum
curl -sL "<https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums>. xt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

Now we have all required tools for creating EKS cluster on AWS. now run below command to create EKS cluster.

```bash

eksctl create cluster --name php-todo-cluster --region ap-south-1
```

### **Step9 — Install Helm for kubernetes**

Now we will install helm for simplifying deployment process and it also reduce deployment time. For installing helm run below command on k8s server.

```bash
#!/bin/bash
curl <https://baltocdn.com/helm/signing.asc> | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] <https://baltocdn.com/helm/stable/debian/> all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

### **Step 10 — Install Prometheus and Grafana using Helm**

We will use helm for deploying monitoring tools like prometheus and grafana for dashboard.

Run below command for monitoring tools setup.

```bash
helm repo add prometheus-community <https://prometheus-community.github.io/helm-charts>
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
kubectl get deployment -n default
```

You will see prometheus anf grafaa deployment is running

Now Expose your Prometheus and Grafana service to access its UI

```bash
#Exposing prometheus service on port 9090
kubectl port-forward svc/prometheus-operated -n default 9090:9090 --address=0.0.0.0 &

#Exposing grafana service on port 8081
kubectl port-forward svc/prometheus-grafana -n default 8081:80 --address=0.0.0.0 &
```

Now go to your browser and paste you public\_ip following port number 9090 and you will be see login page of prometheus.

Initial username and password is admin. After successfully login you will be asked to change initial password.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748257983444/d8e59e35-7823-4f90-8dbc-4a926f3364c3.png align="center")

Now go to your browser and paste you public\_ip following port number 8081 and you will see login page of Grafana. (Username= admin, Password= prom-operator)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258001005/db6b5697-e360-4a7e-bb46-192732dc27b0.png align="center")

Now we will create a dashboard Now got to connection add new data source select prometheus and provide your prometheus url and saved.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258039232/86a4fe74-c85f-445e-9b82-43f60b193b19.png align="center")

Now we are all set to create a dashboard to our grafana Go to dashboard Import dashboard and type 1860 and click on load.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258061143/dbc1a414-a045-4fc7-95ae-0388194cf9ee.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258082761/e3fd49e0-ba52-4a6e-acb7-02221fbcafa9.png align="center")

You will see dashboard like below

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258101121/f215894a-c225-47f3-8596-67dbcaabfc6d.png align="center")

### **Step 11 — Install ArgoCD using Helm**

Now we will install ArgoCD using Helm to automatically deploy our application.

Run below command to install ArgoCD and access its UI.

```bash
helm repo add argo <https://argoproj.github.io/argo-helm>
helm repo update
helm install argocd argo/argo-cd
kubectl get svc -n default
kubectl port-forward svc/argocd-server -n default 8082:443 --address=0.0.0.0 &
```

Now go to your browser and paste you public\_ip following port number 8082 and you will see login page of ArgoCD. Username is **admin** and for password run below command to see initial password.

```bash
kubectl -n default get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258172111/6276d7c6-4b3b-4767-b3c0-70d17dd6ba38.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258179461/cd1273d4-a891-474e-8704-a540b5744c79.png align="center")

Our gitlab repo is private so we need to add our repo with credentials in argocd first Now go to settings repositories and add repo now provide url, username and password of gitlab.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258200954/9491cff4-cb08-4cbb-990b-e059ddc1d078.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258209419/2e2856e1-cb14-41d8-864c-81bd5542a456.png align="center")

now go to application create application and fill all required details like name of app, gitlab repo url, path of menifest file, kubernetes cluster url and at the end namespace where you want to deploy your application. your application will be deployed like below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258237678/23ca5049-2bb9-46e9-8071-ede9978418a0.png align="center")

Now push any changes into devlopement branch and create a merge request to staging branch. Once dev branch successfully merged into staging your webhook will trigger your pipeline and you will see below stages.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258262642/8466db40-e4e5-432f-a7d7-fb2f138ceafa.png align="center")

### **Step 12 — Access the PHP-APP on the Browser**

Now Take load balancer DNS and paste on browser to access the application.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748258312062/9c4ec8e1-e17a-4e21-8b95-8356b7958460.png align="center")