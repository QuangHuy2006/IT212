# Bài 2: Thiết kế Prompt hoàn chỉnh cho Module Authentication của Shop AI

## Prompt theo cấu trúc 5 thành phần

### 1. Vai trò (Role)

Bạn là một **Senior System Analyst** có kinh nghiệm xây dựng tài liệu SRS 
cho các hệ thống thương mại điện tử sử dụng Spring Boot, JWT Authentication và RBAC (Role-Based Access Control).

### 2. Mục tiêu (Task)

Hãy viết phần **Functional Requirement** cho module **Authentication** của dự án **Shop AI**, 
tập trung vào chức năng Đăng nhập (Login), xác thực JWT và phân quyền RBAC.

### 3. Ngữ cảnh (Context)

Dự án Shop AI là hệ thống thương mại điện tử cho phép người dùng đăng nhập bằng tài khoản đã đăng ký. 
Sau khi xác thực thành công, hệ thống sẽ sinh JWT Token để người dùng truy cập các API được cấp quyền. 
Việc phân quyền được thực hiện theo mô hình RBAC với các vai trò như USER, STAFF và ADMIN.

Hãy mô tả đầy đủ:

* Mục tiêu của chức năng đăng nhập.
* Điều kiện tiên quyết (Pre-conditions).
* Luồng xử lý chính (Main Flow).
* Kết quả trả về khi đăng nhập thành công.
* Cách JWT được sinh và sử dụng.
* Cơ chế kiểm tra quyền truy cập dựa trên RBAC.

### 4. Ràng buộc (Constraints)

Bắt buộc phải mô tả chi tiết cách hệ thống xử lý các trường hợp ngoại lệ sau:

1. Người dùng nhập sai mật khẩu quá 5 lần:

    * Tài khoản có bị khóa tạm thời hay không.
    * Thời gian khóa là bao lâu.
    * Hệ thống trả về thông báo lỗi nào.

2. JWT Token hết hạn (Expired) trong khi phiên làm việc đang diễn ra:

    * Hệ thống phát hiện token hết hạn như thế nào.
    * API trả về mã lỗi gì.
    * Người dùng cần thực hiện hành động gì tiếp theo.

3. Tài khoản ở trạng thái Inactive nhưng vẫn cố gắng đăng nhập:

    * Hệ thống kiểm tra trạng thái tài khoản ở bước nào.
    * Từ chối đăng nhập ra sao.
    * Thông báo lỗi trả về cho người dùng.

Ngoài ra:

* Không mô tả ở mức mã nguồn.
* Không sinh code Java.
* Chỉ tập trung vào đặc tả nghiệp vụ.
* Các ngoại lệ phải được mô tả riêng thành mục Exceptions.

### 5. Định dạng đầu ra (Format)

Hãy trình bày tài liệu theo định dạng SRS:

1. Functional Requirement Overview
2. Actors
3. Pre-conditions
4. Main Flow
5. Post-conditions
6. Exceptions

    * Wrong Password (> 5 attempts)
    * Expired JWT Token
    * Inactive Account Login Attempt
7. Security Requirements
8. Business Rules

Sử dụng văn phong chuyên nghiệp, rõ ràng và phù hợp với tài liệu SRS của doanh nghiệp.
