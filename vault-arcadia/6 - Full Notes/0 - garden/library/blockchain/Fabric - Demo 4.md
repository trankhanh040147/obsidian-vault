# Q&A (Demo 4)
### CƠ CHẾ KIỂM TRA KẾT QUẢ WRITE KHÁC NHAU
- [ ] Nếu endorse ra 2 kết quả khác nhau, gateway sẽ verify write set bị sai, nếu bỏ qua bước này thì peer có cơ chế tự verify lại kết quả ko ?
### CA
- [ ] Ai sẽ là người đứng ra tạo cert ? (tổ chức nào)
- [ ] Nếu một tổ chức muốn tạo ra cert mới cho peer/user của tổ chức đó thì cert phải thông qua 
- [ ] Làm sao peer biết được cert của một client là hợp lệ hay không ?
- [ ] Làm sao peer biết được user đó là từ tổ chức nào ? Có quyền gì ? 
- [ ] Làm sao để thu hồi cert của một user ?

- [ ] revoke cert: 
	- [ ] revoke cert của một peer
	- [ ] revoke cert của một user
- [ ] renew cert: 
	- [ ] renew cert của peer và thử chaincode
	- [ ] renew cert của user và thử chaincode
- [ ] register new user:
	- [ ] cấp mới cert cho một user của tổ chức 1 với quyền read 
	- [ ] cấp mới cert cho một user của tổ chức 2 với quyền read, write
### Blockfile
- [ ] Dữ liệu Blockchain lớn quá thì sao ? (Cách fabric xử lý chia file hay nén file khi dữ liệu quá lớn ?)
- [ ] Cách BTC, Etherium xử lý vấn đề blockfile quá lớn ?
- [ ] Trường hợp blockfile quá lớn, khi peer một tổ chức khác tham gia vào mạng thì cần phải chờ quá trình copy file ? Quá trình này có lâu không ? Fabric xử lý như nào ?
	- [ ] Giả sử chain size hiện tại là 100GB, khi một tổ chức mới join vào mạng, thì nó cần phải nhận file này qua đường internet hay thế nào ?
	- [ ] Cơ chế Peer của tổ chức mới join vào mạng nhận blockfile ? Peer nào sẽ gửi file ? Gửi qua Anchor Peer của tổ chức mới rồi gossip hay sao ? 

**Các câu hỏi khác**
### Leading Peer
- [x] Tìm hiểu cách Ordering Service liên hệ với các peers trong một tổ chức thông qua Leading Peer
	The leader election mechanism is used to **elect** one peer per organization which will maintain connection with the ordering service and initiate distribution of newly arrived blocks across the peers of its own organization. Leveraging leader election provides the system with the ability to efficiently utilize the bandwidth of the ordering service. There are two possible modes of operation for a leader election module
	
- [ ] Thử nghiệm config 1 Leading Peer và nhiều Leading Peer cho một tổ chức
	- [ ] Tìm hiểu cách xác định leading peer của một tổ chức 1 khi đã deploy (dynamic)
	- [ ] Thử nghiệm manual chọn 1 leading peer
	- [ ] Kiểm tra cơ chế chọn lại leading peer sau khi leader cũ bị mất kết nối
---
### MANUAL 
- [ ] Thử nghiệm endorse trên từng peer bằng cách sử dụng fabric-sdk-go

- [ ] set gossip bootstrap sai thì bị gì ? (trường hợp tự trỏ vào chính nó)
- [ ] Làm sao để kết đến mạng bằng CCP ?