---
title: "Bảo vệ hạ tầng"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

## Bảo vệ hạ tầng

### Giới thiệu

**Thời gian để hoàn thành**: 25 phút

**Mục tiêu thực hành**:

- Hiểu cách định cấu hình bản vá cho các trường hợp EC2.
- Tạo một cơ chế để đảm bảo rằng các trường hợp của bạn được cập nhật định kỳ.
- Thực hiện cơ chế báo cáo để vá lỗi vi phạm tuân thủ.

### Bài tập

#### Tình huống

Nhóm Voyager Security đã cung cấp các biện pháp kiểm soát và yêu cầu kỹ thuật để đảm bảo rằng các phiên bản EC2 Linux được vá chỉ bằng các bản cập nhật bảo mật thường xuyên sau 10:00 tối Thứ Bảy hàng tuần. Voyager không có giải pháp hiện tại và đang tìm cách áp dụng cách tiếp cận gốc trên đám mây để có thể triển khai nhanh chóng. Ngoài ra, nhóm Security đang yêu cầu bằng chứng cho thấy cơ chế detective được áp dụng để đảm bảo rằng yêu cầu này được đáp ứng. Với tư cách là Cloud Architect, bạn sẽ cần hiểu kiến ​​trúc hiện tại và xác định, dựa trên Hướng dẫn sử dụng EC2 và Quy tắc quản lý cấu hình AWS hoặc các gói tuân thủ, những tùy chọn nào có sẵn để đặt cấu hình giải pháp vá lỗi đáp ứng yêu cầu của khách hàng.

**NIST yêu cầu**

| Control ID |                                                                            Miêu tả control                                                                             |
| ---------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| SI-2c      | Cài đặt các bản cập nhật chương trình cơ sở và phần mềm liên quan đến bảo mật trong [Nhiệm vụ: khoảng thời gian do tổ chức xác định] kể từ khi phát hành bản cập nhật. |

**Khách hàng yêu cầu**

| Control ID          |                               Miêu tả control                               |
| ------------------- | :-------------------------------------------------------------------------: |
| Voyager-ctrl-inf-03 | Instances phải được vá bằng các bản cập nhật bảo mật hàng tuần vào Thứ Bảy. |

**AWS đánh giá tài nguyên**

Bằng cách xem xét các tài nguyên sẵn có sau đây, bạn sẽ đi đến kết luận những gì cần phải làm để đáp ứng các yêu cầu kiểm soát của Voyager. Xem phần kết luận bên dưới.

| Loại                        | Tên                                                                                                                                                                   | Kết luận                                                                                                                                                                                                                               |
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

![FCJ_ws2](/images/2.scenario/1.png)

![FCJ_ws2](/images/2.scenario/2.png)

![FCJ_ws2](/images/2.scenario/3.png)

4. Now that you setup a patching schedule, click on Patch Now, to get current patching status

![FCJ_ws2](/images/2.scenario/1.png)

5. Leave default configuration to only Scan, and press Patch Now

![FCJ_ws2](/images/2.scenario/1.png)

6. Wait a moment for the operation to complete. You should see a Succeeded notification

![FCJ_ws2](/images/2.scenario/1.png)

7. To set up the detection mechanism go to [AWS Config](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#), click on Rules, and Add rule

![FCJ_ws2](/images/2.scenario/1.png)

8. Select an AWS Config Managed rule and find the one named: ec2-managedinstance-patch-compliance-status-check. Click Next

![FCJ_ws2](/images/2.scenario/1.png)

9. On the trigger type keep: all changes. Click Next.

![FCJ_ws2](/images/2.scenario/1.png)

10. Review and confirm details. Click Add rule.

![FCJ_ws2](/images/2.scenario/10.png)

11. Success notification should appear on the top.

![FCJ_ws2](/images/2.scenario/11.png)

12. Click on the rule name (ec2-managedinstance-patch-compliance-status-check).

13. Click on Actions / Re-evaluate.

14. In the Resources in scop drop-down, select All.

15. Confirm the Compliance status is Compliant.

![FCJ_ws2](/images/2.scenario/12.png)

**You have completed the first exercise. Please proceed to the next.**
