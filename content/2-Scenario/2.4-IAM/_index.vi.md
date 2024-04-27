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

1. Open to the [IAM Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#) under the Roles section, search for the DevOps role, and click over the Role Name. (it may have an aleatory suffix)

![FCJ_ws2](/images/2.scenario/211.png)

2. Expand the Policy clicking on the (+) sign. Click the Edit button.

![FCJ_ws2](/images/2.scenario/212.png)

3. Select the JSON tab. Modify the policy to remove the ability to create Traffic Mirror sessions under the NotAction policy element. These two actions:

```
"ec2:CreateTrafficMirrorSession"
"ec2:CreateTrafficMirrorTarget"
```

Can be simplified into one element using a wildcard like this:

```
"ec2:CreateTrafficMirror*"
```

4. Select Review policy and then save changes.

![FCJ_ws2](/images/2.scenario/213.png)

Now that you have reduced the DevOpsRole privileges, it's time to create a mechanism to detect if a traffic mirror is enabled. To do it you are going to create a new custom AWS Config rule. We have prepared a CloudFormation template that validates if TrafficMirror Sessions or Targets are in place. This cloudFormation template creates a Config rule that together with a Lambda function and the necessary permissions detects when a Traffic Mirror is in place. (This is an example of how custom controls can be implemented to suit your needs)

5. [Download this CloudFormation template](https://static.us-east-1.prod.workshops.aws/public/7609f68d-8f02-45f1-ac24-da0e810d440f/static/Custom-Rule-Traffic-Mirror.yaml) to your disk.

6. Open the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=traffic-mirror) and click create a new stack.

![FCJ_ws2](/images/2.scenario/214.png)

7. Select Template is ready and select Upload a template file. Click on Choose a file. Select the file you just downloaded to your disk. Click Next.

![FCJ_ws2](/images/2.scenario/215.png)

8. Provide the following name to the stack, and click Next.

![FCJ_ws2](/images/2.scenario/216.png)

9. Leave the stack options as is, and click Next.

![FCJ_ws2](/images/2.scenario/217.png)

10. Select the check box to acknowledge and press Create stack.

![FCJ_ws2](/images/2.scenario/218.png)

11. Review the status, click on the refresh button until it has finished successfully.
12. Go to the [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#) and under rules option verify that the new config rule was created.

![FCJ_ws2](/images/2.scenario/219.png)

13. To test this rule, go to [VPC console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#)

14. On the left under Traffic Mirroring, select Mirror targets.

15. On the top right, click on Create a new Traffic Mirror target. (Keep in mind that initially we have reduced the privileges of the DevOps role but not yours).

![FCJ_ws2](/images/2.scenario/220.png)

16. Click on the Target textbox and select any of the EC2 ENI. Click Create.

![FCJ_ws2](/images/2.scenario/221.png)

17. Go back to the [AWS Config console](https://us-east-1.console.aws.amazon.com/config/home?region=us-east-1#) select the rule we created, and click Re-evaluate. Wait a moment and refresh. The rule should change from Compliant status to Noncompliant

![FCJ_ws2](/images/2.scenario/222.png)

**Chúc mừng! Bạn đã hoàn thành bài tập cuối cùng.**
