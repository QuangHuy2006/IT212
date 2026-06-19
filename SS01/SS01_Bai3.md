Prompt mới do bạn thiết kế : 
public class TaxCalculator {
    public static double calculateVAT(double amount) {
        return amount * 0.1;
    }
}

- hàm này bị lỗi logic nghiêm trọng khi giá trị hóa đơn đầu vào là số âm (amount <= 0), hãy chỉnh sửa lại

Đoạn code Java đã được AI sửa đổi (nằm trong khối code markdown) : 
    public class TaxCalculator {
        public static double calculateVAT(double amount) {
            if (amount <= 0) {
                throw new IllegalArgumentException("Giá trị hóa đơn phải lớn hơn 0. Đầu vào hiện tại: " + amount);
            }
            return amount * 0.1;
        }
    }