# Logging

## Log là gì

Log là một quá trình ghi lại những thông tin được thông báo, lưu lại trong quá trình hoạt động của một ứng dụng ở một nơi tập trung. Mục đích chính là để có thể xem lại các thông tin hoạt động của ứng dụng trong quá khứ như debug khi có lỗi xảy ra, check health, xem info, error, warning, ...

## Phân loại log (Log Level)

- **All:** đây là cấp độ thấp nhất, Logger và Appender được định nghĩa với cấp độ này, mọi thông tin cần log sẽ được log.
- **Debug:** các thông tin dùng để debug, chúng ta có thể bật/ tắt log này dựa vào mode của application.
- **Info:** các thông tin mà bạn muốn ghi nhận thêm trong quá trình hoạt động của hệ thống. Ví dụ: log số lượng request, status, duration, … để biết traffic của hệ thống thế nào.
- **Error:** các lỗi khi chạy chương trình sẽ được log. Cố gắng log toàn bộ thông tin liên quan nhiều nhất có thể để có thể reproduce lại được mà ít tốn thời gian nhất.
- **Fatal:** log các lỗi nghiêm trọng xảy ra trong chương trình, có thể làm cho chương trình không sử dụng được nữa.
- **Off:** đây là cấp độ cao nhất, được sử dụng khi chúng ta không muốn log bất kỳ thông tin nào nữa.

Thứ tự độ ưu tiên như sau: **ALL < DEBUG < INFO < WARN < ERROR < FATAL < OFF**

## Log rotate

Log rotate là việc cắt nhỏ log ra và lưu trữ trên nhiều file thay vì một file.

Một số chiến lược:

- Có thể lưu file log riêng theo từng ngàg, tuần hoặc tháng. Ví dụ: javacode-app-22072019.log, javacode-app-23072019.log,...
- File log sẽ chia theo loại log: javacode-app-22072019.error.log, javacode-app-22072019.warning.log,...
- File log sẽ cắt theo chiến lược khác như dung lượng file. Ví dụ: mỗi file tối đa 100 MB.

## Reference

- [https://gpcoder.com/5500-gioi-thieu-java-logging/](https://gpcoder.com/5500-gioi-thieu-java-logging/)
