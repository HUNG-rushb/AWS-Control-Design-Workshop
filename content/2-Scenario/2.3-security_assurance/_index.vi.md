---
title: "Đảm bảo an ninh"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

## Đảm bảo an ninh

### Giới thiệu

**Thời gian để hoàn thành**: 25 phút

**Mục tiêu thực hành**:

- Logging tại level OS với **Amazon CloudWatch**.
- Setup thông báo với OS events.

### Bài tập

#### Tình huống

Nhóm Voyager Security đã cung cấp các yêu cầu kỹ thuật để đảm bảo rằng logging của Hệ điều hành (OS) được ghi lại và cảnh báo được gửi đến các nhóm giám sát và phản hồi đối với các sự kiện quan trọng. Ví dụ: quyền truy cập root sẽ được coi là một sự kiện quan trọng và sẽ kích hoạt cảnh báo cho nhóm giám sát qua email. Với tư cách là Kiến trúc sư đám mây, bạn sẽ cần hiểu kiến ​​trúc hiện tại và xác định, dựa trên Quy tắc quản lý **AWS CloudWatch** và **AWS Config** hoặc các gói tuân thủ, những tùy chọn nào có sẵn để đặt cấu hình giải pháp logging đáp ứng yêu cầu của khách hàng.

**NIST yêu cầu**

| Control ID |                 Miêu tả control                 |
| ---------- | :---------------------------------------------: |
| AC-6(9)    |   Ghi logs thực hiện các chức năng đặc quyền.   |
| AU-2       |                  Event Logging                  |
| AU-6(1)    | AĐánh giá, phân tích và báo cáo hồ sơ kiểm toán |

**Khách hàng yêu cầu**

| Control ID          |                                                                       Miêu tả control                                                                       |
| ------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-mon-01 | Hệ thống phải được giám sát thích hợp. Logs cần được ghi lại ở cấp hệ điều hành và các cảnh báo sẽ cần được kích hoạt và gửi đến nhóm SOC khi sử dụng root. |

**AWS đánh giá tài nguyên**

Bằng cách xem xét các tài nguyên sẵn có sau đây, bạn sẽ đi đến kết luận những gì cần phải làm để đáp ứng các yêu cầu kiểm soát của Voyager. Xem phần kết luận bên dưới.

| Loại                        | Tên                                                                                                                                                                                                                                  | Kết luận                                                                                                                                                                                                                                                                                                                   |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS service user guide      | [AWS CloudWatch / What is Amazon CloudWatch Logs? ](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)                                                                                              | **CloudWatch Logs** cho phép bạn tập trung logs từ tất cả các hệ thống, ứng dụng và dịch vụ AWS mà bạn sử dụng trong một dịch vụ duy nhất có khả năng mở rộng cao.                                                                                                                                                         |
| AWS service user guide      | [AWS CloudWatch agent guide / Collecting metrics and logs from Amazon EC2 instances and on-premises servers with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) | Cách tiếp cận gốc trên nền tảng đám mây để ghi lại logs hệ điều hành là cài đặt **AWS CloudWatch agent** vào từng instance.                                                                                                                                                                                                |
| AWS service user guide      | [CloudWatch Alarm events and EventBridge](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-eventbridge.html)                                                                                            | **CloudWatch** gửi sự kiện tới **Amazon EventBridge** bất cứ khi nào cảnh báo **CloudWatch** được tạo, cập nhật, xóa hoặc thay đổi trạng thái cảnh báo. Bạn có thể sử dụng **EventBridge** và những sự kiện này để viết các quy tắc thực hiện hành động, chẳng hạn như thông báo cho bạn khi cảnh báo thay đổi trạng thái. |
| AWS Conformance pack (NIST) | [AWS operational best practices for NIST 800 53 rev5](https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-nist-800-53_rev_5.html)                                                                | (Tìm kiếm AC-6(9), AU-2, AU-6(1)) Sử dụng **Amazon CloudWatch** để thu thập và quản lý tập trung hoạt động sự kiện logs. Việc đưa dữ liệu **AWS CloudTrail** vào sẽ cung cấp thông tin chi tiết về hoạt động lệnh gọi API trong tài khoản AWS của bạn.                                                                     |

