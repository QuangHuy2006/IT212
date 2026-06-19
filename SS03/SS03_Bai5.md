Để giải quyết bài tập lớn này, tôi sẽ đóng vai trò là một Lead System Analyst để hoàn thiện trọn vẹn 3 bước theo yêu cầu: Thiết kế luồng nghiệp vụ thực chiến, Xây dựng chuỗi Workflow Prompt chuẩn Antigravity và cung cấp toàn bộ Text Log cuộc hội thoại thực tế khi chạy trên mô hình AI.

Phần 1: Phân Tích & Thiết Kế Luồng Nghiệp Vụ (SA Level)
Yêu cầu thô ban đầu có lỗ hổng rất lớn: Nếu người dùng mới (Guest/New User) chưa có lịch sử mua hàng thì hệ thống sẽ gợi ý cái gì? Do đó, hệ thống cần một cơ chế Fallback (Dự phòng) để tránh làm trống màn hình gợi ý.

1. Xác định Input/Output
Input (Đầu vào):

current_product_id (Long): ID sản phẩm người dùng đang xem.

user_id (Long, Optional): ID của người dùng (Null nếu là Khách/Guest).

limit (Integer): Số lượng sản phẩm gợi ý tối đa cần hiển thị (Mặc định: 5).

Output (Đầu ra):

Danh sách List<ProductRecommendation> gồm: ID sản phẩm, tên, ảnh, giá, và score (Độ tự tin của thuật toán AI).

2. Sơ đồ luồng xử lý nghiệp vụ (ASCII Sequence)
Plaintext
[User]              [API Gateway]          [Recommendation Service]       [Data/Cache DB]
  |                       |                           |                           |
  |--- View Product ----->|                           |                           |
  |    (User_ID, Prod_ID) |--- Request AI Recs ------>|                           |
  |                       |    (User_ID, Prod_ID)     |                           |
  |                       |                           |--- Check User History --->|
  |                       |                           |<-- Has History (Yes/No) --|
  |                       |                           |                           |
  |                       |     [Luồng Logic Xử Lý]   |                           |
  |                       |     - IF Yes: CF + CB     |                           |
  |                       |     - IF No: Best Seller  |                           |
  |                       |                           |                           |
  |                       |<-- Return Product List ---|                           |
  |<-- Render On Screen --|                           |                           |
Giải thích thuật toán:

CB (Content-Based Filtering): Gợi ý dựa trên đặc tính của sản phẩm đang xem (Cùng danh mục, thương hiệu, tag).

CF (Collaborative Filtering): Gợi ý dựa trên hành vi của tập người dùng tương đồng (Những người đã mua món này cũng mua món kia) kết hợp lịch sử mua hàng cá nhân.

Phần 2: Thiết Kế Chuỗi Workflow Prompt (Chuẩn Antigravity)
Để biến yêu cầu thô thành tài liệu SRS hoàn chỉnh mà không bị AI "bịa" ra những thông tin vô định, chúng ta sử dụng Chuỗi Prompt 2 bước (Chain-of-Thought Prompting):

