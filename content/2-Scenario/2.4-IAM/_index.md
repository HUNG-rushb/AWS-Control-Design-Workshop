---
title: "Identity and Access Management"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 2.4 </b> "
---

## Identity and Access Management

### Introduction

**Time to complete**: 25 minutes

**Learning Objectives**:

- Configure your IAM policies to prevent unauthorized traffic mirroring of your VPC network.
- Deploy an AWS Config custom rule to detect traffic mirroring on the VPC.

### Exercise

#### Requirements Scenario

Voyager Security team has provided the controls and technical requirements to ensure that their developers follow least privileges when it comes to manage their VPC network components. The developer team is using the IAM Role DevOps which is now permissive enough to allow them to place a traffic mirror listener in the account without the security team knowing. Even if this traffic is internal, it is considered highly restricted. Your goal as a Cloud Architect will be to configure the right IAM permissions to prevent traffic mirror action and a detective mechanism to alert.

**NIST Requirement**

| Control ID |                                                                                                   Control Description                                                                                                   |
| ---------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| AC-4(21)   | Separate information flows logically or physically using [Assignment: organization-defined mechanisms and/or techniques] to accomplish [Assignment: organization-defined required separations by types of information]. |
| AC-3       |                                      Enforce approved authorizations for logical access to information and system resources in accordance with applicable access control policies.                                      |

**Customer Requirement**

| Control ID          |                                                   Control Description                                                   |
| ------------------- | :---------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-net-05 | Developers must be restricted to make changes on VPC resources that could expose the network to unauthorized resources. |

**AWS resources review and conclusions**

By reviewing the following available resources, you will come to conclude what needs to be done to meet Voyager’s control requirements. See below the conclusions.

| Resource type           |                                                                            Resource name                                                                             |                                                                                                             Conclusion                                                                                                              |
| ----------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| AWS service IAM actions |                              [Amazon VPC actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/OperationList-query-vpc.html)                               |                                    You can use AWS Systems Manager Patch Manager to automate the process of installing security-related updates for both the operating system and applications.                                     |
| AWS service user guide  |                       [AWS Config / Managed rules](https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html)                        |                                                            There isn’t a managed AWS config rule available to detect traffic mirror so we need to build a custom check.                                                             |
| AWS Service user guide  | [AWS Config / Creating AWS Config custom Lambda rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_develop-rules_lambda-functions.html) | You can develop custom rules and add them to AWS Config with AWS Lambda functions. You associate each custom rule with an Lambda function, which contains the logic that evaluates whether your AWS resources comply with the rule. |

### Instructions

SSM agent has been installed and pre-configured for you. You are going to confirm that the EC2 instances in the environment are reporting to the SSM console. You need to configure SSM patch manager to run periodically during the pre-approved maintenance window. Configure AWS Config rule to report patching compliance.

1. Open the [AWS Systems Manager console](https://console.aws.amazon.com/systems-manager/) , in the left navigation pane, choose Patch Manager.

2. Choose Configure patching. In the Instances to patch section, choose Select Instances Manually: Select the check box next to the name of each managed node you want to patch.

3. In the Patching schedule section, choose:

- Schedule in a new Maintenance Window and select a Maintenance Window schedule. Use a CRON schedule builder
- Under Maintenance Window run frequency, change to Every Saturday at 12:00
- Under Maintenance duration select 3
- Under Maintenance window name write: NISTPatch or as desired
- Under Patching Operation select Scan and install
- Finally press configure patching

![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
![FCJ_ws2](/images/2.scenario/211.png)
