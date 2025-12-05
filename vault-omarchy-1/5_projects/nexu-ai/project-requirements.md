# 1. Business Alignment
1.1. Objective
Sử dụng AI để tạo hình ảnh/video/content => aim: content creator.
1.2. Features
- [Important] Tạo menu cho F&B. 
- [Important] Tạo poster/ads cho quảng cáo.
- Tạo news cho content creators.
- Tạo voice ai dựa trên compose của nhạc sĩ (có custom tone giọng, type giọng)
- Tạo Video (shorts) cho content creator (chủ đề bất kỳ).
2.1. Model
- Gemini Banana Pro
2.2. Auth
- Google Oauth2
2.3. Techstack
- BE: Go
- Fe: React
- DB: Postgres
- Rate limit: Implement hệ thống số lần /feature/user
- Storage: Lưu local
- Infra: Nginx local + Docker cho backend
- VPS: Vn Cloud provider. Domain godaddy.
- Trong 30 ngày đầu, dùng 1 con dev, 4 cores - 8 GB ram.
- Sau 30 ngày, dựng thêm 1 con production: 8 cores, 16 GB ram 

3. Marketing
- Trước mắt sẽ lên bài trên các social media cơ bản: Youtube, Facebook, Tiktok.
- Chạy ads trên Facebook, Tiktok => Cần research giá để lên plan.

Feature chính cốt lõi cần release ra đầu tiên để hút user trend
-  

Features
Tạo poster quảng cáo
Tạo menu cho các nhà hàng, cửa hàng
Thiết kế sản phẩm (bao bì, minh hoạ trực tiếp sản phẩm, thiết kế tạo độ hấp dẫn cho sản phẩm , ẩm thực, …)

## Payment Gateway
Công việc là cần research nhiều bên payment 3rd để lựa chọn provider phù hợp để tích hợp thanh toán:
Cho User VietNam:
https://payos.vn/docs/api/
Cho User Global:
https://www.paypal.com/in/home


