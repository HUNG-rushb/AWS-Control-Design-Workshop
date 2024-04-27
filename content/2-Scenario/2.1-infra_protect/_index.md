---
title: "Infrastructure Protection"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

## Infrastructure Protection

### Introduction

**Time to complete**: 25 minutes

**Learning Objectives**:

- Understand how to configure patching for EC2 instances
- Create a mechanism to ensure that your instances are periodically updated.
- Implement reporting mechanism for patching compliance

### Exercise

#### Requirements Scenario

Voyager Security team has provided the controls and technical requirements to ensure that EC2 Linux instances are patched with only security updates regularly every Saturday after 10:00 pm automatically. Voyager does not have a current solution and is looking to adopt a cloud native approach that can be quickly implemented. Additionally, the Security team is asking for evidence that a detective mechanism is in place to ensure that this requirement is satisfied. You as a Cloud Architect will need to understand the current architecture and determine, based on the EC2 User Guide and AWS Config Manage Rules or conformance packages which options are available to configure a patching solution that meets customer requirements.

**NIST Requirement**

| Control ID |                                                             Control Description                                                              |
| ---------- | :------------------------------------------------------------------------------------------------------------------------------------------: |
| SI-2c      | Install security-relevant software and firmware updates within [Assignment: organization-defined time period] of the release of the updates. |

**Customer Requirement**

| Control ID          |                         Control Description                          |
| ------------------- | :------------------------------------------------------------------: |
| Voyager-ctrl-inf-03 | Instances must be patched with security updates weekly on Saturdays. |

**AWS resources review and conclusions**

By reviewing the following available resources, you will come to conclude what needs to be done to meet Voyager’s control requirements. See below the conclusions.

| Resource type               | Resource name                                                                                                                                                         | Conclusion                                                                                                                                                                                                                             |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide      | [Amazon EC2 User guide / Update management in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/update-management.html)                                 | You can use AWS Systems Manager Patch Manager to automate the process of installing security-related updates for both the operating system and applications.                                                                           |
| AWS service user guide      | [AWS Systems Manager User guide / Creating a patching configuration](https://docs.aws.amazon.com/systems-manager/latest/userguide/create-patching-configuration.html) | In a patching configuration, you associate a patching configuration with an existing maintenance window, create a new maintenance window for the configuration, or run a one-time manual patching operation on a set of managed nodes. |
| AWS Conformance pack (NIST) | [AWS operational best practices for NIST 800 53 rev5](https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html) | (Search for SI-2 ) Enable ec2-managedinstance-patch-compliance-status-check AWS Config rule to check if Amazon EC2 instance patch compliance in AWS Systems Manager is set as required by your organization.                           |

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

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/1.png)

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/2.png)

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/3.png)

4. Now that you setup a patching schedule, click on Patch Now, to get current patching status

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/4.png)

5. Leave default configuration to only Scan, and press Patch Now

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/5.png)

6. Wait a moment for the operation to complete. You should see a Succeeded notification

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/6.png)

7. To set up the detection mechanism go to [AWS Config](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#), click on Rules, and Add rule

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/7.png)

8. Select an AWS Config Managed rule and find the one named: ec2-managedinstance-patch-compliance-status-check. Click Next

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/8.png)

9. On the trigger type keep: all changes. Click Next.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/9.png)

10. Review and confirm details. Click Add rule.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/10.png)

11. Success notification should appear on the top.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/11.png)

12. Click on the rule name (ec2-managedinstance-patch-compliance-status-check).

13. Click on Actions / Re-evaluate.

14. In the Resources in scop drop-down, select All.

15. Confirm the Compliance status is Compliant.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/12.png)

**You have completed the first exercise. Please proceed to the next.**
