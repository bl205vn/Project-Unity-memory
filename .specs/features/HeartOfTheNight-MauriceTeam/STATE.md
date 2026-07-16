# STATE: HeartOfTheNight-MauriceTeam

**Last Updated:** 2026-07-16

> **AI CONTEXT:** This document tracks the current state, active decisions, and known issues. Update it when major architectural decisions are made.

---

## 1. Recent Decisions (ADR)

- **[2026-07-16] Kiến trúc Render Visual 2D (2-Piece Setup & Pivot Alignment):**
  - **Hệ thống 2 Cục Visual (Upper/Lower):** Quyết định loại bỏ cục FullBody. Mọi animation toàn thân (Lướt, Leo tường) sẽ được phát trực tiếp trên `LowerBody` (kèm theo ẩn `UpperBody`). Để làm được điều này, BẮT BUỘC toàn bộ sprite (Thân trên, Thân dưới, Nguyên người) phải được canh chỉnh Custom Pivot tập trung vào 1 điểm cốt lõi (VÙng bụng) trong Sprite Editor. Tọa độ Y cục bộ của cả 2 GameObject được reset về 0 tuyệt đối (Zero-Offset).
  - **Moonwalk (Chạy lùi):** Cả thân trên và thân dưới BẮT BUỘC lật (Flip) theo hướng chuột (Aim) để tránh lỗi "gãy cột sống". Nếu hướng di chuyển (A/D) ngược với hướng ngắm, kích hoạt thông số `RunSpeed = -1` trong Animator để tua ngược hoạt ảnh chân.
  - **Layer Sorting (Màn hình ngang):** Khẳng định KHÔNG sử dụng `Y=1` Custom Axis (chỉ dành cho Top-Down). Platformer 2D sử dụng `Sorting Layer` (Background/Gameplay/Foreground) và `Order in Layer` để xử lý che khuất (Depth).
- **[2026-07-16] Kiến trúc Hoạt ảnh & Điều khiển ngắm bắn (Upper/Lower Body Split):**
  - **Tách Hierarchy Visuals:** Chia Player Visual thành `UpperBody` (chứa súng) và `LowerBody` (chân). Giải quyết triệt để vấn đề "Moonwalk" (chạy lùi) và tránh bùng nổ tổ hợp animation (Combinatorial Explosion).
  - **Logic lật (Flip):** `PlayerMovement` chỉ lật `LowerBody` dựa theo phím di chuyển, kèm theo tự lật local position của `_frontWallCheckPoint` và `_backWallCheckPoint` để vật lý đúng. `PlayerAttack` điều khiển lật `UpperBody` dựa vào vị trí chuột.
  - **Juicy Game Feel:** Áp dụng `DOTween` trực tiếp lên Transform của `UpperBody` để mô phỏng độ giật súng (`DOPunchPosition`) mỗi khi bắn mà không làm ảnh hưởng chân đang chạm đất.
- **[2026-07-16] Tối ưu hóa Code & Vật lý Di chuyển (Movement):**
  - **Cấu trúc luồng chạy (HandleRun):** Áp dụng mô hình điều kiện phân cấp (Hierarchical `if(IsDashing)`). Giúp tối ưu hóa hiệu năng CPU (loại bỏ việc kiểm tra thừa trong 95% thời gian game chạy bình thường) và tách bạch rõ ràng Logic Lướt với Logic Chạy/Nhảy.
  - **Bảo toàn quán tính (doConserveMomentum):** Đã làm rõ công thức tính toán. Cơ chế này chỉ ép gia tốc về 0 (không phanh) khi người chơi đang bay ở tốc độ lớn hơn tốc độ tối đa VÀ đang giữ phím di chuyển thuận chiều bay VÀ đang trên không.
  - **Hiệu ứng Hit Stop (Sleep):** Thay thế `WaitForSeconds` bằng `WaitForSecondsRealtime` trong Coroutine để xử lý đóng băng game (`Time.timeScale = 0`), ngăn chặn lỗi kẹt timer mãi mãi.
  - **Bình luận Code (Documentation):** Toàn bộ hàm `Run()`, `HandleGravity()`, `HandleDashChecks()`, và các hệ thống trọng lực đã được chú thích chi tiết bằng tiếng Việt để dễ bảo trì về sau.
