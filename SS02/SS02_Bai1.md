1. Đáp án lựa chọn
    Phương án C là phương án tối ưu nhất.

2. Phân tích lý do Phương án C là tối ưu nhất
    Phương án C thể hiện tư duy phối hợp công cụ (Tool Orchestration) một cách thông minh, đặt mỗi công cụ vào đúng thế mạnh và phạm vi ngữ cảnh (Context Scope) của nó:

        Viết tài liệu API bằng Web Chat (ChatGPT/Claude): * Quản lý ngữ cảnh: Tài liệu API đòi hỏi khả năng tư duy ngôn ngữ tự nhiên xuất sắc, cấu trúc văn bản rõ ràng và khả năng bao quát nghiệp vụ (business logic). Các Web Chat lớn có Context Window rộng và khả năng lập luận (reasoning) mạnh mẽ, rất phù hợp để xử lý các tác vụ mang tính sáng tạo và tài liệu hóa.

            Năng suất: Việc này không can thiệp trực tiếp vào mã nguồn hiện tại, nên việc tách riêng ra Web Chat giúp giữ sạch không gian làm việc của IDE.

        Tái cấu trúc lớp Book trên 4 tệp tin bằng Agentic/Repository-wide Assistant:

            Quản lý ngữ cảnh: Đây là tác vụ cần ngữ cảnh toàn cục (Global Context) của dự án. Thay vì chỉ nhìn thấy một file, trợ lý dạng Agentic (như Cursor, GitHub Copilot Workspace, hay Claude Dev) có khả năng đọc, hiểu mối quan hệ phụ thuộc giữa các file Book.java, BookService.java, BookController.java, và BookRepository.java để thực hiện thay đổi đồng bộ.

            Hạn chế Context Switching: Lập trình viên không cần phải tự mở từng file, tìm vị trí lỗi và sửa thủ công. Trợ lý tự động "quét" và chỉnh sửa đồng loạt, giảm thiểu tối đa sai sót do quên cập nhật ở một trong các tầng (Layer).

        Sửa lỗi cú pháp nhỏ bằng Inline Code Assistant:

            Quản lý ngữ cảnh: Tác vụ này chỉ cần ngữ cảnh cục bộ (Local Context) tại đúng dòng code đang lỗi.

            Hạn chế Context Switching: Việc sử dụng Inline completion (phím tắt hoặc gợi ý tự động ngay tại dòng code) giúp lập trình viên sửa lỗi trong chưa đầy 1 giây mà không cần rời mắt khỏi dòng code hiện tại, giữ nguyên luồng tập trung (Flow state).

3. Nhược điểm và giới hạn của các phương án còn lại
    Phương án A: Sử dụng Web Chat cho cả 3 tác vụ
        Tác hại nghiêm trọng về Context Switching: Việc liên tục chuyển đổi qua lại giữa cửa sổ IDE và Trình duyệt web (Alt-Tab) làm phân tán sự tập trung. Lập trình viên phải mất thời gian định hình lại mình đang làm đến đâu.

        Gánh nặng quản lý ngữ cảnh thủ công (Manual Context Management): Việc sao chép-dán (Copy-Paste) thủ công 4 tệp tin liên quan đến lớp Book vào Web Chat rất tốn thời gian, dễ sót và dễ vượt quá giới hạn ghi nhớ ngắn hạn của người dùng. Ngoài ra, việc copy ngược lại từ Web Chat vào từng file rất dễ gây ra lỗi chèn đè hoặc sai vị trí.

        Lãng phí tài nguyên cho tác vụ nhỏ: Mở trình duyệt, dán code chỉ để xin một dấu chấm phẩy ; cho tác vụ số 3 là cực kỳ cồng kềnh và kém hiệu quả.

    Phương án B: Sử dụng Inline Code Assistant cho cả 3 tác vụ
        Giới hạn về tầm nhìn ngữ cảnh (Context Blindness): Inline Assistant hoạt động theo cơ chế "nhìn đâu đoán đó", phạm vi ngữ cảnh của nó thường bị giới hạn trong tệp tin đang mở (hoặc vài trăm dòng code xung quanh con trỏ). Do đó, nó không thể tự động hiểu và cập nhật đồng bộ cấu trúc lớp Book trên cả 4 file nằm ở các package khác nhau (Controller, Service, Repository, Entity). Lập trình viên sẽ phải mở từng file và kích hoạt gợi ý 4 lần, dễ dẫn đến bất đồng bộ dữ liệu.

        Hạn chế về khả năng tạo tài liệu dài: Inline Assistant được tối ưu cho việc viết code ngắn (code completion). Nếu ép nó viết một tài liệu hướng dẫn API Markdown dài và chi tiết ngay trong file code, giao diện hiển thị của IDE sẽ bị rối, và khả năng lập luận phân đoạn của công cụ này cũng kém hơn so với Web Chat chuyên dụng.