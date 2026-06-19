1. Vì sao prompt thô "Mã này bị lỗi gì?" dễ khiến AI bỏ sót lỗi logic?

Prompt quá chung chung nên AI thường tập trung vào các lỗi dễ nhận biết trước như:

Lỗi cú pháp (Syntax Error)
Lỗi biên dịch (Compile Error)
NullPointerException
ArrayIndexOutOfBoundsException
Cảnh báo về kiểu dữ liệu

Trong đoạn mã trên:

Code biên dịch thành công.
Không có lỗi cú pháp.
Không có cảnh báo từ compiler.
Chương trình chạy bình thường và luôn trả về một giá trị hợp lệ.

Do đó, nếu chỉ hỏi:

"Mã này bị lỗi gì?"

AI có thể chỉ phân tích ở mức tĩnh (static analysis) và kết luận rằng:

"Không có lỗi cú pháp hay runtime rõ ràng."

Trong khi lỗi thực sự là lỗi logic (Logic Bug), chỉ xuất hiện khi kiểm thử hành vi của chương trình.

Nguyên nhân gốc rễ

Vòng lặp trong:

for (int j = i; j < arr.length; j++)

bắt đầu từ:

j = i

nên ngay lần lặp đầu tiên:

arr[i] == arr[j]

tương đương:

arr[i] == arr[i]

điều này luôn đúng.

Ví dụ:

int[] arr = {1, 2, 3, 4};

Diễn biến:

i = 0
j = 0

arr[0] == arr[0]
1 == 1  // luôn đúng

Hàm lập tức:

return 1;

Mặc dù mảng hoàn toàn không có phần tử trùng lặp.

Vì sao AI dễ bỏ sót?

Prompt không yêu cầu:

Phân tích luồng thực thi (Execution Flow)
Kiểm thử với dữ liệu cụ thể
Tìm lỗi logic
Xét các trường hợp biên (Edge Cases)
Đánh giá tính đúng đắn của thuật toán

Nói cách khác, AI chỉ được yêu cầu xem code, chứ không được yêu cầu chứng minh thuật toán hoạt động đúng hay sai.

Đây là lý do các prompt mơ hồ thường dẫn đến việc bỏ sót những lỗi logic tinh vi dù chương trình vẫn chạy bình thường.

2. Prompt tối ưu mới
ROLE (Vai trò)

Bạn là một Senior Code Auditor và Java Algorithm Reviewer với hơn 10 năm kinh nghiệm phân tích lỗi logic, đánh giá thuật toán và tối ưu hiệu năng.

CONTEXT (Ngữ cảnh)

Tôi nghi ngờ đoạn mã Java dưới đây có lỗi logic mặc dù chương trình biên dịch và chạy bình thường.

Mã nguồn:

public class DuplicateFinder {

    // Lỗi logic: j bắt đầu từ i dẫn đến việc phần tử tự so sánh với chính nó

    public static Integer findDuplicate(int[] arr) {

        for (int i = 0; i < arr.length; i++) {

            for (int j = i; j < arr.length; j++) {

                if (arr[i] == arr[j]) {

                    return arr[i];

                }

            }

        }

        return null;

    }
}
TEST CASE BẮT BUỘC PHÂN TÍCH

Hãy mô phỏng từng bước thực thi của chương trình với dữ liệu:

int[] arr = {1, 2, 3, 4};

Mảng này KHÔNG chứa phần tử trùng lặp.

Tuy nhiên, chương trình hiện tại vẫn trả về:

1

Hãy giải thích chính xác tại sao điều đó xảy ra.

