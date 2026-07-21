---
title: "Worklog Tuần 10"
date: 2026-07-06
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu tuần 10:

* Tiếp tục phát triển đồ án: hoàn thiện API/luồng Avatar và Shop.
* Tích hợp Amazon EventBridge để tự động hóa tác vụ trong đồ án.
* Kiểm thử các luồng mới và sửa lỗi phát sinh.
* Cập nhật tài liệu + sơ đồ kiến trúc theo phần đã làm.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Hoàn thiện API/luồng Avatar trong đồ án <br> - Rà soát quyền S3/CloudFront đang dùng | 06/07/2026 | 06/07/2026 | - |
| 3 | - Thiết kế use-case EventBridge cho đồ án <br> - Tạo rule/target kích hoạt tác vụ tự động | 07/07/2026 | 07/07/2026 | - |
| 4 | - Tích hợp EventBridge vào luồng xử lý đồ án <br> - Test trigger và kết quả xử lý | 08/07/2026 | 08/07/2026 | - |
| 5 | - Fix bug Avatar/Shop/EventBridge <br> - Tối ưu retry/timeout nếu cần | 09/07/2026 | 09/07/2026 | - |
| 6 | - Cập nhật sơ đồ kiến trúc đồ án <br> - Ghi chú cấu hình EventBridge đã triển khai | 10/07/2026 | 10/07/2026 | - |


### Kết quả đạt được tuần 10:

* Phát triển đồ án:
  * Hoàn thiện thêm luồng Avatar và các phần liên quan đến Shop trong phạm vi tuần.
  * Tích hợp được EventBridge cho ít nhất một tác vụ tự động của đồ án.

* Kiểm thử & tài liệu:
  * Kiểm thử trigger event, ghi nhận và sửa lỗi chính.
  * Cập nhật sơ đồ/tài liệu phản ánh đúng phần EventBridge đã thêm vào đồ án.
