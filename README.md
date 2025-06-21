# Global-aws

# 🌐 GlobalShop – AWS Deployment Guide

This document provides instructions on how to deploy, test, and destroy the AWS infrastructure for the GlobalShop application. This project uses services like VPC, ECS (Fargate), RDS, S3, ElastiCache, DynamoDB, ALB, and a CI/CD pipeline.

---

## 📦 Prerequisites

- AWS Account (with appropriate permissions)
- AWS CLI installed and configured
- Git installed
- Docker (for local testing, optional)
- Code and infrastructure templates ready (in your CodeCommit repo)

---

## 🚀 Deployment Steps

### 1. **Create VPC and Networking**

- Create a VPC with CIDR `10.0.0.0/16`
- Create subnets:
  - Public: `10.0.1.0/24`, `10.0.2.0/24`
  - Private: `10.0.3.0/24`, `10.0.4.0/24`
- Create and attach an **Internet Gateway**
- Create a **NAT Gateway** in a public subnet
- Set up **public and private route tables** accordingly

### 2. **Configure Security Groups**

- ALB SG: Allow ports 80, 443 from `0.0.0.0/0`
- ECS SG: Allow traffic from ALB SG
- RDS SG: Allow port 3306 from ECS SG

### 3. **Set Up ECS Cluster and Task**

- Create an ECS Cluster using **AWS Fargate**
- Define Task:
  - Container Image
  - Port 80
  - 0.5 vCPU, 1GB memory
- Enable CloudWatch Logs

### 4. **Create Databases**

- **RDS**: MySQL, Multi-AZ, in private subnets
- **DynamoDB**: Create table with `session_id` as key
- **Redis (ElastiCache)**: Create Redis cluster in private subnet for caching

### 5. **Set Up S3 Buckets**

- `globalshop-assets`: for static files
- `globalshop-logs`: for logging
- Enable encryption and lifecycle policy

### 6. **Configure Application Load Balancer**

- Create ALB in public subnets
- Create a Target Group (type: IP)
- Link ECS service to ALB

### 7. **Set Up CI/CD Pipeline**

- Create a **CodeCommit** repo for source code
- Set up **CodeBuild** to build and test your app
- Create **CodePipeline** to automate deployments to ECS

---

## ✅ Testing the Stack

- Access your application via the **ALB DNS name**
- Confirm ECS tasks are healthy
- Use CloudWatch to:
  - View logs from ECS
  - Monitor metrics for RDS, ALB, and S3
- Confirm email or log notifications from CloudWatch alarms (e.g., CPU usage)

---

## 🧹 Destroying the Stack

> ⚠️ **Only delete resources when you are sure you're done. Some actions cannot be undone.**

1. **Delete ECS services and cluster**
2. **Delete ALB and Target Group**
3. **Delete RDS and ElastiCache clusters**
4. **Delete DynamoDB table**
5. **Delete S3 buckets (after clearing contents)**
6. **Delete CodePipeline, CodeBuild, and CodeCommit repo**
7. **Delete all networking components:**
   - Subnets, route tables, NAT, IGW
   - VPC

You can also use **CloudFormation** or **Terraform** to destroy resources if you deployed using Infrastructure as Code (IaC).

---

## 📌 Notes

- Make sure IAM roles and permissions are scoped properly.
- Test changes in a sandbox environment before pushing to production.
- Monitor cost and delete unused resources.

---

## 👨‍💻 Authors

- GlobalShop AWS Team  
- [Your Name] – Implementation Lead  
- [Other Group Members]
