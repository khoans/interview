1. mới PV 1 cty remote Mỹ làm giờ 10-4h sáng + 2 giờ flexible, lương hơn VN tí chả bõ lắm
   mình bị hỏi issue liên quan đến tối ưu hệ thống bên mình, mình trả lời chủ yếu đo network, vì network có send buffer và receive buffer nên dù gửi 100MB nhưng buffer có 10MB thì cũng chịu à, lúc này phải config lại socket buffer
   race condition:
   bài toán dùng redis làm counter đếm số request tránh 2 threads cùng đếm +1
   dưới db thì tạo 1 cột is null để check có thread nào đang call k, nếu thread gọi row đó thì set A
   -check data bằng version trong bài toán eventually consistent khi sync 2 DB
   ...
   team mình còn gặp là khi transaction đang chạy, đã lock row đó nhưng đột nhiên server sập => thế là row đó bị kẹt vĩnh viễn, nên cần timeout

2.
