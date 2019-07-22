# Unit Test/Logging/Performance

## Unit Test

### Giới thiệu về JUnit

JUnit là một framework đơn giản dùng cho việc tạo các unit testing tự động, và chạy các test có lặp đi lặp lại. Nó chỉ lafmoojt pahaafn của họ kiến trúc xUnit cho việc tạo các unit testing.

### Các khái niệm Unit Test

- Assertion: Là một phát biểu mô tả các công việc kiểm tra cần tiến hành, ví dụ: `AreEqual()`, `IsTrue()`,`IsNotNull()`, ... Mỗi một UnitTest gồm nhiều assertion kiểm tra dữ liệu đầu ra, tính chính xác của các lỗi ngoại lệ ra và các vấn đề phức tạp khác như:
  - Sự tồn tại của một đối tượng
  - Điều kiện biên: các giá trị có vượt ra ngoài giới hạn hay không
  - Thứ tự thực hiện các luồng dữ liệu.
- Test Point: là một đơn vị kiểm tra nhỏ nhất, chỉ chứa đơn giản một assertion nhằm khẳng định tính đúng đắn của một chi tiết code nào đó. Mọi thành viên dự án đều có thể viết một test point.
- Test Case: là một tập hợp các test point nhằm kiểm tra một đặc điểm chức năng cụ thể, ví dụ tòan bộ giai đoạn người dùng nhập dữ liệu cho đến khi thông tin được nhập vào csdl.
- Test Suite: Là một tập hợp các test case định nghĩa cho từng module hoặc hệ thống con.
- Regression Testing: Là phương pháp kiểm nghiệm tự động sử dụng một phần mềm đặc biệt. Cùng một loại dữ liệu kiểm tra giống nhau nhưng được tiến hành nhiều lần lặp lại tự động nhằm ngăn chặn các lỗi cũ phát sinh trở lại. Kết hợp Regression Testing với Unit Testing sẽ đảm bảo các đoạn mã mới vẫn đáp ứng yêu cầu thay đổi và các đoạn mã cũ sẽ không bị ảnh hưởng bởi các hoạt động bảo trì.

### Kiến trúc tổng quan

<div align="center">
    <img src="/media/junit.png" alt="java-thinking">
</div>

### Cách viết một test case

- B1: Tạo maven project và thêm dependency vào file `pom.xml`:

```xml
<dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
</dependency>
```

- B2: Tạo class MyMath trong thư mục `main/java`:

```java

public class MyMath {
    public int add(int a, int b) {
        return a + b;
    }
}

```

- B3: Tạo class `TestAdd` trong thư mục `test/java`

```java
import org.junit.Assert;
import org.junit.Test;

public class TestAdd {
    @Test
    public void testOnePlusOneEqualTwo() {
        Assert.assertEquals(MyMath.add(1, 1), 2);
    }
}
```

## Logging

### Log là gì

Log là một quá trình ghi lại những thông tin được thông báo, lưu lại trong quá trình hoạt động của một ứng dụng ở một nơi tập trung. Mục đích chính là để có thể xem lại các thông tin hoạt động của ứng dụng trong quá khứ như debug khi có lỗi xảy ra, check health, xem info, error, warning, ...

### Phân loại log (Log Level)

- **All:** đây là cấp độ thấp nhất, Logger và Appender được định nghĩa với cấp độ này, mọi thông tin cần log sẽ được log.
- **Debug:** các thông tin dùng để debug, chúng ta có thể bật/ tắt log này dựa vào mode của application.
- **Info:** các thông tin mà bạn muốn ghi nhận thêm trong quá trình hoạt động của hệ thống. Ví dụ: log số lượng request, status, duration, … để biết traffic của hệ thống thế nào.
- **Error:** các lỗi khi chạy chương trình sẽ được log. Cố gắng log toàn bộ thông tin liên quan nhiều nhất có thể để có thể reproduce lại được mà ít tốn thời gian nhất.
- **Fatal:** log các lỗi nghiêm trọng xảy ra trong chương trình, có thể làm cho chương trình không sử dụng được nữa.
- **Off:** đây là cấp độ cao nhất, được sử dụng khi chúng ta không muốn log bất kỳ thông tin nào nữa.

Thứ tự độ ưu tiên như sau: **ALL < DEBUG < INFO < WARN < ERROR < FATAL < OFF**

### Log rotate

Log rotate là việc cắt nhỏ log ra và lưu trữ trên nhiều file thay vì một file.

Một số chiến lược:

- Có thể lưu file log riêng theo từng ngàg, tuần hoặc tháng. Ví dụ: javacode-app-22072019.log, javacode-app-23072019.log,...
- File log sẽ chia theo loại log: javacode-app-22072019.error.log, javacode-app-22072019.warning.log,...
- File log sẽ cắt theo chiến lược khác như dung lượng file. Ví dụ: mỗi file tối đa 100 MB.

## Performance

### Throughput

Throughput (Thông lượng): là số hành động được thực hiện hay số kết quả được đưa ra trong một đơn vị thời gian.

### Latency

Latency (Độ trễ): là thời gian để thực hiện một số hành động hoặc để tạo ra một số kết quả.

### P99 Latency

Một cách đơn giản để tính P99, là sắp xếp giá trị và lấy giá trị thứ 99/100. Ví dụ, nếu bạn có 1000 giá trị latency, đặt chúng trong một mảng, và sắp chúng, sau đó lấy gía trị thứ 990. Đó sẽ là 99% tile, nó thể hiện giá trị latency là 99% thấp hơn.

<div align="center">
    <img src="/media/p99.png" src="java-thinking">
</div>

## Reference

- [https://viblo.asia/p/testing-with-junit-in-java-dWrvwWODvw38](https://viblo.asia/p/testing-with-junit-in-java-dWrvwWODvw38)
- [https://viblo.asia/p/unit-test-la-gi-maGK7m3Llj2](https://viblo.asia/p/unit-test-la-gi-maGK7m3Llj2)
- [https://gpcoder.com/5500-gioi-thieu-java-logging/](https://gpcoder.com/5500-gioi-thieu-java-logging/)
- [P99 Latency](https://blog.bramp.net/post/2018/01/16/measuring-percentile-latency/)
