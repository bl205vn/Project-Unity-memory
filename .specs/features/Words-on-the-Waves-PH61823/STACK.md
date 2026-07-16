# STACK: Words on the Waves PH61823 (Dự án 2)

**Last Updated:** 2026-07-04

## 1. Định danh & Mục tiêu (Identity & Goals)
- **Tên dự án:** Words on the Waves PH61823
- **Thể loại:** Mô phỏng đời sống & Quản lý (Life Simulation & Management)
- **Nền tảng:** Mobile (Android/iOS)
- **Mục tiêu:** Tạo ra một tựa game quản lý hiệu sách di động thư giãn. Không có cơ chế "Game Over". Tập trung tối đa vào tối ưu hóa hiệu năng, kiến trúc hướng dữ liệu (Data-Driven), và trải nghiệm game (Game Feel).

## 2. Công nghệ sử dụng (Tech Stack)
- **Engine:** Unity 3D (URP) - Version 6000.0.74f1
- **Ngôn ngữ:** C#
- **Kiến trúc:** Data-Driven + Finite State Machine (FSM) + Observer Pattern
- **Hệ thống UI:** UGUI (Canvas)
- **Mạng (Networking):** Không có (none)
- **Công cụ hiệu ứng (Feel Tools):** Tweening (DOTween hoặc tự viết), Hệ thống âm thanh (Pitch Shifting)

## 3. Phạm vi cốt lõi (Core Scope)
- **Kho hàng/Vận chuyển (Inventory/Cargo):** 7 thể loại sách, hệ thống nhập kiện hàng (cấu hình bằng JSON).
- **Vòng lặp phục vụ (Service Loop):** Kéo & thả sách, đọc lời thoại khách hàng, tư vấn đúng thể loại sách.
- **Tiến trình (Progression):** Đồ trang trí (Decor) & Kỷ vật (Mementos) cung cấp các chỉ số Buff, di chuyển qua lại giữa các địa điểm trên bản đồ.
