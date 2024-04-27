---
title: "Clean Up"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

### Clean Up

{{% notice note  %}}
If you are using this in an instructor led session, with AWS Event Engine you do not need to run the cleanup steps.
{{% /notice %}}

In order to prevent charges to your account we recommend cleaning up the infrastructure that was created during this workshop. If you plan to keep things running so you can
examine the workshop later, remember to do the cleanup when you are done.

{{% notice note  %}}
If you are running this in your own account. You will need to manually delete some resources before you delete the CloudFormation stacks so please do the following steps in order.
{{% /notice %}}

1. Disable termination protection for the applicable EC2 instances

- Open to EC2 console
- Select the instance
- Click on Actions, Instance settings, change termination protection
- Untick the Enable checkbox, click Save.

2. Delete the CloudFormation stack

- Go to the AWS CloudFormation console
- Select the appropriate stack
- Select Action
- Click Delete Stack

3. Go to the AWS Config console

- Click on Controls
- Delete the controls that are not needed.
