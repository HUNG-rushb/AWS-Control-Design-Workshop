---
title: "Security Assurance"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

## Security Assurance

### Introduction

**Time to complete**: 25 minutes

**Learning Objectives**:

- Setup Logging at OS Level using Amazon CloudWatch.
- Setup notifications concerning relevant OS events.

### Exercise

#### Requirements Scenario

Voyager Security team has provided the technical requirements to ensure that Operating Systems (OS) logs are captured, and alerts are sent to monitoring and response teams for critical events. For example, root access shall be considered a critical event and should trigger an alert to the monitoring team via email. You as a Cloud Architect will need to understand the current architecture and determine, based on the AWS CloudWatch and AWS Config Manage Rules or conformance packages which options are available to configure a logging solution that meets customer requirements.

**NIST Requirement**

| Control ID |             Control Description              |
| ---------- | :------------------------------------------: |
| AC-6(9)    |  Log the execution of privileged functions.  |
| AU-2       |                Event Logging                 |
| AU-6(1)    | Audit Record Review, Analysis, and Reporting |

**Customer Requirement**

| Control ID          |                                                                       Control Description                                                                       |
| ------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-mon-01 | Systems must be appropriately monitored. Logs needs to be captured at the OS level and alerts will need to be triggered and sent to SOC team when root is used. |

**AWS resources review and conclusions**

By reviewing the following available resources, you will come to conclude what needs to be done to meet Voyager’s control requirements. See below the conclusions.

| Resource type               | Resource name                                                                                                                                                                                                                        | Conclusion                                                                                                                                                                                                                                                     |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide      | [AWS CloudWatch / What is Amazon CloudWatch Logs? ](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)                                                                                              | CloudWatch Logs enables you to centralize the logs from all of your systems, applications, and AWS services that you use, in a single, highly scalable service.                                                                                                |
| AWS service user guide      | [AWS CloudWatch agent guide / Collecting metrics and logs from Amazon EC2 instances and on-premises servers with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) | The cloud native approach to record OS logs is by installing AWS CloudWatch agent in each instance.                                                                                                                                                            |
| AWS service user guide      | [CloudWatch Alarm events and EventBridge](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-eventbridge.html)                                                                                            | CloudWatch sends events to Amazon EventBridge whenever a CloudWatch alarm is created, updated, deleted, or changes alarm state. You can use EventBridge and these events to write rules that take actions, such as notifying you, when an alarm changes state. |
| AWS Conformance pack (NIST) | [AWS operational best practices for NIST 800 53 rev5](https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html)                                                                | (Search for AC-6(9), AU-2, AU-6(1)) Use Amazon CloudWatch to centrally collect and manage log event activity. Inclusion of AWS CloudTrail data provides details of API call activity within your AWS account.                                                  |

### Instructions

#### CloudWatch Logs introduction

Logs are an essential tool to gain visibility into what is happening in your instances. Besides Metrics, CloudWatch allows you to collect and search logs.

Before we dive into CloudWatch Logs, we need to understand the concepts of log stream and log groups .

A log stream is a sequence of log events coming from the same source (e.g., EC2 instance).

A log group is a group of log streams that shares the same retention, monitoring and access control settings. Used for logical grouping of the log streams. If EC2 instances are running the same application, we can group their log streams into one log group.

#### CloudWatch Logs analysis

Our EC2 instances generates many log files. The Linux operating system has its own log, and our application running on it can also generate their own logs.

These logs can be collected by the CloudWatch agent , an agent application that (optionally) runs in your EC2 instances.

CloudWatch agent is pre-installed and configured to collect the logs in all the EC2 instances for this part of the workshop.

1. To see the logs, open the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)
2. In the left navigation pane, under Logs, choose Log groups.

![FCJ_ws2](/images/2.scenario/111.png)

3. There is already one log group named /var/log/secure. Click on it and you should see the log group details and a few log streams under for each instance.

![FCJ_ws2](/images/2.scenario/112.png)

4. Click Actions/Edit retention setting. Select the desired expiration time. (for this exercise choose 12 month). Click Save.

![FCJ_ws2](/images/2.scenario/113.png)

5. Click Actions from the top-right corner and click create metric filter.

![FCJ_ws2](/images/2.scenario/114.png)

6. Define a pattern to search for. You can input `session opened for user root` and it will look for it in the logs. Click Next.

![FCJ_ws2](/images/2.scenario/115.png)

7. Complete the metric filter details as shown in the image. Click Next

![FCJ_ws2](/images/2.scenario/116.png)

8. Review details and click on Create metric filter.

![FCJ_ws2](/images/2.scenario/117.png)

9. Click on Audit under Metric.

![FCJ_ws2](/images/2.scenario/118.png)

10. An empty graph will appear. CloudWatch will only generate metrics from events generated after the metric was generated.

![FCJ_ws2](/images/2.scenario/119.png)

11. Navigate to the [EC2 console](https://us-east-1.console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:), click on instances, select one of the instances and click Connect(top right) .

![FCJ_ws2](/images/2.scenario/120.png)

12. Under Session Manager, click Connect.

![FCJ_ws2](/images/2.scenario/121.png)

13. Once the terminal connection opens. you can run sudo su to escalate to root user. This way you will generate additional events in the log. Click Terminate twice.

![FCJ_ws2](/images/2.scenario/122.png)

14. Go back to [CloudWatch console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1#) on the left under Metrics, click All Metrics, and under Custom namespaces, click Audit and then Metrics with no dimensions.

![FCJ_ws2](/images/2.scenario/123.png)

15. Check session opened for user root checkbox. A dot with the session we generated will appear in the graph. (It can take up to 5 mins for the information to get displayed)

![FCJ_ws2](/images/2.scenario/124.png)

16. (Optional) To learn more about filter patterns look at the [CloudWatch documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)

17. (Optional) Once you configure a metric you can configure [CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) to receive a notification every time someone logins with root user.

**You have completed exercise three. Please proceed to the next exercise.**
