# 🌐 GlobalShop – AWS Stack Deployment, Testing, and Destroy Guide

1) ✅ How to Deploy the Stack?
# GlobalShop – AWS Stack Deployment, Testing, and Destroy Guide
1) How to Deploy the Stack?
   
The setup of the networking environment should be started in order to implement the GlobalShop application on AWS. This involves the establishment of Virtual Private Cloud (VPC) of 10.0.0.0/16CIDR, two public subnets thereof and two private subnets on varied availability zones (e.g. us-east-1a and us-east-2b). Internet Gateway is then created and connected to the VPC so that the internet could be accessed by public subnets, and a NAT Gateway is inserted in one of the subnets in the public subnets so that resources in the private subnets can access the internet. In that presence since the public traffic would come in through the Internet Gateway and the private through the NAT Gateway, route tables would be configured in this manner.

After installation of networking, one creates security groups next. The Application Load Balancer (ALB) will be added with the one security group to permit internet traffic through ports 80 and 443. The other security group is utilized in ECS activities and it permits traffic to be ingress through the ALB. The RDS MySQL database is secured by a third security group and opens the traffic only to ECS over port 3306.

After establishing the security, an ECS Cluster on the AWS Fargate is created, so that we could be able to run a container on the AWS without managing the servers. A task definition is developed which specifies the necessary CPU and memory (e.g. 0.5 vCPU and 1 GB RAM), the container image and port bindings (usually port 80). It is configured through monitoring with CloudWatch enabling the container activity and performance to be captured. It will also be provided the ECS service in the private subnets and connected to the Application Load Balancer (ALB) to enable the provision of load balancing among the running containers. The ALB is set to have listeners on ports 80 and 443 in order to respond to incoming HTTP and HTTPS requests. These listeners are made to make smart decisions and transfer and redirect heavy traffic to several ECS tasks assigned in various Availability Zones. This makes sure the application is able to handle more load easily with a good availability and performance without any downtime.

Then, the backend is configured by provisioning an RDS MySQL database in isolated subnets, and Multi-AZ deployment is implemented to provide server availability and encryption is activated. A DynamoDB table is also created to save session or cart data also and we set ElastiCache (Redis) to store data that is frequently hit like all the product data and it has a better performance because of setting up ElastiCache. The application assets and application logs are stored in S3 buckets and the storage costs are controlled by encryption and lifecycle policies.

The last deployment stage is deployment of CI/CD pipeline. The source code repository entitled as a CodeCommit is created to hosts the source code, then, the building of the Docker image is invoked as a CodeBuild project. A CodePipeline is set up to automatically push new deployments to repository whenever the code is pushed. This architecture makes sure uninterrupted provision of application updates to ECS.

---

2) 🧪 How to Test the Stack?

Testing the deployed AWS stack involves several key checks. First, the Application Load Balancer’s DNS URL should be opened in a web browser to confirm that the GlobalShop application is running and accessible to users. The ECS console is checked to verify that tasks are running without issues and are placed in the correct subnets. CloudWatch logs are then reviewed to ensure that application logs are being collected properly, which helps with debugging and monitoring.

Such services as ECS, ALB, and RDS should be tracked according to the metrics of the CPU usage, the amount of memory used, and the number of requests made. The load balancer must have healths checks that are passing, which means that the application is responsive. Besides, one can make a trivial modification in the code and push it to the CodeCommit repository to check the CI/CD configuration. This must initiate the codePipeline to reconstruct the image and redeploying the service ensuring that the automation is running properly.

---
3) 🧹 How to Destroy the stack?

When the deployment is no longer needed, the stack should be destroyed carefully to avoid unnecessary AWS charges. The teardown process begins by deleting the ECS service and the ECS cluster itself. Next, the Application Load Balancer and its associated target group and listeners should be removed. After that, backend services like the RDS database instance, DynamoDB table, and ElastiCache Redis cluster must be deleted.

The CI/CD elements like CodePipeline, CodeBuild project and CodeCommit repository should also be deleted. The emptying of the S3 buckets before deleting them is necessary because AWS does not permit deletion of the non-empty buckets. After deleting the services, the last part is cleaning of the networking resources which include NAT Gateway, Internet Gateway, subnets, route tables and eventually VPC.

It is also suggested to remove all the existing CloudWatch log groups and alarms that are not maintained, but this step is optional. Upon doing this, all the resources will be severed securely and that new charges on idle infrastructure will not be charged.
