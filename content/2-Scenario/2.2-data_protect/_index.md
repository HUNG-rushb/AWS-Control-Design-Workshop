---
title: "Data Protection"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.2 </b> "
---

## Data Protection

### Introduction

**Time to complete**: 25 minutes

**Learning Objectives**:

- Learn how to tag your [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/) as Restricted.
- Modify an [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/) Customer Managed Key (CMK) policy to be more restrictive.

### Exercise

#### Requirements Scenario

Voyager Security team has provided the controls and technical requirements to ensure that databases are tagged according to their data classification requirements and where applicable approved key types with restrictive policies are in use. In this case the FRL application RDS, needs to be classified as restricted (tag key: restricted) and according to the requirements there must be an AWS KMS Customer managed Key (CMK) with some policy restrictions to ensure access follows least privileges. You as a Cloud Architect will need to understand the current architecture and determine, based on the RDS and KMS User Guides how to configure the right tag and an appropriate encryption key policy. Additionally, you will need to find and enable the appropriate AWS Config Manage Rule that should detect if a tag key: restricted is in place.

**NIST Requirement**

| Control ID |                                                                                                                            Control Description                                                                                                                            |
| ---------- | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| SC-8(4)    |                                             Implement cryptographic mechanisms to conceal or randomize communication patterns unless otherwise protected by [Assignment: organization-defined alternative physical controls].                                             |
| SC-12      | Establish and manage cryptographic keys when cryptography is employed within the system in accordance with the following key management requirements: [Assignment: organization-defined requirements for key generation, distribution, storage, access, and destruction]. |

**Customer Requirement**

| Control ID         |                                                                             Control Description                                                                             |
| ------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-dp-02 |                             Storage resources including Databases shall be tagged with Data classification key (public, internal or restricted)                             |
| Voyager-ctrl-dp-04 | Cryptographic key access policy to protect restricted data shall be limited to the authorized service and resource. Access to this key shall not be granted to individuals. |

**AWS resources review and conclusions**

By reviewing the following available resources, you will come to conclude what needs to be done to meet Voyager’s control requirements. See below the conclusions.

| Resource type          | Resource name                                                                                                                                       | Conclusion                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide | [Amazon RDS user guide / Tagging Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.html)                    | An Amazon RDS tag is a name-value pair that you define and associate with an Amazon RDS resource. The name is referred to as the key. Supplying a value for the key is optional. You can use tags to assign arbitrary information to an Amazon RDS resource.                                                                                                                                                            |
| AWS service user guide | [Amazon RDS user guide / Encrypting Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)          | Amazon RDS uses an AWS KMS key to encrypt its resources. For an Amazon RDS encrypted DB instance, all logs, backups, and snapshots are encrypted.                                                                                                                                                                                                                                                                       |
| AWS service user guide | [AWS KMS user guide / Permissions for AWS services in key policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-services.html) | AWS KMS key policy must allow the service the minimum permissions that it requires to protect the resource on your behalf. We recommend that you follow the principle of least privilege: give the service only the permissions that it requires. You can do this effectively by learning which permissions the service needs and using AWS global condition keys and AWS KMS condition keys to refine the permissions. |

### Instructions

SSM agent has been installed and pre-configured for you. You are going to confirm that the EC2 instances in the environment are reporting to the SSM console. You need to configure SSM patch manager to run periodically during the pre-approved maintenance window. Configure AWS Config rule to report patching compliance.

Open the AWS Systems Manager console , in the left navigation pane, choose Patch Manager.

Choose Configure patching. In the Instances to patch section, choose Select Instances Manually: Select the check box next to the name of each managed node you want to patch.

In the Patching schedule section, choose:

Schedule in a new Maintenance Window and select a Maintenance Window schedule. Use a CRON schedule builder
Under Maintenance Window run frequency, change to Every Saturday at 12:00
Under Maintenance duration select 3
Under Maintenance window name write: NISTPatch or as desired
Under Patching Operation select Scan and install
Finally press configure patching

![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
![FCJ_ws2](/images/2.scenario/51.png)