### Hướng dẫn

#### Giới thiệu CloudWatch Logs

Logs là một công cụ thiết yếu để có được khả năng hiển thị về những gì đang xảy ra trong phiên bản của bạn. Ngoài số liệu, **CloudWatch** cho phép bạn thu thập và tìm kiếm logs.

Trước khi đi sâu vào **CloudWatch Logs**, chúng ta cần hiểu các khái niệm về luồng logs và nhóm logs.

Luồng logs là một chuỗi các sự kiện logs đến từ cùng một nguồn (ví dụ: phiên bản EC2).

Nhóm logs là một nhóm các luồng logs có cùng cài đặt lưu giữ, giám sát và kiểm soát truy cập. Được sử dụng để nhóm logic các luồng logs. Nếu các phiên bản EC2 đang chạy cùng một ứng dụng, chúng ta có thể nhóm các luồng logs của chúng thành một nhóm logs.

#### Phân tích CloudWatch Logs

Các EC2 instances tạo ra rất nhiều file log. Hệ điều hành Linux có logs riêng và ứng dụng của chúng tôi chạy trên đó cũng có thể tạo logs riêng.

Logs được thu thập bởi **CloudWatch agent** , một ứng dụng agent (tùy chọn) chạy trongEC2 instances.

CloudWatch agent được cài đặt sẵn và định cấu hình để thu thập nhật ký trong tất cả các EC2 instance cho phần này của workshop.

1. Để xem logs, truy cập [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)
2. Ở menu bẹn trái, mục **Logs**, chọn **Log groups**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/111.png)

3. Đã có sẵn 1 log group **/var/log/secure**. Nhấp vào nó và bạn sẽ thấy chi tiết log group và một vài luồng log bên dưới cho từng instance.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/112.png)

4. Nhấn **Actions/Edit retention setting**. Chọn thời gian hết hạn mong muốn. (đối với workshop này chọn 12 tháng). Nhấn **Save**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/113.png)

5. Nhấn **Actions** phía trên bên phải và nhấn **Create metric filter**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/114.png)

6. Xác định pattern để tìm kiếm. Bạn có thể nhập `session opened for user root` và nó sẽ tìm logs. Nhấn Next.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/115.png)

7. Hoàn thành chi tiết metric filter như trong hình. Nhấn **Next**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/116.png)

8. Kiếm tra lại và nhấn **Create metric filter**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/117.png)

9. Nhấn **Audit** bên dưới **Metric**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/118.png)

10. Một biểu đồ trống sẽ xuất hiện. **CloudWatch** sẽ chỉ tạo số liệu từ các sự kiện được tạo sau khi số liệu được tạo.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/119.png)

11. Truy cập [EC2 console](https://us-east-1.console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:), vào **Instances** và chọn 1 instance, nhấn **Connect**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/120.png)

12. Chọn **Session Manager**, nhấn **Connect**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/121.png)

13. Khi kết nối thiết bị đầu cuối mở ra. bạn có thể chạy `sudo su` để chuyển lên root user. Bằng cách này, bạn sẽ tạo thêm các sự kiện trong log. nhấn **Terminate** 2 lần.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/122.png)

14. Trở về [CloudWatch console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1#). Bên trái bên dưới **Metrics**, nhấn **All Metrics**, và dưới **Custom namespaces**, nhấn **Audit** và **Metrics with no dimensions**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/123.png)

15. Kiểm tra phiên đã mở cho hộp kiểm gốc của người dùng. Một dấu chấm có phiên chúng tôi đã tạo sẽ xuất hiện trong biểu đồ. (Có thể mất tối đa 5 phút để thông tin được hiển thị)

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/124.png)

16. (Tùy chọn) Tìm hiểu thêm về pattern [CloudWatch documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)

17. (Tùy chọn) Một khi bạn đã cấu hình metric, bạn có thể cấu hình [CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) để nhận thông báo mỗi khi có user đăng nhập bằng root user.

**Bạn đã hoàn thành bài tập thứ ba. Hãy chuyển sang bài tập tiếp theo.**
