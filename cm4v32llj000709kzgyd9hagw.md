---
title: "AWS DevOps- Day10"
datePublished: Thu Dec 19 2024 08:52:31 GMT+0000 (Coordinated Universal Time)
cuid: cm4v32llj000709kzgyd9hagw
slug: aws-devops-day10
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734593616536/71decfad-1d71-4b15-9233-da98ca563dad.jpeg
tags: aws

---

## **🚀 Day 10: Mastering AWS CloudFormation Templates & Infrastructure as Code (IAC)**

Welcome to **Day 10** of our **30-Day AWS DevOps Challenge**! Today, we’re exploring the powerful world of **AWS CloudFormation Templates (CFT)** and **Infrastructure as Code (IAC)**—essential tools for automating and managing cloud resources. Ready to level up your AWS skills? Let’s dive in! 🌟

### 🧱 **What are AWS CloudFormation Templates?**

AWS CloudFormation Templates are like **blueprints** for your cloud infrastructure. These declarative files describe the resources you need—be it EC2 instances, S3 buckets, or RDS databases—and provision them programmatically.

**Why Use CloudFormation?**

* **Consistency**: Ensure uniform deployments.
    
* **Repeatability**: Simplify complex infrastructure provisioning.
    
* **Automation**: Reduce manual effort and errors.
    

### 🔄 **CFT vs. AWS CLI: What's the Difference?**

While **AWS CLI** executes individual commands, **CloudFormation** uses declarative templates to manage infrastructure holistically.

💡 *Pro Tip*: Use version-controlled templates for large-scale projects—they’re easier to manage and audit.

### ✍️ **Writing Your First CloudFormation Template**

Start with **YAML** (preferred) or JSON to define your resources.

🚀 *Example*:  
Provisioning an S3 Bucket:

```yaml
Resources:  
  MyS3Bucket:  
    Type: AWS::S3::Bucket  
    Properties:  
      BucketName: my-first-s3-bucket  
      VersioningConfiguration:  
        Status: Enabled  
```

### 🖥️ **Step-by-Step Demo: Creating Resources with CloudFormation**

Here’s a step-by-step guide to create an S3 bucket using a CloudFormation Template:

#### **Step 1: Prepare Your Template**

Write a YAML file (e.g., `s3-template.yaml`) with the following content:

```yaml
Resources:  
  MyS3Bucket:  
    Type: AWS::S3::Bucket  
    Properties:  
      BucketName: my-demo-s3-bucket  
      VersioningConfiguration:  
        Status: Enabled  
```

#### **Step 2: Open AWS Management Console**

1. Log in to the **AWS Management Console**.
    
2. Navigate to the **CloudFormation** service.
    

#### **Step 3: Create a New Stack**

1. Click **Create Stack** → Select **With new resources (standard)**.
    
2. Upload your `s3-template.yaml` file.
    
3. Click **Next**.
    

#### **Step 4: Configure Stack Details**

1. Name your stack (e.g., `DemoS3Stack`).
    
2. Leave other options as default and click **Next**.
    

#### **Step 5: Review and Create**

1. Review the stack details.
    
2. Click **Create Stack** to deploy the resources.
    

#### **Step 6: Verify the Resources**

1. Once the stack status shows **CREATE\_COMPLETE**, go to the **S3** service.
    
2. Confirm the creation of the bucket named `my-demo-s3-bucket` with versioning enabled.
    

### ⚡ **Practical Tips & Tricks**

* **Use Visual Studio Code**: Pair it with the **AWS Toolkit** for template validation and auto-completion.
    
* **Drift Detection**: Regularly check for configuration changes outside CloudFormation to ensure infrastructure consistency.
    
* **Explore Documentation**: AWS official docs are packed with examples and guidelines to master CloudFormation.
    

### ✨ **Advanced Features**

* **S3 Versioning**: Keep a record of all object versions for enhanced data protection.
    
* **Drift Detection**: Detect and reconcile changes made outside CloudFormation.