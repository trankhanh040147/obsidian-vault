Contents:
1. Mongo Basics
	- Định nghĩa NOSQL: _NoSQL là 1 dạng CSDL mã nguồn mở không sử dụng Transact-SQL để truy vấn thông tin. NoSQL viết tắt bởi: None-Relational SQL, hay có nơi thường gọi là Not-Only SQL. CSDL này được phát triển trên Javascript Framework với kiểu dữ liệu JSON. (Cú pháp của JSON là “key:value”) NoSQL ra đời như là 1 mảnh vá cho những khuyết điểm và thiếu xót cũng như hạn chế của mô hình dữ liệu quan hệ RDBMS về tốc độ, tính năng, khả năng mở rộng, memory cache,.._. 
	- MongoDB là một chương trình cơ sở dữ liệu mã nguồn mở được thiết kế theo kiểu hướng đối tượng trong đó các bảng được cấu trúc một cách linh hoạt cho phép các dữ liệu lưu trên bảng không cần phải tuân theo một dạng cấu trúc nhất định nào. Chính do cấu trúc linh hoạt này nên MongoDB có thể được dùng để lưu trữ các dữ liệu có cấu trúc phức tạp và đa dạng và không cố định (hay còn gọi là Big Data).
	- Database: Là nơi chứa các Collection, giống với Database trong cơ sở dữ liệu RDMS chúng chứa các bảng. Mỗi Database có một tập tin riêng lưu trữ trên bộ nhớ vật lý. Một máy chủ MongoDB có thể chứa nhiều Database.
	- Collection: Là một nhóm chứa nhiều document trong MongoDB. Collection có thể được hiểu là một bảng (table) tương ứng trong cơ sở dữ liệu RDBMS (Relational Database Management System). Collection nằm trong một cơ sở dữ liệu duy nhất. Các collection không phải định nghĩa các cột, các hàng hay kiểu dữ liệu trước.
	- **Data as documents**: MongoDB stores data in a binary representation called BSON (Binary JSON). The BSON encoding extends the popular JSON (Javascript Object Notation) representation to include additional types such as int, long , date, floating point,... MongoDB documents tend to have all data for a given record in a single document, whereas in a relational databse information for a given record is usually spread across many tables. `Data as documents: simpler for developers, faster for users.`
	- - **Document** – Một bản ghi thuộc một Collection thì được gọi là một Document. Các Document lần lượt bao gồm các trường tên và giá trị.
	- - **Field** – Là một cặp name – value trong một document. Một document có thể có không hoặc nhiều trường. Các trường giống các cột ở cơ sở dữ liệu quan hệ.
	- - **Index** – Là những cấu trúc dữ liệu đặc biệt, dùng để chứa một phần nhỏ của các tập dữ liệu một cách dễ dàng để quét. Chỉ số lưu trữ giá trị của một fields cụ thể hoặc thiết lập các fields, sắp xếp theo giá trị của các fields này. Index hỗ trợ độ phân tích một cách hiệu quả các truy vấn. Nếu không có chỉ mục, MongoDB sẽ phải quét tất cả các documents của collection để chọn ra những document phù hợp với câu truy vấn. Quá trình quét này là không hiệu quả và yêu cầu MongoDB để xử lý một khối lượng lớn dữ liệu.
	- **Indexing**: Nếu không lập chỉ mục, CSDL sẽ không truy vấn hiệu quả vì phải quét mọi tài liệu của tập hợp để chọn những tài liệu phù hợp cho việc truy vấn đó. Vì vậy, để tìm kiếm hiệu quả, Indexing là điều bắt buộc và MongoDB sử dụng nó để xử lý khối lượng dữ liệu khổng lồ trong thời gian rất ngắn.
1. Problem/Situation
	- Why 2d management (manual) is difficult ?
		- *insert img 
		- hard for non-tech
	- There are no tools that help comparing indexes in a collection/database --> every action is take effect directly
2. Solution
3. Introducing DRManager
4. Key Features
	- Easy-using UI
5. How it works 
6. Benefits
7. Real-life examples/scenarios
8. Demo
9. Q&A
10. Conclusion