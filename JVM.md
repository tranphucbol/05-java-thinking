# JVM

## Định nghĩa JVM

**Java Virtual Machine** (Viết tắt là JVM) là môi trường dùng để chạy ứng dụng được viết bằng ngôn ngữ lập trình Java.

<div align="center">
    <img src="/media/jvm.png" alt="java-thinking">
</div>

Nhờ có JVM mà Java có thể chạy trên nhiều Platform khác nhau. JVM giống như một cái máy ảo, muốn khởi chạy Java thì bắt buộc phải chạy trên cái máy ảo này. Cứ với mỗi Platform ta sẽ có một JVM tương ứng, ví dụ như Ubuntu thì sẽ có bản JVM cho Ubuntu, Windows thì có JVM cho Windows. Và cơ chế hoạt động của JVM ở mọ nền tảng là hoàn toàn như nhau cho nên ứng dụng Java viết trên Window chạy được trên JVM của Window, khi đem cái ứng dụng đó qua Ubuntu thì chỉ cần cài JVM lên Ubuntu là ứng dụng chạy được.

## Các thành phần chính của JVM

<div align="center">
    <img src="/media/jvm-component.png" alt="java-thinking">
</div>

- **Class Loader:** là một hệ thống con của JVM, làm nhiệm vụ tải các lớp được định nghĩa.
- **Class Area:** lưu trữ cấu trúc của các lớp, thuộc tính, phương thức của lớp, và code của các phương thức
- **Heap:** là vùng nhớ lưu trử các đối tượng được khởi tạo trong quá trình thực thi.
- **Stack:** chứa các frame. Mỗi frame chứa các biến cục bộ và thực thi một hàm gọi và trả kết quả về. Mỗi tiến trình có một Stack riêng, được khởi tạo cùng lúc với tiến trình. Mỗi frame sẽ được tạo khi một hàm được gọi và hủy khi việc gọi hàm kết thúc.
- **Programming Counter Register** chứa địa chỉ của máy chủ ảo đang thực thi
- **Native Method Stack:** chứa các hàm của hệ thống được sử dụng trong chương trình
- **Execution Engine:** là một hệ thống bao gồm: bộ xử lý ảo, trình thông dịch (đọc Java byte code và thực thi các chỉ thị), JIT compiler biên dịch mã byte code sang mã máy. Các nhiệm vụ chính của JVM bao gồm: tải code, kiểm tra code, thực thi code, cung cấp môi trường runtime.

## Cơ chế hoạt động của JVM

JVM được chia thành 3 module chính:

- **Class-Loader Subsystem:** chuyên tìm kiếm và laod các file .class vào vùng nhớ của Java
- **Runtime Data Area:** vùng nhớ hệ thống cấp phát cho Java Virtual Machine.
- **Execution Engine:** chuyển các lệnh của JVM trong file .class thành các lệnh của máy, hệ điều hành tương ứng và thực thi chúng

## JRE

<div align="center">
    <img src="/media/jdk-jvm.png" alt="java-thinking">
</div>

**JRE** (là viết tắt của Java Runtime Environment) được sử dụng để cung cấp môi trường runtime. Nó là trình triển khai của JVM. JRE bao gồm tập hợp các thư viện và các file khác mà JVM sử dụng tại runtime. Trình triển khai của JVM cũng được công bố bởi các công ty khác ngoài Sun Micro Systems.

## JDK

**JDK** (là viết tắt của Java Development Kit) bao gồm JRE và các Development Tool.
