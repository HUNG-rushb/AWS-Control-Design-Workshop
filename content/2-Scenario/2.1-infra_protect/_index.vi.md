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

| Loại                        | Tên                                                                                                                                                                   | Kết luận                                                                                                                                                                                                                                         |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| AWS service user guide      | [Amazon EC2 User guide / Update management in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/update-management.html)                                 | Bạn có thể sử dụng **AWS Systems Manager Patch Manager** để tự động hóa quá trình cài đặt các bản cập nhật liên quan đến bảo mật cho cả hệ điều hành và ứng dụng.                                                                                |
| AWS service user guide      | [AWS Systems Manager User guide / Creating a patching configuration](https://docs.aws.amazon.com/systems-manager/latest/userguide/create-patching-configuration.html) | Trong cấu hình vá lỗi, bạn liên kết cấu hình vá lỗi với cửa sổ bảo trì hiện có, tạo cửa sổ bảo trì mới cho cấu hình đó hoặc chạy thao tác vá lỗi thủ công một lần trên một tập hợp các nút được quản lý.                                         |
| AWS Conformance pack (NIST) | [AWS operational best practices for NIST 800 53 rev5](https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html) | (Search for SI-2 ) Bật AWS Config rule **ec2-managedinstance-patch-compliance-status-check** để kiểm tra xem việc tuân thủ bản vá phiên bản **Amazon EC2** trong **AWS Systems Manager** có được đặt theo yêu cầu của tổ chức của bạn hay không. |

### Hướng dẫn

Agent SSM đã được cài đặt và cấu hình sẵn cho bạn. Bạn sẽ xác nhận rằng các phiên bản EC2 trong môi trường đang báo cáo lên bảng điều khiển SSM. Bạn cần định cấu hình trình quản lý bản vá SSM để chạy định kỳ trong thời gian bảo trì được phê duyệt trước. Cấu hình rule AWS Config để báo cáo việc tuân thủ bản vá.

1. Mở [AWS Systems Manager console](https://console.aws.amazon.com/systems-manager/), ở menu bên trái, chọn **Patch Manager**.

2. Chọn **Configure patching**. Mục **Instances to patch**, chọn **Select Instances Manually**: Chọn tên của từng node được quản lý mà bạn muốn vá.

3. Mục **Patching schedule**, chọn:

- Chọn **Schedule in a new Maintenance Window** và chọn **Maintenance Window schedule**, sau đó chọn **CRON schedule builder**.
- Mục **Maintenance Window run frequency**, chọn **Every Saturday at 12:00**.
- Mục **Maintenance duration** nhập `3`.
- Mục **Maintenance window name**, nhập `NISTPatch` hoặc tên bạn muốn.
- Mục **Patching Operation**, chọn **Scan and install**.
- Nhấn **configure patching**.

![FCJ_ws2](/images/2.scenario/1.png)

![FCJ_ws2](/images/2.scenario/2.png)

![FCJ_ws2](/images/2.scenario/3.png)

4. Bây giờ bạn đã thiết lập lịch vá lỗi, hãy nhấp vào **Patch Now** để biết trạng thái vá lỗi hiện tại.

![FCJ_ws2](/images/2.scenario/4.png)

5. Để lại cấu hình mặc định **Scan**, và nhấn **Patch Now**.

![FCJ_ws2](/images/2.scenario/5.png)

6. Đợi một lát để thao tác hoàn tất. Bạn sẽ thấy thông báo thành công.

![FCJ_ws2](/images/2.scenario/6.png)

7. Để set up detection mechanism, truy cập [AWS Config](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#), Nhấn **Rules**, và **Add rule**.

![FCJ_ws2](/images/2.scenario/7.png)

8. Chọn **AWS Config Managed rule** và tìm: **`ec2-managedinstance-patch-compliance-status-check`**. Nhấn **Next**.

![FCJ_ws2](/images/2.scenario/8.png)

9. Mục **trigger type** giữ nguyên: **all changes**. Nhấn **Next**.

![FCJ_ws2](/images/2.scenario/9.png)

10. Xem xét và xác nhận **Details**. Nhấn **Add rule**.

![FCJ_ws2](/images/2.scenario/10.png)

11. Thông báo thành công sẽ xuất hiện ở trên.

![FCJ_ws2](/images/2.scenario/11.png)

12. Nhấn rule **ec2-managedinstance-patch-compliance-status-check**.

13. Nhấn **Actions / Re-evaluate**.

14. Mục **Resources** trong drop-down, chọn **All**.

15. Xác nhận **Compliance status** là **Compliant**.

![FCJ_ws2](/images/2.scenario/12.png)

**Bạn đã hoàn thành bài tập đầu tiên. Hãy chuyển sang phần tiếp theo.**
