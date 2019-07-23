# Networking

## Connection pooling

Một connection là một bộ đệm duy trì các kết nối tới database, Các kết tới database sau khi sử dụng sẽ không đống lại ngay mà sẽ được dùng lại khi được yêu cầu trong tương lai

Cơ chế hoạt động của nó như sau: khi một connection được tạo, nó sẽ được đưa  vào pool và sử dụng lại cho các yêu cầu kết tiếp theo và chỉ đóng khi hết thời gian timeout.

Bây giờ user kết nối tới database (truy vấn database), hệ thống sẽ kiểm tra trong connection pool có két nối nào đang rảnh không?

- Trường hợp chưa có kết nối nào trong connection pool hoặc tất cả các kết nối đều bận (đang được sử dụng bởi user khác) và sống lượng connection trong conneciotn < 10 thì sẽ tạo một connection mới tới database để kết nối tới database đồng thời kết nối đó sẽ được đưa vào connection pool.
- Trường hợp tất cả các kết nối đáng bận và số lượng connection trong connection pool = 10 thì người dùng phải đợi cho các user dùng xong để được dùng.

<div align="center">
    <img src="/media/connection-pool.png" alt="java-thinking">
</div>

## Caching

**Caching** cải thiện thời gian tải trang và giảm thiểu việc phải tương tác với database của server. Cở sở dữ liệu thường hoạt động tốt nhờ việc phân phối đồng đề các lần đọc ghi trên các phân vùng của nó. Các item phổ biến sẽ làm mất cân bằng phân phối, gây nghẽn cổ chai. Đưa một bộ nhớ cache ở phía trước cơ sở dữ liệu có thể giải quyết vấn đề tải không đều và sự tăng đột biến lượng truy cập.

Có thể thấy được tác dụng lớn của cache. Giả sử mỗi giây ta nhận được 100 requesr, mỗi request sẽ mất 1s để chờ query database. Database sẽ dễ dàng bị quá tải. Sử dụng cache để lưu lại kết quả của câu query, lúc này dữ liệu được lưu trong RAM, thời gian truy xuất chỉ còn 50-100ms, không cần phải truy cập vào database. Hệ thống được giảm tải, còn người dùng lại nhận được kết quả nhanh hơn rất nhiều.

### Cache với Guava

Đầu tiên, ta sẽ tạo **CacheLoader**, dùng để tính giá trị lưu trữ cache. Ta sẽ dùng tiện ích **CacheBuilder** để xây dựng một cache bằng các cấu hình ta đã chỉ định:

```java
@Test
public void whenCacheMiss_thenValueIsComputed() {
    CacheLoader<String, String> loader;
    loader = new CacheLoader<String, String>() {
        @Override
        public String load(String key) {
            return key.toUpperCase();
        }
    };

    LoadingCache<String, String> cache;
    cache = CacheBuilder.newBuilder().build(loader);

    assertEquals(0, cache.size());
    assertEquals("HELLO", cache.getUnchecked("hello"));
    assertEquals(1, cache.size());
}
```

### Cache với Redis

