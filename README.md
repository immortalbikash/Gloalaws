# 🌐 GlobalShop – AWS Deployment Guide

This README indicates that the GlobalShop e-commerce platform can be installed with the help of AWS services such as VPC, ECS, RDS, S3, ElastiCache, DynamoDB, Load Balancer, and CI/CD tools. It also includes the mechanism of testing the setup and destroying the resources safely when you have completed the setup.

---


## How to Deploy the Stack

### 1. Set Up the Network (VPC & Subnets)
- Create a VPC with CIDR: `10.0.0.0/16`
- Create 2 **public subnets**: `10.0.1.0/24` and `10.0.2.0/24`
- Create 2 **private subnets**: `10.0.3.0/24` and `10.0.4.0/24`
- Create and attach an Internet Gateway
- Create a NAT Gateway in one of the public subnets
- Update route tables accordingly for internet and private access

### 2. Create Security Groups
- ALB Security Group: Allow ports 80 (HTTP) and 443 (HTTPS) from anywhere
- ECS Security Group: Allow traffic from ALB only
- RDS Security Group: Allow MySQL traffic (port 3306) from ECS only

### 3. ECS Cluster and Task Definition
- Create an ECS Cluster using AWS Fargate
- Define a Task with:
  - Docker image URL
  - Port mapping: 80
  - 0.5 vCPU and 1 GB memory
- Enable CloudWatch logging

### 4. Databases and Caching
- **RDS**: Create a MySQL DB in private subnets, Multi-AZ enabled
- **DynamoDB**: Create a table for cart/session data
- **Redis (ElastiCache)**: Set up for caching product and cart data

### 5. S3 Buckets
- Create two buckets:
  - `globalshop-assets` (for images/files)
  - `globalshop-logs` (for logs)
- Enable encryption and add lifecycle rules (e.g., move to Glacier after 30 days)

### 6. Load Balancer Setup
- Create an Application Load Balancer (ALB) in the public subnets
- Create a Target Group and register ECS service
- Add listeners for HTTP and HTTPS

### 7. CI/CD Pipeline
- Create a CodeCommit repository and push your app code
- Set up CodeBuild to build the container image
- Use CodePipeline to automate the flow:
  CodeCommit → CodeBuild → ECS Deployment

---

## 🧪 How to Test the Stack

After everything is deployed:

- Open the ALB DNS URL in a browser to check if the site is running
- Check CloudWatch logs for ECS to confirm containers are working
- Monitor RDS and ECS metrics for CPU and memory usage
- Try updating the code and pushing to CodeCommit — confirm if the pipeline rebuilds and redeploys the app

---

## 🧹 How to Destroy the Stack

Once you're done, follow these steps to clean everything up:

1. **Delete ECS services and the cluster**
2. **Delete the Load Balancer and Target Group**
3. **Delete RDS and ElastiCache clusters**
4. **Delete the DynamoDB table**
5. **Delete the S3 buckets (empty them first!)**
6. **Delete the CI/CD pipeline and CodeCommit repo**
7. **Delete the VPC, subnets, NAT, IGW, and route tables**

This helps avoid unnecessary charges on your AWS account.

---
