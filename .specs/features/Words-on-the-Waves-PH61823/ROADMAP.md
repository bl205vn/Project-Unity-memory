# ROADMAP: Words on the Waves PH61823

**Last Updated:** 2026-07-04
**Thời gian:** 17/06/2026 - 24/07/2026 (5 tuần)

## Các mốc thời gian (Milestones)

### 🟢 Tuần 1 (17/06 - 23/06): Thiết kế Hệ thống & Mô hình Dữ liệu JSON
- [ ] Vẽ Sơ đồ luồng hoạt động hệ thống (System Flow Diagram) đưa vào `README.md`.
- [ ] Khởi tạo dự án Unity (Tạo thư mục, thiết lập gitignore).
- [ ] Xây dựng Data Models (7 loại sách) & file JSON cấu hình (Địa điểm, Kiện hàng).
- [ ] Viết lớp `DataManager` (phân tích cú pháp JSON).
- [ ] Dựng khung kiến trúc FSM và thiết kế UI Mockup thô cơ bản.

### 🟡 Tuần 2 (24/06 - 30/06): Vòng lặp Gameplay Cốt lõi (Core Loop)
- [ ] Lập trình logic Hệ thống nhập hàng (Cargo System).
- [ ] Thiết lập Kho lưu trữ (Storage) và Kệ xe kéo (Wagon Shelves).
- [ ] Kéo & Thả kết hợp Ghost Mesh & Snapping.
- [ ] Xây dựng hệ thống `CustomerSpawner` & Bong bóng thoại.
- [ ] Hoàn thiện HUD Lựa chọn sách & Logic Thuật toán đối sánh (Matching Logic).

### 🟡 Tuần 3 (01/07 - 07/07): Kinh tế, JSON Save, Đồ trang trí & Kỷ vật
- [ ] Xây dựng Hệ thống Save/Load dữ liệu JSON.
- [ ] Làm UI Cửa hàng (Shop) & logic thay đổi Mesh 3D cho Đồ trang trí.
- [ ] Viết Hệ thống thuộc tính Buff (Modifier System).
- [ ] Lập trình Khách hàng Đặc biệt & rơi Kỷ vật (Mementos) thông qua Events.

### 🟡 Tuần 4 (08/07 - 14/07): Mở khóa Bản đồ, Game Feel & UI/UX
- [ ] UI Chọn bản đồ & Logic di chuyển (Travel Logic).
- [ ] Tích hợp Tweening (Scale, Squash, Shake).
- [ ] Thiết lập AudioManager & tính năng Combo Pitch Shift.
- [ ] Chốt giao diện HUD (Thời gian, tiền, menu tạm dừng).

### 🟡 Tuần 5 (15/07 - 24/07): Tối ưu hóa, Kiểm thử & Đóng gói sản phẩm
- [ ] Tối ưu Sprite Atlas & Batches (Đảm bảo < 50).
- [ ] Tích hợp Object Pooling & kiểm chứng Zero GC.
- [ ] Kiểm thử Hộp đen (NullReference, rò rỉ bộ nhớ, lỗi cận biên).
- [ ] Cập nhật file `README.md` với các hướng dẫn chi tiết.
- [ ] Xuất bản build APK/IPA và quay video gameplay.
