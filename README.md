#Global-Shop - Deployment Guide
This README file explains how to set up the GlobalShop e-commerce platform using AWS services like VPC, ECS, RDS, S3, ElastiCache, DynamoDB, Load Balancer, and CI/CD tools. It also covers how to test the setup and safely destroy the resources when you're done.
--
#How to Deploy the stack?
-First create the VPC with CIDR 10.0.2.0/24
-Create two public subnets: 10.0.1.0/24 and 10.0.2.0/24
-create two private subnets 10.0.3.0/24 and 10.0.4.0/24
-create and attach an Internet gateway
-create a NAT Gateway in one of the public subnets
-update route tables accordingly for internet and private access

#create security groups
