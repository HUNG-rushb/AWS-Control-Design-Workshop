---
title: "Quản lý danh tính và quyền truy cập"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 2.4 </b> "
---

## Quản lý danh tính và quyền truy cập (IAM)

### Giới thiệu

**Thời gian để hoàn thành**: 25 phút

**Mục tiêu thực hành**:

- Cấu hình IAM policies để ngăn chặn việc traffic mirroring trái phép vào mạng VPC của bạn.
- Triển khai **AWS Config custom rule** để phát hiện traffic mirroring trên VPC.

### Bài tập

#### Tình huống

Nhóm Voyager Security đã cung cấp các biện pháp kiểm soát và yêu cầu kỹ thuật để đảm bảo rằng các nhà phát triển của họ tuân theo các đặc quyền tối thiểu khi quản lý các thành phần mạng VPC của họ. Nhóm nhà phát triển đang sử dụng **IAM Role DevOps** hiện đủ cho phép để cho phép họ đặt trình nghe nhân bản lưu lượng truy cập vào tài khoản mà nhóm bảo mật không biết. Ngay cả khi lưu lượng truy cập này là nội bộ, nó được coi là bị hạn chế cao. Mục tiêu của bạn với tư cách là **Cloud Architect** sẽ là định cấu hình các quyền IAM phù hợp để ngăn chặn hành động nhân bản lưu lượng truy cập và cơ chế thám tử để cảnh báo.

**NIST yêu cầu**

| Control ID |                                                                                                     Miêu tả control                                                                                                      |
| ---------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| AC-4(21)   | Phân tách các luồng thông tin một cách logic hoặc vật lý bằng cách sử dụng [Nhiệm vụ: cơ chế và/hoặc kỹ thuật do tổ chức xác định] để hoàn thành [Nhiệm vụ: phân tách bắt buộc do tổ chức xác định theo loại thông tin]. |
| AC-3       |                                    Thực thi các ủy quyền đã được phê duyệt để truy cập hợp lý vào thông tin và tài nguyên hệ thống theo các chính sách kiểm soát truy cập hiện hành.                                     |

**Khách hàng yêu cầu**

| Control ID          |                                                          Miêu tả control                                                          |
| ------------------- | :-------------------------------------------------------------------------------------------------------------------------------: |
| Voyager-ctrl-net-05 | Nhà phát triển phải bị hạn chế thực hiện các thay đổi đối với tài nguyên VPC có thể khiến mạng tiếp cận các tài nguyên trái phép. |

**AWS đánh giá tài nguyên**

Bằng cách xem xét các tài nguyên sẵn có sau đây, bạn sẽ đi đến kết luận những gì cần phải làm để đáp ứng các yêu cầu kiểm soát của Voyager. Xem phần kết luận bên dưới.

| Loại                    |                                                                                 Tên                                                                                  |                                                                                                                      Kết luận                                                                                                                       |
| ----------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| AWS service IAM actions |                              [Amazon VPC actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/OperationList-query-vpc.html)                               |                                               Sử dụng **AWS Systems Manager Patch Manager** để tự động hóa quá trình cài đặt các bản cập nhật liên quan đến bảo mật cho cả hệ điều hành và ứng dụng.                                                |
| AWS service user guide  |                       [AWS Config / Managed rules](https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html)                        |                                                              Không có sẵn quy tắc cấu hình AWS để phát hiện traffic mirror nên chúng ta cần xây dựng một quy trình kiểm tra tùy chỉnh.                                                              |
| AWS Service user guide  | [AWS Config / Creating AWS Config custom Lambda rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_develop-rules_lambda-functions.html) | Bạn có thể phát triển các quy tắc tùy chỉnh và thêm chúng vào **AWS Config** bằng các hàm **AWS Lambda**. Bạn liên kết từng quy tắc tùy chỉnh với hàm Lambda, hàm này chứa logic đánh giá xem tài nguyên AWS của bạn có tuân thủ quy tắc hay không. |

### Hướng dẫn

1. Mở [IAM Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#), mục **Roles**, tìm `DevOps`, và nhấn vào **Role Name**. (nó có thể có một hậu tố thay thế)

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/211.png)

2. Mở rộng phần **Policy** bằng cách nhấn vào dấu (+). Nhấn **Edit**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/212.png)

3. Chọn JSON tab. Loại bỏ quyền tạo **Traffic Mirror sessions** bằng cách thêm vào 2 action bên dưới vào mục **NotAction**.

```
"ec2:CreateTrafficMirrorSession"
"ec2:CreateTrafficMirrorTarget"
```

Có thể đơn giản hóa thành một phần tử bằng cách sử dụng wildcard như thế này:

```
"ec2:CreateTrafficMirror*"
```

4. Chọn **Review policy** và **Save changes**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/213.png)

Bây giờ bạn đã giảm các đặc quyền **DevOpsRole**, đã đến lúc tạo một cơ chế để phát hiện xem **traffic mirror** có được bật hay không. Để làm điều đó, bạn sẽ tạo một quy tắc AWS Config tùy chỉnh mới. Chúng tôi đã chuẩn bị mẫu **CloudFormation** để xác thực xem **TrafficMirror Sessions** hoặc **Targets** có sẵn sàng hay không. **Template cloudFormation** này tạo **Config rule** cùng với **Lambda function** và các quyền cần thiết để phát hiện khi có **Traffic Mirror**. (Đây là ví dụ về cách triển khai các điều khiển tùy chỉnh để phù hợp với nhu cầu của bạn)

5. [Tải về CloudFormation template](https://static.us-east-1.prod.workshops.aws/public/7609f68d-8f02-45f1-ac24-da0e810d440f/static/Custom-Rule-Traffic-Mirror.yaml).

6. Mở [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=traffic-mirror) và nhấn **create a new stack**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/214.png)

7. Chọn **Template is ready**, Chọn **Upload a template file**. Nhấn **Choose a file**. Chọn file vừa tải và nhấn **Next**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/215.png)

8. Nhập `traffic-mirror` và nhấn **Next**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/216.png)

9. Giữ nguyên và nhấn **Next**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/217.png)

10. Xác nhận checkbox và nhấn **Create stack**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/218.png)

11. Kiểm tra status, nhấn **refresh** cho tới khi thành công.
12. Tới [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#), vào **Rules**, hãy xác minh rằng config rule mới đã được tạo.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/219.png)

13. Để thử nghiệm rule này, tới [VPC console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#)

14. Bên trái, dưới **Traffic Mirroring**, chọn **Mirror targets**.

15. Phía trên bên phải, nhấn **Create a new Traffic Mirror target**. (Hãy nhớ rằng ban đầu chúng tôi đã giảm các đặc quyền của vai trò DevOps nhưng không có đặc quyền của bạn).

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/220.png)

16. Nhấn **Target** textbox and chọn any of the **EC2 ENI**. Nhấn **Create**.

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/221.png)

17. Trở về [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#) chọn rule vừa tạo, và nhấn **Re-evaluate**. Đợi một lát và làm mới. Rule nên thay đổi từ **Compliant** sang **Noncompliant**

![FCJ_ws2](/AWS-Control-Design-Workshop/images/2.scenario/222.png)

**Chúc mừng! Bạn đã hoàn thành bài tập cuối cùng.**
