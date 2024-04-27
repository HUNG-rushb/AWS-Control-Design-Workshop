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

- Thực hiện tag [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/) với Restricted.
- Thay đổi [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/) Customer Managed Key (CMK) policy để trở nên chặt chẽ hơn.

### Bài tập

#### Tình huống

Nhóm Voyager Security đã cung cấp các biện pháp kiểm soát và yêu cầu kỹ thuật để đảm bảo rằng cơ sở dữ liệu được gắn thẻ theo yêu cầu phân loại dữ liệu của họ và khi sử dụng các loại khóa được phê duyệt hiện hành với các chính sách hạn chế. Trong trường hợp này, RDS của ứng dụng FRL cần được phân loại là bị hạn chế **(tag key: restricted)** và theo yêu cầu thì phải có Khóa do khách hàng AWS KMS quản lý (CMK) với một số hạn chế về chính sách để đảm bảo quyền truy cập tuân theo các đặc quyền tối thiểu. Với tư cách là **Cloud Architect**, bạn sẽ cần hiểu kiến ​​trúc hiện tại và xác định, dựa trên hướng dẫn sử dụng **RDS** và **KMS**, cách định cấu hình thẻ phù hợp và chính sách khóa mã hóa phù hợp. Ngoài ra, bạn sẽ cần tìm và kích hoạt **AWS Config Manage Rule** thích hợp để phát hiện xem có **tag key: restricted** hay không.

**NIST yêu cầu**

| Control ID |                                                                                                  Miêu tả control                                                                                                   |
| ---------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| SC-8(4)    |                   Triển khai các cơ chế mật mã để che giấu hoặc ngẫu nhiên hóa các mẫu liên lạc trừ khi được bảo vệ bởi [Nhiệm vụ: các biện pháp kiểm soát vật lý thay thế do tổ chức xác định].                   |
| SC-12      | Thiết lập và quản lý khóa mật mã khi mật mã được sử dụng trong hệ thống theo các yêu cầu quản lý khóa sau: [Nhiệm vụ: các yêu cầu do tổ chức xác định đối với việc tạo, phân phối, lưu trữ, truy cập và hủy khóa]. |

**Khách hàng yêu cầu**

| Control ID         |                                                                                 Miêu tả control                                                                                 |
| ------------------ | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-dp-02 |                               Các tài nguyên lưu trữ bao gồm Cơ sở dữ liệu phải được gắn khóa phân loại Dữ liệu (công khai, nội bộ hoặc hạn chế)                                |
| Voyager-ctrl-dp-04 | Chính sách truy cập khóa mật mã để bảo vệ dữ liệu bị hạn chế sẽ được giới hạn ở dịch vụ và tài nguyên được ủy quyền. Quyền truy cập vào khóa này sẽ không được cấp cho cá nhân. |

**AWS đánh giá tài nguyên**

Bằng cách xem xét các tài nguyên sẵn có sau đây, bạn sẽ đi đến kết luận những gì cần phải làm để đáp ứng các yêu cầu kiểm soát của Voyager. Xem phần kết luận bên dưới.

| Loại                   | Tên                                                                                                                                                 | Kết luận                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide | [Amazon RDS user guide / Tagging Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.html)                    | Tag **Amazon RDS** là một cặp **key-value** mà bạn xác định và liên kết với tài nguyên Amazon RDS. Việc cung cấp giá trị cho khóa là tùy chọn. Bạn có thể sử dụng thẻ để gán thông tin tùy ý cho tài nguyên **Amazon RDS**.                                                                                                                                                                                                                        |
| AWS service user guide | [Amazon RDS user guide / Encrypting Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)          | **Amazon RDS** sử dụng khóa **AWS KMS** để mã hóa tài nguyên của mình. Đối với phiên bản DB được mã hóa **Amazon RDS**, tất cả log, bản sao lưu và bản kết xuất nhanh đều được mã hóa.                                                                                                                                                                                                                                                             |
| AWS service user guide | [AWS KMS user guide / Permissions for AWS services in key policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-services.html) | Chính sách khóa của **AWS KMS** phải cấp cho dịch vụ những quyền tối thiểu cần thiết để thay mặt bạn bảo vệ tài nguyên. Chúng tôi khuyên bạn nên tuân theo nguyên tắc đặc quyền tối thiểu: chỉ cấp cho dịch vụ những quyền mà nó yêu cầu. Bạn có thể thực hiện việc này một cách hiệu quả bằng cách tìm hiểu các quyền mà dịch vụ cần và sử dụng các khóa điều kiện chung của AWS cũng như các khóa điều kiện **AWS KMS** để điều chỉnh các quyền. |

