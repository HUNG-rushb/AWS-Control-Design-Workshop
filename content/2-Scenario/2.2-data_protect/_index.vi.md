---
title: "Bảo vệ data"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.2 </b> "
---

## Bảo vệ data

### Giới thiệu

**Thời gian để hoàn thành**: 25 phút

**Mục tiêu thực hành**:

- Learn how to tag your [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/) as Restricted.
- Modify an [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/) Customer Managed Key (CMK) policy to be more restrictive.

### Bài tập

#### Tình huống

Nhóm Voyager Security đã cung cấp các biện pháp kiểm soát và yêu cầu kỹ thuật để đảm bảo rằng cơ sở dữ liệu được gắn thẻ theo yêu cầu phân loại dữ liệu của họ và khi sử dụng các loại khóa được phê duyệt hiện hành với các chính sách hạn chế. Trong trường hợp này, RDS của ứng dụng FRL cần được phân loại là bị hạn chế **(tag key: restricted)** và theo yêu cầu thì phải có Khóa do khách hàng AWS KMS quản lý (CMK) với một số hạn chế về chính sách để đảm bảo quyền truy cập tuân theo các đặc quyền tối thiểu. Với tư cách là **Cloud Architect**, bạn sẽ cần hiểu kiến ​​trúc hiện tại và xác định, dựa trên hướng dẫn sử dụng **RDS** và **KMS**, cách định cấu hình thẻ phù hợp và chính sách khóa mã hóa phù hợp. Ngoài ra, bạn sẽ cần tìm và kích hoạt **AWS Config Manage Rule** thích hợp để phát hiện xem có **tag key: restricted** hay không.

**NIST yêu cầu**

| Control ID |                                                                                                                            Control Description                                                                                                                            |
| ---------- | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| SC-8(4)    |                                             Implement cryptographic mechanisms to conceal or randomize communication patterns unless otherwise protected by [Assignment: organization-defined alternative physical controls].                                             |
| SC-12      | Establish and manage cryptographic keys when cryptography is employed within the system in accordance with the following key management requirements: [Assignment: organization-defined requirements for key generation, distribution, storage, access, and destruction]. |

**Khách hàng yêu cầu**

| Control ID         |                                                                             Control Description                                                                             |
| ------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-dp-02 |                             Storage resources including Databases shall be tagged with Data classification key (public, internal or restricted)                             |
| Voyager-ctrl-dp-04 | Cryptographic key access policy to protect restricted data shall be limited to the authorized service and resource. Access to this key shall not be granted to individuals. |

**AWS đánh giá tài nguyên**

Bằng cách xem xét các tài nguyên sẵn có sau đây, bạn sẽ đi đến kết luận những gì cần phải làm để đáp ứng các yêu cầu kiểm soát của Voyager. Xem phần kết luận bên dưới.

| Resource type          | Resource name                                                                                                                                       | Conclusion                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide | [Amazon RDS user guide / Tagging Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.html)                    | An Amazon RDS tag is a name-value pair that you define and associate with an Amazon RDS resource. The name is referred to as the key. Supplying a value for the key is optional. You can use tags to assign arbitrary information to an Amazon RDS resource.                                                                                                                                                            |
| AWS service user guide | [Amazon RDS user guide / Encrypting Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)          | Amazon RDS uses an AWS KMS key to encrypt its resources. For an Amazon RDS encrypted DB instance, all logs, backups, and snapshots are encrypted.                                                                                                                                                                                                                                                                       |
| AWS service user guide | [AWS KMS user guide / Permissions for AWS services in key policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-services.html) | AWS KMS key policy must allow the service the minimum permissions that it requires to protect the resource on your behalf. We recommend that you follow the principle of least privilege: give the service only the permissions that it requires. You can do this effectively by learning which permissions the service needs and using AWS global condition keys and AWS KMS condition keys to refine the permissions. |

### Instructions

1. Open the [Amazon RDS console](https://us-east-1.console.aws.amazon.com/rds/home?region=us-east-1#)

2. Select the Databases section and click on the DB identifier to open instance properties

![FCJ_ws2](/images/2.scenario/51.png)

3. Select Tags tab and click on Add

![FCJ_ws2](/images/2.scenario/52.png)

4. Enter Tag Key: Classification, and Value: Restricted. Click Add

![FCJ_ws2](/images/2.scenario/53.png)

5. Select the Configuration tab, click on the AWS KMS Key ID to navigate into its properties.

![FCJ_ws2](/images/2.scenario/54.png)

6. Click Edit and review the Key policy. You will find that:

- It allows the KMSAdminRole and root (Administrator Principal) to decrypt data. Typically, Admins should not be allowed this Action.
- It allows the DevOpsRole (User Principal) to Delete or Revoke the key. Typically, users should not be allowed this Action.

7. Click Edit to update the Key policy:

- Remove the following Action(s) from the Administrators section (KMSAdminRole)

```
"kms:Encrypt",
"kms:Decrypt",
"kms:ReEncrypt*",
"kms:GenerateDataKey*",
```

- Remove the following Action(s) from the User section (DevOpsRole)

```
"kms:Revoke*",
"kms:Disable*",
"kms:Delete*",
```

- Once you finish Save changes

![FCJ_ws2](/images/2.scenario/55.png)

8. Go to the [AWS Config console](https://console.aws.amazon.com/config/home?region=us-east-1) to validate if the RDS instance has been tagged with data classification key. Click on Rules, Add rule

![FCJ_ws2](/images/2.scenario/56.png)

9. Select Add AWS Managed rule and search for required-tags. Select it and click Next.

![FCJ_ws2](/images/2.scenario/57.png)

10. Under Resource category, select AWS resources. Under Resource type pick AWS RDS DBInstance.

![FCJ_ws2](/images/2.scenario/58.png)

11. Under Parameters enter the following Key/Value

```
Key: tag1Key
Value: Classification
(Replace the value from CostCenter to Classification)

Key: tag1Value
Value: Public,Confidential,Restricted
```

![FCJ_ws2](/images/2.scenario/59.png)

12. Click Next

13. Review and click Add rule.

14. Click on the new rule name (required-tags)

15. Click on Actions, Re-evaluate

16. In the Resources in scope, click the Refresh arrow and validate compliance status. (It can take a moment to update the compliance status)

![FCJ_ws2](/images/2.scenario/60.png)

17. Optional) Go back to the resource, change or remove the tagValue and validate if it changes the compliance status. Remember to re-evaluate, wait a moment, and refresh the resources in scope section to see the Compliance change.

![FCJ_ws2](/images/2.scenario/61.png)

**You have completed exercise two. Please proceed to the next exercise.**
