# Bài 3: Thiết kế Prompt cho Antigravity 

## Prompt

```text id="8fr5zv"
Act as a Senior System Analyst.

Dự án Guai-api đã được index đầy đủ. 
Hãy phân tích lớp CartService, đặc biệt là phương thức addToCart(Long userId, Long productId, int quantity).

Thực hiện đồng thời hai nhiệm vụ sau:

1. Phân tích lỗ hổng logic của mã nguồn hiện tại:
   - Kiểm tra trường hợp quantity <= 0.
   - Kiểm tra trường hợp quantity âm.
   - Kiểm tra trường hợp tổng số lượng sản phẩm trong giỏ vượt quá số lượng tồn kho thực tế.
   - Kiểm tra trường hợp sản phẩm hết hàng nhưng vẫn có thể thêm vào giỏ.
   - Phân tích các rủi ro phát sinh đối với dữ liệu và nghiệp vụ nếu không kiểm tra các điều kiện trên.

2. Từ các vấn đề phát hiện được, xây dựng danh sách Business Rules theo chuẩn SRS để đội phát triển triển khai bản vá.

Yêu cầu Business Rules phải bao gồm:

- Rule ID.
- Rule Name.
- Business Description.
- Validation Condition.
- Expected System Response.
- Error Message trả về cho người dùng.

Bắt buộc phải có tối thiểu các quy tắc:

BR-01: Số lượng sản phẩm thêm vào giỏ phải lớn hơn 0.

BR-02: Không cho phép số lượng âm.

BR-03: Tổng số lượng sản phẩm trong giỏ không được vượt quá số lượng tồn kho.

BR-04: Không cho phép thêm sản phẩm đã hết hàng.

BR-05: Nếu sản phẩm không tồn tại thì hệ thống phải trả về lỗi thích hợp.

Ngoài ra, hãy đề xuất thêm các Business Rules khác nếu phát hiện rủi ro nghiệp vụ liên quan.

Định dạng đầu ra:

1. Logic Vulnerabilities
2. Risk Analysis
3. Business Rules (SRS Format)
4. Recommendations for Development Team

Không sinh code Java. Chỉ tập trung vào phân tích nghiệp vụ và đặc tả yêu cầu.
```
