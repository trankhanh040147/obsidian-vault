`1. Quản lý mã QR của tôi**`
- Hiển thị mã QR hiện tại của một user
	- Tắt: không có mã QR
	- Bật: hiển thị mã QR hiện tại của user
- Chia sẻ QR của user
- Bật trạng thái QR
	- tạo mã QR code 
	- *notes: tự động bật khi cập nhật phiên bản mới hoặc đăng nhập lần đầu* 
- Tắt trạng thái QR
	- xóa tất cả  mã QR
- Tải mã QR
- Thay đổi mã QR:
	- Xóa tất cả mã QR cũ
	- Sinh mã QR mới và trả về mã đó

2. **Kết bạn thông qua mã QR**
- Quét mã QR:
	- Mã hết hiệu lực: báo lỗi mã không hợp  lệ
		- *when: mã bị thay đổi, hoặc bị tắt chế độ mã QR*
	- Mã còn hiệu lực: Trả về thông tin user sở hữu mã QR
- User đang ở UI camera của ứng dụng khác hoặc của thiết bị`

### Model

| Column    | Type     |
| --------- | -------- |
| _id       | ObjectID |
| CreatedAt | Time     |
| UpdatedAt | Time     |
| Owner     | ObjectID |
| IsActive  | bool     |
| qr_key    | string   |



abcd1234: false


## API

| endpoints              | body              | description   | response                                                        | function                                                                                                                                                                                                                        |     |
| ---------------------- | ----------------- | ------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| **/qr-code/get**       |                   |               | qr_code: <string>                                               | User gọi api này để lấy mã QR của mình<br>- trường hợp không có record --> sinh mã mới với trạng thái Active                                                                                                                    |     |
| **/qr/reset**          |                   |               | qr_code: <string>                                               | User gọi api này để sinh mã QR (trường hợp sinh mã mới hoặc sinh lại mã QR).                                                                                                                                                    |     |
| **/qr/update-setting** | is_enable         | true \| false | success:true                                                    | User gọi api này để bật tắt tính năng QR.<br><br>- Bật: Chuyển status của QR = active <br>*(kiểm tra nếu không có mã nào --> tự động thêm một mã mới)*<br>- Tắt: Chuyển status của QR mới nhất thành disable                    |     |
| **/qr/scan**           | qr_code: <string> |               | owner: {<br>name,<br>username,<br>alias,<br>avatar,<br>...<br>} | User gọi api này để quét một QR Code:<br>- QR hợp lệ: trả về thông tin của người sở hữu QR<br>- QR không hợp lệ: báo lỗi (trường hợp sinh mã không tồn tại, hoặc mã có tồn tại nhưng status = Disable)<br>  **Kiểm tra policy** |     |
|                        |                   |               |                                                                 |                                                                                                                                                                                                                                 |     |

- Delete user
- Bắn tin khi update setting