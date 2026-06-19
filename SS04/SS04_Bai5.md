ROLE

Bạn là Senior Java Backend Engineer với kinh nghiệm xây dựng hệ thống chống gian lận (Fraud Detection) cho các ứng dụng FinTech.

TASK

Hãy thiết kế và triển khai lớp Java có tên:

FraudDetector

với phương thức:

public List detectFraud(List transactions)

để phát hiện các giao dịch đáng ngờ.

BUSINESS RULES
Một giao dịch có giá trị (amount) lớn hơn 100,000,000 VND
phải được đánh dấu là giao dịch đáng ngờ.

Nếu tồn tại hai giao dịch liên tiếp trên cùng một cardId:

được thực hiện tại hai địa điểm khác nhau
khoảng cách thời gian giữa hai giao dịch nhỏ hơn 10 phút

thì cả hai giao dịch đều phải được đánh dấu đáng ngờ.

Ví dụ:

Transaction A:
cardId = CARD001
location = Hanoi
time = 2026-06-01T10:00

Transaction B:
cardId = CARD001
location = Saigon
time = 2026-06-01T10:08

=> Cả A và B đều là giao dịch đáng ngờ.

DATA MODEL

Giả sử class Transaction có các thuộc tính:

private String transactionId;
private String cardId;
private BigDecimal amount;
private String location;
private LocalDateTime transactionTime;

TECHNICAL REQUIREMENTS
Sử dụng Java 8 trở lên.
Bắt buộc sử dụng:
LocalDateTime
Duration.between()

để tính khoảng cách thời gian.

Không sử dụng Date hoặc Calendar cũ.
Code phải rõ ràng, dễ bảo trì.
Tuân thủ Clean Code.
EDGE CASES

Phải xử lý đầy đủ các trường hợp:

transactions == null
-> trả về danh sách rỗng.
transactions.isEmpty()
-> trả về danh sách rỗng.
transaction.cardId == null
-> bỏ qua giao dịch đó.
transaction.location == null
-> bỏ qua quy tắc so sánh vị trí.
transaction.transactionTime == null
-> bỏ qua quy tắc so sánh thời gian.
amount == null
-> không kiểm tra quy tắc số tiền lớn.
OUTPUT REQUIREMENTS
Trả về danh sách các Transaction bị đánh dấu đáng ngờ.
Không trả về phần tử trùng lặp.
Giải thích ngắn gọn:
Thuật toán đang dùng
Độ phức tạp thời gian (Time Complexity)
Độ phức tạp bộ nhớ (Space Complexity)
Sinh đầy đủ:
class Transaction
class FraudDetector
ví dụ main() để chạy thử
CODING STYLE
Sử dụng Set để tránh giao dịch bị thêm nhiều lần.
Tên biến có ý nghĩa.
Không dùng magic number:
tạo hằng số:
MAX_ALLOWED_AMOUNT
MAX_TIME_DIFFERENCE_MINUTES
Thêm comment cho các đoạn logic nghiệp vụ quan trọng.

Dưới đây là một cách triển khai đáp ứng đầy đủ các yêu cầu nghiệp vụ, xử lý edge cases và tuân thủ Clean Code.

Transaction.java
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Objects;

public class Transaction {

    private String transactionId;
    private String cardId;
    private BigDecimal amount;
    private String location;
    private LocalDateTime transactionTime;

    public Transaction(String transactionId,
                       String cardId,
                       BigDecimal amount,
                       String location,
                       LocalDateTime transactionTime) {
        this.transactionId = transactionId;
        this.cardId = cardId;
        this.amount = amount;
        this.location = location;
        this.transactionTime = transactionTime;
    }

    public String getTransactionId() {
        return transactionId;
    }

    public String getCardId() {
        return cardId;
    }

    public BigDecimal getAmount() {
        return amount;
    }

    public String getLocation() {
        return location;
    }

    public LocalDateTime getTransactionTime() {
        return transactionTime;
    }

