## _Introduction_

This guide provides a comprehensive walkthrough for deploying a secure web application using AWS services. The focus is on setting up a robust infrastructure that integrates Amazon Route 53, Elastic Load Balancer (ALB), 
AWS Certificate Manager (ACM), and EC2 instances. By the end of this guide, your application will be accessible over HTTPS with automatic redirection from HTTP, ensuring secure communication for end-users.

We will begin by creating and configuring a custom Virtual Private Cloud (VPC) with subnets, an internet gateway, and a route table. This foundational setup ensures the application is securely hosted within AWS. Next, we’ll launch an EC2 instance, 
set up an application load balancer, and integrate DNS resolution via Route 53. Finally, we'll use AWS Certificate Manager to provision an SSL/TLS certificate for enabling HTTPS traffic.

## _Why AWS_

Amazon Web Services (AWS) is one of the leading cloud platforms, offering a wide range of services to build, deploy, and scale applications. It provides robust security, scalability, and flexibility, making it the ideal choice 
for hosting modern applications. With a global presence of data centers, AWS ensures low latency and high availability for your applications.

Key benefits of using AWS include:

1) Ease of Use: AWS simplifies infrastructure management with intuitive tools and interfaces.
2) Scalability: AWS services can scale automatically to handle traffic spikes or drops.
3) Cost Efficiency: Pay-as-you-go pricing ensures you only pay for the resources you use.
4) Security: Built-in security features like IAM, VPCs, and ACM help protect your data and applications.

## _What You Will Achieve_

By following this guide, you will:

1) Create and configure a VPC with public subnets for hosting resources.
2) Set up an application load balancer to distribute traffic across EC2 instances.
3) Integrate Route 53 for custom domain DNS resolution.
4) Secure your application using SSL/TLS certificates via AWS Certificate Manager.
5) Enable HTTPS traffic with redirection from HTTP for a secure browsing experience.

This process ensures your application is not only functional but also adheres to industry best practices for security and reliability.

## _Prerequisites_

There aren't many requirements when you look into the implementation of this projection. You just need to make sure of the following:

1) Must have an AWS account with a valid payment method.
2) Have basic knowledge of networking, Linux system administration, basic knowledge of AWS architecture/flow and a domain (could be from google/cloudflare/namecheap etc).
3) PC/Laptop and an internet connection.

## _Architectural Flow_

This diagram represents a secure AWS architecture where an end user accesses the application via Route 53 (DNS). Traffic is routed to an Application Load Balancer, which distributes it across EC2 instances hosted in a public subnet within a VPC. An Internet Gateway and Route Table facilitate internet connectivity for the application.

![AWS-ACM Architecture](https://github.com/user-attachments/assets/06fb083d-206c-4787-a4e0-e0d42389160a)