[Redis](https://gitlab.zalopay.vn/phuctt4/04-database-thinking/blob/master/redis.md)

## Cơ bản về protocol trong networking

### HTTP

<div align="center">
    <img src="/media/http.png" alt="java-thinking">
</div>

**HTTP** hoạt động dựa trên mô hình **Client – Server**. Trong mô hình này, các máy tính của người dùng sẽ đóng vai trò làm máy khách (Client). Sau một thao tác nào đó của người dùng, các máy khách sẽ gửi yêu cầu đến máy chủ (Server) và chờ đợi câu trả lời từ những máy chủ này.

**HTTP** là một **stateless protocol**. Hay nói cách khác, request hiện tại không biết những gì đã hoàn thành trong request trước đó.

HTTP cho phép tạo các yêu cầu gửi và nhận các kiểu dữ liệu, do đó cho phép xây dựng hệ thống độc lập với dữ liệu được truyển giao.

<div align="center">
    <img src="/media/http-request.png" alt="java-thinking">
</div>

**Giao thức HTTP** định nghĩa một tập các phương thức request, client có thể sử dụng một trong các phương thức này để tạo request tới HTTP server, dưới đây liệt kê một số phương thức phổ biến.

<div align="center">
    <img src="/media/http-request-method.png" alt="java-thinking">
</div>

**HTTP Response:**

<div align="center">
    <img src="/media/http-response.png" alt="java-thinking">
</div>

### WebSocket

Websocket là giao thức chuẩn cho trao đổi dữ liệu hai chiều giữa client và server. Giao thức WebSocket không chạy trên HTTP, thay vào đó nó thực hiện trên giao thức TCP. WebSocket là một kiểu Reverse Ajax

Người ta thường dùng Websocket thay vì HTTP cho những trường hợp yêu cầu real time (thời gian thực). Ví dụ bạn muốn hiện thị biểu đồ, chỉ số chứng khoán, web chat… thì không thể gửi lệnh AJAX liên tiếp tới server để lấy dữ liệu mới rồi cập nhật lên màn hình, như thế sẽ tốn nhiều tài nguyên, traffic.

Gói tin của WebSockets nhẹ hơn HTTP rất nhiều, giảm độ trễ của network lên đến 3 lần, không cần phải gửi request liên tiếp như HTTP.

<div align="center">
    <img src="/media/websocket.png" alt="java-thinking">
</div>

#### Cấu trúc của websocket

- Chuẩn giao thức của websocket là **ws://** cho chuẩn thông thường và **wss://** cho chuẩn secure (tương tự **http://** và **https://**)
- Message data types: chuẩn giao tiếp là String, hiện tại đã hỗ trợ buffered arrays và blobs. Vẫn chưa hỗ trợ JSON (tuy nhiên có thể serialize thành String)

### gRPC

**gRPC** là một framework dành cho RPC của Google, cái tên của nó nếu viết đầy đủ ra thì không máy tính nào chạy nổi vì bị stack overflow: “gRPC Remote Procedure Call”, một cách đặt tên đệ quy quen thuộc với mấy anh em PHP (PHP Hypertext Processor).

RPC có thể được xem là một giao thức request & respone thông thường. Tuy nhiên, nó được dùng cho việc giao tiếp giữa các server với nhau (server-server) nhiều hơn là client-server. Việc này có ý nghĩa rất quan trọng, vì trong các hệ thống phân tán – distributed system – application code ở nhiều server hơn là một server. Ví dụ thường thấy nhất chính là kiến trúc Microservice.

Điều này nghĩa là, một request từ phía client có thể cần nhiều service, chạy trên nhiều server, tổng hợp thông tin rồi sau đó mới response cho client.

Sự liên lạc giữa các service trên các server lúc là sẽ là vấn đề, trước đây, tất cả các service thì chạy trên cùng một server thoải mái, vì local call thì chi phí không đáng kể. Nhưng bây giờ hệ thống phân tán các service được chia ra ở nhiều server khác nhau. Các server muốn giao tiếp với nhau, thì server phải encode data (XML, JSON, ...) để gửi đi, sau đó decode data để đọc data, sau đó lại encode. Các quá trình này encode, decode này khá tốn tài nguyên của CPU.

**Tối ưu cho việc "giao tiếp" giữa các server là lý do gRPC ra đời.**

Để giải bài toán trên, gRPC sử dụng binary để truyền đi, thay vì phải encode chúng thành các ngôn ngữ trung gian như JSON, XML, …

<div align="center">
    <img src="/media/grpc.png" alt="java-thinking">
</div>

Việc này làm tăng tốc độ giao tiếp giữa các server lên rất nhiều, đồng thời làm giảm overhead cho CPUs. Google cũng làm ra luôn cả protobuf (Protocol Buffers), đây là ngôn ngữ mà gRPC dùng như một default serialization format. Implement phần này thật sự phải là tay to lắm nên Google xử dụng protobuf như một script trung gian để generate phần “hard core” cho các lập trình viên ở các ngôn ngữ phổ biến như: C++, C#, Go, Java, Python, NodeJS….

Thứ giúp gRPC giao tiếp binary ngon vậy chính là HTTP/2, đây vốn là giao thức có rất nhiều cải tiến so với HTTP/1.1. Bản thân HTTP/2 cũng được coi như là sự thay thế cho SPDY – giao thức mà cũng chính Google phát triển – open source vào năm 2012 và ngừng hỗ trợ vào 2015 (HTTP/2 có implement và thay thế rồi).

### SSL/TLS

**SSL** là chữ viết tắt của **Secure Sockets Layer (Lớp socket bảo mật)**. Một loại bảo mật giúp mã hóa liên lạc giữa website và trình duyệt. Công nghệ này đang lỗi thời và được thay thế hoàn toàn bởi **TLS**.

**TLS** là chữ viết tắt của **Transport Layer Security**, nó cũng giúp bảo mật thông tin truyền giống như SSL. Nhưng vì SSL không còn được phát triển nữa, nên **TLS** mới là thuật ngữ đúng nên dùng.

**HTTPS** là phần mở rộng **bảo mật** của HTTP. Website được cài đặt chứng chỉ SSL/TLS có thể dùng giao thức HTTPS để thiết lập kênh kết nối an toàn tới server. Tóm lại, các điều cơ bản bạn cần nắm rõ về SSL là:

- Mục tiêu của SSL/TLS là bảo mật các thông tin nhạy cảm trong quá trình truyền trên internet như, thông tin cá nhân, thông tin thanh toán, thông tin đăng nhập.
- Nó là giải pháp thay thế cho phướng pháp truyền thông tin văn bản dạng plain text, văn bản loại này khi truyền trên internet sẽ không được mã hóa, nên việc áp dụng mã hóa vào sẽ khiến cho các bên thứ 3 không xâm nhập được bào thông tin của bạn, không đánh cắp hay chỉnh sửa được các thông tin đó.
- Hầu hết mọi người đều quen thuộc với các chứng chỉ SSL/TLS, đang được dùng bởi các website lớn và các webmaster nghiêm túc trong việc bảo vệ các giao dịch người dùng.

#### Cách hoạt động chứng chỉ SSL/TLS

Chứng chỉ SSL/TLS hoạt động bằng cách tích hợp key mã hóa vào thông tin định danh công ty. Nó sẽ giúp công ty mã hóa mọi thông tin được truyền mà không bị ảnh hưởng hoặc chỉnh sửa bởi các bên thứ 3.

SSL/TLS hoạt động bằng cách sử dụng public và private key, đồng thời các khóa duy nhất của mỗi phiên giao dịch. Mỗi khi khách truy cập điền vào thanh địa chỉ SSL thông tin web browser hoặc chuyển hướng tới trang web được bảo mật, trình duyệt và web server đã thiết lập kết nối.

Trong phiên kết nối ban đầu, public và private key được dùng để tạo session key, vốn được dùng để mã hóa và giải mã dữ liệu được truyền đưa. Session key sẽ được sử dụng trong một khoảng thời gian nhất định và chỉ có thể dùng cho phiên giao dịch này.

## RESTful API

**RESTful API** là một tiêu chuẩn dùng trong việc thiết kế API cho các ứng dụng web (thiết kế Web services) để tiện cho việc quản lý các resource. Nó chú trọng vào tài nguyên hệ thống (tệp văn bản, ảnh, âm thanh, video, hoặc dữ liệu động…), bao gồm các trạng thái tài nguyên được định dạng và được truyền tải qua HTTP.

Chức năng quan trọng nhất của **REST** là quy định cách sử dụng các HTTP method (như GET, POST, PUT, DELETE, ...) và cách định dạng các URL cho ứng dụng web để quản lý các resource. RESTful không quy định logic code ứng dụng và không giới hạn bởi ngôn ngữ lập trình ứng dụng, bất kỳ ngôn ngữ hoặc framework nào cũng có thể sử dụng để thiết kết một **RESTful API**

### Cách hoạt động của RESTful

<div align="center">
    <img src="/media/restful-api.png" alt="java-thinking">
</div>

REST hoạt động chủ yếu dựa vào giao thức HTTP. Các hoạt động cơ bản nêu trên sẽ sử dụng những phương thức HTTP riêng.

- **GET** (SELECT): trả về một Resource hoặc một danh sách Resource.
- **POST** (CREATE): Tạo mới một Resource
- **PUT** (UPDATE): Cập nhật thông tin cho Resource
- **DELETE** (DELETE): Xóa một Resource

Những phương thức hay hoạt động này thường được gọi là **CRUD** tương ứng với Create, Read, Update, Delete

[Nguyên tắc thiết kế cho RESTful API](https://medium.com/eway/nguy%C3%AAn-t%E1%BA%AFc-thi%E1%BA%BFt-k%E1%BA%BF-rest-api-23add16968d7)

## Reference

- [Connection pool](https://stackjava.com/faq/connection-pool-la-gi-khai-niem-connection-pool-trong-database.html)
- [Guava](https://www.baeldung.com/guava-cache)
- [HTTP (1)](https://www.javatpoint.com/computer-network-http)
- [HTTP (2)](https://viblo.asia/p/tim-hieu-ve-http-hypertext-transfer-protocol-bJzKmgewl9N)
- [gRPC](https://gopher.vn/2018/08/11/grpc-la-gi-co-nen-dung-khong/)
- [Protobuf](https://viblo.asia/p/protocol-buffers-la-gi-va-nhung-dieu-can-ban-can-biet-ve-no-maGK7D99Zj2)
- [SSL/TLS](https://www.hostinger.vn/huong-dan/https-tls-ssl-la-gi/)
