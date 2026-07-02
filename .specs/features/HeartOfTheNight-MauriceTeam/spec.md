# spec.md: HeartOfTheNight-MauriceTeam

**Description:** Game Platformer / Metroidvania 2D. Nhân vật chính là Maurice.

## Tech Stack Đặc Thù
- **Thể loại:** 2D Platformer.
- **Core Loop:** Di chuyển, chiến đấu với quái vật bóng tối, vượt chướng ngại vật.
- **Thư viện đặc thù:** (Sẽ cập nhật thêm khi phát triển).

## Điểm lưu ý kiến trúc
- **Player Controller:** Cần xử lý vật lý 2D, Raycast chạm đất (Ground Check), hệ thống State Machine cho nhân vật (Idle, Run, Jump, Fall, Dash, Wall Climb, Double Jump).
- **Game Feel:** Tập trung vào độ mượt mà khi di chuyển và phản hồi điều khiển nhanh (Coyote Time, Jump Buffer).
