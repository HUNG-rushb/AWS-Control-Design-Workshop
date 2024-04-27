---
title: "Không tham dự event của AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 1.2 </b> "
---

### Không tham dự event của AWS

{{% notice info %}}
Nếu bạn đang tham dự một sự kiện do AWS tổ chức, bạn có thể ngừng đọc phần tiếp tục với bước tiếp theo bằng cách sử dụng các tài khoản được cung cấp.
{{% /notice %}}

1. Tải xuống (chuột phải và nhấn Save link as...) [CloudFormation template cho kiến trúc cơ bản](https://static.us-east-1.prod.workshops.aws/public/7609f68d-8f02-45f1-ac24-da0e810d440f/static/architecture.json) cần cho workshop này.

2. Sử dụng tài khoản thử nghiệm của riêng bạn (vui lòng không sử dụng tài khoản product) đi tới [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=control-design)

3. Nhấn vào **create stack**, sau đó **With new resources (standard)**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/2.png)

4. Nhập Stack name `control-design`. Nhấn Next

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/4.png)

5. Sử dụng **default stack options**. Nhấn Next

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/3.png)

6. Kiểm tra lại **Details**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/5.png)

7. Nhấn **"I acknowledge..."** checkbox. Nhấn **Create stack**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/6.png)

8. Đợi tất cả các tài nguyên Khởi tạo. Nếu bất kỳ lỗi nào xuất hiện, hãy tham khảo phần **Hỗ trợ/Troubleshooting**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/1.introduce/7.png)

{{% notice info %}}
Khi bạn hoàn thành workshop này, hãy đến phần **Hỗ trợ/Dọn dẹp**, để giảm chi phí phát sinh.
{{% /notice %}}