    /**
     * Dùng transactionId để xác định giao dịch duy nhất.
     */
    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }

        if (!(o instanceof Transaction)) {
            return false;
        }

        Transaction that = (Transaction) o;
        return Objects.equals(transactionId, that.transactionId);
    }

    @Override
    public int hashCode() {
        return Objects.hash(transactionId);
    }

    @Override
    public String toString() {
        return "Transaction{" +
                "transactionId='" + transactionId + '\'' +
                ", cardId='" + cardId + '\'' +
                ", amount=" + amount +
                ", location='" + location + '\'' +
                ", transactionTime=" + transactionTime +
                '}';
    }
}
FraudDetector.java
import java.math.BigDecimal;
import java.time.Duration;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class FraudDetector {

    private static final BigDecimal MAX_ALLOWED_AMOUNT =
            new BigDecimal("100000000");

    private static final long MAX_TIME_DIFFERENCE_MINUTES = 10;

    public List<Transaction> detectFraud(List<Transaction> transactions) {

        // Edge Case: null hoặc rỗng
        if (transactions == null || transactions.isEmpty()) {
            return new ArrayList<>();
        }

        // Set để tránh giao dịch bị thêm trùng lặp
        Set<Transaction> suspiciousTransactions = new LinkedHashSet<>();

        // Rule 1: Giao dịch vượt ngưỡng số tiền
        detectLargeAmountTransactions(
                transactions,
                suspiciousTransactions
        );

        // Rule 2: Hai giao dịch liên tiếp khác địa điểm trong vòng < 10 phút
        detectLocationJumpTransactions(
                transactions,
                suspiciousTransactions
        );

        return new ArrayList<>(suspiciousTransactions);
    }

    /**
     * Rule:
     * amount > 100,000,000 VND
     */
    private void detectLargeAmountTransactions(
            List<Transaction> transactions,
            Set<Transaction> suspiciousTransactions) {

        for (Transaction transaction : transactions) {

            if (transaction == null) {
                continue;
            }

            BigDecimal amount = transaction.getAmount();

            if (amount == null) {
                continue;
            }

            if (amount.compareTo(MAX_ALLOWED_AMOUNT) > 0) {
                suspiciousTransactions.add(transaction);
            }
        }
    }

    /**
     * Rule:
     * Hai giao dịch liên tiếp cùng card
     * khác location
     * cách nhau < 10 phút
     */
    private void detectLocationJumpTransactions(
            List<Transaction> transactions,
            Set<Transaction> suspiciousTransactions) {

        Map<String, List<Transaction>> transactionsByCard = new HashMap<>();

        for (Transaction transaction : transactions) {

            if (transaction == null) {
                continue;
            }

            String cardId = transaction.getCardId();

            // Edge Case:
            // cardId == null -> bỏ qua
            if (cardId == null) {
                continue;
            }

            transactionsByCard
                    .computeIfAbsent(cardId, key -> new ArrayList<>())
                    .add(transaction);
        }

        for (List<Transaction> cardTransactions : transactionsByCard.values()) {

            cardTransactions.sort(
                    Comparator.comparing(Transaction::getTransactionTime,
                            Comparator.nullsLast(Comparator.naturalOrder()))
            );

            for (int i = 1; i < cardTransactions.size(); i++) {

                Transaction previous = cardTransactions.get(i - 1);
                Transaction current = cardTransactions.get(i);

                if (isSuspiciousLocationJump(previous, current)) {
                    suspiciousTransactions.add(previous);
                    suspiciousTransactions.add(current);
                }
            }
        }
    }

    /**
     * Kiểm tra:
     * - khác địa điểm
     * - cách nhau < 10 phút
     */
    private boolean isSuspiciousLocationJump(
            Transaction previous,
            Transaction current) {

        if (previous == null || current == null) {
            return false;
        }

        String previousLocation = previous.getLocation();
        String currentLocation = current.getLocation();

        LocalDateTime previousTime = previous.getTransactionTime();
        LocalDateTime currentTime = current.getTransactionTime();

        // Edge Case:
        // location == null
        if (previousLocation == null || currentLocation == null) {
            return false;
        }

        // Edge Case:
        // transactionTime == null
        if (previousTime == null || currentTime == null) {
            return false;
        }

        boolean differentLocation =
                !previousLocation.equalsIgnoreCase(currentLocation);

        if (!differentLocation) {
            return false;
        }

        long minutesDifference =
                Math.abs(
                        Duration.between(previousTime, currentTime)
                                .toMinutes()
                );

        return minutesDifference < MAX_TIME_DIFFERENCE_MINUTES;
    }
}
Main.java (Demo)
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        List<Transaction> transactions = Arrays.asList(

                new Transaction(
                        "TXN001",
                        "CARD001",
                        new BigDecimal("50000000"),
                        "Hanoi",
                        LocalDateTime.of(
                                2026, 6, 1,
                                10, 0)
                ),

                new Transaction(
                        "TXN002",
                        "CARD001",
                        new BigDecimal("20000000"),
                        "Saigon",
                        LocalDateTime.of(
                                2026, 6, 1,
                                10, 8)
                ),

                new Transaction(
                        "TXN003",
                        "CARD002",
                        new BigDecimal("150000000"),
                        "Danang",
                        LocalDateTime.of(
                                2026, 6, 1,
                                11, 0)
                ),

                new Transaction(
                        "TXN004",
                        "CARD002",
                        new BigDecimal("1000000"),
                        "Danang",
                        LocalDateTime.of(
                                2026, 6, 1,
                                11, 30)
                )
        );

        FraudDetector detector = new FraudDetector();

        List<Transaction> suspiciousTransactions =
                detector.detectFraud(transactions);

        System.out.println("=== Suspicious Transactions ===");

        suspiciousTransactions.forEach(System.out::println);
    }
}
Kết quả mong đợi

