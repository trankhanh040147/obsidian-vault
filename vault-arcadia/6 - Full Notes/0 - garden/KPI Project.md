
# Requirements
--------
## Hệ thống quản lý KPI:
Yêu cầu chức năng:
1. Quản lý user:
	- Đăng ký
	- Đăng nhập
	- Thay đổi mật khẩu
	- Thông tin người dùng
	- Cập nhật thông tin
2. Quản lý team: có quản lý role các thành viên trong team
	- Tạo team
	- Cập nhật team
	- Thêm thành viên (trưởng nhóm)
	- Xóa thành viên (trưởng nhóm)
	- Rời nhóm
	- Xóa nhóm
3. Quản lý kpi: phân chia kpi cho team, cá nhân
	- Tạo KPI (theo cá nhân/theo team)
	- Cập nhật thông tin KPI
	- Thêm kết quả vào KPI
4. Dashboard: thống kê theo team, cá nhân

- Tôi muốn thêm tính năng đặt lịch:  bắt đầu, kết thúc, nhắc hẹn 😁

---
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

## Thời gian thực hiện:
1 tháng: BE (10/11 -> 12/12)
- Khởi tạo project base: 3 ngày
- Chức năng Login/Tạo tài khoản: 3 ngày
- Bộ API CRUD để quản lý các file script python/golang: 4 ngày
- Run script stream: 4 ngày
- stop script: 2 ngày
- Share file: 2 ngày
- Build docker file
(9/5->?)

3 Tuần: FE