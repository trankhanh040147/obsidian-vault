## SITUATION - TASKS
- Cần có một service để quản lý index tạm thời trước khi muốn apply trên db thực. Việc thêm index trực tiếp trên db thực sẽ xảy ra rủi ro nếu không được kiểm tra một cách cẩn thận. Ví dụ: 
- Cần có một nơi để quản lý tập chung index của database cho nhiều môi trường: Giả sử có các môi trường: Dev, Testing, Staging, Production. Giả sử ta muốn thêm 1 index trên db thực, làm cách nào để có thể apply xuống tất cả các db ?  
- Người dùng có thể quản lý index của database tương tự như thao tác trên db thực: Có thể thêm - sửa - xóa index. Đồng thời có thể xem sự khác biệt index trên service và database thực.  
## FEATURES
**Quản lý tài khoản**: Hỗ trợ người dùng các chức năng tạo tài khoản và đăng nhập. Có quản lý access token và refresh token và có cơ chế token rotation.
*Token Rotation: Refresh tokens are typically longer-lived and can be used to request new access tokens after the shorter-lived access tokens expire*
https://www.descope.com/_next/image?url=https%3A%2F%2Fimages.ctfassets.net%2Fxqb1f63q68s1%2F1leZ6rz3OviojAZYKJSaGE%2Fb75caece6b6c5ecfb06ebf5ea5f2376a%2FRefresh_Token_Rotation.png&w=750&q=75
**Quản lý kết nối database**: Người dùng có thể tạo kết nối đến bất kỳ database nào, hệ thống có hỗ trợ tính năng kiểm tra kết nối đến database để đảm bảo kết nối đến database là thành công. 
**Quản lý Index**
User có thể quản lý index trên service. Điểm khác biệt giữa quản lý index trên service và thao tác trên db thực là: Các thao tác thêm, sửa xóa index trên service sẽ không lập tức take effect trên db. Mọi index có sự thay đổi sẽ chỉ mang tính tạm thời. Chỉ khi người dùng đã chắc chắn về toàn bộ dữ liệu index đã tạo ra thì họ mới đến với bước tiếp theo.
**Kiểm tra sự khác biệt index**
Sau khi tạo xong các index cần thiết, người dùng sẽ muốn có một chức năng dùng để kiểm tra sự khác nhau giữa index mà mình đã tạo trên service so với index hiện đang có trên db thực. Kết quả của sự so sánh index này sẽ giúp cho người dùng biết:

  ? Trên Db thực hiện tại đã đã có sẵn các index nào mà admin đã tạo trên service. Các index này khi thực hiện quy trình đồng bộ index sẽ không bị tác động.

  ? Trên DB thực hiện tại đang còn thiếu những index nào do admin tạo ra trên service, nhưng lại chưa có trên db thực ? Các index này khi thực hiện quy trình đồng bộ sẽ được tạo mới

  ? Trên DB thực hiện tại đang có dư thừa những index nào mà trên service không tạo ra ? Các index này sẽ bị xóa khi thực hiện quy trình đồng bộ
**Đồng bộ Index**
+ Đồng bộ index: Sau quá trình kiểm tra sự khác biệt index, người dùng sẽ muốn toàn bộ index mình tạo ra phía service được mang hoàn toàn sang db thực. Quá trình này đòi hỏi…
## RESULTS
Thông qua service, người dùng có thể:
- Tạo tài khoản, đăng nhập
- Tạo, sửa, xóa, và xem danh sách các kết nối database
- Tạo, sửa, xóa và xem danh sách các indexes trên collection của database
- Kiểm tra sự khác biệt indexes giữa database/collection service - database/collection thực
- Đồng bộ indexes giữa database/collection service - database/collection thực 