Các giao dịch bị đánh dấu:

TXN001  (Hanoi)
TXN002  (Saigon)

Do:

Cùng CARD001
Khác location
10:00 -> 10:08
< 10 phút

Và:

TXN003

Do:

150,000,000 > 100,000,000
Thuật toán
Rule 1: Số tiền lớn

Duyệt toàn bộ danh sách:

amount > MAX_ALLOWED_AMOUNT
=> suspicious
Rule 2: Di chuyển bất thường
Nhóm giao dịch theo cardId
Sắp xếp giao dịch của từng thẻ theo transactionTime
So sánh từng cặp giao dịch liên tiếp:
khác location
thời gian < 10 phút
Nếu thỏa mãn:
đánh dấu cả giao dịch trước và sau
Time Complexity

Giả sử:

n = số giao dịch
Nhóm giao dịch
O(n)
Sắp xếp

Tổng cộng:

O(n log n)

(trường hợp xấu nhất tất cả giao dịch thuộc cùng một card)

Duyệt kiểm tra
O(n)
Tổng
O(n log n)
Space Complexity

Map nhóm theo card:

O(n)

Set lưu giao dịch đáng ngờ:

O(n)

Tổng:

O(n)
Điểm Clean Code nổi bật
Không dùng magic number.
Tách nhỏ phương thức theo từng nghiệp vụ.
Sử dụng Set để loại bỏ trùng lặp.
Sử dụng LocalDateTime và Duration.between().
Xử lý đầy đủ các edge cases.
Tên biến và tên hàm thể hiện rõ ý nghĩa nghiệp vụ.
ROLE

Bạn là Principal Software Architect và Performance Auditor chuyên đánh giá các hệ thống FinTech quy mô lớn.

CONTEXT

Dưới đây là mã nguồn FraudDetector do AI sinh ra ở bước trước.

[MÃ NGUỒN ĐƯỢC DÁN VÀO ĐÂY]

TASK

Hãy thực hiện kiểm toán hiệu năng (Performance Audit) đối với đoạn mã trên.

AUDIT OBJECTIVES
Phân tích thuật toán hiện tại.
Xác định chính xác:
vòng lặp nào gây tốn thời gian nhất
có đang sử dụng thuật toán O(N²) hay không
Giả sử hệ thống xử lý:
10.000 giao dịch
100.000 giao dịch
1.000.000 giao dịch

hãy đánh giá khả năng mở rộng (Scalability).