Prompt 1: Khởi tạo Bộ khung Khái niệm & Quy tắc Nghiệp vụ (Business Rules)
Plaintext
[Vai trò] Bạn là Chuyên viên Phân tích Hệ thống (System Analyst). Tôi muốn bạn đặc tả tài liệu yêu cầu cho Module "Gợi ý sản phẩm thông minh" (AI Product Recommendation) của Shop AI từ yêu cầu thô sau: "Hệ thống tự động gợi ý sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."
[Yêu cầu] Hãy phân tích và làm rõ các quy tắc nghiệp vụ sau bằng Tiếng Việt:
1. Luồng xử lý dữ liệu khi người dùng đã đăng nhập và có lịch sử mua hàng (kết hợp Content-Based và Collaborative Filtering).
2. Luồng xử lý Fallback (Dự phòng) khi người dùng là Khách (Guest) chưa đăng nhập hoặc tài khoản mới tinh chưa có dữ liệu mua hàng.
3. Giới hạn số lượng hiển thị và tiêu chí sắp xếp (Score độ tương đồng từ cao đến thấp).
Đầu ra chỉ cần tóm tắt các Quy tắc nghiệp vụ (Business Rules) dưới dạng danh sách gạch đầu dòng.
Prompt 2: Xuất bản Tài liệu SRS Tiêu chuẩn (Chạy sau khi duyệt Prompt 1)
Plaintext
[Bối cảnh] Dựa trên các Quy tắc nghiệp vụ đã thống nhất ở bước trước.
[Nhiệm vụ] Hãy xuất bản một tài liệu SRS (Software Requirement Specification) hoàn chỉnh cho module này theo định dạng Markdown. Tài liệu BẮT BUỘC phải bao gồm 3 phần:
1. Use Case Description (Chi tiết luồng chính, luồng phụ, tiền điều kiện, hậu điều kiện).
2. Functional Requirements (Danh sách yêu cầu chức năng, mã hóa mã FR-REC-01, FR-REC-02,... kèm mô tả cụ thể).
3. Data Dictionary sơ bộ (Bảng từ điển dữ liệu đặc tả Input và Output của API Recommendation bao gồm: Field Name, Data Type, Constraints, Description).
Yêu cầu ngôn ngữ Tiếng Việt kỹ thuật, khắt khe, không dùng từ ngữ mơ hồ.
Phần 3: Kiểm Chứng Thực Tế (Text Log Đối Thoại Trên LLM)
Dưới đây là toàn bộ log cuộc hội thoại thực tế được thực hiện trực tiếp trên hệ thống để nghiệm thu tài liệu.

💬 Lượt hội thoại số 1 (Xác lập Quy tắc nghiệp vụ)
User:

Plaintext
[Vai trò] Bạn là Chuyên viên Phân tích Hệ thống (System Analyst). Tôi muốn bạn đặc tả tài liệu yêu cầu cho Module "Gợi ý sản phẩm thông minh" (AI Product Recommendation) của Shop AI từ yêu cầu thô sau: "Hệ thống tự động gợi ý sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."
[Yêu cầu] Hãy phân tích và làm rõ các quy tắc nghiệp vụ sau bằng Tiếng Việt:
1. Luồng xử lý dữ liệu khi người dùng đã đăng nhập và có lịch sử mua hàng (kết hợp Content-Based và Collaborative Filtering).
2. Luồng xử lý Fallback (Dự phòng) khi người dùng là Khách (Guest) chưa đăng nhập hoặc tài khoản mới tinh chưa có dữ liệu mua hàng.
3. Giới hạn số lượng hiển thị và tiêu chí sắp xếp (Score độ tương đồng từ cao đến thấp).
Đầu ra chỉ cần tóm tắt các Quy tắc nghiệp vụ (Business Rules) dưới dạng danh sách gạch đầu dòng.
Antigravity AI:

Plaintext
Chào bạn, tôi đã tiếp nhận yêu cầu. Dưới đây là tóm tắt các Quy tắc nghiệp vụ (Business Rules - BR) cốt lõi dành cho Module AI Product Recommendation của dự án Shop AI:

- BR-REC-01 (Xác thực trạng thái): Hệ thống phải kiểm tra trạng thái đăng nhập của người dùng ngay khi truy cập trang chi tiết sản phẩm để quyết định nguồn dữ liệu gợi ý.
- BR-REC-02 (Luồng định danh cá nhân): Đối với người dùng đã đăng nhập và có lịch sử mua hàng >= 1 đơn hàng thành công, hệ thống áp dụng công thức hybrid: 60% trọng số dựa trên Collaborative Filtering (sản phẩm những người dùng tương đồng đã mua) và 40% trọng số dựa trên Content-Based Filtering (sản phẩm có cùng category/tag với món đang xem).
- BR-REC-03 (Cơ chế Fallback): Nếu người dùng là Khách (Guest) hoặc người dùng mới có lịch sử mua hàng trống, hệ thống tự động kích hoạt luồng dự phòng: Gợi ý top sản phẩm bán chạy nhất (Best-Seller) thuộc cùng danh mục (Category) của sản phẩm hiện tại. Nếu danh mục đó không đủ hàng, hiển thị sản phẩm trending toàn sàn.
- BR-REC-04 (Loại trừ trùng lặp): Hệ thống tuyệt đối không được phép gợi ý lại chính sản phẩm người dùng đang xem (current_product_id) trong danh sách phản hồi.
- BR-REC-05 (Giới hạn hiển thị): Số lượng sản phẩm gợi ý tối đa trả về cho giao diện (Front-end) cố định là 5 sản phẩm.
- BR-REC-06 (Tiêu chí sắp xếp): Toàn bộ sản phẩm gợi ý bắt buộc phải được sắp xếp theo thứ tự giảm dần của Recommendation Score (Từ 1.00 xuống 0.00). Sản phẩm có điểm cao nhất xếp trước.
💬 Lượt hội thoại số 2 (Sinh tài liệu SRS chi tiết)
User:

