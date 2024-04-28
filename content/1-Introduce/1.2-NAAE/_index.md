---
title: "Not Attending AWS hosted event"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 1.2 </b> "
---

### Not Attending AWS hosted event

{{% notice info %}}
If you are attending an AWS hosted event, you can stop reading an continue with next step using the provided accounts.
{{% /notice %}}

1. Download (right-click and Save link as...) [CloudFormation template with the base architecture](https://static.us-east-1.prod.workshops.aws/public/7609f68d-8f02-45f1-ac24-da0e810d440f/static/architecture.json) required to create the environment for this workshop.

2. Using your own test account (please don't use production accounts) go to the [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=control-design)

3. Click on create stack, With new resources (standard)

![FCJ_ws2](/images/1.introduce/2.png)

4. Enter Stack name control-design. Click Next

![FCJ_ws2](/images/1.introduce/4.png)

5. Use default stack options. Click Next

![FCJ_ws2](/images/1.introduce/3.png)

6. Review Details

![FCJ_ws2](/images/1.introduce/5.png)

7. Click the "I acknowledge..." checkbox. Click Create stack

![FCJ_ws2](/images/1.introduce/6.png)

8. Wait for all resources to complete creation. If any error appears, refer to the Support/Troubleshooting section.

![FCJ_ws2](/images/1.introduce/7.png)

{{% notice info %}}
Once you finish this workshop, go to the Support/Cleanup section, to reduce incurred costs.
{{% /notice %}}