Tính:
Time Complexity
Space Complexity

cho từng phần logic.

OPTIMIZATION REQUIREMENTS

Nếu phát hiện đang dùng thuật toán O(N²):

Đề xuất thuật toán tối ưu hơn.

Tối ưu bằng cách:

a. Gom nhóm giao dịch theo cardId

Map<String, List>

b. Sắp xếp từng nhóm theo transactionTime

Collections.sort(...)
hoặc Comparator.comparing(...)

c. Chỉ so sánh các giao dịch lân cận trong cùng cardId

thay vì so sánh mọi cặp giao dịch.

Phân tích vì sao cách tiếp cận này giảm độ phức tạp.
EXPECTED ANALYSIS

So sánh:

Phiên bản hiện tại:
O(N²)

Phiên bản tối ưu:
O(N log N)

hoặc tốt hơn nếu có thể.

OUTPUT FORMAT
1. Executive Summary

Tóm tắt ngắn gọn kết quả kiểm toán.

2. Performance Bottlenecks

Liệt kê các điểm nghẽn hiệu năng.

3. Complexity Analysis

Bảng phân tích Big-O.

4. Optimization Proposal

Mô tả thuật toán tối ưu.

5. Refactored Code

Viết lại mã nguồn tối ưu hoàn chỉnh.

6. Benchmark Expectation

Ước lượng mức cải thiện khi:

N = 10.000
N = 100.000
N = 1.000.000
7. Risk Assessment

Đánh giá rủi ro khi triển khai phiên bản tối ưu.

REVIEW RULES
Không chỉ nhận xét chung chung.
Phải chỉ rõ dòng logic gây O(N²).
Giải thích bằng Big-O.
Đưa ra mã nguồn Java tối ưu hoàn chỉnh.
Tuân thủ Clean Code và Java 8+.

Mã nguồn:

Transaction.java
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Objects;

public class Transaction {

    private String transactionId;
    private String cardId;
    private BigDecimal amount;
    private String location;
    private LocalDateTime transactionTime;

    public Transaction(String transactionId,
                       String cardId,
                       BigDecimal amount,
                       String location,
                       LocalDateTime transactionTime) {
        this.transactionId = transactionId;
        this.cardId = cardId;
        this.amount = amount;
        this.location = location;
        this.transactionTime = transactionTime;
    }

    public String getTransactionId() {
        return transactionId;
    }

    public String getCardId() {
        return cardId;
    }

    public BigDecimal getAmount() {
        return amount;
    }

    public String getLocation() {
        return location;
    }

    public LocalDateTime getTransactionTime() {
        return transactionTime;
    }

    /**
     * Dùng transactionId để xác định giao dịch duy nhất.
     */
    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }

        if (!(o instanceof Transaction)) {
            return false;
        }

        Transaction that = (Transaction) o;
        return Objects.equals(transactionId, that.transactionId);
    }

    @Override
    public int hashCode() {
        return Objects.hash(transactionId);
    }

    @Override
    public String toString() {
        return "Transaction{" +
                "transactionId='" + transactionId + '\'' +
                ", cardId='" + cardId + '\'' +
                ", amount=" + amount +
                ", location='" + location + '\'' +
                ", transactionTime=" + transactionTime +
                '}';
    }
}

