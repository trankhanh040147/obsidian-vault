## Auth
- Đầu tiên, để người dùng có thể sử dụng được các tính năng của hệ thống (Quản lý database và quản lý index), người dùng cần phải có một tài khoản hợp lệ và phải đăng nhập bằng tài khoản đó. --> #Signup
- Sau khi đăng ký tài khoản, sẽ cần phải gọi API đăng nhập để lấy AccessToken: dùng cho việc Authen (thực hiện request tới những Protected Resource - những resource yêu cầu quyền authen) và RefreshToken: dùng để thực hiện cơ chế Token Rotation. #Login
- Sau khi Access Token hết hạn, nếu người dùng tiếp tục sử dụng Token đó để đăng nhập sẽ bị báo lỗi Token không hợp lệ. Người dùng cần phải lấy Access Token mới bằng bằng cách Refresh bằng Refresh Token đã được cấp trước đó. #Login 
- Trường hợp sử dụng đăng nhập lại và sử dụng Access Token cũ (token này chưa hết hạn) để đăng nhập, lúc này Token cũ đã không còn hiệu lực

## Database

## Index

## Compare

## Syncing

### DEMO: 
### Script
 - Account:
	 - Register: nhập sai email, bỏ trống fields
	 - Login --> dùng AT --> Login lại --> AT hết hạn
	 - Login --> dùng AT --> Refresh --> AT hết hạn
- Database
- Index