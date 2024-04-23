---
title: "Architecture"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1.1 </b> "
---

### Architecture

The architecture is a 3-Tier web app based on [Amazon EC2 Auto Scaling](https://aws.amazon.com/pt/ec2/autoscaling/) group using [Amazon RDS database](https://aws.amazon.com/rds/) instance as persistent application storage.

![FCJ_ws2](/images/1.introduce/1.png)

### Base Services

The workshops uses the following services as the base infrastructure:

- Amazon Elastic Cloud Computing (EC2)
- Amazon Relational Database Service (RDS)
- Amazon Elastic Load Balancer (ELB)
- mazon Virtual Private Cloud (VPC) - Internet Gateway (IG)

### Control Services

The workshops leverages the following services to implement our controls:

- AWS Key Management Service (KMS)
- AWS Identity and Access Management (IAM)
- AWS Systems Manager (SSM) - Patch Manager
- AWS Config
- Amazon CloudWatch
- Amazon Simple Notification Service (SNS)
- AWS Secrets Manager
