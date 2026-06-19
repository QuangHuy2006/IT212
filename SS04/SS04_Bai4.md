Đáp án đúng: B

Phương án B là phản hồi tối ưu nhất theo kỹ thuật Iterative Prompting vì nó:

Chỉ rõ vấn đề hiện tại
Xác định chính xác nguyên nhân: thuật toán đệ quy Fibonacci có độ phức tạp thời gian rất lớn (thực tế là O(2
N
), thường được mô tả là tăng theo cấp số mũ).
Nêu rõ hậu quả: chương trình bị treo khi n = 50.
Đưa ra yêu cầu kỹ thuật cụ thể
Yêu cầu sử dụng Dynamic Programming.
Chỉ rõ hai hướng triển khai hợp lệ:
Memoization (Top-down)
Tabulation (Bottom-up)
Đặt mục tiêu tối ưu rõ ràng
Thời gian: O(N)
Bộ nhớ: O(N) hoặc O(1)
Giữ nguyên ràng buộc thiết kế
Không thay đổi kiểu trả về (long).

Đây chính là bản chất của Iterative Prompting: không chỉ nói "hãy làm tốt hơn", mà phải chỉ ra:

Vấn đề hiện tại là gì.
Tiêu chí đánh giá kết quả mới.
Ràng buộc kỹ thuật cần giữ nguyên.
Vì sao phương án A chưa đạt yêu cầu?

A:

"Code chạy chậm quá, viết lại bằng thuật toán khác tối ưu hơn giúp tôi."

Nhược điểm
Quá mơ hồ.
Không nêu rõ nguyên nhân là do thuật toán đệ quy Fibonacci.
Không chỉ định thuật toán mong muốn.
Không đưa ra mục tiêu độ phức tạp.

AI có thể trả về nhiều phương án khác nhau:

Dùng vòng lặp.
Dùng ma trận lũy thừa.
Dùng công thức Binet.
Dùng cache.
Hoặc thậm chí vẫn dùng đệ quy nhưng chỉnh sửa không đáng kể.

=> Kết quả không kiểm soát được và không đảm bảo đạt mục tiêu học tập về Dynamic Programming.

Vì sao phương án C chưa đạt yêu cầu?

C:

"Hãy viết lại code tính Fibonacci bằng cách sử dụng Java Stream API để code chạy song song (parallel) cho nhanh hơn."

Nhược điểm
Tập trung vào công nghệ thay vì thuật toán
Vấn đề gốc là độ phức tạp thuật toán.
Parallel Stream không giải quyết được việc đệ quy Fibonacci sinh ra số lượng lời gọi hàm khổng lồ.
Không giảm độ phức tạp
Thuật toán vẫn có bản chất cấp số mũ.
Chỉ phân phối công việc lên nhiều luồng.
Có thể còn chậm hơn
Chi phí tạo và quản lý thread.
Overhead của ForkJoinPool.
Đồng bộ hóa tài nguyên.
Sai hướng tối ưu

Quy tắc tối ưu hiệu năng:

Tối ưu thuật toán trước, tối ưu song song hóa sau.

Ví dụ:

Fibonacci đệ quy:
O(2
N
)
Fibonacci Dynamic Programming:
O(N)

Việc giảm từ O(2
N
) xuống O(N) mang lại lợi ích lớn hơn rất nhiều so với việc chạy song song một thuật toán kém hiệu quả.

Kết luận
Phương án	Đánh giá
A	❌ Quá chung chung, không có tiêu chí kỹ thuật rõ ràng
B	✅ Tốt nhất: nêu rõ vấn đề, thuật toán mong muốn, độ phức tạp mục tiêu và ràng buộc
C	❌ Tập trung vào parallelism thay vì tối ưu thuật toán, không giải quyết nguyên nhân gốc

Đáp án cuối cùng: B.