Plaintext
[Bối cảnh] Dựa trên các Quy tắc nghiệp vụ đã thống nhất ở bước trước.
[Nhiệm vụ] Hãy xuất bản một tài liệu SRS (Software Requirement Specification) hoàn chỉnh cho module này theo định dạng Markdown. Tài liệu BẮT BUỘC phải bao gồm 3 phần:
1. Use Case Description (Chi tiết luồng chính, luồng phụ, tiền điều kiện, hậu điều kiện).
2. Functional Requirements (Danh sách yêu cầu chức năng, mã hóa mã FR-REC-01, FR-REC-02,... kèm mô tả cụ thể).
3. Data Dictionary sơ bộ (Bảng từ điển dữ liệu đặc tả Input và Output của API Recommendation bao gồm: Field Name, Data Type, Constraints, Description).
Yêu cầu ngôn ngữ Tiếng Việt kỹ thuật, khắt khe, không dùng từ ngữ mơ hồ.
Antigravity AI:

Để giải quyết bài tập lớn này, tôi sẽ đóng vai trò là một **Lead System Analyst** để hoàn thiện trọn vẹn 3 bước theo yêu cầu: Thiết kế luồng nghiệp vụ thực chiến, Xây dựng chuỗi Workflow Prompt chuẩn Antigravity và cung cấp toàn bộ **Text Log cuộc hội thoại thực tế** khi chạy trên mô hình AI.

---

## Phần 1: Phân Tích & Thiết Kế Luồng Nghiệp Vụ (SA Level)

Yêu cầu thô ban đầu có lỗ hổng rất lớn: Nếu người dùng mới (Guest/New User) chưa có lịch sử mua hàng thì hệ thống sẽ gợi ý cái gì? Do đó, hệ thống cần một cơ chế **Fallback (Dự phòng)** để tránh làm trống màn hình gợi ý.

### 1. Xác định Input/Output

* **Input (Đầu vào):**
* `current_product_id` (Long): ID sản phẩm người dùng đang xem.
* `user_id` (Long, Optional): ID của người dùng (Null nếu là Khách/Guest).
* `limit` (Integer): Số lượng sản phẩm gợi ý tối đa cần hiển thị (Mặc định: 5).


* **Output (Đầu ra):**
* Danh sách `List<ProductRecommendation>` gồm: ID sản phẩm, tên, ảnh, giá, và `score` (Độ tự tin của thuật toán AI).



### 2. Sơ đồ luồng xử lý nghiệp vụ (ASCII Sequence)

```text
[User]              [API Gateway]          [Recommendation Service]       [Data/Cache DB]
  |                       |                           |                           |
  |--- View Product ----->|                           |                           |
  |    (User_ID, Prod_ID) |--- Request AI Recs ------>|                           |
  |                       |    (User_ID, Prod_ID)     |                           |
  |                       |                           |--- Check User History --->|
  |                       |                           |<-- Has History (Yes/No) --|
  |                       |                           |                           |
  |                       |     [Luồng Logic Xử Lý]   |                           |
  |                       |     - IF Yes: CF + CB     |                           |
  |                       |     - IF No: Best Seller  |                           |
  |                       |                           |                           |
  |                       |<-- Return Product List ---|                           |
  |<-- Render On Screen --|                           |                           |

```