FraudDetector.java
import java.math.BigDecimal;
import java.time.Duration;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class FraudDetector {

    private static final BigDecimal MAX_ALLOWED_AMOUNT =
            new BigDecimal("100000000");

    private static final long MAX_TIME_DIFFERENCE_MINUTES = 10;

    public List<Transaction> detectFraud(List<Transaction> transactions) {

        // Edge Case: null hoặc rỗng
        if (transactions == null || transactions.isEmpty()) {
            return new ArrayList<>();
        }

        // Set để tránh giao dịch bị thêm trùng lặp
        Set<Transaction> suspiciousTransactions = new LinkedHashSet<>();

        // Rule 1: Giao dịch vượt ngưỡng số tiền
        detectLargeAmountTransactions(
                transactions,
                suspiciousTransactions
        );

        // Rule 2: Hai giao dịch liên tiếp khác địa điểm trong vòng < 10 phút
        detectLocationJumpTransactions(
                transactions,
                suspiciousTransactions
        );

        return new ArrayList<>(suspiciousTransactions);
    }

    /**
     * Rule:
     * amount > 100,000,000 VND
     */
    private void detectLargeAmountTransactions(
            List<Transaction> transactions,
            Set<Transaction> suspiciousTransactions) {

        for (Transaction transaction : transactions) {

            if (transaction == null) {
                continue;
            }

            BigDecimal amount = transaction.getAmount();

            if (amount == null) {
                continue;
            }

            if (amount.compareTo(MAX_ALLOWED_AMOUNT) > 0) {
                suspiciousTransactions.add(transaction);
            }
        }
    }

    /**
     * Rule:
     * Hai giao dịch liên tiếp cùng card
     * khác location
     * cách nhau < 10 phút
     */
    private void detectLocationJumpTransactions(
            List<Transaction> transactions,
            Set<Transaction> suspiciousTransactions) {

        Map<String, List<Transaction>> transactionsByCard = new HashMap<>();

        for (Transaction transaction : transactions) {

            if (transaction == null) {
                continue;
            }

            String cardId = transaction.getCardId();

            // Edge Case:
            // cardId == null -> bỏ qua
            if (cardId == null) {
                continue;
            }

            transactionsByCard
                    .computeIfAbsent(cardId, key -> new ArrayList<>())
                    .add(transaction);
        }

        for (List<Transaction> cardTransactions : transactionsByCard.values()) {

            cardTransactions.sort(
                    Comparator.comparing(Transaction::getTransactionTime,
                            Comparator.nullsLast(Comparator.naturalOrder()))
            );

            for (int i = 1; i < cardTransactions.size(); i++) {

                Transaction previous = cardTransactions.get(i - 1);
                Transaction current = cardTransactions.get(i);

                if (isSuspiciousLocationJump(previous, current)) {
                    suspiciousTransactions.add(previous);
                    suspiciousTransactions.add(current);
                }
            }
        }
    }

    /**
     * Kiểm tra:
     * - khác địa điểm
     * - cách nhau < 10 phút
     */
    private boolean isSuspiciousLocationJump(
            Transaction previous,
            Transaction current) {

        if (previous == null || current == null) {
            return false;
        }

        String previousLocation = previous.getLocation();
        String currentLocation = current.getLocation();

        LocalDateTime previousTime = previous.getTransactionTime();
        LocalDateTime currentTime = current.getTransactionTime();

        // Edge Case:
        // location == null
        if (previousLocation == null || currentLocation == null) {
            return false;
        }

        // Edge Case:
        // transactionTime == null
        if (previousTime == null || currentTime == null) {
            return false;
        }

        boolean differentLocation =
                !previousLocation.equalsIgnoreCase(currentLocation);

        if (!differentLocation) {
            return false;
        }

        long minutesDifference =
                Math.abs(
                        Duration.between(previousTime, currentTime)
                                .toMinutes()
                );

        return minutesDifference < MAX_TIME_DIFFERENCE_MINUTES;
    }
}

Main.java (Demo)
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        List<Transaction> transactions = Arrays.asList(

                new Transaction(
                        "TXN001",
                        "CARD001",
                        new BigDecimal("50000000"),
                        "Hanoi",
                        LocalDateTime.of(
                                2026, 6, 1,
                                10, 0)
                ),

                new Transaction(
                        "TXN002",
                        "CARD001",
                        new BigDecimal("20000000"),
                        "Saigon",
                        LocalDateTime.of(
                                2026, 6, 1,
                                10, 8)
                ),

                new Transaction(
                        "TXN003",
                        "CARD002",
                        new BigDecimal("150000000"),
                        "Danang",
                        LocalDateTime.of(
                                2026, 6, 1,
                                11, 0)
                ),

                new Transaction(
                        "TXN004",
                        "CARD002",
                        new BigDecimal("1000000"),
                        "Danang",
                        LocalDateTime.of(
                                2026, 6, 1,
                                11, 30)
                )
        );

        FraudDetector detector = new FraudDetector();

        List<Transaction> suspiciousTransactions =
                detector.detectFraud(transactions);

        System.out.println("=== Suspicious Transactions ===");

        suspiciousTransactions.forEach(System.out::println);
    }
}

