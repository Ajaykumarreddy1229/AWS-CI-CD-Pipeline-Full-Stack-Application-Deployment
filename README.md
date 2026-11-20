# AWS CI/CD Pipeline – Full-Stack Application Deployment Using AWS CodeBuild, CodeDeploy & CodePipeline

## 1. Project Overview

This project demonstrates how to deploy a full-stack application on AWS using a three-tier architecture and automated CI/CD pipelines.

The main objective of this project is to automate the build and deployment process for frontend and backend applications using AWS DevOps services.

The infrastructure is created inside a custom Amazon VPC with public and private subnets. The Web Tier, Application Tier, and Database Tier are separated to provide better security, scalability, and availability.

AWS CodePipeline, CodeBuild, and CodeDeploy are used to automate the application deployment process whenever new code is pushed to GitHub.

### Project Highlights

- Designed a three-tier architecture using AWS services.
- Created a custom VPC with public and private subnets.
- Configured Internal and External Application Load Balancers.
- Implemented Auto Scaling Groups for Web and Application servers.
- Configured Amazon RDS MySQL for database management.
- Used AWS Systems Manager Parameter Store for application configuration and credentials.
- Created separate CI/CD pipelines for frontend and backend.
- Automated application builds using AWS CodeBuild.
- Automated deployments using AWS CodeDeploy.
- Managed the complete CI/CD workflow using AWS CodePipeline.
- Used Amazon S3 to store build artifacts.

---

## 2. AWS Services Used

| AWS Service | Purpose |
|---|---|
| Amazon VPC | Creates an isolated network environment |
| Public Subnets | Hosts public-facing resources |
| Private Subnets | Hosts Web, Application, and Database resources |
| Internet Gateway | Provides internet connectivity |
| NAT Gateway | Provides outbound internet access to private resources |
| Amazon EC2 | Hosts application and web servers |
| Security Groups | Controls inbound and outbound traffic |
| AWS IAM | Manages permissions and roles |
| Amazon S3 | Stores build artifacts |
| Amazon RDS MySQL | Database management |
| Application Load Balancer | Distributes application traffic |
| Auto Scaling Groups | Automatically manages EC2 capacity |
| AWS CodeBuild | Builds application code |
| AWS CodeDeploy | Deploys application code |
| AWS CodePipeline | Automates CI/CD workflows |
| Systems Manager Parameter Store | Stores application configuration |
| Amazon CloudWatch | Monitors application logs |
| Amazon CloudFront | Content delivery |
| Amazon Route 53 | DNS management |

---

## 3. Three-Tier Architecture

The application infrastructure is divided into three separate tiers.

### 3.1 Web / Presentation Tier

The Web Tier is responsible for receiving user requests and forwarding them to the Application Tier.

Components:

- Amazon EC2
- Nginx Web Server
- External Application Load Balancer
- Auto Scaling Group
- Private Web Subnets

Configuration:

- Created a Launch Template for Web Servers.
- Used Amazon Linux 2023.
- Installed and configured Nginx using User Data.
- Created an empty Target Group.
- Configured an Internet-Facing Application Load Balancer.
- Created an Auto Scaling Group across two Availability Zones.
- Configured CPU-based target tracking scaling.

### 3.2 Application Tier

The Application Tier handles application logic and communicates with the database.

Components:

- Amazon EC2
- Node.js Application
- Internal Application Load Balancer
- Auto Scaling Group
- Private Application Subnets

Configuration:

- Created a Launch Template for Application Servers.
- Used Amazon Linux 2023.
- Configured the application using User Data.
- Created an empty Target Group.
- Configured an Internal Application Load Balancer.
- Created an Auto Scaling Group across two Availability Zones.
- Configured application health checks.
- Configured CPU-based target tracking scaling.

### 3.3 Database Tier

The Database Tier stores application data.

Components:

- Amazon RDS
- MySQL
- Private Database Subnets
- Database Security Group

Configuration:

- Created an RDS Subnet Group.
- Selected database subnets across two Availability Zones.
- Created an Amazon RDS MySQL instance.
- Disabled public accessibility.
- Enabled database encryption.
- Configured Security Groups to allow database access from the Application Tier.

---

## 4. VPC and Network Configuration

Created a custom VPC named `3-tier-project`.

**VPC Configuration:**

- VPC CIDR: `192.168.0.0/16`
- Tenancy: Default
- DNS Hostnames: Enabled