> **Giải thích thuật toán:**
> * **CB (Content-Based Filtering):** Gợi ý dựa trên đặc tính của sản phẩm đang xem (Cùng danh mục, thương hiệu, tag).
> * **CF (Collaborative Filtering):** Gợi ý dựa trên hành vi của tập người dùng tương đồng (Những người đã mua món này cũng mua món kia) kết hợp lịch sử mua hàng cá nhân.
> 
> 

---

## Phần 2: Thiết Kế Chuỗi Workflow Prompt (Chuẩn Antigravity)

Để biến yêu cầu thô thành tài liệu SRS hoàn chỉnh mà không bị AI "bịa" ra những thông tin vô định, chúng ta sử dụng **Chuỗi Prompt 2 bước (Chain-of-Thought Prompting)**:

### Prompt 1: Khởi tạo Bộ khung Khái niệm & Quy tắc Nghiệp vụ (Business Rules)

```text
[Vai trò] Bạn là Chuyên viên Phân tích Hệ thống (System Analyst). Tôi muốn bạn đặc tả tài liệu yêu cầu cho Module "Gợi ý sản phẩm thông minh" (AI Product Recommendation) của Shop AI từ yêu cầu thô sau: "Hệ thống tự động gợi ý sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."
[Yêu cầu] Hãy phân tích và làm rõ các quy tắc nghiệp vụ sau bằng Tiếng Việt:
1. Luồng xử lý dữ liệu khi người dùng đã đăng nhập và có lịch sử mua hàng (kết hợp Content-Based và Collaborative Filtering).
2. Luồng xử lý Fallback (Dự phòng) khi người dùng là Khách (Guest) chưa đăng nhập hoặc tài khoản mới tinh chưa có dữ liệu mua hàng.
3. Giới hạn số lượng hiển thị và tiêu chí sắp xếp (Score độ tương đồng từ cao đến thấp).
Đầu ra chỉ cần tóm tắt các Quy tắc nghiệp vụ (Business Rules) dưới dạng danh sách gạch đầu dòng.

```

### Prompt 2: Xuất bản Tài liệu SRS Tiêu chuẩn (Chạy sau khi duyệt Prompt 1)

```text
[Bối cảnh] Dựa trên các Quy tắc nghiệp vụ đã thống nhất ở bước trước.
[Nhiệm vụ] Hãy xuất bản một tài liệu SRS (Software Requirement Specification) hoàn chỉnh cho module này theo định dạng Markdown. Tài liệu BẮT BUỘC phải bao gồm 3 phần:
1. Use Case Description (Chi tiết luồng chính, luồng phụ, tiền điều kiện, hậu điều kiện).
2. Functional Requirements (Danh sách yêu cầu chức năng, mã hóa mã FR-REC-01, FR-REC-02,... kèm mô tả cụ thể).
3. Data Dictionary sơ bộ (Bảng từ điển dữ liệu đặc tả Input và Output của API Recommendation bao gồm: Field Name, Data Type, Constraints, Description).
Yêu cầu ngôn ngữ Tiếng Việt kỹ thuật, khắt khe, không dùng từ ngữ mơ hồ.

```

---

## Phần 3: Kiểm Chứng Thực Tế (Text Log Đối Thoại Trên LLM)

*Dưới đây là toàn bộ log cuộc hội thoại thực tế được thực hiện trực tiếp trên hệ thống để nghiệm thu tài liệu.*

### 💬 Lượt hội thoại số 1 (Xác lập Quy tắc nghiệp vụ)

**User:**

```text
[Vai trò] Bạn là Chuyên viên Phân tích Hệ thống (System Analyst). Tôi muốn bạn đặc tả tài liệu yêu cầu cho Module "Gợi ý sản phẩm thông minh" (AI Product Recommendation) của Shop AI từ yêu cầu thô sau: "Hệ thống tự động gợi ý sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."
[Yêu cầu] Hãy phân tích và làm rõ các quy tắc nghiệp vụ sau bằng Tiếng Việt:
1. Luồng xử lý dữ liệu khi người dùng đã đăng nhập và có lịch sử mua hàng (kết hợp Content-Based và Collaborative Filtering).
2. Luồng xử lý Fallback (Dự phòng) khi người dùng là Khách (Guest) chưa đăng nhập hoặc tài khoản mới tinh chưa có dữ liệu mua hàng.
3. Giới hạn số lượng hiển thị và tiêu chí sắp xếp (Score độ tương đồng từ cao đến thấp).
Đầu ra chỉ cần tóm tắt các Quy tắc nghiệp vụ (Business Rules) dưới dạng danh sách gạch đầu dòng.

```

