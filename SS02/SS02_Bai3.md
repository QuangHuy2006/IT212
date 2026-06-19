1. Phần giải thích nguyên lý sinh lỗi ảo giác của AI.
- Hiện tượng AI tự bịa ra phương thức StringUtils.hasSpecialCharacters() khi bị "mớm mồi" trong prompt xuất phát từ các nguyên lý vận hành sau của Mô hình ngôn ngữ lớn (LLM):
    Cơ chế dự đoán từ tiếp theo dựa trên xác suất (Next-token Prediction): AI không chạy thử code hay tra cứu tài liệu thời gian thực (trừ khi có công cụ search tích hợp). Nó hoạt động bằng cách tính toán xem từ nào có khả năng xuất hiện tiếp theo cao nhất dựa trên dữ liệu đã học.

    Sự trùng khớp về ngữ nghĩa ngữ cảnh (Semantic Similarity): Trong kho dữ liệu của AI, StringUtils của Apache Commons Lang đi kèm với rất nhiều hàm kiểm tra chuỗi bắt đầu bằng "is" hoặc "has" (như hasLength(), isAlpha(), isAlphanumeric()). Khi prompt khẳng định có hàm hasSpecialCharacters(), AI sẽ nhận diện cấu trúc tên này hoàn toàn logic và "hợp tai" về mặt ngữ nghĩa, từ đó tự tin viết tiếp đoạn code giả lập dựa trên thói quen đặt tên của thư viện.

    Hiệu ứng "Lấy lòng" người dùng (Sycophancy): AI có xu hướng tuân thủ và khẳng định lại các giả định do người dùng đưa ra thay vì phủ quyết chúng. Khi bạn khẳng định "tôi muốn dùng phương thức X", AI sẽ ưu tiên tối ưu hóa việc đáp ứng mệnh lệnh đó hơn là phản biện xem X có tồn tại hay không.

2. Prompt tối ưu mới do bạn thiết kế.
- Prompt tối ưu mới:
"Tôi cần viết một hàm bằng Java để kiểm tra xem một chuỗi văn bản đầu vào có chứa bất kỳ ký tự đặc biệt nào hay không (ký tự đặc biệt ở đây được định nghĩa là các ký tự không phải chữ cái và không phải chữ số).

Yêu cầu nghiêm ngặt chống ảo giác (Anti-Hallucination):

    Chỉ được phép sử dụng các API chính thức, phổ biến và đã được kiểm chứng của thư viện chuẩn Java (Java Standard Library) hoặc thư viện Apache Commons Lang/Guava (nếu dùng thư viện ngoài, phải chỉ rõ phiên bản).

    Trước khi viết code, bạn phải giải thích rõ cơ sở và trích dẫn hoặc khẳng định phương thức/lớp bạn dùng nằm trong gói (package) nào của tài liệu chính thống (Javadoc) để chứng minh nó thực sự tồn tại. Tuyệt đối không tự bịa ra các phương thức không có sẵn trong thư viện.

    Hãy tối ưu hàm này về mặt hiệu năng để có thể xử lý các chuỗi văn bản dài.

Hãy cung cấp giải pháp kèm mã nguồn Java hoàn chỉnh."

3. Đoạn mã nguồn Java do AI sinh ra sau khi chạy thử prompt mới (nằm trong khối code markdown).
package com.library.util;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * Tiện ích kiểm tra cấu trúc chuỗi văn bản.
 * Sử dụng API chính thức từ Java Standard Library, đảm bảo không lỗi biên dịch.
 */
public class StringUtils {

    // Kỹ thuật tối ưu hiệu năng: Biên dịch trước (Pre-compile) Regex Pattern dưới dạng hằng số
    // Việc này giúp tránh việc biên dịch lại Regex mỗi khi gọi hàm, tối ưu cho chuỗi lớn hoặc vòng lặp dài.
    private static final Pattern SPECIAL_CHARACTERS_PATTERN = Pattern.compile("[^a-zA-Z0-9 ]");

    /**
     * Kiểm tra xem chuỗi đầu vào có chứa ký tự đặc biệt hay không.
     * Ký tự đặc biệt được định nghĩa là ký tự ngoài: chữ cái, chữ số và khoảng trắng.
     *
     * @param input Chuỗi văn bản cần kiểm tra
     * @return true nếu có chứa ký tự đặc biệt, false nếu ngược lại hoặc chuỗi rỗng.
     */
    public static boolean hasSpecialCharacters(String input) {
        if (input == null || input.trim().isEmpty()) {
            return false;
        }

        // Tạo đối tượng Matcher để so khớp dữ liệu với Pattern đã biên dịch trước
        Matcher matcher = SPECIAL_CHARACTERS_PATTERN.matcher(input);
        
        // Hàm find() của Matcher sẽ duyệt chuỗi và trả về true ngay khi tìm thấy ký tự đặc biệt đầu tiên (Short-circuiting)
        return matcher.find();
    }

    public static void main(String[] args) {
        // Thử nghiệm các ca kiểm thử (Test Cases)
        String validStr = "DaoTruongSon 2026";
        String invalidStr = "DaoTruongSon@2026!";

        System.out.println("Chuỗi '" + validStr + "' có ký tự đặc biệt không? -> " + hasSpecialCharacters(validStr)); // Kết quả: false
        System.out.println("Chuỗi '" + invalidStr + "' có ký tự đặc biệt không? -> " + hasSpecialCharacters(invalidStr)); // Kết quả: true
    }
}