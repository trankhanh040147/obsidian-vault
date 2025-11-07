Long-tail latency là hiện tượng user thi thoảng gặp phải độ trễ cực lớn (tiếng Nôm là lag lòi mắt) khi sử dụng các ứng dụng hay website. Long-tail latency thường được đo bằng [[percentiles]] - bách vị phân. Percentiles là phương pháp cho biết có bao nhiêu phần trăm dữ liệu nhỏ hơn một mốc giá trị cụ thể. Ví dụ, bạn đo latency của 10 request gần nhất và sắp xếp kết quả tăng dần:

```
1, 2, 5, 5, 18, 25, 33, 46, 122, 1000 (ms)
```

trong ví dụ này:

- [[50th percentiles]] hay còn gọi là **p50** là **18ms**, nghĩa là 50% user sẽ trải nghiệm độ trễ thấp hơn 18ms.
    
- **p90** là **122ms**, nghĩa là 90% user sẽ trải nghiệm độ trễ thấp hơn 122ms, 10% còn lại sẽ thấy độ trễ cao hơn 122ms.
    

Tail latency liên quan trực tiếp đến trải nghiệm người dùng. Amazon ước tính rằng mỗi 100ms latency khiến họ mất 1% doanh thu (doanh thu 2024 của Amazon là hơn $600B). Năm 2006, Google tính toán được rằng mỗi 0.5 giây latency tăng thêm ở trang tìm kiếm làm giảm 20% lưu lượng truy cập.

[

](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe29dd630-a28a-4fa7-9a53-539e173560a2_1594x1034.png)