# AWS Three-Tier Architecture with CI/CD Pipeline

## Project Overview

This project demonstrates how to deploy a full-stack application on AWS using a **Three-Tier Architecture** and an automated CI/CD pipeline.

The infrastructure is hosted inside a custom Amazon VPC with public and private subnets across multiple Availability Zones. The application is divided into three layers:

- **Web Tier:** Uses Nginx to serve frontend application requests.
- **Application Tier:** Uses Node.js to handle application logic.
- **Database Tier:** Uses Amazon RDS MySQL to store application data.

AWS CodePipeline, CodeBuild, and CodeDeploy automate the application build and deployment process whenever new code is pushed to GitHub.

## Architecture
<img width="1536" height="1024" alt="Architecture" src="https://github.com/user-attachments/assets/edc0f7ef-3a44-42e3-b9b2-7c21e47c80b8" />

The architecture includes AWS infrastructure, application hosting, CI/CD automation, monitoring, and security components.

## Project Workflow

1. Developer pushes application code to GitHub.
2. GitHub triggers the AWS CodePipeline.
3. AWS CodeBuild installs dependencies and builds the application.
4. Build artifacts are stored in Amazon S3.
5. AWS CodeDeploy deploys the application to EC2 instances.
6. Nginx serves the frontend application.
7. Node.js handles backend application requests.
8. Amazon RDS MySQL stores application data.
9. Amazon CloudWatch is used for monitoring and logs.

## AWS Services Used

- Amazon VPC
- Amazon EC2
- Public and Private Subnets
- Application Load Balancer
- Auto Scaling Groups
- Amazon RDS MySQL
- Amazon S3
- AWS CodePipeline
- AWS CodeBuild
- AWS CodeDeploy
- AWS IAM
- AWS Systems Manager Parameter Store
- Amazon CloudWatch
- Amazon CloudFront
- Amazon Route 53

## Technologies Used

- **Frontend:** Nginx
- **Backend:** Node.js
- **Database:** MySQL
- **CI/CD:** AWS CodePipeline, CodeBuild, CodeDeploy
- **Source Control:** GitHub
- **Cloud Platform:** AWS

## Key Features

- Three-tier application architecture.
- Infrastructure deployed across multiple Availability Zones.
- Public and private subnet separation.
- Automated frontend and backend deployment.
- Load balancing and Auto Scaling.
- Centralized application and build logs.
- Database credentials managed through Parameter Store.
- Build artifacts stored in Amazon S3.

## Learning Outcomes

Through this project, I gained practical experience in:

- Building AWS VPC and subnet configurations.
- Deploying frontend and backend applications on EC2.
- Configuring Application Load Balancers and Auto Scaling.
- Connecting applications with Amazon RDS MySQL.
- Creating automated CI/CD pipelines.
- Managing IAM roles and security groups.
- Using Amazon S3 for build artifact storage.
- Monitoring application logs using CloudWatch.

## Conclusion

This project helped me understand how AWS infrastructure, three-tier application architecture, and CI/CD automation work together.

It provided hands-on experience with AWS DevOps services and automated application deployment in a scalable and highly available environment.

**Technologies:** AWS | GitHub | EC2 | VPC | RDS | S3 | CodePipeline | CodeBuild | CodeDeploy | IAM | CloudWatch | Nginx | Node.js
