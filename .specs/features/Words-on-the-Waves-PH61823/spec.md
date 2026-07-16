# spec.md: Words-on-the-Waves-PH61823

**Description:** Game Life Simulation & Management 3D. Quản lý hiệu sách di động trên xe kéo dọc bờ biển. Không có cơ chế Game Over.

## Tech Stack Đặc Thù

- **Thể loại:** Mô phỏng đời sống & Quản lý (Life Simulation).
- **Core Loop:** Nhập kiện hàng, sắp xếp sách lên kệ xe kéo, lắng nghe tâm tư khách hàng (NPC) và tư vấn sách phù hợp.
- **Thư viện đặc thù:** Hệ thống Tweening (như DOTween) và Quản lý JSON.

## Điểm lưu ý kiến trúc

- **Kiến trúc Cốt lõi:** Data-Driven Architecture (100% data từ tệp JSON), quản lý luồng bằng FSM (Finite State Machine) và giao tiếp qua Observer Pattern.
- **Tối ưu Hiệu năng:** Bắt buộc Zero GC Alloc (không cấp phát bộ nhớ trong Update), Object Pooling toàn diện và khống chế Draw Calls (Batches) < 50.
- **Game Feel:** Tương tác Kéo & Thả (Drag & Drop) mượt mà với Raycast, Ghost Mesh, Snapping và hệ thống âm thanh phản hồi liên tục (Combo Pitch Shifting).
