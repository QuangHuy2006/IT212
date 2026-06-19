- Prompt thô hiện tại:
"Hãy viết cho tôi class VNPayPaymentService bằng Java để thực hiện thanh toán trực tuyến. Đây là cấu hình của tôi: public class VNPayConfig { public static final String HASH_SECRET = \"VNPAY_SECRET_KEY_123456_ABCD_XYZ\"; }"
- Nội dung Prompt sau khi tối ưu : 
"Bạn là một chuyên gia lập trình Java Backend và bảo mật hệ thống. Hãy viết giúp tôi lớp VNPayPaymentService bằng Java để thực hiện tích hợp thanh toán trực tuyến qua cổng VNPay.

Yêu cầu kỹ thuật và Bảo mật:

Tuyệt đối không hardcode HASH_SECRET, vnp_TmnCode hoặc bất kỳ thông tin cấu hình nhạy cảm nào trực tiếp trong mã nguồn.

Hãy sử dụng cơ chế nạp cấu hình bảo mật từ biến môi trường (Environment Variables) thông qua hàm System.getenv().

Trong logic khởi tạo/xử lý, bắt buộc phải viết thêm logic kiểm tra (Validation): Nếu các biến môi trường này bị thiếu (null hoặc rỗng), hệ thống phải lập tức ném ra ngoại lệ phù hợp (ví dụ: IllegalStateException) kèm thông báo rõ ràng để đảm bảo an toàn, tránh lỗi vận hành (fail-fast).

