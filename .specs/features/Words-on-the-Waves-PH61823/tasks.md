# TASKS: Words on the Waves PH61823

**Last Updated:** 2026-07-04

## Giai đoạn 1: Nền tảng (Tuần 1)
- [ ] Vẽ Sơ đồ luồng hoạt động hệ thống (System Flow Diagram) và nhúng vào README.
- [ ] Khởi tạo cấu trúc thư mục Unity Project và thiết lập Git.
- [ ] Khởi tạo các Data Model bằng C# cho 7 thể loại sách.
- [ ] Tạo các file `.json` cấu hình cho Địa điểm, Kiện hàng, và Lời thoại.
- [ ] Lập trình `DataManager` để đọc và parse file JSON.
- [ ] Lập trình lớp Base FSM và các trạng thái con (MainMenu, Cargo, Prep, Map, Service, v.v.).
- [ ] Thiết lập UI Mockup và kiểm tra luồng chuyển đổi trạng thái FSM.

## Giai đoạn 2: Vòng lặp cốt lõi (Tuần 2)
- [ ] Xây dựng logic Hệ thống nhập hàng (Cargo System) - rơi kiện hàng ngẫu nhiên.
- [ ] Lập trình cơ chế Kéo & Thả (Drag & Drop) với `Physics.Raycast`.
- [ ] Thêm hiệu ứng xem trước (Ghost Mesh) và logic bắt dính (Snapping).
- [ ] Lập trình cơ chế sinh khách hàng (`CustomerSpawner`).
- [ ] Lập trình Thuật toán đối sánh (Matching Logic) để kiểm tra ID sách với ID lời thoại.

## Giai đoạn 3: Mở rộng Hệ thống (Tuần 3 & 4)
- [ ] Viết hệ thống Save/Load dữ liệu bằng JSON.
- [ ] Xây dựng UI Cửa hàng, hệ thống Trang trí (Decor) và các chỉ số Buff.
- [ ] Tạo Bản đồ địa điểm & Logic di chuyển.
- [ ] Tích hợp hiệu ứng Game Feel (Tweening) và Hệ thống âm thanh.

## Giai đoạn 4: Đánh bóng & Bàn giao (Tuần 5)
- [ ] Tối ưu hóa bộ nhớ (Object Pooling, Sprite Atlas, Batching).
- [ ] QA & Kiểm thử lỗi (Testing).
- [ ] Xuất bản Build cuối cùng & Viết tài liệu bàn giao.
