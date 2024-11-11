SECURED WORDPRESS DEPLOYMENT ON AWS

Project Overview

In this project, I deployed a secure WordPress site on AWS using Amazon ECS, RDS, VPC,EFS, ACM, Route 53, and other AWS services. 
This setup is designed to ensure high availability, security, and scalability for the WordPress application. 
Let me walk you through the process I followed, step-by-step.

How I Built It

1. Set Up a Custom VPC and Subnets
The first step was to create a custom VPC to control the network for the entire infrastructure.
I configured the VPC to have both public and private subnets:
![vpc csn](https://github.com/user-attachments/assets/edb8edbd-f38d-47c8-80a8-2fe6da4c7491)
![subnet1](https://github.com/user-attachments/assets/48059c67-76f0-49ad-a240-6abc60d219b5)

Public Subnets: Used for internet-facing resources like the Load Balancer.
Private Subnets: Isolated for internal resources, like the RDS database, for security.
Setting up the Internet Gateway and configuring route tables helped me ensure that only the public subnets had internet access,
while the private subnets remained secure and isolated.



2. Security Groups for Controlled Access
To lock down access, I created separate Security Groups for each service:

Web Server Security Group: This allowed HTTP and HTTPS traffic from the internet.
Database Security Group: Configured to only accept MySQL traffic from the ECS tasks associated with my WordPress containers.
This setup helped enforce strict access rules, ensuring that only necessary traffic reached each part of the infrastructure.
![SECURITY GROUP](https://github.com/user-attachments/assets/0e9cf074-01d3-4524-bcfe-55f2fc6aa590)
![sg](https://github.com/user-attachments/assets/1da38942-e447-4c55-a4bd-2912aefd13c6)



3. Deploying WordPress on ECS with Fargate
With the network ready, I moved to deploying WordPress on Amazon ECS.
I created an ECS Cluster and defined a Task Definition for the WordPress container.
Using Fargate allowed me to run the container without worrying about underlying EC2 instances.
![taskDefinition](https://github.com/user-attachments/assets/0dff9184-8b83-4b50-b253-3ad73d451f0e)
![cluster1](https://github.com/user-attachments/assets/88a1d06c-284d-47e3-943c-53757008bbaf)
![taskDefinitionContainer](https://github.com/user-attachments/assets/dcdedd11-c282-4847-8ba7-052f4d54497d)
![task](https://github.com/user-attachments/assets/1cae3a92-1e36-4ebb-8b70-cef3064d2351)

For the container image, I used the official WordPress Docker image. I then configured the task to connect to the RDS database, 
which we’ll set up in the next step, and enabled EFS for shared storage between multiple WordPress containers.



4. Setting Up Amazon EFS for Shared Storage
To enable persistent and scalable storage for WordPress media files, I set up Amazon EFS:
![efs](https://github.com/user-attachments/assets/adb05cb5-489d-49c5-bda0-0a8ef8915770)

EFS File System: This is Configured to allow access from ECS tasks, creating a centralized file store for WordPress uploads and media.
Mount Target: Set up in each private subnet so that ECS tasks could access the EFS file system regardless of which subnet they were running in.
EFS provided a shared, persistent layer for WordPress data, ensuring that uploaded files would be accessible even 
if containers were restarted or scaled across multiple instances.



5. Setting Up Amazon RDS for WordPress Database
To keep the database secure and reliable, I launched an RDS MySQL instance in one of the private subnets. During setup, I made sure to:
![rds](https://github.com/user-attachments/assets/04b6f66c-5a08-4f1d-9d46-99194b216210)

Select a multi-AZ deployment for high availability.
Use encrypted storage for added security.
Configure the RDS security group to only accept inbound traffic from my ECS tasks, ensuring a secure link between WordPress and its database.
After launching the RDS instance, I noted down the endpoint, username, and password, as these details are essential for WordPress configuration.



6. Configuring the Application Load Balancer (ALB)
To manage incoming web traffic, I set up an Application Load Balancer in the public subnet:
![loadbalancer1](https://github.com/user-attachments/assets/f676ad34-d9ec-48ba-a395-edbc6fc0460e)
![abl](https://github.com/user-attachments/assets/ba30da5c-d062-4305-8b50-20edb786cd84)
![alb1](https://github.com/user-attachments/assets/4252108c-cdd0-4854-ac8a-f16024a7b9c0)
![lb](https://github.com/user-attachments/assets/a79d988e-1b96-4189-a8f7-8ab8f2b5c6f6)

The ALB directs traffic to my ECS tasks through Target Groups.
I attached my previously-created Web Server Security Group to the ALB to ensure it could handle HTTP and HTTPS traffic securely.
The ALB improves both the security and scalability of my WordPress setup, allowing it to handle more users and traffic seamlessly.



7. Setting Up ACM and Route 53 for SSL and DNS
To provide a secure connection with SSL/TLS:
![rout53](https://github.com/user-attachments/assets/d2c578ce-3826-4293-9e28-8d52c4060f19)
![cert](https://github.com/user-attachments/assets/2a822cd5-a750-47d0-b93c-887eb80b4788)

I used AWS Certificate Manager (ACM) to request an SSL certificate for my domain, which I had registered through Namecheap. 
ACM provided the certificate at no extra cost, as long as it’s used with AWS services.
With the certificate ready, I configured Route 53 to route traffic to the ALB using the domain name. 
This setup allows users to access the WordPress site securely via HTTPS.



8. Deploying and Testing the WordPress Site
After everything was set up, I tested the WordPress deployment:
![wordpress site](https://github.com/user-attachments/assets/eb1b8c52-bffc-4af6-b049-3c588e23c613)
![Screenshot 2024-11-09 192417](https://github.com/user-attachments/assets/eb8d9547-46a4-4a14-9006-2b5735f9432d)
![secured wordpress](https://github.com/user-attachments/assets/ed53d044-32d7-4a22-aa44-293e28b3c1d5)

I accessed the WordPress setup by going to the Route 53 domain.
The setup wizard prompted me to configure the database settings, where I used the RDS credentials noted earlier.
After completing these steps, I could see the WordPress site live, secured, and ready for use!


Report From One of My Instructor Mr Oluwatosin
![project report](https://github.com/user-attachments/assets/6aba18ba-1c67-4695-9370-3f7d545fcc7c)


Below are some screenshots illustrating key stages of the setup.
![Screenshot 2024-11-09 183806](https://github.com/user-attachments/assets/ad4f3cfb-a529-416f-bbed-1d5b40d7e6e9)
![ROUTE 53 NS in Namecheap @my domain name](https://github.com/user-attachments/assets/3728204a-e87d-44e5-921c-98952e42b5e5)
![R53](https://github.com/user-attachments/assets/dfff7c3d-2cd1-4813-b4c9-87f09cf6aca0)
![loadbalancer1](https://github.com/user-attachments/assets/4c78a9c8-9406-44ee-b15d-88427e3e8703)
![SUBNET](https://github.com/user-attachments/assets/eda950d0-575e-4db9-9c44-7dc217d92482)
![network mapping](https://github.com/user-attachments/assets/45317117-c927-41db-956a-1c0c977a7ba8)
![ACM Certificate Status](https://github.com/user-attachments/assets/1a769171-c840-4aee-a7f6-eedcc4e43489)
![http's](https://github.com/user-attachments/assets/4dee937a-b4f3-4c2c-b373-1d37ee138278)
![VPC](https://github.com/user-attachments/assets/79455746-d875-4a7c-9fc8-8172a2849873)
![CLUSTER2](https://github.com/user-attachments/assets/76257a93-3fe3-4d9d-bb3a-5edfe9ed7cfb)

This README should guide you through my experience and make it easy for others to replicate or build on this secure WordPress deployment on AWS.