### Subnet Configuration

Created two public subnets and six private subnets.

| Subnet | CIDR Range | Purpose |
|---|---|---|
| Public Subnet 1 | 192.168.1.0/24 | Public resources |
| Public Subnet 2 | 192.168.2.0/24 | Public resources |
| Web Subnet 1 | 192.168.3.0/24 | Web Tier |
| Web Subnet 2 | 192.168.4.0/24 | Web Tier |
| App Subnet 1 | 192.168.5.0/24 | Application Tier |
| App Subnet 2 | 192.168.6.0/24 | Application Tier |
| DB Subnet 1 | 192.168.7.0/24 | Database Tier |
| DB Subnet 2 | 192.168.8.0/24 | Database Tier |

### Network Components

**Internet Gateway:**

- Created an Internet Gateway.
- Attached it to the custom VPC.
- Connected the public route table to the Internet Gateway.

**NAT Gateway:**

- Created a NAT Gateway in Public Subnet 1.
- Allocated an Elastic IP.
- Configured the private route table to use the NAT Gateway.

**Route Tables:**

- Public Route Table: Associated with both public subnets.
- Private Route Table: Associated with all six private subnets.

---

## 5. Security Group Configuration

Created separate Security Groups to control communication between infrastructure components.

| Security Group | Access Configuration |
|---|---|
| Bastion-SG | SSH access |
| WebALB-SG | HTTP and HTTPS |
| Web-SG | HTTP/HTTPS from Web ALB and SSH from Bastion |
| AppALB-SG | HTTP from Web Tier |
| App-SG | Application port 3200 and SSH from Bastion |
| Database-SG | MySQL access from Application Tier |

Security Groups are used to control communication between the Web Tier, Application Tier, and Database Tier.

---

## 6. Bastion Host Configuration

Created a Bastion Host to access private EC2 instances.

Configuration:

- Instance Type: t3.micro
- Operating System: Amazon Linux 2023
- Subnet: Public Subnet
- Security Group: Bastion-SG

The Bastion Host acts as a Jump Server to connect to private servers.

---

## 7. IAM Roles Configuration

Created IAM roles for AWS services to access the required resources.

### EC2 IAM Role

Role Name: `3-tier-ec2-role`

Used for EC2 instances and required AWS service permissions.

### CodeBuild IAM Role

Role Name: `3-tier-codebuild-role`

Used by AWS CodeBuild to access:

- Amazon S3
- AWS Systems Manager Parameter Store
- Amazon CloudWatch Logs

### CodeDeploy IAM Role

Role Name: `3-tier-codedeploy-role`

Used by AWS CodeDeploy to perform application deployment operations.

The project also uses a CodePipeline service role to manage pipeline operations.

---

## 8. Amazon RDS MySQL Configuration

Created an Amazon RDS MySQL database for the application.

Configuration:

- Engine: MySQL
- Database Identifier: dev-db-instance
- VPC: Custom VPC
- DB Subnet Group: tier-Subnet-Group
- Availability Zones: Two
- Public Accessibility: Disabled
- Encryption: Enabled
- Security Group: Database-SG

### Database Connectivity

Installed the MySQL-compatible client on the Bastion Host.

```bash
sudo dnf install mariadb105 -y
```

Connected to the RDS database using:

```bash
mysql -h <RDS-ENDPOINT> -u <DB-USERNAME> -p
```

Database and table configuration was performed using the project backend database files.

---

## 9. Application Tier Configuration

Created the infrastructure required to run the backend application.

### Launch Template

Name: `application-tier-LT`

Configuration:

- AMI: Amazon Linux 2023
- Instance Type: m7i-flex.large
- Security Group: App-SG
- IAM Role: 3-tier-ec2-role
- User Data: Application Tier User Data Script

### Target Group

Name: `app-tier-tg`

Configuration:

- Target Type: Instance
- Protocol: HTTP
- Port: 3200
- Health Check: `/health`

### Internal Application Load Balancer

Name: `app-tier-internal-alb`

Configuration:

- Scheme: Internal
- Availability Zones: Two
- Subnets: Private Application Subnets
- Listener: HTTP Port 80
- Target Group: app-tier-tg

### Application Auto Scaling Group

Name: `application-tier-ASG`

Configuration:

- Launch Template: application-tier-LT
- Desired Capacity: 2
- Minimum Capacity: 2
- Maximum Capacity: 4
- Availability Zones: Two
- Health Checks: EC2 and Elastic Load Balancing
- Scaling Policy: Target Tracking
- CPU Target: 50%