1. Executive Summary
Kết luận kiểm toán

Đoạn mã hiện tại KHÔNG sử dụng thuật toán O(N²).

Đây là điểm rất quan trọng vì yêu cầu audit giả định khả năng tồn tại O(N²), nhưng khi phân tích thực tế:

Rule 1 (Amount Check): O(N)
Rule 2 (Location Jump Detection):
Group theo cardId: O(N)
Sort từng nhóm: O(Σ Ki log Ki)
Scan từng nhóm: O(N)

Trong đó:

Σ Ki = N

=> Độ phức tạp tổng thể:

O(N + Σ Ki log Ki + N)
≈ O(Σ Ki log Ki)

Trường hợp xấu nhất:

O(N log N)

Không tồn tại vòng lặp lồng nhau duyệt mọi cặp giao dịch.

Do đó:

Current Version = O(N log N)

chứ không phải:

O(N²)
2. Performance Bottlenecks
Bottleneck #1: Sorting theo từng Card

Đây là đoạn tốn thời gian nhất toàn hệ thống.

cardTransactions.sort(
    Comparator.comparing(
        Transaction::getTransactionTime,
        Comparator.nullsLast(
            Comparator.naturalOrder()
        )
    )
);

Giả sử:

CARD001 = 500.000 transactions

thì riêng card này sẽ tốn:

O(500000 log 500000)
Bottleneck #2: Object Allocation

Trong bước grouping:

transactionsByCard
    .computeIfAbsent(cardId, key -> new ArrayList<>())
    .add(transaction);

Hệ thống tạo:

HashMap
List cho từng card
Iterator
Comparator

Với:

1.000.000 transactions

chi phí bộ nhớ bắt đầu đáng kể.

Bottleneck #3: Duration.between()

Trong mỗi lần so sánh:

Duration.between(previousTime, currentTime)
        .toMinutes();

API này tạo thêm object Duration.

Ở quy mô hàng triệu giao dịch:

Hàng triệu Duration object

gây:

GC Pressure
3. Complexity Analysis
Logic	Time Complexity	Space Complexity
detectLargeAmountTransactions	O(N)	O(1)
Group by cardId	O(N)	O(N)
Sort each card group	O(Σ Ki log Ki)	O(log Ki)
Adjacent comparison	O(N)	O(1)
LinkedHashSet insert	O(1) average	O(M)
Total	O(N log N)	O(N)

Trong đó:

N = số giao dịch
Ki = số giao dịch của card i
M = số giao dịch đáng ngờ
4. Optimization Proposal
Nếu phiên bản cũ là O(N²)

Ví dụ anti-pattern thường gặp:

for (Transaction t1 : transactions) {
    for (Transaction t2 : transactions) {

        if (sameCard(t1, t2)) {
            ...
        }
    }
}

Complexity:

O(N²)
Phiên bản hiện tại đã áp dụng đúng chiến lược tối ưu
Bước 1

Group theo card

Map<String, List<Transaction>>

Chi phí:

O(N)
Bước 2

Sort từng card

Collections.sort(...)

Chi phí:

O(N log N)

Worst Case.

Bước 3

So sánh giao dịch liền kề

Thay vì:

mọi cặp giao dịch

ta chỉ kiểm tra:

previous
current

Chi phí:

O(N)
Tại sao chỉ cần so sánh lân cận?

Sau khi sort:

10:00 Hanoi
10:05 Saigon
10:06 Danang
10:30 Hanoi

Nếu:

10:00 ↔ 10:05

đã thỏa:

< 10 phút

thì:

10:00 ↔ 10:06

không cần duyệt lại toàn bộ danh sách.

Đây chính là kỹ thuật:

Sort + Linear Scan

thay thế:

Brute Force Pairwise Comparison
5. Refactored Code

