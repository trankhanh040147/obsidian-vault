
# Q
- [ ] Data Diode
	- [x] USG là gì ?
	- USG (Unidirectional Security Gateway) là một **giải pháp an ninh mạng hoàn chỉnh** được thiết kế để **thi hành luồng dữ liệu một chiều (unidirectional) tuyệt đối** giữa hai mạng.
	- Nói một cách đơn giản, nó là một hệ thống đầy đủ được xây dựng xung quanh một Data Diode.
	- Nếu **Data Diode** là "van một chiều" vật lý, thì **USG** là toàn bộ "hệ thống đường ống" (bao gồm máy chủ, phần mềm) để biến cái van đó trở nên hữu dụng.
	- [ ] UDP
	- [ ] Cách truyền dữ liệu 1 IP/1 port
	- [ ] Các ứng dụng đã sử dụng Data Diode
	- [ ] Cách kết nối Diode 
- [ ] Mã hóa 
	- [ ] Mật mã hậu lượng tử (Post‑Quantum Cryptography – PQC)
	- [ ] Mã hóa lượng tử
	- [ ] Session key
	- [ ] [[ML-KEM-1024]]
	- [ ] [[Key-encapsulation mechanism (KEM)]] 
	- [ ] Thuật toán để mã hóa file
		- [ ] AES --> Trade off ?
		- [ ] Thuật toán hiệu quả nhất
- [ ] Kafka 
	- [ ] Truyền dữ liệu file mã hóa qua Kafka (5MB/chunk)
- [ ] Code
	- [ ] Mã hóa lượng tử
	- [ ] Truyền dữ liệu qua UDP
---
# Concept
## Data Diode
- **Data Diode:** Là một **linh kiện phần cứng** (hardware). Nó chỉ là cái van vật lý. Bạn không thể "gửi file" qua một mình nó vì nó không hiểu các giao thức.
## Unidirectional Security Gateway (USG)
- **Unidirectional Security Gateway (USG):** Là một **giải pháp hoàn chỉnh** (system). Nó là sự kết hợp của (1) Data Diode + (2) Phần cứng máy chủ + (3) Phần mềm chuyên dụng (proxies) để xử lý các giao thức (như truyền file, sao chép database, gửi log...).

## UDP

### 1. Tại sao giao thức mạng lại là UDP?

Vì **UDP (User Datagram Protocol)** là giao thức "gửi và quên" (fire-and-forget). Nó không yêu cầu bất kỳ phản hồi nào từ bên nhận.

- **TCP (Giao thức 2 chiều):** Yêu cầu một "bắt tay 3 bước" (3-way handshake) để bắt đầu và yêu cầu bên nhận gửi lại gói tin xác nhận **(ACK)** cho mọi dữ liệu nhận được.
    
- **Data Diode (Phần cứng 1 chiều):** Về mặt vật lý, nó **chặn đứng** mọi tín hiệu quay trở lại, bao gồm cả các gói tin ACK.
    
- **Kết quả:** Nếu bạn cố gắng dùng TCP, kết nối sẽ thất bại ngay lập tức vì bên gửi sẽ gửi gói tin đầu tiên (SYN) và "đứng hình" chờ đợi gói tin phản hồi (SYN-ACK) không bao giờ đến.
    

Do đó, **UDP là lựa chọn duy nhất** ở tầng giao vận (transport layer) có thể hoạt động tương thích với một đường truyền một chiều tuyệt đối.

---

### 2. Có thể sử dụng TCP không?

**Không, bạn không thể sử dụng TCP _trực tiếp_ để truyền qua Data Diode.**

Nhưng đây chính là lý do **USG (Unidirectional Security Gateway)** tồn tại. USG là một hệ thống _giả lập_ và _bắc cầu_ cho TCP:

1. **Phía Bên Gửi (Mạng Tin cậy):** Ứng dụng của bạn (ví dụ: một máy chủ file) thực hiện một kết nối **TCP** hoàn toàn bình thường đến **máy chủ Proxy** của USG.
    
2. **Máy chủ Proxy (Gửi):** Nó "nhận" kết nối TCP đó, lấy dữ liệu ra, đóng gói lại thành các gói **UDP**.
    
3. **Data Diode (Ở giữa):** Các gói **UDP** này được "bắn" qua Data Diode một chiều.
    
