# 🌐 GlobalShop – AWS Deployment Guide

This README indicates that the GlobalShop e-commerce platform can be installed with the help of AWS services such as VPC, ECS, RDS, S3, ElastiCache, DynamoDB, Load Balancer, and CI/CD tools. It also includes the mechanism of testing the setup and destroying the resources safely when you have completed the setup.

---


## How to Deploy the Stack

### 1. Set Up the Network (VPC & Subnets)
- Create a VPC: `10.0.0.0/16`
- Create 2 **public subnets**: `10.0.1.0/24` and `10.0.2.0/24`
- Create 2 **private subnets**: `10.0.3.0/24` and `10.0.4.0/24`
- Make an Internet gateway and connect it
- Establish a NAT gateway in a public subnet
- Route tables should be updated appropriately for private and internet access 

### 2. Create Security Groups
- Allow port 80(HTTP) and 433(HTTPS) from any location with ALB security group.
- ECS Security Group: Only let ALB Traffic
- RDS Security Group: only accept MYSQL traffic from ECS on port 336

### 3. ECS Cluster and Task Definition
- use AWS Fargate to establish an ECS cluster.
- Define a Task with: Docker image URL, Port mapping: 80, 0.5 vCPU and 1 GB memory, Enable CloudWatch logging

### 4. Databases and Caching
- **RDS**: Create a MySQL DB in private subnets, Multi-AZ enabled
- **DynamoDB**: Create a table for cart and session data
- **Redis (ElastiCache)**: Set up for caching product and cart data

### 5. S3 Buckets
- Create two buckets:`globalshop-assets` (for images/files), and `globalshop-logs` (for logs)
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

## How to Test the Stack

After everything is deployed:

- Open the ALB DNS URL in a browser to check if the site is running
- Check CloudWatch logs for ECS to confirm containers are working
- Monitor RDS and ECS metrics for CPU and memory usage
- Try updating the code and pushing to CodeCommit — confirm if the pipeline rebuilds and redeploys the app

---

## How to Destroy the Stack

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
