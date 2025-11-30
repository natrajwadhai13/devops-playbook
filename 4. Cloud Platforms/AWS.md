---
title: "• AWS"
parent: "4. Cloud Platforms"
nav_order: 1
has_children: true
---


# **AWS Course Content for DevOps Engineer**

---

## **📘 AWS Course Syllabus Table**

| **Section**  | **Topic**                                   | **Detailed Contents**                                                                                                                                                                                                              |
| ------------ | ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1**        | **AWS Fundamentals**                        | • AWS Global Infrastructure (Regions, AZs) <br> • IAM Users, Groups, Roles, Policies <br> • AWS CLI <br> • Billing, Budgets, Pricing Models <br> • Well-Architected Framework                                                      |
| **Hands-On** |                                             | • Create IAM Users & Policies <br> • Setup Billing Alerts <br> • Install & Configure AWS CLI                                                                                                                                       |
| **2**        | **EC2 (Compute)**                           | • EC2 Types, AMI, Security Groups <br> • EBS Volumes, Snapshots <br> • Key Pairs <br> • Placement Groups <br> • Load Balancers (ALB, NLB) <br> • Auto Scaling Group                                                                |
| **Hands-On** |                                             | • Launch Linux/Windows EC2 <br> • Create AMI <br> • Attach EBS <br> • Setup ALB + ASG (Web App)**                                                                                                                                  |
| **3**        | **VPC & Networking**                        | • VPC, Public/Private Subnets <br> • Route Tables <br> • Internet Gateway, NAT Gateway <br> • NACLs vs Security Groups <br> • VPC Peering <br> • Transit Gateway <br> • VPC Endpoints (S3, DynamoDB, Interface) <br> • PrivateLink |
| **Hands-On** |                                             | • Create Custom VPC <br> • Deploy EC2 in Public/Private Subnets <br> • NAT Gateway Setup <br> • VPC Peering Lab <br> • S3 VPC Endpoint Setup                                                                                       |
| **4**        | **S3 (Storage Services)**                   | • S3 Buckets <br> • Versioning <br> • Lifecycle Policies <br> • Encryption (SSE-S3, SSE-KMS) <br> • Cross-Region Replication <br> • S3 Static Website Hosting <br> • S3 Security (Policies, ACL)                                   |
| **Hands-On** |                                             | • Create S3 Bucket <br> • Enable Versioning <br> • Lifecycle Rule Lab <br> • Website Hosting on S3                                                                                                                                 |
| **5**        | **CloudFront & CDN**                        | • CDN Concepts <br> • CloudFront Distributions <br> • Origin Access Control (OAC) <br> • Cache Behavior <br> • S3 + CloudFront Website                                                                                             |
| **Hands-On** |                                             | • Setup CloudFront CDN for S3 Website                                                                                                                                                                                              |
| **6**        | **RDS, DynamoDB, ElastiCache**              | • RDS (MySQL, Postgres) <br> • Backups, Multi-AZ <br> • Read Replicas <br> • DynamoDB Basics <br> • ElastiCache (Redis/Memcached)                                                                                                  |
| **Hands-On** |                                             | • Deploy MySQL RDS <br> • Connect EC2 to RDS <br> • DynamoDB Demo                                                                                                                                                                  |
| **7**        | **Elastic Beanstalk & Application Hosting** | • EB Concepts <br> • Deployment Policies <br> • Environments <br> • CloudFormation (Intro)                                                                                                                                         |
| **Hands-On** |                                             | • Deploy a Web App using Beanstalk <br> • WordPress Setup on EC2 <br> • LAMP Stack Installation                                                                                                                                    |
| **8**        | **ECS, ECR, Docker (DevOps Core)**          | • Docker Images & Containers <br> • ECR Repository <br> • ECS Cluster (EC2 & Fargate) <br> • Task Definitions <br> • Autoscaling in ECS                                                                                            |
| **Hands-On** |                                             | • Push Docker Image to ECR <br> • Deploy Container App using ECS                                                                                                                                                                   |
| **9**        | **CI/CD – DevOps on AWS**                   | • CodeCommit <br> • CodeBuild <br> • CodeDeploy <br> • CodePipeline <br> • Blue/Green Deployments                                                                                                                                  |
| **Hands-On** |                                             | • CI/CD Pipeline for EC2 <br> • CI/CD Pipeline for ECS                                                                                                                                                                             |
| **10**       | **Monitoring & Logging**                    | • CloudWatch Metrics <br> • CloudWatch Logs <br> • CloudWatch Alarms <br> • CloudTrail <br> • VPC Flow Logs                                                                                                                        |
| **Hands-On** |                                             | • Create CloudWatch Alarms <br> • Enable CloudTrail Logs                                                                                                                                                                           |
| **11**       | **Security & IAM Advanced**                 | • KMS <br> • Secret Manager <br> • Parameter Store <br> • Shield, WAF <br> • GuardDuty <br> • Inspector                                                                                                                            |
| **Hands-On** |                                             | • Create KMS Keys <br> • Secret Manager for DB Passwords                                                                                                                                                                           |
| **12**       | **Migration & Hybrid Services**             | • AWS Migration Hub <br> • DMS (Database Migration) <br> • Snowball <br> • Direct Connect                                                                                                                                          |
| **Hands-On** |                                             | • DMS Sample Migration Lab                                                                                                                                                                                                         |
| **13**       | **Serverless (DevOps Scope)**               | • AWS Lambda <br> • API Gateway <br> • Step Functions <br> • EventBridge                                                                                                                                                           |
| **Hands-On** |                                             | • Lambda Trigger from S3 <br> • API Gateway + Lambda                                                                                                                                                                               |
| **14**       | **Cost Optimization (Important)**           | • AWS Cost Explorer <br> • Budgets <br> • Saving Plans & Reserved Instances                                                                                                                                                        |
| **Hands-On** |                                             | • Setup Cost Alerts                                                                                                                                                                                                                |
| **15**       | **Interview & Practical Scenarios**         | • Real-time AWS Architecture Q&A <br> • DevOps case studies (ECS, EKS, CI/CD, VPC) <br> • Troubleshooting Scenarios                                                                                                                |

