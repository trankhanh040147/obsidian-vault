Fabric CA demo
Kịch bản demo: 
- [x] Triển khai mạng Fabric với
	- Org1: 3 peers
	- Org2: 3 peers
	- Orderder: 1 node
	- CA.org.1
	- CA.org.2
	- CA.orderder
- Quá trình tạo register identity và enroll (generate private key và public key certificate)
	- Đầu tiên, enroll CA admin được tạo lúc bootstrap server (admin-adminpw) để lấy chứng chỉ của admin
		- Enroll CA admin để làm gì ?
- [ ] Giải thích về MSP và CA: 
	- CA: Certificate Authorities issue identities by generating a public and private key which forms a key-pair that can be used to prove identity. Because a private key can never be shared publicly, a mechanism is required to enable that proof which is where the MSP comes in. For example, a peer uses its private key to digitally sign, or endorse, a transaction.
	- MSP: The MSP contains the peer's public key which is then used to verify that the signature attached to the transaction is valid. The private key is used to produce a signature on a transaction that only the corresponding public key, that is part of an MSP, can match. Thus, the MSP is the mechanism that allows that identity to be trusted and recognized by the rest of the network without ever revealing the member’s private key.
- [ ] Giải thích local MSP của một peer sẽ gồm những gì ? (peer certificate, CA certificate, private key,...) Và các cert/key trong đó dùng để làm gì ?
- [ ] Giải thích cách channel lưu thông tin root cert của các CA mà nó tin tưởng (lưu thông tin MSP) ? 
	- [ ] Base64 cert của các CA và show kết quả base64 khớp với trong block config
- [x] Cấp chứng chỉ mới cho user mới (kenny) trong tổ chức 1
	- [ ] Giải thích 
- Thu hồi chứng chỉ của kenny
- Thu hồi chứng chỉ của một peer trong tổ chức 1 và thử gọi gateway đến peer đó
---
## FAQ 
- [ ] Làm sao để xác định ai có quyền chạy register và enroll? 
	- [ ] Có quyền 
	- [ ] register trên peer host được không ?
	- [ ] enroll có chạy trên peer host ? 
- [ ] DB của CA Server 
	- [ ] Các bảng dữ liệu trong đây làm gì ?
	- [ ] Tại sao DB của CA là readonly ? Có đổi thành write được không
	- [ ] Tại sao cần lưu các identity (trong db của CA server )

- [ ] Identity trong mạng?
	Là các client, peer, admin, orderer (được định nghĩa trong NodeOUs)
- [ ] Kiến trúc PKI là gì ?
	- [ ] X.509 gồm những thông tin gì ?
- [ ] Nếu một peer dùng certificate không hợp lệ
- [x] Có thể dùng chung 1 CA duy nhất cho cả 2 tổ chức và orderer không ? 
      Được, chỉ cần config tất cả chung một root_certs
- [x] 1 tổ chức có thể có 2 CA được không? 
	- Chỉ có một root
	- Có thể có thêm các intermediate CA để cấp cert
- [x] Tại sao mọi identity đều cần phải giữ rootCA ? Dùng rootCA đó để làm gì ?
	- Mọi định danh (identity) cần giữ chứng chỉ gốc của CA (rootCA) để **xác minh danh tính của những người khác**. Nó hoạt động như một "gốc rễ của sự tin cậy" (root of trust).
---
## FAQ 2
- [ ] Quá trình một actor verify [[digital certificate]] của một actor khác?
	- [ ] Cơ chế xác thực certificate của peer0 bằng rootCA (ca-org1) ? Dựa vào Fingerprints ?
- [ ] Cách tạo thêm intermediate CA để cấp cert