Mặc dù hiện tại đã đạt O(N log N), vẫn có thể tối ưu CPU và GC.

Cải tiến

Thay:

Duration.between(...)

bằng:

ChronoUnit.MINUTES.between(...)

tránh tạo object.

private boolean isSuspiciousLocationJump(
        Transaction previous,
        Transaction current) {

    if (previous == null || current == null) {
        return false;
    }

    if (previous.getLocation() == null
            || current.getLocation() == null) {
        return false;
    }

    if (previous.getTransactionTime() == null
            || current.getTransactionTime() == null) {
        return false;
    }

    if (previous.getLocation()
            .equalsIgnoreCase(current.getLocation())) {
        return false;
    }

    long minutesDifference =
            Math.abs(
                    ChronoUnit.MINUTES.between(
                            previous.getTransactionTime(),
                            current.getTransactionTime()
                    )
            );

    return minutesDifference
            < MAX_TIME_DIFFERENCE_MINUTES;
}
Tối ưu bộ nhớ HashMap

Nếu biết trước kích thước:

Map<String, List<Transaction>> transactionsByCard =
        new HashMap<>(
                (int)(transactions.size() / 0.75f) + 1
        );

Giảm rehash.

Tối ưu toàn bộ detectLargeAmountTransactions

Có thể gộp vào bước grouping:

for(Transaction tx : transactions){

    // Rule 1
    ...

    // Rule 2 grouping
    ...
}

Giảm từ:

2 lần duyệt N

thành:

1 lần duyệt N

Mặc dù Big-O không đổi:

O(N)

nhưng CPU giảm đáng kể.

6. Benchmark Expectation
Trường hợp hiện tại (O(N log N))
N = 10.000
N log₂N
≈ 10.000 × 13.3
≈ 133.000 operations

Hoàn toàn tức thời.

N = 100.000
100.000 × 16.6
≈ 1.660.000 operations

Rất ổn.

N = 1.000.000
1.000.000 × 20
≈ 20.000.000 operations

Vẫn khả thi trên JVM hiện đại.

Nếu dùng O(N²)
N = 10.000
100.000.000 comparisons

Đã bắt đầu chậm.

N = 100.000
10.000.000.000 comparisons

Không khả thi.

N = 1.000.000
1.000.000.000.000 comparisons

Gần như không thể chạy thực tế.

Tỷ lệ cải thiện
N	O(N²)	O(N log N)
10.000	100 triệu	133 nghìn
100.000	10 tỷ	1.66 triệu
1.000.000	1 nghìn tỷ	20 triệu

Mức cải thiện:

~50.000 lần

đến

~100.000 lần

ở quy mô lớn.

7. Risk Assessment
Risk 1: Memory Consumption

Với:

1.000.000 transactions

Map sẽ giữ toàn bộ dữ liệu:

Map<String, List<Transaction>>

Memory:

O(N)

Nếu mỗi transaction ~300 bytes:

≈ 300 MB

chưa tính overhead object.

Cần monitor Heap.

Risk 2: Skewed Distribution

Ví dụ:

1 card duy nhất
1.000.000 giao dịch

khi đó:

sort = O(N log N)

và trở thành điểm nghẽn chính.

Risk 3: Data Quality

Nếu dữ liệu nguồn chưa được sắp xếp thời gian và có nhiều:

transactionTime == null

thì số lượng giao dịch bị bỏ qua sẽ tăng.

Nên ghi log hoặc metric để theo dõi.

Kết luận cuối cùng

Sau kiểm toán:

Current FraudDetector:
Time Complexity  = O(N log N)
Space Complexity = O(N)

Không phát hiện thuật toán O(N²).

Thiết kế hiện tại đã áp dụng đúng chiến lược:

Group by cardId
        +
Sort per card
        +
Adjacent comparison

đây là cách tiếp cận phù hợp cho hệ thống FinTech xử lý từ hàng chục nghìn đến hàng triệu giao dịch. Các tối ưu còn lại chủ yếu là giảm CPU, GC và memory overhead, chứ không phải thay đổi độ phức tạp thuật toán.