---

=========================
# AWS Course Content Table (Updated With Diagrams)


| Module                                    | Topics                                                  | Architecture Diagram                                                                                                           |
| ----------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **1. AWS Basics**                         | AWS Global Infrastructure, Regions, AZs, Edge Locations | ![AWS Global Infra](https://d1.awsstatic.com/global-infrastructure/GlobalInfra.4d7d2d8fddbfd4eaf4a4e0fb89e21793e7c8e1b4.png)   |
| **2. IAM (Identity & Access Management)** | Users, Groups, Roles, Policies, MFA, Permissions        | ![IAM Architecture](https://d1.awsstatic.com/diagrams/iam-how-it-works.d3f2b3a2f.k.png)                                        |
| **3. VPC Networking**                     | VPC, Subnets, Route Tables, IGW, NAT, SG, NACL          | ![VPC Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/vpc-basic-architecture.7781a96ddc.png) |
| **4. VPC Peering**                        | Peering Setup, Routing, Cross VPC Access                | ![VPC Peering](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/vpc-to-vpc-peering.32.png)                  |
| **5. EC2**                                | Launch EC2, AMI, Key Pairs, EBS, Snapshots, ENI         | ![EC2 Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/ec2-instance.3.png)                    |
| **6. Load Balancing (ELB)**               | ALB, NLB, Target Groups, Health Checks                  | ![ALB Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/elastic-load-balancing.99.png)         |
| **7. Auto Scaling**                       | Launch Templates, Desired/Min/Max Capacity              | ![Auto Scaling](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/auto-scaling.22.png)                       |
| **8. S3 & CloudFront**                    | S3 Buckets, Versioning, Lifecycle, CDN                  | ![S3 Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-s3.44.png)                       |
| **9. RDS & Aurora**                       | RDS Instances, Multi-AZ, Backups                        | ![RDS Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-rds.33.png)                     |
| **10. DynamoDB**                          | NoSQL, GSI, LSI, Streams                                | ![DynamoDB Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-dynamodb.88.png)           |
| **11. ECR & ECS (DevOps)**                | ECS Fargate, EC2 Mode, Task Definitions                 | ![ECS Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-ecs.32.png)                     |
| **12. EKS (Kubernetes)**                  | Cluster, Nodes, Pods, Ingress, ALB                      | ![EKS Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-eks.39k.png)                    |
| **13. Lambda (Serverless)**               | Functions, Invocation Types, Layers                     | ![Lambda Architecture](https://d1.awsstatic.com/serverless/diagrams/serverless-what-is-lambda.15.png)                          |
| **14. CloudWatch**                        | Logs, Metrics, Alarms, Dashboards                       | ![CloudWatch Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-cloudwatch.77.png)       |
| **15. CloudTrail**                        | API Logging, Security Analysis                          | ![CloudTrail Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/aws-cloudtrail.55.png)          |
| **16. SNS & SQS**                         | Queues, Topics, Pub/Sub                                 | ![SNS SQS Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/amazon-sns.88.png)                 |
| **17. CodeCommit**                        | Private Git Repository                                  | ![CodeCommit Architecture](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/aws-codecommit.45.png)          |
| **18. CodeBuild**                         | CI Build Process                                        | ![CodeBuild](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/aws-codebuild.31.png)                         |
| **19. CodeDeploy**                        | EC2/On-Prem/Blue-Green Deployments                      | ![CodeDeploy Diagram](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/aws-codedeploy.44.png)               |
| **20. CodePipeline**                      | CI/CD Pipeline Setup                                    | ![CodePipeline Diagram](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/aws-codepipeline.67.png)           |


====================
# AWS Course Content

| **Section** | **Topic Title** | **Subtopics / Lessons** |
|------------|------------------|--------------------------|
| **1. Introduction to AWS & Cloud Computing** | 1. Introduction to Course Content | |
| | 2. What is Cloud Computing (Overview) | |
| | 3. AWS Important Services (Must Learn) | |
| | 4. How to Create Free Tier AWS Account | |
| | 5. AWS Global Architecture (Regions, AZs, Edge Locations) | |
| **2. EC2 – Virtual Servers & Website Hosting** | Introduction to EC2 Instance Types | |
| | EC2 Pricing | |
| | Launch Your First Linux EC2 Server | |
| | Connect Linux Server via PuTTY | |
| | Launch Windows Server & Connect | |
| | Take Backup & Restore in AWS | |
| | Amazon Machine Images (AMI) | |
| | Launch Multiple Servers | |
| | AWS EFS (Elastic File System) | |
| | AWS LifeCycle Manager (Backup Automation) | |
| | Practice Assignment – Configure ELB | |
| | Elastic Load Balancer (ELB) Concepts | |
| | Create Template & Launch Instance | |
| **3. EBS – Elastic Block Storage** | What are EBS Volumes | |
| | Snapshots & Backup | |
| | Volume Types | |
| **4. EC2 – Load Balancing & Auto Scaling** | Load Balancing & Auto Scaling – How It Works | |
| | LAB – Setup Load Balancer & Auto Scaling | |
| | EC2 & EBS Summary + Exam Tips | |
| **5. Route 53 – DNS & Website Hosting** | Route 53 Concepts | |
| | Route 53 Service – Part 2 | |
| | Best Practices & Costs | |
| **6. VPC – Isolated Network in AWS** | VPC & Subnets | |
| | Route Tables | |
| | Internet Gateway | |
| | NAT Gateway | |
| | Virtual Private Gateway | |
| | Elastic IPs | |
| | VPC Peering | |
| | Network ACLs | |
| | Security Groups | |
| | DHCP Options | |
| | Endpoints | |
| | Endpoint Services | |
| | Site-to-Site VPN | |
| | Introduction to VPC Components | |
| | LAB – Create VPC & Launch EC2 | |
| | Internet Gateway, NAT Gateway, NAT Instance | |
| | VPC Important Terms + Exam Tips | |
| **7. S3 – Simple Storage Service** | S3 Introduction | |
| | S3 Cross Region Replication | |
| | Versioning Lab | |
| | Bucket Policy | |
| | Static Website Hosting | |
| | Lifecycle Management | |
| | CloudFront & CDNs | |
| | Security & Encryption | |
| | Snowball, Import/Export | |
| | S3 Best Practices | |
| **8. IAM – Identity & Access Management** | IAM Overview | |
| | Users, Groups, Roles | |
| | MFA – Multi-Factor Authentication | |
| **9. CloudWatch – Monitoring Service** | CloudWatch LAB (Alarm, Events, Billing Alerts) | |
| **10. Databases – RDS, DynamoDB, ElastiCache** | RDS Setup & MySQL Lab | |
| | DynamoDB Overview & Demo | |
| | ElastiCache Demo | |
| **11. Application Services** | CloudFront Introduction | |
| | CloudFront LAB | |
| | Elastic Beanstalk LAB | |
| | CloudFormation LAB | |
| | Install WordPress on EC2 | |
| | Install LAMP on EC2 | |
| **12. Messaging Services** | SNS – Simple Notification Service | |
| | SES – Simple Email Service | |
| | WorkMail & WorkDocs | |
| **13. Migration Services** | Direct Connect | |
| | AWS Snowball | |
| **14. Security & Compliance** | AWS CloudTrail | |
| | AWS Config | |
| **15. AWS Best Practices & Interview Prep** | Check Free Tier Expiry | |
| | AWS Best Practices & Billing Calculator | |
| | Interview Tips | |


---