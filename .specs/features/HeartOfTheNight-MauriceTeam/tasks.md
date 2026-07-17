# Kế hoạch công việc: HeartOfTheNight-MauriceTeam

> Trạng thái dự án: Đang phát triển tích cực.

## Current Sprint Tasks

- [x] **Task 1:** Tối ưu hóa hệ thống `PlayerMovement.cs` (Sửa lỗi logic Dash phase 2, chốt cơ chế Double Jump và Wall Climb).
- [x] **Task 2:** Tách logic Player Attack khỏi Movement để thành lập State Machine riêng biệt.
- [x] **Task 3:** Cập nhật Inspector cho `PlayerData` (ScriptableObject) dễ chỉnh thông số hơn, tooltips chính xác với toán học nội tại.
- [x] **Task 4:** Hoàn thiện cơ chế bắn đạn: Tích hợp BulletPool (Zero-GC), Linecast quét tốc độ cao (Anti-Tunneling).
- [x] **Task 5:** Cài đặt hệ thống gây sát thương từ Đạn sang Enemy thông qua interface `NhanSatThuong` và ScriptableObject `GunWeaponData`.
- [x] **Task 6:** Tách riêng hình ảnh/animation phần thân trên (cầm súng) và thân dưới (chân di chuyển). Thiết lập `DOTween` làm hiệu ứng giật súng (recoil).

- [ ] **Task 7:** Refactor Wall Check Logic (`PlayerMovement.cs`). Đổi `Front/Back` thành `Right/Left` cố định, xóa code lật Transform trong hàm `Turn()` để tách bạch vật lý và hình ảnh.
- [x] **Task 8:** Hoàn thiện kiến trúc "2 Cục Visual" (`UpperBody` & `LowerBody`). Reset `localPosition = (0,0,0)`, đồng bộ 100% Custom Pivot về phần bụng trong Sprite Editor.
- [x] **Task 9:** Cấu hình Animator State Machine. Xử lý logic bật/tắt `UpperBody` khi lướt/leo tường, và lập trình biến `RunSpeed` để xử lý Moonwalk (chạy lùi) khi hướng đi ngược hướng súng.
- [ ] **Task 10:** Xây dựng hệ thống Vũ khí Data-Driven (10 biến thể súng chuột trái/phải). Áp dụng kiến trúc: 3 logic đạn cốt lõi (Thường, Laze, AOE) + tinh chỉnh qua ScriptableObject.

## Backlog

- [x] **Task (Backlog):** Refactor Kiến trúc FSM-Callback Animation. Đã chuyển đổi giao tiếp giữa `PlayerMovement` và `PlayerAnimation` sang mô hình Callback, loại bỏ triệt để việc Animation phải tự dò trạng thái, fix dứt điểm lỗi kẹt State và Moonwalk Dash. Đảm bảo nguyên tắc: Movement quyết định trạng thái, Animation phản ứng.
- [x] **Task (Backlog):** Tinh chỉnh Game Feel cho Wall Jump Animation. Đã hoàn thiện mẹo đảo Scale trong `PlayerAnimation` để tái sử dụng clip `Duoi-TruotTuong` mô phỏng động tác đạp tường bật ra mà không đụng chạm đến logic vật lý của FSM.