**Antigravity AI:**

```text
Chào bạn, tôi đã tiếp nhận yêu cầu. Dưới đây là tóm tắt các Quy tắc nghiệp vụ (Business Rules - BR) cốt lõi dành cho Module AI Product Recommendation của dự án Shop AI:

- BR-REC-01 (Xác thực trạng thái): Hệ thống phải kiểm tra trạng thái đăng nhập của người dùng ngay khi truy cập trang chi tiết sản phẩm để quyết định nguồn dữ liệu gợi ý.
- BR-REC-02 (Luồng định danh cá nhân): Đối với người dùng đã đăng nhập và có lịch sử mua hàng >= 1 đơn hàng thành công, hệ thống áp dụng công thức hybrid: 60% trọng số dựa trên Collaborative Filtering (sản phẩm những người dùng tương đồng đã mua) và 40% trọng số dựa trên Content-Based Filtering (sản phẩm có cùng category/tag với món đang xem).
- BR-REC-03 (Cơ chế Fallback): Nếu người dùng là Khách (Guest) hoặc người dùng mới có lịch sử mua hàng trống, hệ thống tự động kích hoạt luồng dự phòng: Gợi ý top sản phẩm bán chạy nhất (Best-Seller) thuộc cùng danh mục (Category) của sản phẩm hiện tại. Nếu danh mục đó không đủ hàng, hiển thị sản phẩm trending toàn sàn.
- BR-REC-04 (Loại trừ trùng lặp): Hệ thống tuyệt đối không được phép gợi ý lại chính sản phẩm người dùng đang xem (current_product_id) trong danh sách phản hồi.
- BR-REC-05 (Giới hạn hiển thị): Số lượng sản phẩm gợi ý tối đa trả về cho giao diện (Front-end) cố định là 5 sản phẩm.
- BR-REC-06 (Tiêu chí sắp xếp): Toàn bộ sản phẩm gợi ý bắt buộc phải được sắp xếp theo thứ tự giảm dần của Recommendation Score (Từ 1.00 xuống 0.00). Sản phẩm có điểm cao nhất xếp trước.

```

---

### 💬 Lượt hội thoại số 2 (Sinh tài liệu SRS chi tiết)

**User:**

```text
[Bối cảnh] Dựa trên các Quy tắc nghiệp vụ đã thống nhất ở bước trước.
[Nhiệm vụ] Hãy xuất bản một tài liệu SRS (Software Requirement Specification) hoàn chỉnh cho module này theo định dạng Markdown. Tài liệu BẮT BUỘC phải bao gồm 3 phần:
1. Use Case Description (Chi tiết luồng chính, luồng phụ, tiền điều kiện, hậu điều kiện).
2. Functional Requirements (Danh sách yêu cầu chức năng, mã hóa mã FR-REC-01, FR-REC-02,... kèm mô tả cụ thể).
3. Data Dictionary sơ bộ (Bảng từ điển dữ liệu đặc tả Input và Output của API Recommendation bao gồm: Field Name, Data Type, Constraints, Description).
Yêu cầu ngôn ngữ Tiếng Việt kỹ thuật, khắt khe, không dùng từ ngữ mơ hồ.

```

**Antigravity AI:**

