# Bài 1: Phân tích & Lựa chọn công cụ xây dựng tài liệu SRS cho Checkout Flow

## Đáp án lựa chọn

**Phương án B**

## Giải thích lý do lựa chọn

Phương án B là lựa chọn tối ưu nhất vì tận dụng được sức mạnh của công cụ Agentic AI được học
Việc khởi tạo workspace và index toàn bộ dự án Guai-api giúp AI hiểu được ngữ cảnh (Context) của toàn bộ hệ thống 
thay vì chỉ nhìn thấy từng đoạn mã riêng lẻ.

Khi sử dụng prompt:

> "Act as a System Analyst. Đọc toàn bộ luồng xử lý từ API endpoint /api/v1/checkout, 
đi qua các service và filter bảo mật. Hãy tổng hợp logic nghiệp vụ thành một bản đặc tả Use Case (SRS format) 
bao gồm: Pre-conditions, Main flow, và Exceptions."

AI có thể:

* Theo dõi toàn bộ luồng xử lý từ `Controller → JWT Filter → Service → Repository`.
* Hiểu được mối quan hệ giữa các thành phần trong hệ thống.
* Phân tích cả logic nghiệp vụ và cơ chế bảo mật.
* Tự động tổng hợp thành tài liệu SRS có cấu trúc chuẩn.
* Hạn chế tối đa việc người dùng phải sao chép mã nguồn thủ công.

Đây chính là ưu điểm lớn nhất của Agentic AI: **Repository-wide Context** 
AI có khả năng hiểu toàn bộ dự án thay vì chỉ một vài file riêng lẻ.

---

## Nhược điểm của Phương án A

Phương án A sử dụng Github Copilot Chat để giải thích từng đoạn code riêng biệt. 
Cách làm này có một số hạn chế:

* AI chỉ nhìn thấy phần code được bôi đen nên rất dễ mất ngữ cảnh tổng thể.
* Người dùng phải tự tổng hợp thông tin từ nhiều đoạn giải thích khác nhau, dễ bỏ sót các bước quan trọng.
* Không theo dõi được luồng xử lý xuyên suốt từ Controller đến Security Filter và Repository.
* Khi dự án lớn, việc chuyển đổi liên tục giữa các file gây ra hiện tượng **Context Switching**, làm giảm năng suất.

### Rủi ro Context Loss

AI có thể giải thích đúng từng file nhưng lại không hiểu:

* Endpoint nào gọi Service nào.
* JWT Filter kiểm tra gì trước khi vào Controller.
* Repository nào được sử dụng trong Checkout Flow.
* Điều kiện lỗi nào phát sinh trong toàn bộ Use Case.

Do đó tài liệu SRS có thể thiếu hoặc sai luồng nghiệp vụ.

---

## Nhược điểm của Phương án C

Phương án C yêu cầu người dùng copy thủ công nội dung các file và dán vào Web Chat. 
Đây là phương án có nhiều rủi ro:

### 1. Thất thoát ngữ cảnh (Context Loss)

Nếu người dùng:

* Quên copy một file Service.
* Bỏ sót JWT Filter.
* Không copy Interface hoặc DTO liên quan.

AI sẽ không có đầy đủ thông tin để phân tích Checkout Flow chính xác.

### 2. Giới hạn Context Window

Khi số lượng file lớn:

* Nội dung có thể vượt quá cửa sổ ngữ cảnh của mô hình.
* AI sẽ phải rút gọn hoặc bỏ quên một phần thông tin đã nhập trước đó.
* Kết quả SRS có thể thiếu bước hoặc sai logic.

### 3. Tăng Context Switching

Người dùng phải:

* Mở file.
* Copy code.
* Dán vào Web Chat.
* Kiểm tra lại.
* Copy thêm file khác.

Quy trình này mất nhiều thời gian và làm giảm hiệu quả làm việc.

---

## Kết luận

Phương án **B** là lựa chọn tối ưu nhất vì Agentic AI có khả năng phân tích toàn bộ repository, 
duy trì ngữ cảnh xuyên suốt giữa nhiều thành phần của hệ thống và tự động tổng hợp thành tài liệu SRS chuẩn. 
Trong khi đó, Phương án A và C đều có nguy cơ cao xảy ra **Context Loss**, 
khiến AI không hiểu đầy đủ logic Checkout Flow và làm giảm chất lượng tài liệu đặc tả nghiệp vụ.
