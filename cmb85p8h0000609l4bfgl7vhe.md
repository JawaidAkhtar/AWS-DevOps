---
title: "GitltLab for DevOps Beginners – Part 1: Understanding the Basics"
datePublished: Wed May 28 2025 16:25:20 GMT+0000 (Coordinated Universal Time)
cuid: cmb85p8h0000609l4bfgl7vhe
slug: gitltlab-for-devops-beginners-part-1-understanding-the-basics
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748449418129/9990758d-002e-4e05-b279-b2d692ef7a0a.webp
tags: devops

---

Hey DevOps enthusiast! 👋  
Welcome to **Part 1** of our **GitLab Blog Series**, where we’ll explore GitLab from scratch — What it is, why it matters, how it's different from GitHub and Bitbucket, and the key components every **fresher DevOps engineer** should know! 💡

## 📌 What is GitLab?

**GitLab** is an all-in-one DevOps platform 🧰  
It lets you **plan, create, test, and deploy software** – all from a single place!

Think of GitLab as:  
👉 **Git Repository + CI/CD + Issue Tracking + Deployment Automation**

## 🤔 Why GitLab is Important for DevOps?

🔧 DevOps is all about automating and speeding up software delivery.  
🔁 GitLab gives you everything you need to automate and collaborate.

Here’s what makes GitLab a DevOps super-tool:

* 📁 **Track code changes**
    
* 👨‍💻👩‍💻 **Collaborate with team members**
    
* ⚙️ **Automate testing and deployments with CI/CD**
    
* 🔐 **Manage access and security**
    
* 📊 **Monitor code quality and security scans**
    

## ⚔️ GitLab vs GitHub vs Bitbucket

Let’s quickly compare GitLab with other popular tools 👇

| Feature | GitLab 🦊 | GitHub 🐱 | Bitbucket 🧵 |
| --- | --- | --- | --- |
| CI/CD Built-in | ✅ Yes (Native) | ⚠️ Yes (via Actions) | ⚠️ Yes (via Pipelines) |
| Self-hosting Option | ✅ Yes | ⚠️ Limited | ✅ Yes |
| DevOps Lifecycle | ✅ Complete | ❌ Partial | ❌ Partial |
| Open Source Option | ✅ Yes | ❌ No | ❌ No |

🔍 **Verdict**:

* **GitHub** is great for open-source
    
* **Bitbucket** is good for teams using Jira
    
* **GitLab** is perfect for full **DevOps automation** 💥
    

## 🧩 Key GitLab Components (For DevOps Engineers)

Let’s explore what you’ll actually use in real DevOps work 👇

### 📁 1. Repository (Repo)

A **Repo** is where your project’s source code lives.  
Every version of your code is tracked here 🧾.

### 🌿 2. Branches

A branch is like a **copy of your main code**.  
You create a branch to work on a feature without touching the main code.

### 🔀 3. Merge Request (MR)

When you’re done working on a branch, you open a **Merge Request** to combine it back into `main`.

### ⚙️ 4. CI/CD Pipelines

The **core of automation** in GitLab.

A **pipeline** is a set of steps to:

* Run tests ✅
    
* Build code 🛠️
    
* Deploy to servers 🚀
    

### 📄 5. `.gitlab-ci.yml` File

This file **controls your pipeline**!  
It tells GitLab what to do when you push code.

Example:

```yaml
stages:
  - build
  - deploy

build-job:
  stage: build
  script:
    - echo "Building the app..."

deploy-job:
  stage: deploy
  script:
    - echo "Deploying the app..."
```

💡 Pro Tip: This file goes in the **root directory** of your repo.

### 🧱 6. Jobs

A **job** is a task in your pipeline.

Example jobs:

* 🧪 `test-job`: Run unit tests
    
* 📦 `build-job`: Build your app
    
* ☁️ `deploy-job`: Deploy to cloud
    

### 🏗️ 7. Stages

**Stages** are groups of jobs that run in order.  
For example:  
➡️ `build` ➡️ `test` ➡️ `deploy`

Jobs in the same stage run **in parallel**, and GitLab moves to the next stage only if the current one succeeds.

### 📦 8. Artifacts

Artifacts are the **outputs of your jobs**, like:

* Compiled code
    
* Test reports
    
* Logs
    

You can **store and reuse** these between jobs.

```yaml
artifacts:
  paths:
    - build/
```

### 🧮 9. Variables

**CI/CD Variables** let you store secrets and config values:

* 🧪 `ENV=production`
    
* 🔑 `AWS_ACCESS_KEY_ID`
    
* 📦 `DOCKER_IMAGE_NAME`
    

You can define variables in:

* `.gitlab-ci.yml`
    
* GitLab UI → Project → Settings → CI/CD → Variables
    

### 🏃‍♂️ 10. Runners

Runners are what **actually run your pipeline jobs**.

#### 🔹 GitLab-Hosted Runner

* Pre-configured by GitLab 🛠️
    
* Good for quick setup
    

#### 🔸 Self-Hosted Runner

* You install it on your own server 🖥️
    
* Offers more control & security
    
* Required for private infrastructure or custom environments
    

```bash
# Register a self-hosted runner
sudo gitlab-runner register
```

### 🌍 11. Environments

Helps you define stages like:

* `development`
    
* `staging`
    
* `production`
    

You can even monitor **which commit is deployed where** using GitLab's environment view!

## 💡 Real-Life Scenario (How It All Comes Together)

Let’s say you’re working on a **web app**:

1. You push code to GitLab 📤
    
2. GitLab reads `.gitlab-ci.yml` 📄
    
3. The **build job** compiles the app 🧱
    
4. The **test job** checks for errors 🧪
    
5. The **deploy job** uploads it to AWS EC2 ☁️
    
6. Artifacts are saved, environment is updated 📦
    
7. You get notified via email or Slack 📬
    

✨ All this happens **automatically**! That’s the **power of GitLab CI/CD**.

## 🧠 Summary – Why GitLab is a Must-Know for DevOps

✅ Full DevOps lifecycle in one tool  
✅ Automates build, test, and deployment  
✅ Perfect for collaboration and version control  
✅ Secure, scalable, and flexible  
✅ Huge demand in the DevOps job market 📈

## 📚 Coming Up Next…

👉 In **Part 2**, we’ll go **hands-on**:

* Set up a GitLab project
    
* Create a `.gitlab-ci.yml`
    
* Deploy a sample app using CI/CD 🚀
    

🔖 **Bookmark this post**, share with your DevOps gang 💬, and comment if you want a **PDF version or Markdown version** for easy download!