4. **Máy chủ Proxy (Nhận):** Nó hứng các gói **UDP**, giải nén chúng ra thành dữ liệu ban đầu.
    
5. **Phía Bên Nhận (Mạng Kém tin cậy):** Máy chủ Proxy này lại mở một kết nối **TCP** mới đến máy chủ đích cuối cùng và gửi dữ liệu qua.
    

Nói cách khác, USG "chấm dứt" (terminate) kết nối TCP ở bên gửi và "tạo mới" (originate) một kết nối TCP ở bên nhận, với một đường hầm UDP một chiều ở giữa.

---

### 3. Làm thế nào bên nhận đảm bảo nhận đủ gói tin?

Vì UDP không có cơ chế đảm bảo, bản thân **phần mềm USG** phải tự làm điều đó ở tầng ứng dụng (application layer). Phương pháp phổ biến nhất là:

**Sử dụng Checksum hoặc Hash (Ví dụ: SHA-256)**

Đây là cách đáng tin cậy nhất cho việc gửi file:

1. **Bên Gửi:** Trước khi gửi, Proxy bên gửi tính toán một "dấu vân tay" kỹ thuật số (gọi là **hash**) cho toàn bộ file.
    
2. **Truyền Dữ Liệu:** Proxy gửi các gói UDP chứa dữ liệu file, sau đó gửi gói cuối cùng chứa giá trị **hash** đã tính toán.
    
3. **Bên Nhận:** Proxy bên nhận hứng tất cả các gói UDP và **tái tạo lại file** đầy đủ.
    
4. **Kiểm Tra (Verification):** Proxy bên nhận tự mình tính toán giá trị **hash** của file nó vừa tái tạo.
    
5. **So Sánh:**
    
    - Nếu hash nó tính ra **trùng khớp** với hash mà bên gửi gửi qua -> File chính xác 100%, không thiếu bit nào.
        
    - Nếu hash **không khớp** -> File đã bị lỗi hoặc thiếu gói tin trong quá trình truyền. Hệ thống sẽ báo lỗi và (thường là) xóa file lỗi đó.
        

**Lưu ý:** Bên nhận **không thể** yêu cầu bên gửi "gửi lại" gói tin bị thiếu (vì đường truyền là một chiều), vì vậy các hệ thống USG cao cấp còn sử dụng thêm kỹ thuật **FEC (Forward Error Correction)**. Kỹ thuật này gửi thêm một số gói tin "dự phòng" (parity packets) để bên nhận có thể tự khôi phục một vài gói tin bị mất mà không cần hỏi lại.

---
## Giao tiếp với Data Diode 

### 1. Có thể gửi HTTP trực tiếp đến Data Diode không?

**Không.** Bạn **không thể** gửi một request HTTP (hoặc bất kỳ giao thức nào dựa trên TCP) _trực tiếp_ qua một Data Diode.

Lý do rất đơn giản:

- **HTTP chạy trên TCP:** Giao thức HTTP/1.1 và HTTP/2 yêu cầu kết nối TCP.
    
- **TCP là giao thức 2 chiều:** Để bắt đầu một kết nối TCP, máy khách (Client) phải gửi một gói tin `SYN` và máy chủ (Server) _bắt buộc_ phải gửi lại một gói tin `SYN-ACK` để xác nhận. Đây gọi là "bắt tay 3 bước" (3-way handshake).
    
- **Data Diode là 1 chiều:** Data Diode được thiết kế ở cấp độ phần cứng để _chặn đứng_ mọi tín hiệu quay trở lại. Gói tin `SYN-ACK` sẽ không bao giờ quay lại được máy khách.
    

Do đó, kết nối TCP sẽ thất bại ngay lập tức trước khi bất kỳ dữ liệu HTTP nào (như `GET /index.html`) được gửi đi.

Giải pháp (Cách nó hoạt động trong thực tế):

Đây chính là lý do bạn cần một USG (Unidirectional Security Gateway). USG sử dụng các máy chủ Proxy ở hai đầu:

1. **Client (Mạng Nguồn):** Gửi một request HTTP (TCP) bình thường đến **Proxy Gửi** (đóng vai trò là máy chủ).
    
2. **Proxy Gửi:** "Chấm dứt" (terminate) kết nối TCP này. Nó "giả vờ" là máy chủ và hoàn tất việc bắt tay với Client.
    
