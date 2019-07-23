# Threading

## Thread, multithreading & concurrency

### Thread

Mỗi process bao gồm một hoặc nhiều thread. Một thread là một đơn vị hoạt động bên trong process. Nói cách khác, một thread là sự trựu tượng chịu trách nhiệm thực thi code và duy trạng thái running của process.

Hầu hết các process chỉ bao gồm một thread, chúng được gọi là single-threaded. Process chứa nhiều thread, được gọi là multithreaded.

Một thread bao gồm một stack (lưu các biến cục bộ của nó), trạng thái processor, và vị trí hiện tại trong object code. Hầu hết các phần còn lại của process được chia sẻ giữa tất cả các thread, đáng chú ý nhất là không gian địa chỉ process. Bằng cách này, các thread chia sẻ các bộ nhớ ảo trừu tượng trong khi duy trì processor ảo trừu tượng.

Các trạng thái của thread:

- **New thread:** khi một thread mới được tạo, nó là new state. Khi một thread ở trạng thái này, thì thread vẫn chưa hoạt động.
- **Runnable:** một thread đã sẵn sàng để chạy, nó sẽ chuyển sang trạng thái runnable. Trong trạng thái này, một thread có thể thực sự đang chạy hoặc có thể sẵn sàng chạy bất cứ lúc nào.
- **Blocked/Waiting:** khi một thread tạm thời không hoạt động, sau đó nó có thể nằm trong một những trạng thái sau:
  - Blocked
  - Waiting

### Multithreading

Trong các hệ thống đa lõi và đã xử lý thì đa luồng tức là các thread được thực hiện cùng lúc trên lõi hoặc bộ vị xử lý khác nhau.

Trong các hệ thống lõi đơn thì đa luồng chia thời gian giữu các thread. System sẽ gửi 1 số lượng nhất định các hướng dẫn từ mỗi Thread để xử lý. Các Thread không được thực hiện đồng thời. System chỉ mô phỏng thực hiện đồng thời của chúng. Tsinh năng này của System được gọi là đa luồng.

<div align="center">
    <img src="/media/multithreading.png" alt="java-thinking">
</div>

### Concurrency

Concurrency (tính đồng thời) là khả năng xử lí nhiều tác vụ cùng 1 lúc.

Vậy làm thế nào để 1 máy tính có CPU 1 nhân có thể xử lí nhiều tác vụ cùng lúc, trong khi 1 nhân CPU chỉ có thể xử lí 1 việc tại 1 thời điểm? Concurrency tức là xử lí 1 tác vụ tại 1 thời điểm, nhưng CPU không xử lí hết 1 tác vụ rồi mới đến tác vụ khác, mà sẽ dành 1 lúc cho tác vụ này, 1 lúc cho tác vụ kia. Do vậy, chúng ta có cảm giác máy tính thực hiện nhiều tác vụ cùng 1 lúc, nhưng thực chất chỉ có 1 tác vụ được xử lí tại 1 thời điểm.

<div align="center">
    <img src="/media/concurrency.jpg" alt="java-thinking">
</div>

Từ biểu đồ trên, chúng ta có thể thấy rằng, CPU 1 nhân phân chia thời gian làm việc dựa trên độ ưu tiên của cùng tác vụ. Ví dụ, khi đang scroll trang, việc nghe nhạc sẽ có độ ưu tiên thấp hơn, nên có thể nhạc của bạn sẽ bị dừng do đường truyền kém, nhưng bạn vẫn có thể kéo trang lên xuống.

### Thread-safety

Trong môi trường multithreaded, chúng ta cần viết một bảng cài đặt thread-safe. Có nghĩa là nhiều thread khác nhau có thể truy cập vào một tài nguyên khác nhau không có một behavior nào sai hoặc kết quả không đoán trước được.

#### Một số cách để đạt được Thread-safety

##### Stateless Implementation

Trong hầu hết trường hợp, lỗi trong ứng dụng multithreaded là kết quả của sharing state không đúng giữa các class khác nhau.

Do đó, cách tiếp cận đầu tiên là chúng ta sẽ xem xét cách đạt được thread-safety là dùng stateless implementations.

```java
public class MathUtils {
    public static BigInteger factorial(int number) {
        BigInteger f = new BigInteger("1");
        for (int i = 2; i <= number; i++) {
            f = f.multiply(BigInteger.valueOf(i));
        }
        return f;
    }
}
```

Hàm `factorial` là một hàm không trạng thái. Cho vào một input cụ thể, sẽ luôn ra một output giống nhau.

##### Immutable Implememtations

**Nếu ta cần share state giữa các thread khác nhau, chúng ta có thể tạo ra thread-safe class bằng cách làm cho nó immutable**.

Cách đơn giản nhất để tạo một immutable class trong Java là khai báo tất cả các field `private` và final, không cung cấp setter:

```java
public class MessageService {
    private final String message;

    public MessageService(String message) {
        this.message = message;
    }
    // standard getter
}
```

