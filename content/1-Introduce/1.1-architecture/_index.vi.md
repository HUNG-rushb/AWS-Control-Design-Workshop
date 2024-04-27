---
title: "Kiến trúc"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1.1 </b> "
---

### Kiến trúc

Kiến trúc này là một ứng dụng web 3 tầng dựa trên nhóm [Amazon EC2 Auto Scaling](https://aws.amazon.com/pt/ec2/autoscaling/) sử dụng [cơ sở dữ liệu Amazon RDS](https://aws.amazon .com/rds/) làm bộ lưu trữ ứng dụng liên tục.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/1.png)

### Các dịch vụ nền tảng

Các workshop dụng các dịch vụ sau làm cơ sở hạ tầng cơ sở:

- Amazon Elastic Cloud Computing (EC2)
- Amazon Relational Database Service (RDS)
- Amazon Elastic Load Balancer (ELB)
- Amazon Virtual Private Cloud (VPC) - Internet Gateway (IG)

### Dịch vụ quản lý

Các workshop tận dụng các dịch vụ sau để thực hiện các biện pháp kiểm soát của chúng tôi:

- AWS Key Management Service (KMS)
- AWS Identity and Access Management (IAM)
- AWS Systems Manager (SSM) - Patch Manager
- AWS Config
- Amazon CloudWatch
- Amazon Simple Notification Service (SNS)
- AWS Secrets Manager
