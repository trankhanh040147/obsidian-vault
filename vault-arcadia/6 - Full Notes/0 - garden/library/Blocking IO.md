Khi client gửi 1 command lên server, về bản chất client đang gửi data lên 1 [socket](https://en.wikipedia.org/wiki/Network_socket). Nói thêm, socket là một endpoint để 2 máy tính có thể giao tiếp với nhau qua network, nó được định nghĩa bởi (địa chỉ IP, port number). Để đọc data từ socket, server cần listen (lắng nghe) socket [[file descriptor (FD)]] và gọi [[system call]] [read](https://man7.org/linux/man-pages/man2/read.2.html):

```
ssize_t read(int fd, void *buf, size_t count);
```

Bản chất của system call read là một blocking call, nghĩa là nếu tại thời điểm server gọi read, data chưa sẵn sàng ở socket, thì server sẽ chuyển vào trạng thái sleep (blocking), và đợi cho đến khi data sẵn sàng để đọc thì “tỉnh dậy” để đọc. Rõ ràng, đây là một sự lãng phí tài nguyên, bởi thay vì đi làm việc gì đó hữu ích, thì thread của server lại “đi ngủ”.

Một trong những cách phổ biến để hạn chế sự lãng phí này là mô hình **One thread per connection**: với mỗi client, server sẽ sử dụng 1 thread riêng biệt để xử lý I/O từ client đó. Nhờ đó, nếu 1 thread đang bị blocking thì các thread khác vẫn có thể tiếp tục hoạt động. Mô hình này được các database như MySQL, Postgres sử dụng (thực ra Postgres dùng process-per-connection model). Mô hình này có 2 **nhược điểm**:

1. Số lượng connection giới hạn: việc tạo mới thread cho mỗi connection dẫn tới server không thể chấp nhận quá nhiều client truy cập cùng lúc.
    
2. Lock contention: nhiều thread cùng muốn thay đổi 1 data trong database có thể dẫn đến hiện tượng race condition.

Để khắc phục race condition, chúng ta phải sử dụng locking: khi một thread đang lock và xử lý data thì các thread khác muốn access cùng một data sẽ phải đợi cho đến khi lock được release. V
    
```
    acquire_lock
    read(x)
    x = x + 1
    write(x)
    release_lock
```
    
Tất nhiên, không có bữa trưa nào là miễn phí, sử dụng locking dẫn đến hiện tượng lock contention: việc nhiều thread cùng phải đợi lock được release ảnh hưởng nghiêm trọng đến performance của hệ thống. Tôi đoán Antirez đã nghĩ đến tình huống này, và quyết định không sử dụng mô hình **one thread per connection** cho Redis**.** Thay vào đó, Redis sử dụng một cơ chế khác: **I/O Multiplexing**.