`MessageService` object là một immutable hiểu quả, bởi vì state của nó không thể thay đổi sau khi được khởi tạo. Do đó, nó thread-safe.

##### Synchronized Methods

Mặc dù các cách tiếp cận trước đó rất cho các collections và primitives, đôi khi, ta cần kiểm soát nhiều hơn thế. Các tiếp cận phổ biến khác để đạt được thread-safe là implementing synchronized method.

Đơn giản là **chỉ một thread có thể access vào synchronized method tại một thời điểm**. Các thread còn lại sẽ bị blocked cho đến khi thread đầu tiên hoàn thành hoặc throw một exception

Ta có thể tạo một thread-safe bằng cách:

```java
public synchronized void incrementCounter() {
    counter += 1;
}
```

##### Synchronized Statements

Đôi khi, synchronized toàn bộ method có thể làm quá mức, nếu chúng ra chỉ cần làm cho một đoạn code trong method thread-safe.

```java
public void incrementCounter() {
    // additional unsynced operations
    synchronized(this) {
        counter += 1;
    }
}
```

### Thread pool

**ThreadPool** trong java rất hữu ích khi cần giới hạn số lượng Thread được chạy bên trong ứng dụng của bạn cùng 1 thời điểm. Sẽ có vấn đề về hiệu suất khi tạo mới 1 Thread (new Thread) và mỗi thread được tạo ra cũng được phân bổ bộ nhớ cho việc sử dụng.

**Ví dụ:** Bạn có 100 tập tin cần tải về từ trên mạng, mỗi tệp tin bạn cần 1 thread để download, như vậy sẽ có 100 thread hoạt động cùng 1 thời điểm trong ứng dụng của bạn, sẽ gây ra 1 vấn đề nghiêm trọng về bộ nhớ và hiệu suất và có thể dẫn đến gây lỗi (crash) chương trình.

Thay vì phải tạo mới thread cho mỗi task (nhiệm vụ) được thực hiện đồng thời, các nhiệm vụ cần đưa vào 1 thread pool (hồ bơi dành cho luồng). Và ngay sau khi trong hồ bơi có bất kì luồng nào đang nhàn rỗi (no task) các nhiệm vụ sẽ được gán vào 1 trong số chúng và được thực thi.

<div align="center">
    <img src="/media/thread-pool.png" alt="java-thinking">
</div>

Bên trong ThreadPool các nhiệm vụ được chèn vào một **Task Queue** (hàng đợi được khóa) nơi mà các Thread sẽ lấy chúng ra và thực thi lần lượt. Mỗi khi có 1 task mới được thêm vào Queue, sau đó chỉ có 1 thread nhàn rỗi sẽ vào queue và lấy chúng ra, các thread nhàn rỗi còn lại phải chờ sau khi thread trước đó lấy nhiệm vụ ra thành công.

Thread Pool thường xuyên được sử dụng trong các máy chủ thực hiện đa luồng. Mỗi kết nối đến với máy chủ thông qua mạng được bao bọc như là 1 nhiệm vụ (task) và đưa vào thread pool. Các thread trong thread pool sẽ xử lý các yêu cầu về kết nối đồng thời.

### Executors

Chúng có thể tạo một Executor bằng cách sử dụng một trong các phương thức được cung cấp bởi lớp tiện ích **Executors** như sau:

- **newSingleThreadExecutor()**: trong ThreadPool chỉ có 1 Thread và các task (nhiệm vụ) sẽ được xử lý một cách tuần tự.
- **newCachedThreadPool()**: trong ThreadPool sẽ có nhiều Thread và các nhiệm vụ sẽ được xử lý một cách song song. Các Thread cũ sau khi xử lý xong sẽ được sử dụng lại cho nhiệm vụ mới. Mặc định nếu một Thread không được sử dụng trong vòng 60 giây thì Thread đó sẽ bị tắt.
- **newFixedThreadPool(int n)**: trong ThreadPool sẽ được cố định các Thread. Nếu một nhiệm vụ mới được đưa vào mà các Thread đều đang “bận rộn” thì nhiệm vụ đó sẽ được gửi vào Blocking Queue và sau đó nếu có một Thread đã thực thi xong nhiệm vụ của nó thì nhiệm vụ đang ở trong Queue đó sẽ được push ra khỏi Queue và được Thread đó xử lý tiếp.
- **newScheduledThreadPool(int corePoolSize):** tương tự như **newCachedThreadPool()**nhưng sẽ có thời gian delay giữa các Thread.
- **newSingleThreadScheduledExecutor():** tương tự như **newSingleThreadExecutor()** nhưng sẽ có khoảng thời gian delay giữa các Thread.

## Reference

- [Concurrency](https://techmaster.vn/posts/35021/1542501886113)
- [Executors](https://gpcoder.com/3548-huong-dan-tao-va-su-dung-threadpool-trong-java/)
