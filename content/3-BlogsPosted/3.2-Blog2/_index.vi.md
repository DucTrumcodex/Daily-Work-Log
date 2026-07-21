---
title: "RAG Đa Phòng Ban An Toàn Với Amazon Bedrock"
date: 2026-07-21
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---
## Secure Multi-Tenant RAG: Kiểm Soát Truy Cập Tài Liệu Trong Doanh Nghiệp Với Amazon Bedrock và Verified Permissions

## 1. Vai trò và bối cảnh nghiên cứu
Doanh nghiệp lớn khi xây ứng dụng GenAI nội bộ thường gặp bài toán:
- Mỗi phòng ban chỉ được xem tài liệu của mình.
- Lãnh đạo cần quyền truy cập xuyên phòng ban.
- Không muốn dựng Knowledge Base riêng cho từng team (tốn kém và khó vận hành).

RAG giúp gắn foundation model với dữ liệu doanh nghiệp theo thời gian thực. Bài viết mở rộng pattern metadata filtering trước đó bằng cách **đưa logic phân quyền ra ngoài code**, quản lý bằng Cedar policy trên Amazon Verified Permissions — cập nhật quyền trong vài phút, không cần redeploy.

## 2. Các điểm nổi bật về kỹ thuật

### Isolation model
- Pattern này cung cấp **logical isolation** (filter-level), không phải hard isolation bằng IAM boundary giữa các khách hàng SaaS.
- Phù hợp cho **intra-tenant**: phòng ban/team/role trong cùng một tổ chức.
- Không thay thế kiến trúc “một Knowledge Base mỗi tenant” khi cần ranh giới tuân thủ cứng giữa các tổ chức khác nhau.

### Defense-in-depth với 2 lớp độc lập
| Lớp | Thành phần | Quyết định | Khi deny |
| --- | --------- | ---------- | -------- |
| **Layer 1** | Lambda Authorizer trên API Gateway | User có được gọi API không? | Trả 403 |
| **Layer 2** | Middleware Lambda | User được truy cập department tags nào? | Empty result set |
| **KB filter** | Amazon Bedrock Knowledge Bases | Áp metadata filter trước retrieval | Loại document trái phép |
| **Guardrails** | Amazon Bedrock Guardrails | Response có grounded không? | Chặn/sửa response |

Hai lớp gọi Verified Permissions độc lập và theo hướng **fail closed (deny-by-default)**. Nếu Layer 1 bị bypass, Layer 2 vẫn giữ cô lập ở mức document.

### Ingestion pipeline gắn metadata
1. Upload document lên S3 theo prefix phòng ban (`docs/dept-a/report.pdf`).
2. EventBridge → SQS → Lambda ghi file sidecar `.metadata.json` với attribute `department`.
3. Lambda lịch trình (mỗi 5 phút) gọi `StartIngestionJob`.
4. Document không có sidecar bị loại khỏi ingestion để tránh lọt filter.

### Query flow
1. User gửi câu hỏi kèm JWT từ Amazon Cognito (có claim `cognito:groups`).
2. CloudFront + AWS WAF kiểm soát ở edge.
3. Lambda Authorizer đánh giá Cedar policy (Layer 1).
4. Middleware Lambda hỏi Verified Permissions, dựng metadata filter, gọi `RetrieveAndGenerate`.
5. Foundation model chỉ nhận context từ document đã được phép.

Ví dụ Cedar policy: `dept-a` chỉ query Knowledge Base của mình; `dept-c` (leadership) có quyền query xuyên phòng ban.

## 3. Những dịch vụ AWS xuất hiện trong kiến trúc
- **Amazon Bedrock Knowledge Bases** (RAG + metadata filtering)
- **Amazon Verified Permissions** + Cedar policies
- **Amazon Cognito** (JWT + group claims)
- **Amazon API Gateway** + Lambda Authorizer
- **AWS Lambda** (tagging, authorizer, middleware)
- **Amazon S3, EventBridge, SQS**
- **Amazon CloudFront + AWS WAF**
- **Amazon Bedrock Guardrails**
- **Amazon DynamoDB** (session context)
- **AWS CloudTrail / CloudWatch** (audit & monitoring)

## 4. Giá trị thực tế
- **Một Knowledge Base dùng chung**: giảm chi phí và overhead vận hành so với KB riêng cho từng phòng ban.
- **Đổi quyền không cần redeploy**: sửa Cedar policy trên console, request tiếp theo áp dụng ngay.
- **Audit trail sẵn có**: CloudTrail ghi mọi quyết định `IsAuthorized`.
- **Mở rộng linh hoạt**: thêm phòng ban = thêm policy + gắn metadata document, không cần stack hạ tầng mới.

## 5. Hạn chế và lưu ý triển khai
- Đây là **filter-level isolation**: nếu middleware fail-open, document có thể bị lộ — cần thiết kế deny-by-default chặt.
- Có race condition ngắn giữa lúc upload và lúc sidecar được tạo; cần safeguard trước khi ingestion.
- Không dùng pattern này thay cho hard tenant isolation giữa các khách hàng/organization khác nhau.
- Cần bảo vệ sidecar metadata (S3 Versioning, hạn chế `PutObject`, cảnh báo CloudTrail/CloudWatch).
- Chi phí phát sinh từ Bedrock, Lambda, Cognito, Verified Permissions… — nên clean up sau khi lab.

## Kết luận
Pattern Secure Multi-Tenant RAG cho thấy doanh nghiệp có thể phục vụ nhiều phòng ban từ một ứng dụng RAG duy nhất, vẫn giữ cô lập tài liệu ở mức logic, cập nhật quyền linh hoạt và có audit đầy đủ. Kết hợp Amazon Bedrock Knowledge Bases với Amazon Verified Permissions là hướng tiếp cận rất thực tiễn cho GenAI nội bộ cần bảo mật và kiểm soát truy cập tinh.

*Nguồn: AWS Architecture Blog*
*Tài liệu gốc: [Secure multi-tenant RAG with Amazon Bedrock and Verified Permissions](https://aws.amazon.com/blogs/architecture/secure-multi-tenant-rag-with-amazon-bedrock-and-verified-permissions/)*