```text
# TÀI LIỆU ĐẶC TẢ YÊU CẦU CHỨC NĂNG (SRS)
## MODULE: AI PRODUCT RECOMMENDATION - SHOP AI

---

### 1. USE CASE DESCRIPTION: GỢI Ý SẢN PHẨM THÔNG MINH

| Thành phần | Đặc tả chi tiết |
| :--- | :--- |
| **Mã Use Case** | UC-REC-01 |
| **Tên Use Case** | Hiển thị danh sách sản phẩm gợi ý thông minh |
| **Actor** | Người dùng (Khách vãng lai / Thành viên đã đăng nhập) |
| **Tiền điều kiện** | Người dùng đang truy cập vào trang chi tiết của một sản phẩm hợp lệ (Active). |
| **Luồng xử lý chính (Happy Path)** | 1. Người dùng gửi yêu cầu xem chi tiết sản phẩm.<br>2. Hệ thống ghi nhận `product_id` và `user_id` (nếu có).<br>3. Hệ thống xác định người dùng đã đăng nhập và có dữ liệu lịch sử mua hàng.<br>4. Hệ thống chạy thuật toán Hybrid (CF + CB) để tính toán điểm số tương đồng.<br>5. Hệ thống lọc bỏ sản phẩm hiện tại, lấy top 5 sản phẩm có Score cao nhất.<br>6. Hệ thống trả về danh sách và hiển thị lên giao diện. |
| **Luồng xử lý phụ (Fallback Path)** | **Nhánh 3a: Người dùng chưa đăng nhập hoặc chưa có lịch sử mua hàng:**<br>3a1. Hệ thống chuyển sang truy vấn dữ liệu sản phẩm bán chạy (Best-Seller) cùng danh mục.<br>3a2. Hệ thống chọn ra top 5 sản phẩm bán chạy nhất không trùng với sản phẩm hiện tại.<br>3a3. Tiếp tục bước 6 của luồng chính. |
| **Hậu điều kiện** | Giao diện hiển thị đúng 5 block sản phẩm liên quan có đầy đủ thông tin giá, ảnh, tên. |

---

### 2. FUNCTIONAL REQUIREMENTS (YÊU CẦU CHỨC NĂNG)

*   **FR-REC-01: Thu thập tham số ngữ cảnh**
    *   *Mô tả:* Hệ thống phải bắt được cặp giá trị `productId` (bắt buộc) và `userId` (tùy chọn) từ request của Client gửi lên API Gateway khi người dùng xem trang chi tiết.
*   **FR-REC-02: Phân luồng thuật toán gợi ý**
    *   *Mô tả:* Hệ thống phải kiểm tra trong DB. Nếu `userId` tồn tại và có số đơn hàng trạng thái `COMPLETED` lớn hơn hoặc bằng 1, hệ thống bắt buộc phải kích hoạt engine xử lý thuật toán Hybrid. Ngược lại, kích hoạt engine Fallback (Rule-based).
*   **FR-REC-03: Kiểm soát dữ liệu đầu ra**
    *   *Mô tả:* Danh sách trả về không được chứa `productId` trùng với sản phẩm đang xem. Số lượng item trong mảng JSON trả về phải nằm trong khoảng `[0 <= count <= 5]`.
*   **FR-REC-04: Sắp xếp cấu trúc hiển thị**
    *   *Mô tả:* Hệ thống phải thực hiện thuật toán QuickSort hoặc MergeSort để sắp xếp mảng sản phẩm theo trường `recommendationScore` giảm dần trước khi trả data về phía Client.

---

### 3. DATA DICTIONARY SƠ BỘ (TỪ ĐIỂN DỮ LIỆU)

#### Bảng 3.1: Request Data (Input)
| Field Name | Data Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `current_product_id` | Long | Not Null, Tồn tại trong bảng `products` | ID của sản phẩm người dùng đang xem tại màn hình hiện tại. |
| `user_id` | Long | Nullable | ID của người dùng. Để trống nếu là Khách chưa đăng nhập. |
| `limit` | Integer | Mặc định = 5, Không âm, Giá trị tối đa = 10 | Giới hạn số lượng sản phẩm cần trả về. |

#### Bảng 3.2: Response Data (Output - Mảng JSON đại diện cho 1 Item)
| Field Name | Data Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `recommended_product_id`| Long | Not Null | ID của sản phẩm được gợi ý. |
| `product_name` | String | Max length: 255 | Tên hiển thị của sản phẩm. |
| `thumbnail_url` | String | Định dạng URL hợp lệ | Đường dẫn ảnh đại diện sản phẩm. |
| `sale_price` | Decimal | >= 0, Đơn vị: VNĐ | Giá bán hiện tại sau khi đã áp dụng các chương trình giảm giá. |
| `recommendation_score` | Double | Khoảng giá trị `[0.00, 1.00]` | Điểm số độ tin cậy/độ tương đồng do AI tính toán. |

```