---

## 10. Web Tier Configuration

Created the infrastructure required to serve the frontend application through Nginx.

### Launch Template

Name: `web-tier-LT`

Configuration:

- AMI: Amazon Linux 2023
- Instance Type: t3.micro
- Security Group: Web-SG
- IAM Role: 3-tier-ec2-role
- User Data: Web Tier User Data Script
- Web Server: Nginx

### Target Group

Name: `web-tier-tg`

Configuration:

- Target Type: Instance
- Protocol: HTTP
- Port: 80
- Health Check: `/health`

### External Application Load Balancer

Name: `web-tier-external-alb`

Configuration:

- Scheme: Internet-Facing
- Availability Zones: Two
- Subnets: Public Subnets
- Listener: HTTP Port 80
- Target Group: web-tier-tg

### Web Auto Scaling Group

Name: `web-tier-ASG`

Configuration:

- Launch Template: web-tier-LT
- Desired Capacity: 2
- Minimum Capacity: 2
- Maximum Capacity: 4
- Availability Zones: Two
- Scaling Policy: Target Tracking
- CPU Target: 50%

---

## 11. AWS Systems Manager Parameter Store

Used AWS Systems Manager Parameter Store to manage application configuration and database credentials.

Created the following parameters:

| Parameter Name | Purpose |
|---|---|
| `/nodeapp/db/hostname` | Database hostname |
| `/nodeapp/db/name` | Database name |
| `/nodeapp/db/password` | Database password |
| `/nodeapp/db/port` | Database port |
| `/nodeapp/db/user` | Database username |

These parameters are referenced in the backend `buildspec.yml` file.

Example:

```yaml
parameter-store:
  DB_PASSWORD: "/nodeapp/db/password"
  DB_HOST: "/nodeapp/db/hostname"
  DB_PORT: "/nodeapp/db/port"
  DB_USER: "/nodeapp/db/user"
  DB_NAME: "/nodeapp/db/name"
```

This avoids directly writing database credentials inside the application build configuration.

For sensitive values, SecureString parameters should be used.

---

## 12. Amazon S3 Artifact Bucket

Created a private S3 bucket to store application build artifacts.

Bucket Name:

`3-tier-project-codebuild-artifacts`

Purpose:

- Stores build artifacts.
- Provides artifact storage for CI/CD pipelines.
- Allows CodePipeline to pass build outputs between pipeline stages.

---

# 13. CI/CD Pipeline Implementation

Configured separate CI/CD pipelines for the frontend and backend applications.

The project uses three important AWS DevOps services:

### AWS CodeBuild

CodeBuild automatically builds the application using the instructions defined in `buildspec.yml`.

### AWS CodeDeploy

CodeDeploy automates application deployment to EC2 instances.

### AWS CodePipeline

CodePipeline connects source, build, and deployment stages into an automated workflow.

---

## 14. Backend CI/CD Pipeline

### Step 1: GitHub Source Configuration

Connected the GitHub repository to AWS CodeBuild using a GitHub connection.

Configured:

- GitHub Connection: githubconnection
- Repository: Backend Application Repository
- Branch: main
- Webhook Event: Push

Whenever code is pushed to the configured branch, the build process can start automatically.

### Step 2: Backend CodeBuild Configuration

Created a CodeBuild project.

Project Name:

`backend-build`

Configuration:

- Source: GitHub
- Service Role: 3-tier-codebuild-role
- Build Specification: `backend/buildspec.yml`
- CloudWatch Log Group: `codebuild-logs-backend`

The buildspec file defines the backend application build process.

### Step 3: Backend CodeDeploy Configuration

Created a CodeDeploy application.

Application Name:

`backend-deploy`

Deployment Group:

`backend-Deployment-grp`

Configuration:

- Compute Platform: EC2
- Deployment Type: In-Place
- Environment: application-tier-ASG
- Deployment Target Group: app-tier-tg

CodeDeploy handles application deployment to the backend EC2 instances.

### Step 4: Backend CodePipeline Configuration

Created the backend pipeline.

Pipeline Name:

`Backend-Pipeline`

Stages:

1. Source – GitHub
2. Build – AWS CodeBuild
3. Deploy – AWS CodeDeploy

Configuration:

- GitHub Connection: githubconnection
- Branch: main
- Build Project: backend-build
- Deploy Application: backend-deploy

---

## 15. Frontend CI/CD Pipeline

### Step 1: Frontend CodeBuild Configuration

Created a CodeBuild project.

Project Name:

`frontend-build`

Configuration:

- Source: GitHub
- Service Role: 3-tier-codebuild-role
- Build Specification: `frontend/buildspec.yml`
- CloudWatch Log Group: `codebuild-logs-frontend`

The buildspec file defines the frontend application build process.

### Step 2: Frontend CodeDeploy Configuration

Created a CodeDeploy application.

Application Name:

`frontend-deploy`

Deployment Group:

`frontend-Deployment-grp`

Configuration:

- Compute Platform: EC2
- Deployment Type: In-Place
- Environment: web-tier-ASG
- Deployment Target Group: web-tier-tg

### Step 3: Frontend CodePipeline Configuration

Created the frontend pipeline.

Pipeline Name:

`frontend-Pipeline`

Stages:

1. Source – GitHub
2. Build – AWS CodeBuild
3. Deploy – AWS CodeDeploy

Configuration:

- GitHub Connection: githubconnection
- Branch: main
- Build Project: frontend-build
- Deploy Application: frontend-deploy

---

## 16. Important CI/CD Configuration Files

### buildspec.yml

The `buildspec.yml` file is used by AWS CodeBuild.

It defines:

- Installation of dependencies.
- Build commands.
- Test commands.
- Artifact generation.
- Build output configuration.

### appspec.yml

The `appspec.yml` file is used by AWS CodeDeploy.

It defines:

- Application deployment instructions.
- File copying.
- Deployment lifecycle hooks.
- Application installation and configuration steps.

Both files are important for automating the application build and deployment process.

---

## 17. Monitoring and Logging

Used Amazon CloudWatch to monitor application and infrastructure logs.

Configured CloudWatch Log Groups:

- `codebuild-logs-backend`
- `codebuild-logs-frontend`
- `node-app-logs-backend`

CloudWatch helps to:

- Monitor build logs.
- Troubleshoot deployment issues.
- Check application logs.
- Identify errors during execution.

---

## 18. HTTPS, CloudFront and Route 53

The final project steps include:

### HTTPS
Configure secure HTTPS communication for the application.

### Amazon CloudFront
Configure content delivery using CloudFront.

### Amazon Route 53
Configure DNS records to connect the application domain to the required AWS resources.

---

## 19. Complete Application Workflow

The overall application workflow is:

1. Developer pushes code to GitHub.
2. GitHub webhook detects the code change.
3. AWS CodePipeline starts the pipeline.
4. AWS CodeBuild builds the application.
5. Build artifacts are stored in Amazon S3.
6. AWS CodeDeploy deploys the application.
7. Backend application runs on Application Tier EC2 instances.
8. Frontend application runs on Web Tier EC2 instances.
9. Internal and External Load Balancers distribute traffic.
10. Backend application communicates with Amazon RDS MySQL.
11. Amazon CloudWatch collects logs.

---

## 20. Project Learning Outcomes

Through this project, I gained practical knowledge of:

- AWS networking and VPC configuration.
- Public and private subnet architecture.
- IAM roles and Security Groups.
- EC2 Launch Templates and Auto Scaling Groups.
- Internal and External Application Load Balancers.
- Amazon RDS MySQL configuration.
- AWS Systems Manager Parameter Store.
- Amazon S3 artifact management.
- AWS CodeBuild build automation.
- AWS CodeDeploy deployment automation.
- AWS CodePipeline workflow automation.
- GitHub integration with AWS.
- CloudWatch logging and monitoring.
- Three-tier application deployment.

---

## 21. Conclusion

This project demonstrates how to deploy a full-stack application using AWS infrastructure and automated CI/CD pipelines.

By combining Amazon VPC, EC2, RDS, Load Balancers, Auto Scaling, S3, CodeBuild, CodeDeploy, and CodePipeline, the project provides practical experience in building and managing an automated application deployment environment on AWS.

It helped me understand how infrastructure configuration, application building, artifact management, and deployment automation work together in a DevOps environment.

**Technologies:** AWS | GitHub | EC2 | VPC | IAM | S3 | RDS MySQL | ALB | Auto Scaling | CodeBuild | CodeDeploy | CodePipeline | Systems Manager | CloudWatch | Nginx | CloudFront | Route 53