NHIỆM VỤ
Xác định lỗi logic gốc rễ (Root Cause).
Mô phỏng luồng thực thi từng bước với test case đã cho.
Giải thích tại sao compiler không thể phát hiện lỗi này.
Đưa ra các test case khác có thể làm lộ lỗi.
Đánh giá độ phức tạp thời gian và bộ nhớ của thuật toán hiện tại.
Refactor thuật toán bằng HashSet để:
Tìm phần tử xuất hiện lặp đầu tiên.
Loại bỏ vòng lặp lồng nhau.
Giảm độ phức tạp thời gian từ O(N²) xuống O(N).
Viết lại mã nguồn hoàn chỉnh theo chuẩn Clean Code.
So sánh giải pháp cũ và mới bằng bảng gồm:
Tính đúng đắn
Time Complexity
Space Complexity
Khả năng mở rộng
Khả năng bảo trì
YÊU CẦU ĐẦU RA

Trả lời theo cấu trúc:

Root Cause Analysis
Execution Trace
Edge Cases
Complexity Analysis
Refactored Solution (HashSet)
Comparison Table
Final Recommendation

Không chỉ nêu lỗi, hãy chứng minh lỗi bằng quá trình chạy thực tế của chương trình.
1. Root Cause Analysis
Lỗi gốc rễ (Root Cause)

Lỗi nằm ở vòng lặp bên trong:

for (int j = i; j < arr.length; j++)

Biến j được khởi tạo bằng chính i.

Điều này dẫn đến lần lặp đầu tiên của vòng lặp trong luôn xảy ra:

arr[i] == arr[i]

Mà một phần tử luôn bằng chính nó.

Do đó điều kiện:

if (arr[i] == arr[j])

sẽ luôn đúng ngay lần kiểm tra đầu tiên.

Kết quả:

return arr[i];

được thực thi ngay lập tức.

Thuật toán không hề kiểm tra sự trùng lặp giữa hai phần tử khác nhau trong mảng mà chỉ kiểm tra phần tử với chính nó.

Vấn đề thiết kế thuật toán

Mục tiêu:

Tìm hai vị trí khác nhau chứa cùng giá trị.

Điều kiện đúng phải là:

i != j && arr[i] == arr[j]

hoặc đơn giản hơn:

for (int j = i + 1; j < arr.length; j++)

để tránh việc tự so sánh.

2. Execution Trace
Input
int[] arr = {1, 2, 3, 4};

Không có phần tử nào trùng lặp.

Bước 1
i = 0

Vòng lặp trong:

j = i = 0

So sánh:

arr[0] == arr[0]

Tương đương:

1 == 1

Kết quả:

true

Đi vào khối lệnh:

return arr[0];

Trả về:

1
Luồng thực thi thực tế
Bước	i	j	arr[i]	arr[j]	Kết quả
1	0	0	1	1	true
2	-	-	-	-	return 1

Chương trình kết thúc ngay tại đây.

Không hề kiểm tra:

(1,2)
(1,3)
(1,4)
(2,3)
(2,4)
(3,4)

nên hoàn toàn không phát hiện được việc mảng không có phần tử trùng lặp.

3. Tại sao Compiler không phát hiện được?

Compiler Java chỉ kiểm tra:

Cú pháp

Ví dụ:

for (...)

có hợp lệ không.

Kiểu dữ liệu

Ví dụ:

int == int

là hợp lệ.

Luồng điều khiển cơ bản

Ví dụ:

return Integer

đúng với kiểu trả về.

Compiler KHÔNG hiểu ý định nghiệp vụ của lập trình viên.

Compiler không biết rằng:

Bạn muốn tìm phần tử trùng lặp giữa các vị trí khác nhau.

Đối với compiler:

arr[i] == arr[i]

là hoàn toàn hợp lệ.

Đây là:

Logical Error (Semantic Bug)

chứ không phải:

Syntax Error
Compile-time Error
Runtime Error
4. Edge Cases
Test Case 1
int[] arr = {5};

Kỳ vọng:

null

Thực tế:

5
Test Case 2
int[] arr = {10, 20};

Kỳ vọng:

null

Thực tế:

10
Test Case 3
int[] arr = {7, 8, 9};

Kỳ vọng:

null

Thực tế:

7
Test Case 4
int[] arr = {1, 2, 3, 1};

Kỳ vọng:

1

Thực tế:

1

Trường hợp này vô tình cho kết quả đúng.

