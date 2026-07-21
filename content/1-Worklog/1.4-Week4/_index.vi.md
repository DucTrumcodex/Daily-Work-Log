---
title: "Worklog Tuần 4"
date: 2026-05-25
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---



### Mục tiêu tuần 4:

* Nắm vững AWS IAM: User, Group, Role, Policy.
* Thiết lập phân quyền theo nguyên tắc least privilege.
* Kích hoạt MFA cho tài khoản/user quan trọng.
* Thực hành gắn policy và kiểm tra quyền truy cập.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu IAM User, Group, Policy <br> - Tạo user/group và gắn quyền cơ bản | 25/05/2026 | 25/05/2026 | <https://000002.awsstudygroup.com/> |
| 3 | - Tìm hiểu IAM Role và trust policy <br> - Thực hành assume role | 26/05/2026 | 26/05/2026 | <https://000002.awsstudygroup.com/> |
| 4 | - Viết và gắn IAM Policy theo least privilege <br> - Kiểm tra quyền bằng Console/CLI | 27/05/2026 | 27/05/2026 | <https://000002.awsstudygroup.com/> |
| 5 | - Bật MFA cho user <br> - Rà soát quyền thừa và chỉnh lại | 28/05/2026 | 28/05/2026 | - |
| 6 | - Tổng hợp kiến thức IAM tuần 4 <br> - Dọn dẹp user/role lab không dùng | 29/05/2026 | 29/05/2026 | - |


### Kết quả đạt được tuần 4:

* IAM cơ bản:
  * Hiểu rõ vai trò của User, Group, Role và Policy trong việc quản lý quyền truy cập AWS.
  * Phân biệt được khi nào nên dùng User/Group và khi nào nên dùng Role để giả định quyền.

* Phân quyền an toàn:
  * Biết thiết lập quyền theo nguyên tắc least privilege, chỉ cấp đúng quyền cần thiết cho từng tác vụ.
  * Thực hành viết và gắn policy, đồng thời kiểm tra quyền bằng Console/CLI để xác nhận cấu hình đúng.

* Bảo mật đăng nhập:
  * Đã kích hoạt MFA cho user quan trọng, giúp giảm rủi ro bị chiếm tài khoản.
  * Rà soát và thu hẹp các quyền thừa sau khi lab, tránh để lại cấu hình không an toàn.