3. Proxy Gửi lấy dữ liệu HTTP (header, body), đóng gói nó vào một giao thức một chiều (thường là UDP độc quyền) và "bắn" qua Data Diode.
    
4. **Proxy Nhận:** Hứng các gói UDP, ráp chúng lại thành dữ liệu HTTP gốc.
    
5. **Proxy Nhận (Mạng Đích):** Tạo một kết nối TCP _mới_ (đóng vai trò là máy khách) đến máy chủ Web thực sự ở mạng đích và gửi request HTTP đó đi.
    

---

### 2. Dữ liệu gửi đến Data Diode sẽ ở format nào?

Dữ liệu _thực sự_ đi qua phần cứng Data Diode sẽ ở dạng một luồng (stream) các gói tin **UDP**.

Nó **không** phải là format HTTP. Bản thân gói tin HTTP (ví dụ: `GET / HTTP/1.1 \r\n Host: ...`) bị coi là "dữ liệu thô" (payload) và bị băm nhỏ ra.

Đây là một **format độc quyền (proprietary)** do nhà sản xuất USG (như Owl Cyber Defense, Forcepoint, BAE Systems) tự định nghĩa để đảm bảo độ tin cậy.

#### Cấu trúc Header và Body (của gói tin UDP độc quyền này)

Khi Proxy Gửi đóng gói dữ liệu vào UDP để "bắn" qua diode, nó sẽ thêm một "header độc quyền" của riêng mình vào. Cấu trúc chung sẽ như sau:

**Gói tin UDP:**

- `UDP Header` (Chuẩn)
    
- `USG Header` (Độc quyền)
    
- `Data Chunk` (Phần thân - Mảnh dữ liệu)
    

**Chi tiết về Header và Body độc quyền của USG:**

- Header (USG Header):
    
    Đây là phần metadata mà phần mềm USG thêm vào để quản lý việc truyền tin, vì UDP không đảm bảo bất cứ điều gì. Header này thường chứa:
    
    - **Số thứ tự (Sequence Number):** Quan trọng nhất. Dùng để Proxy Nhận có thể sắp xếp lại các gói tin theo đúng thứ tự (vì UDP có thể làm gói tin đến lộn xộn) và phát hiện gói tin bị mất.
        
    - **Mã phiên (Session ID):** Để phân biệt nhiều luồng dữ liệu (ví dụ: 10 file được gửi cùng lúc) trên cùng một đường truyền.
        
    - **Loại gói tin (Packet Type):** Đánh dấu xem đây là gói "Bắt đầu file", "Dữ liệu", "Kết thúc file", hay "Gói chứa hash/checksum".
        
    - **Mã kiểm tra (Checksum/FEC):** Có thể chứa dữ liệu sửa lỗi (Forward Error Correction) để Proxy Nhận có thể tự khôi phục một vài gói tin bị mất mà không cần hỏi lại.
        
- Body (Data Chunk):
    
    Đây đơn giản là một mảnh (chunk) của dữ liệu gốc. Nếu bạn gửi một file 10MB, nó sẽ bị cắt thành hàng ngàn mẩu nhỏ (ví dụ, mỗi mẩu 1400 byte), và mỗi mẩu chính là "Body" của một gói tin UDP này.
    

---

### 🔗 Link nguồn tham khảo

Dưới đây là các tài liệu kỹ thuật từ các nhà cung cấp hàng đầu, xác nhận các cơ chế này:

1. **Owl Cyber Defense (Nhà cung cấp lớn):**
    
    - [Giải thích về Proxy của USG (Mục "How do data diodes address two-way protocols?")](https://www.google.com/search?q=&authuser=2)
        
        : Họ giải thích rõ ràng rằng các proxy ở hai đầu sẽ "chấm dứt" (terminate) và "khởi tạo" (initiate) các phiên TCP mới.
        
    - _(Link thay thế nếu link trên không truy cập được)_ [Owl FAQ (PDF)](https://www.google.com/search?q=&authuser=2)
        
        : "Owl data diode solutions use proxies on both the send and receive sides to satisfy the transport layer (i.e. TCP connection) requirements..." (Giải pháp của Owl dùng proxy ở cả hai phía để đáp ứng các yêu cầu của tầng transport (ví dụ: kết nối TCP)...)
        
2. **BAE Systems (Nhà cung cấp lớn khác):**
    
    - [Mô tả sản phẩm Data Diode](https://www.google.com/search?q=&authuser=2)
        
        : "The solution converts data into sequenced UDP packets that are then transferred across the Data Diode device." (Giải pháp chuyển đổi dữ liệu thành các gói UDP có thứ tự (sequenced) để truyền qua thiết bị Data Diode.)
        
3. **Fibersystem (Nhà cung cấp):**
    
    - [What is a Data Diode?](https://www.google.com/search?q=&authuser=2)
        
        : Giải thích tại sao UDP là hoàn hảo cho Data Diode vì nó là "stateless" (không cần phản hồi), trong khi các giao thức 2 chiều sẽ bị phá vỡ.
---
# Q&A

## Có thể gửi HTTP đến DD không ? 
### 1. Có thể gửi HTTP trực tiếp đến Data Diode không?

**Không.** Bạn **không thể** gửi một request HTTP (hoặc bất kỳ giao thức nào dựa trên TCP) _trực tiếp_ qua một Data Diode.

Lý do rất đơn giản:

- **HTTP chạy trên TCP:** Giao thức HTTP/1.1 và HTTP/2 yêu cầu kết nối TCP.
    
- **TCP là giao thức 2 chiều:** Để bắt đầu một kết nối TCP, máy khách (Client) phải gửi một gói tin `SYN` và máy chủ (Server) _bắt buộc_ phải gửi lại một gói tin `SYN-ACK` để xác nhận. Đây gọi là "bắt tay 3 bước" (3-way handshake).
    
- **Data Diode là 1 chiều:** Data Diode được thiết kế ở cấp độ phần cứng để _chặn đứng_ mọi tín hiệu quay trở lại. Gói tin `SYN-ACK` sẽ không bao giờ quay lại được máy khách.
    

Do đó, kết nối TCP sẽ thất bại ngay lập tức trước khi bất kỳ dữ liệu HTTP nào (như `GET /index.html`) được gửi đi.

Giải pháp (Cách nó hoạt động trong thực tế):

Đây chính là lý do bạn cần một USG (Unidirectional Security Gateway). USG sử dụng các máy chủ Proxy ở hai đầu:

1. **Client (Mạng Nguồn):** Gửi một request HTTP (TCP) bình thường đến **Proxy Gửi** (đóng vai trò là máy chủ).
    
2. **Proxy Gửi:** "Chấm dứt" (terminate) kết nối TCP này. Nó "giả vờ" là máy chủ và hoàn tất việc bắt tay với Client.
    
3. Proxy Gửi lấy dữ liệu HTTP (header, body), đóng gói nó vào một giao thức một chiều (thường là UDP độc quyền) và "bắn" qua Data Diode.
    
4. **Proxy Nhận:** Hứng các gói UDP, ráp chúng lại thành dữ liệu HTTP gốc.
    
5. **Proxy Nhận (Mạng Đích):** Tạo một kết nối TCP _mới_ (đóng vai trò là máy khách) đến máy chủ Web thực sự ở mạng đích và gửi request HTTP đó đi.
    

---

### 2. Dữ liệu gửi đến Data Diode sẽ ở format nào?

Dữ liệu _thực sự_ đi qua phần cứng Data Diode sẽ ở dạng một luồng (stream) các gói tin **UDP**.

Nó **không** phải là format HTTP. Bản thân gói tin HTTP (ví dụ: `GET / HTTP/1.1 \r\n Host: ...`) bị coi là "dữ liệu thô" (payload) và bị băm nhỏ ra.

Đây là một **format độc quyền (proprietary)** do nhà sản xuất USG (như Owl Cyber Defense, Forcepoint, BAE Systems) tự định nghĩa để đảm bảo độ tin cậy.

#### Cấu trúc Header và Body (của gói tin UDP độc quyền này)

Khi Proxy Gửi đóng gói dữ liệu vào UDP để "bắn" qua diode, nó sẽ thêm một "header độc quyền" của riêng mình vào. Cấu trúc chung sẽ như sau:

**Gói tin UDP:**

- `UDP Header` (Chuẩn)
    
- `USG Header` (Độc quyền)
    
- `Data Chunk` (Phần thân - Mảnh dữ liệu)
    

**Chi tiết về Header và Body độc quyền của USG:**

- Header (USG Header):
    
    Đây là phần metadata mà phần mềm USG thêm vào để quản lý việc truyền tin, vì UDP không đảm bảo bất cứ điều gì. Header này thường chứa:
    
    - **Số thứ tự (Sequence Number):** Quan trọng nhất. Dùng để Proxy Nhận có thể sắp xếp lại các gói tin theo đúng thứ tự (vì UDP có thể làm gói tin đến lộn xộn) và phát hiện gói tin bị mất.
        
    - **Mã phiên (Session ID):** Để phân biệt nhiều luồng dữ liệu (ví dụ: 10 file được gửi cùng lúc) trên cùng một đường truyền.
        
    - **Loại gói tin (Packet Type):** Đánh dấu xem đây là gói "Bắt đầu file", "Dữ liệu", "Kết thúc file", hay "Gói chứa hash/checksum".
        
    - **Mã kiểm tra (Checksum/FEC):** Có thể chứa dữ liệu sửa lỗi (Forward Error Correction) để Proxy Nhận có thể tự khôi phục một vài gói tin bị mất mà không cần hỏi lại.
        
- Body (Data Chunk):
    
    Đây đơn giản là một mảnh (chunk) của dữ liệu gốc. Nếu bạn gửi một file 10MB, nó sẽ bị cắt thành hàng ngàn mẩu nhỏ (ví dụ, mỗi mẩu 1400 byte), và mỗi mẩu chính là "Body" của một gói tin UDP này.
    

---

#### 🔗 Link nguồn tham khảo

Dưới đây là các tài liệu kỹ thuật từ các nhà cung cấp hàng đầu, xác nhận các cơ chế này:

1. **Owl Cyber Defense (Nhà cung cấp lớn):**
    
    - [Giải thích về Proxy của USG (Mục "How do data diodes address two-way protocols?")](https://www.google.com/search?q=&authuser=2)
        
        : Họ giải thích rõ ràng rằng các proxy ở hai đầu sẽ "chấm dứt" (terminate) và "khởi tạo" (initiate) các phiên TCP mới.
        
    - _(Link thay thế nếu link trên không truy cập được)_ [Owl FAQ (PDF)](https://www.google.com/search?q=&authuser=2)
        
        : "Owl data diode solutions use proxies on both the send and receive sides to satisfy the transport layer (i.e. TCP connection) requirements..." (Giải pháp của Owl dùng proxy ở cả hai phía để đáp ứng các yêu cầu của tầng transport (ví dụ: kết nối TCP)...)
        
2. **BAE Systems (Nhà cung cấp lớn khác):**
    
    - [Mô tả sản phẩm Data Diode](https://www.google.com/search?q=&authuser=2)
        
        : "The solution converts data into sequenced UDP packets that are then transferred across the Data Diode device." (Giải pháp chuyển đổi dữ liệu thành các gói UDP có thứ tự (sequenced) để truyền qua thiết bị Data Diode.)
        
3. **Fibersystem (Nhà cung cấp):**
    
    - [What is a Data Diode?](https://www.google.com/search?q=&authuser=2)
        
        : Giải thích tại sao UDP là hoàn hảo cho Data Diode vì nó là "stateless" (không cần phản hồi), trong khi các giao thức 2 chiều sẽ bị phá vỡ.

## Cách request đến DD thông qua Proxy gửi - nhận

### 1. Cách API Service Gửi Dữ Liệu (Gửi đến đâu?)

Dịch vụ API của bạn (sender-service) **không gửi dữ liệu trực tiếp đến phần cứng Data Diode**. Thay vào đó, nó gửi dữ liệu đến **Proxy Phía Gửi (Source Proxy)** của hệ thống USG.

Hãy coi **Proxy Gửi** này như một "hộp thư" bảo mật. Công việc của bạn là bỏ thư (dữ liệu) vào hộp thư đó đúng cách.

Cách phổ biến nhất mà các nhà cung cấp USG thực hiện việc này là:

- **Proxy Gửi hoạt động như một máy chủ HTTP:** Nó sẽ mở một cổng (ví dụ: 8080) trên mạng nguồn.
    
- **Sender-service** của bạn chỉ cần thực hiện một **request HTTP POST (hoặc PUT) hoàn toàn bình thường** đến địa chỉ IP và cổng của Proxy Gửi đó.
    

Ví dụ: Thay vì gửi request đến `http://dich-vu-nhan.com/api/data`, bạn sẽ cấu hình sender-service của mình gửi request đến `http://<IP-của-Proxy-Gửi>:8080/api/data`.

Proxy Gửi sẽ "chấm dứt" (terminate) kết nối HTTP/TCP đó, lấy dữ liệu ra và tự xử lý phần còn lại (đóng gói vào UDP, bắn qua diode).

---

### 2. Cấu Trúc Request (Header & Body)

Đây là tin tốt: Vì bạn đang nói chuyện với một Proxy "giả lập" máy chủ HTTP, bạn **sử dụng cấu trúc request HTTP hoàn toàn tiêu chuẩn**.

Hệ thống USG được thiết kế để **trong suốt (transparent)** nhất có thể. Bạn không cần phải định dạng một gói tin UDP đặc biệt nào cả.

#### Headers (Tiêu đề)

Bạn chỉ cần gửi các header HTTP chuẩn:

- `Content-Type`: **Bắt buộc phải có** và **phải đúng**. Đây là cách bạn báo cho Proxy biết nó đang nhận loại dữ liệu gì.
    
    - `Content-Type: application/json`
        
    - `Content-Type: application/octet-stream` (cho file nhị phân)
        
    - `Content-Type: text/plain`
        
    - `Content-Type: multipart/form-data`
        
- `Host`: Trỏ đến địa chỉ của Proxy Gửi.
    
- Các header tùy chỉnh (Custom Headers): Bất kỳ header nào bạn cần (ví dụ: `X-Request-ID`, `Authorization`) đều thường được hỗ...Proxy Gửi sẽ lấy luôn các header này, coi chúng là "metadata" và gửi kèm theo "body" đến Proxy Nhận.
    

#### Body (Nội dung)

Bạn có thể gửi **bất kỳ định dạng body nào** miễn là header `Content-Type` của bạn khớp:

- **JSON:** Rất phổ biến.
    
- **Form Data (multipart/form-data):** Hoàn toàn có thể, dùng để gửi file và các trường dữ liệu kèm theo.
    
- **Binary (nhị phân):** Hoàn toàn có thể (ví dụ: `application/octet-stream`). Proxy Gửi sẽ lấy toàn bộ luồng byte (raw byte stream) của body và chuyển đi.
    
- **XML, Text, v.v...:** Đều được.
    

**Tóm lại:** Proxy Gửi không _quan tâm_ nội dung body của bạn là gì, miễn là nó là một luồng byte hợp lệ. Nó sẽ "bê" nguyên xi luồng byte đó và gửi qua diode. Proxy Nhận sẽ tái tạo lại chính xác luồng byte đó và gửi đến máy chủ đích cuối cùng.

---

### Quy trình tóm tắt

Đây là luồng đầy đủ của một request API qua USG:

1. **Sender-service** của bạn tạo một request:
    
    - `POST /api/v1/upload`
        
    - `Content-Type: application/json`
        
    - `Body: {"sensor_id": 123, "value": 98.6}`
        
2. Nó gửi request này đến `http://<IP-Proxy-Gửi>/api/v1/upload`.
    
3. **Proxy Gửi** nhận request, "bắt tay" TCP và ngay lập tức gửi lại một phản hồi "giả" (spoofed response) cho sender-service, thường là **`202 Accepted`**.
    
    - **Lưu ý quan trọng:** Đây _không_ phải là phản hồi từ máy chủ đích. Đây chỉ là xác nhận "tôi đã nhận được và sẽ gửi đi". Sender-service sẽ _không bao giờ_ nhận được phản hồi thực sự từ máy chủ đích (vì đường truyền là một chiều).
        
4. **Proxy Gửi** lấy toàn bộ nội dung (header + body) của request, đóng gói vào các gói UDP có đánh số thứ tự.
    
5. Nó "bắn" các gói UDP này qua **Data Diode**.
    
6. **Proxy Nhận** hứng các gói UDP, kiểm tra số thứ tự, ráp chúng lại.
    
7. **Proxy Nhận** tạo một request HTTP _mới_ y hệt bản gốc (`POST /api/v1/upload`, body JSON...) và gửi nó đến máy chủ đích cuối cùng (receiver-service) trên mạng đích.


## Cách gửi dữ liệu fille dạng mã hóa và trunking đến Data Diode 