Tuy nhiên:

Không phải vì thuật toán tìm được phần tử trùng.
Mà vì nó trả về phần tử đầu tiên ngay từ đầu.

Đây là lỗi rất nguy hiểm vì dễ tạo cảm giác chương trình hoạt động đúng.

Test Case 5
int[] arr = {5, 6, 7, 8, 7};

Kỳ vọng:

7

Thực tế:

5

Lộ lỗi rõ ràng.

5. Complexity Analysis
Thuật toán hiện tại
Time Complexity (lý thuyết)

Có hai vòng lặp lồng nhau:

for (...)
    for (...)

=>

O(N
2
)
Nhưng với bug hiện tại

Do luôn:

return arr[0];

ở lần kiểm tra đầu tiên nên thực tế chạy:

O(1)

Tuy nhiên đây không phải là tối ưu mà là hậu quả của lỗi logic.

Space Complexity

Không sử dụng cấu trúc dữ liệu phụ.

O(1)
6. Refactored Solution (HashSet)
Ý tưởng

Duyệt mảng từ trái sang phải.

Nếu phần tử đã tồn tại trong HashSet
→ đây là lần xuất hiện lặp đầu tiên.
Nếu chưa tồn tại
→ thêm vào Set.
Ví dụ
{1,2,3,2,4}

Duyệt:

1 -> add
2 -> add
3 -> add
2 -> đã tồn tại -> return 2
Clean Code Version
import java.util.HashSet;
import java.util.Set;

public final class DuplicateFinder {

    private DuplicateFinder() {
        // Prevent instantiation
    }

    public static Integer findFirstDuplicate(int[] numbers) {

        if (numbers == null || numbers.length == 0) {
            return null;
        }

        Set<Integer> visitedNumbers = new HashSet<>();

        for (int number : numbers) {

            if (visitedNumbers.contains(number)) {
                return number;
            }

            visitedNumbers.add(number);
        }

        return null;
    }

    public static void main(String[] args) {

        int[] numbers = {1, 2, 3, 2, 4};

        Integer duplicate = findFirstDuplicate(numbers);

        System.out.println(duplicate);
    }
}
Luồng chạy

Input:

{1,2,3,2,4}
Bước	Number	Set	Kết quả
1	1	{1}	add
2	2	{1,2}	add
3	3	{1,2,3}	add
4	2	{1,2,3}	duplicate
5	return 2		
6. Comparison Table
Tiêu chí	Thuật toán cũ	Thuật toán HashSet
Tính đúng đắn	❌ Sai	✅ Đúng
Tìm phần tử lặp đầu tiên	❌ Không	✅ Có
Xử lý mảng không trùng lặp	❌ Trả sai	✅ Trả null
Time Complexity	O(N²) (thiết kế)	O(N)
Space Complexity	O(1)	O(N)
Khả năng mở rộng	Kém	Tốt
Hiệu năng với dữ liệu lớn	Kém	Tốt
Dễ bảo trì	Trung bình	Cao
Nguy cơ bug logic	Cao	Thấp
Readability	Trung bình	Cao
7. Final Recommendation
Kết luận

Lỗi xảy ra vì:

j = i

khiến chương trình luôn thực hiện:

arr[i] == arr[i]

ở lần kiểm tra đầu tiên.

Với test case:

{1, 2, 3, 4}

chương trình thực tế chỉ thực hiện:

1 == 1

sau đó trả về:

1

mà không hề kiểm tra bất kỳ cặp phần tử khác nhau nào.

Nếu chỉ muốn sửa tối thiểu
for (int j = i + 1; j < arr.length; j++)

sẽ loại bỏ lỗi tự so sánh.

Nếu muốn tối ưu và dùng trong hệ thống thực tế

Nên thay toàn bộ giải pháp bằng HashSet vì:

Đúng về mặt logic.
Giảm độ phức tạp từ O(N²) xuống O(N).
Dễ đọc và dễ bảo trì hơn.
Hoạt động tốt với mảng có hàng triệu phần tử.