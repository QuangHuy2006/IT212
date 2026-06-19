# Bài 4: Thiết kế Prompt 

## Prompt

```text id="b7u4nx"
Act as a Senior System Analyst và Performance Architect.

Dự án Guai-api đã được index đầy đủ. 
Hãy xây dựng phần Non-Functional Requirements (NFR) cho hệ thống theo chuẩn tài liệu SRS 
dành cho môi trường thương mại điện tử có lưu lượng truy cập lớn (High Traffic System).

Mục tiêu:
Phân tích kiến trúc hiện tại của Guai-api và 
đề xuất các yêu cầu phi chức năng có chỉ tiêu kỹ thuật rõ ràng, có thể đo lường và kiểm thử 

Bắt buộc phải phân tích chi tiết 3 nhóm yêu cầu sau:

1. Response Time Requirements - Thời gian phản hồi

Đối với các API tra cứu sản phẩm như:

- GET /api/v1/products
- GET /api/v1/products/{id}
- GET /api/v1/products/search

Hãy mô tả:

- Thời gian phản hồi trung bình (Average Response Time).
- Thời gian phản hồi tối đa (Maximum Response Time).
- Mức SLA mong muốn.
- Response Time dưới điều kiện tải cao.
- Số lượng request đồng thời hệ thống phải đáp ứng.

Yêu cầu đưa ra số liệu cụ thể (ví dụ: < 200ms, < 500ms, 1000 concurrent users,...).

------------------------------------------------

2. MySQL Database Performance & Indexing

Phân tích:

- Các bảng có tần suất truy vấn cao.
- Các cột nên tạo Index.
- Có cần Composite Index hay không.
- Trường hợp nào cần Unique Index.
- Đề xuất Index cho:

  + username
  + email
  + product_name
  + category_id
  + created_at
  + order_status

Ngoài ra hãy đánh giá:

- Ảnh hưởng của Index đến INSERT/UPDATE.
- Chiến lược tối ưu truy vấn JOIN.
- Chiến lược phân trang (Pagination).
- Khả năng mở rộng dữ liệu trong tương lai.

------------------------------------------------

3. JWT Security & Latency Requirements

Phân tích luồng:

Login -> Generate JWT -> Client Request -> JWT Validation -> Authorization.

Hãy đặc tả:

- Thời gian tối đa để cấp JWT sau khi đăng nhập.
- Thời gian tối đa để xác thực JWT trên mỗi request.
- Thời gian sống (Expiration Time) của Access Token.
- Có sử dụng Refresh Token hay không.
- Cơ chế xử lý JWT Expired.
- Cơ chế chống JWT giả mạo.
- Thuật toán ký JWT được khuyến nghị.
- Cách bảo vệ Secret Key.
- Cơ chế thu hồi token (Token Revocation).

------------------------------------------------

Ràng buộc:

- Tất cả yêu cầu phải có chỉ số định lượng cụ thể.
- Các tiêu chí phải đo lường được.
- Phải giải thích lý do lựa chọn các chỉ tiêu kỹ thuật.
- Chỉ tập trung vào NFR, không mô tả Functional Requirement.
- Không sinh code Java.

------------------------------------------------

Định dạng đầu ra:

1. NFR Overview

2. Response Time Requirements
   - Metric
   - Target Value
   - SLA
   - Measurement Method

3. Database Performance Requirements
   - Query Performance
   - Indexing Strategy
   - Pagination Strategy
   - Scalability

4. JWT Security Requirements
   - Token Generation
   - Token Validation
   - Expiration Policy
   - Secret Management
   - Token Revocation

5. Performance Risks and Recommendations

Sử dụng văn phong chuyên nghiệp theo chuẩn tài liệu SRS của doanh nghiệp.
```
