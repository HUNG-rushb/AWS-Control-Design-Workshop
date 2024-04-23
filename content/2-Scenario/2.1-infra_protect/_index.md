---
title: "Infrastructure Protection"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

### Infrastructure Protection

Introduction
Time to complete: 25 minutes

Learning Objectives:

Understand how to configure patching for EC2 instances
Create a mechanism to ensure that your instances are periodically updated.
Implement reporting mechanism for patching compliance
Exercise
Requirements Scenario
Voyager Security team has provided the controls and technical requirements to ensure that EC2 Linux instances are patched with only security updates regularly every Saturday after 10:00 pm automatically. Voyager does not have a current solution and is looking to adopt a cloud native approach that can be quickly implemented. Additionally, the Security team is asking for evidence that a detective mechanism is in place to ensure that this requirement is satisfied. You as a Cloud Architect will need to understand the current architecture and determine, based on the EC2 User Guide and AWS Config Manage Rules or conformance packages which options are available to configure a patching solution that meets customer requirements.

1. At **AWS Management Console**, find **S3** and select **S3**.

2. At **S3** console, select **Create bucket**.

3. In create bucket steps:

- For **AWS Region**, select **Asia Pacific (Singapore) ap-southeast-1**.
- For **Bucket name**, insert **`logging-workshop`**.

4. Tiếp tục:

- For **Block Public Access settings for this bucket**, untick **Block all public access**.
- For **Turning off block all public access might result in this bucket and the objects within becoming public**, confirm this.

5. Scroll down, select **Create bucket**.

6. Confirm bucket is created successfully.

7. Continue creating bucket **logging-workshop-destination**

- For **AWS Region**, select **Asia Pacific (Singapore) ap-southeast-1**.
- For **Bucket name**, insert **`logging-workshop-destination`**.
- No need to untick **Block Public Access settings for this bucket**.
- Scroll down, select **Create bucket**.
- Confirm bucket is created successfully.
