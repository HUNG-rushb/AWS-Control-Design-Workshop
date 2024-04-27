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

1. Open to the [IAM Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#) under the Roles section, search for the DevOps role, and click over the Role Name. (it may have an aleatory suffix)

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/211.png)

2. Expand the Policy clicking on the (+) sign. Click the Edit button.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/212.png)

3. Select the JSON tab. Modify the policy to remove the ability to create Traffic Mirror sessions under the NotAction policy element. These two actions:

```
"ec2:CreateTrafficMirrorSession"
"ec2:CreateTrafficMirrorTarget"
```

Can be simplified into one element using a wildcard like this:

```
"ec2:CreateTrafficMirror*"
```

4. Select Review policy and then save changes.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/213.png)

Now that you have reduced the DevOpsRole privileges, it's time to create a mechanism to detect if a traffic mirror is enabled. To do it you are going to create a new custom AWS Config rule. We have prepared a CloudFormation template that validates if TrafficMirror Sessions or Targets are in place. This cloudFormation template creates a Config rule that together with a Lambda function and the necessary permissions detects when a Traffic Mirror is in place. (This is an example of how custom controls can be implemented to suit your needs)

5. [Download this CloudFormation template](https://static.us-east-1.prod.workshops.aws/public/7609f68d-8f02-45f1-ac24-da0e810d440f/static/Custom-Rule-Traffic-Mirror.yaml) to your disk.

6. Open the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=traffic-mirror) and click create a new stack.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/214.png)

7. Select Template is ready and select Upload a template file. Click on Choose a file. Select the file you just downloaded to your disk. Click Next.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/215.png)

8. Provide the following name to the stack, and click Next.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/216.png)

9. Leave the stack options as is, and click Next.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/217.png)

10. Select the check box to acknowledge and press Create stack.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/218.png)

11. Review the status, click on the refresh button until it has finished successfully.
12. Go to the [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#) and under rules option verify that the new config rule was created.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/219.png)

13. To test this rule, go to [VPC console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#)

14. On the left under Traffic Mirroring, select Mirror targets.

15. On the top right, click on Create a new Traffic Mirror target. (Keep in mind that initially we have reduced the privileges of the DevOps role but not yours).

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/220.png)

16. Click on the Target textbox and select any of the EC2 ENI. Click Create.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/221.png)

17. Go back to the [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#) select the rule we created, and click Re-evaluate. Wait a moment and refresh. The rule should change from Compliant status to Noncompliant

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/222.png)

**Congratulations! You have completed the last exercise.**
