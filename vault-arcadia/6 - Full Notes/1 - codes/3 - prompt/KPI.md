# 1 
## Yêu cầu tính năng
Ngôn ngữ: **Go**
Framework: **GoFiber**
Database: **MongoDB**
Nội dung: Phát triển hệ thống quản lý KPI

**Giai đoạn 1: BE** 
- Khởi tạo base project
- Login bằng JWT sử dụng ecdsa
- Các API  quản lý tài khoản cá nhân 
- Các API  quản lý team: có quản lý role các thành viên trong team
- Các API quản lý KPI: 
	- Phân bổ KPI theo team/cá nhân theo khoảng thời gian
	- Có chức năng đặt lịch cho KPI: có thông báo nhắc nhở
- Các API Dashboard: thống kê theo team, cá nhân theo khoảng thời gian
- Build Dockerfile 

**Giai đoạn 2: FE**
- Hoàn thành giao diện web để sử dụng các tính năng
- Web có trang xem KPI theo lịch
- Build Dockerfile 

*Yêu cầu:*
- Gửi thông báo bằng WebSocket
- Cần một dịch vụ cron job để xử lý lập lịch

`Hãy đánh giá yêu cầu chức năng này`

---