---
title: "Dọn dẹp"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

### Dọn dẹp

{{% notice note  %}}
Nếu bạn đang sử dụng điều này trong phiên LED của người hướng dẫn, với AWS Event Engine, bạn không cần phải chạy các bước dọn dẹp.
{{% /notice %}}

Để ngăn chặn các khoản phí, chúng tôi khuyên bạn nên dọn dẹp cơ sở hạ tầng được tạo ra trong workshop này. Nếu bạn có kế hoạch giữ mọi thứ hoạt động để bạn có thể học hỏi thêm sau, hãy nhớ dọn dẹp khi bạn hoàn thành.

{{% notice note  %}}
Nếu bạn đang sử dụng tài khoản của riêng bạn. Bạn sẽ cần phải xóa thủ công một số tài nguyên trước khi bạn xóa các stack CloudFormation, vì vậy vui lòng thực hiện các bước sau theo thứ tự.
{{% /notice %}}

1. Tắt EC2 instances

- Mở **EC2 console**
- Chọn instance
- Nhấn **Actions**, **Instance settings**,sau đó **change termination protection**
- Bỏ chọn **Enable**, Nhấn **Save**.

2. Xóa CloudFormation stack

- Truy cập **AWS CloudFormation** console
- Chọn stack mà bạn sử dụng
- Chọn Action
- Nhấn **Delete Stack**

3. Truy cập **AWS Config** console

- Nhấn **Controls**
- Xóa các điều khiển không cần thiết.