- **[2026-07-16] Cơ chế Tương tác Tường (Wall Interaction):**
  - Bổ sung tùy chọn `allowReverseWallClingDash` để cho phép nhảy lướt bật ra khỏi tường.
  - Bổ sung cơ chế "Bảo vệ Dash" (Anti-waste): Nếu người chơi lướt thẳng vào tường khi không được phép bật ra, game sẽ hủy lướt và reset lại buffer để không lãng phí kỹ năng.
- **[2026-07-16] Cơ chế Bắn Đạn (Shooting & Projectiles):**
  - Thêm `allowShootWhileSliding` để cấu hình khả năng vừa bám tường vừa xả đạn.
  - Xử lý hiệu ứng Doppler (Đạn dồn ứ/giãn): Quyết định GỠ BỎ hoàn toàn logic cộng hưởng đà (Inherit Velocity). Lý do: Thay vì phức tạp hóa vật lý, Game Design giải quyết bằng cách giãn `fireRate` (ví dụ 0.5s/viên). Ở tần suất bắn thấp, độ lệch khoảng cách đạn là không đáng kể và không làm hỏng trải nghiệm, đồng thời code súng sẽ cực kỳ sạch và dễ đoán.
  - Chống kẹt/sai số thời gian bắn (Frame Spillover): Đã thử nghiệm cộng dồn cứng `_lastFireTime += fireRate` để giữ khoảng cách đạn hoàn hảo, nhưng phát hiện bug "Burst bắn đuổi" nghiêm trọng (xả toàn bộ đạn bù cho thời gian nghỉ). Quyết định: Giữ nguyên `_lastFireTime = Time.time` như chuẩn mực an toàn, chấp nhận sai số vài pixel vì nó không đáng kể trong thực tế.
  - Xử lý đạn xuyên tường (Anti-Tunneling): Chuyển từ Collider Trigger thuần sang dùng `Physics2D.Linecast` trong `FixedUpdate` của `Bullet.cs`. Giải quyết dứt điểm lỗi đạn bay tốc độ cao xuyên tường vì CCD của Unity không hoạt động trên IsTrigger.
- **[2026-07-11] Cơ chế Lướt (Dash):**
  - Đã fix logic pha `dashEndTime` bằng cách bổ sung `Run(Data.dashEndRunLerp)`.
  - Đã thêm tùy chọn `lockFacingToDashDirection`.
- **[2026-07-11] Cơ chế Nhảy Đôi (Double Jump):** Giữ nguyên thiết kế gốc (không hồi lại khi bám tường).

## 2. Blockers

- **Chưa phát hiện rủi ro nghiêm trọng.**

## 3. Lessons Learned

- **Bài học 3 (Extrapolate vs Nhảy Tường):** TUYỆT ĐỐI KHÔNG DÙNG `Extrapolate` cho Rigidbody của Player. Chế độ đoán trước tương lai này sẽ đẩy Transform làm lệch các hộp kiểm tra va chạm (Raycast/OverlapBox), khiến cờ `IsTouchingWall` bị sai lầm và bóp méo tính năng Nhảy Tường (gây nhảy đôi giữa không trung). Các vật thể cần mượt (Player, Bullet) chỉ được dùng `Interpolate`.
- **Bài học 2 (Kiểm duyệt Physics Layer):** Cần tách bạch Layer `Dan` ra khỏi Player để áp dụng thiết kế Zero GC cho Object Pooling, tránh tính toán va chạm ngược lên chính người chơi.
- **Bài học 1 (Coroutine vs FixedUpdate):** Cẩn thận khi kết hợp Coroutine và FixedUpdate. Việc set cứng `RB.linearVelocity` trong Coroutine sẽ "vô hiệu hóa" mọi nỗ lực `AddForce` hoặc `velocity` lerp.