Cung cấp hàm mẫu để khởi tạo URL thanh toán VNPay (vnp_Url mặc định của sandbox: https://sandbox.vnpayment.vn/paymentv2/vpcpay.html).

Hãy viết mã nguồn rõ ràng, clean code và tuân thủ các nguyên lý bảo mật."
- Đoạn mã nguồn Java hoàn chỉnh, an toàn do AI sinh ra (nằm trong khối code markdown) :

package com.library.service;

import java.net.URLEncoder;
import java.nio.charset.StandardCharsets;
import java.util.*;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;

/**
 * Service xử lý tích hợp thanh toán VNPay.
 * Đảm bảo an toàn thông tin theo tiêu chuẩn OWASP, không hardcode thông tin nhạy cảm.
 */
public class VNPayPaymentService {

    private static final String VNP_URL = "https://sandbox.vnpayment.vn/paymentv2/vpcpay.html";
    private static final String VNP_VERSION = "2.1.0";
    private static final String VNP_COMMAND = "pay";

    private final String hashSecret;
    private final String tmnCode;

    /**
     * Khởi tạo service và bắt buộc nạp cấu hình từ Biến môi trường (Environment Variables).
     * Áp dụng nguyên lý Fail-Fast: Ngăn chặn ứng dụng chạy nếu cấu hình thiếu an toàn.
     */
    public VNPayPaymentService() {
        // Nạp cấu hình từ Environment Variables để bảo mật thông tin nhạy cảm
        this.hashSecret = System.getenv("VNPAY_HASH_SECRET");
        this.tmnCode = System.getenv("VNPAY_TMN_CODE");

        // RÀNG BUỘC BẢO MẬT: Kiểm tra nghiêm ngặt sự tồn tại của cấu hình nhạy cảm
        if (hashSecret == null || hashSecret.trim().isEmpty()) {
            throw new IllegalStateException("CRITICAL ERROR: Biến môi trường 'VNPAY_HASH_SECRET' chưa được cấu hình. Hệ thống từ chối khởi tạo vì lý do bảo mật!");
        }
        if (tmnCode == null || tmnCode.trim().isEmpty()) {
            throw new IllegalStateException("CRITICAL ERROR: Biến môi trường 'VNPAY_TMN_CODE' chưa được cấu hình. Hệ thống từ chối khởi tạo vì lý do bảo mật!");
        }
    }

    /**
     * Tạo URL thanh toán VNPay từ các thông tin đơn hàng đầu vào.
     * * @param orderId  Mã đơn hàng (Unique)
     * @param amount   Số tiền thanh toán (Đơn vị: VND)
     * @param orderInfo Nội dung thanh toán
     * @param returnUrl URL nhận kết quả trả về sau khi khách hàng thanh toán xong
     * @param ipAddress Địa chỉ IP của khách hàng thực hiện giao dịch
     * @return Chuỗi URL hoàn chỉnh để redirect sang cổng VNPay
     */
    public String createPaymentUrl(String orderId, long amount, String orderInfo, String returnUrl, String ipAddress) {
        
        Map<String, String> vnpParams = new HashMap<>();
        vnpParams.put("vnp_Version", VNP_VERSION);
        vnpParams.put("vnp_Command", VNP_COMMAND);
        vnpParams.put("vnp_TmnCode", this.tmnCode);
        vnpParams.put("vnp_Amount", String.valueOf(amount * 100)); // VNPay yêu cầu nhân 100 để bỏ phần thập phân
        vnpParams.put("vnp_CurrCode", "VND");
        vnpParams.put("vnp_TxnRef", orderId);
        vnpParams.put("vnp_OrderInfo", orderInfo);
        vnpParams.put("vnp_OrderType", "other");
        vnpParams.put("vnp_Locale", "vn");
        vnpParams.put("vnp_ReturnUrl", returnUrl);
        vnpParams.put("vnp_IpAddr", ipAddress);

        // Lấy thời gian tạo giao dịch (vnp_CreateDate: yyyyMMddHHmmss)
        Calendar cld = Calendar.getInstance(TimeZone.getTimeZone("Etc/GMT+7"));
        java.text.SimpleDateFormat formatter = new java.text.SimpleDateFormat("yyyyMMddHHmmss");
        String vnpCreateDate = formatter.format(cld.getTime());
        vnpParams.put("vnp_CreateDate", vnpCreateDate);

        // Sắp xếp các tham số theo thứ tự alphabet (Bắt buộc bởi VNPay)
        List<String> fieldNames = new ArrayList<>(vnpParams.keySet());
        Collections.sort(fieldNames);

        StringBuilder hashData = new StringBuilder();
        StringBuilder query = new StringBuilder();
        
        try {
            Iterator<String> itr = fieldNames.iterator();
            while (itr.hasNext()) {
                String fieldName = itr.next();
                String fieldValue = vnpParams.get(fieldName);
                if ((fieldValue != null) && (fieldValue.length() > 0)) {
                    // Build hash data
                    hashData.append(fieldName);
                    hashData.append('=');
                    hashData.append(URLEncoder.encode(fieldValue, StandardCharsets.US_ASCII.toString()));
                    
                    // Build query url
                    query.append(URLEncoder.encode(fieldName, StandardCharsets.US_ASCII.toString()));
                    query.append('=');
                    query.append(URLEncoder.encode(fieldValue, StandardCharsets.US_ASCII.toString()));
                    
                    if (itr.hasNext()) {
                        query.append('&');
                        hashData.append('&');
                    }
                }
            }
            
            // Tính toán mã băm bảo mật (Secure Hash) bằng thuật toán HMAC-SHA512
            String vnpSecureHash = hmacSHA512(this.hashSecret, hashData.toString());
            query.append("&vnp_SecureHash=").append(vnpSecureHash);

            return VNP_URL + "?" + query.toString();
            
        } catch (Exception e) {
            throw new RuntimeException("Lỗi trong quá trình mã hóa và tạo URL thanh toán VNPay", e);
        }
    }

    /**
     * Hàm tiện ích mã hóa HMAC-SHA512 để ký số giao dịch
     */
    private String hmacSHA512(final String key, final String data) {
        try {
            if (key == null || data == null) {
                throw new NullPointerException("Key hoặc Data để mã hóa không được null");
            }
            final Mac hmac512 = Mac.getInstance("HmacSHA512");
            byte[] hmacKeyBytes = key.getBytes(StandardCharsets.UTF_8);
            final SecretKeySpec secretKey = new SecretKeySpec(hmacKeyBytes, "HmacSHA512");
            hmac512.init(secretKey);
            byte[] dataBytes = data.getBytes(StandardCharsets.UTF_8);
            byte[] result = hmac512.doFinal(dataBytes);
            StringBuilder sb = new StringBuilder(2 * result.length);
            for (byte b : result) {
                sb.append(String.format("%02x", b & 0xff));
            }
            return sb.toString();
        } catch (Exception ex) {
            return "";
        }
    }
}