### Hướng dẫn

1. Mở [Amazon RDS console](https://us-east-1.console.aws.amazon.com/rds/home?region=us-east-1#)

2. Chọn **Databases** và nhấn vào tên của DB để mở cấu hình.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/51.png)

3. Chọn tab **Tags** vả nhấn **Add**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/52.png)

4. Nhập Tag Key: `Classification`, và Value: `Restricted`. Nhấn **Add**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/53.png)

5. Chọn tab **Configuration**, nhấn vào **AWS KMS Key ID** đề điều hướng tới cấu hình.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/54.png)

6. Nhấn **Edit** và xem **Key policy**. Bạn sẽ thấy:

- **KMSAdminRole** và **root** (**Administrator Principal**) có quyền giải mã data. Thông thường, **Admins** không được thực hiện.
- **DevOpsRole** (**User Principal**) có quyền **Delete or Revoke** key. Thông thường, người dùng không được thực hiện.

7. Nhấn **Edit** để điều chỉnh **Key policy**:

- Bỏ **Action(s)** dưới đây trong mục **Administrators** (**KMSAdminRole**)

```
"kms:Encrypt",
"kms:Decrypt",
"kms:ReEncrypt*",
"kms:GenerateDataKey*",
```

- Bỏ **Action(s)** dưới đây trong mục **User** (**DevOpsRole**).

```
"kms:Revoke*",
"kms:Disable*",
"kms:Delete*",
```

- Xong hãy nhấn **Save changes**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/55.png)

8. Truy cập [AWS Config console](https://console.aws.amazon.com/config/home?region=us-east-1) để xác thực xem phiên bản RDS đã được gắn tag khóa data classification chưa. Nhấn vào **Rules**, sau đó**Add rule**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/56.png)

9. Chọn **Add AWS Managed rule** và tìm `required-tags`. Chọn và nhấn **Next**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/57.png)

10. Mục **Resource category**, chọn **AWS resources**. Mục **Resource type** chọn **AWS RDS DBInstance**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/58.png)

11. Mục **Parameters**, nhập

```
Key: tag1Key
Value: Classification
(Replace the value from CostCenter to Classification)

Key: tag1Value
Value: Public,Confidential,Restricted
```

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/59.png)

12. Nhấn **Next**.

13. Xem lại và nhấn **Add rule**.

14. Nhấn **New rule name (required-tags)**.

15. Nhấn **Actions**, **Re-evaluate**.

16. mục **Resources in scope**, nhấn mũi tên **Refresh** và xác nhận trạng thái tuân thủ. (Có thể mất chút thời gian để cập nhật trạng thái **Compliance**).

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/60.png)

17. (Tùy chọn) Quay lại tài nguyên, thay đổi hoặc xóa **tagValue** và xác thực xem nó có thay đổi trạng thái tuân thủ hay không. Hãy nhớ đánh giá lại, đợi một lát và làm mới các tài nguyên trong phần phạm vi để xem sự thay đổi về **Compliance**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/61.png)

**Bạn đã hoàn thành bài tập thứ hai. Hãy chuyển sang bài tập tiếp theo.**
