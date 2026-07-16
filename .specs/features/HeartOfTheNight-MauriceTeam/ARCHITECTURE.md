# ARCHITECTURE: Kiến trúc Code & Tư duy Hệ thống

**Last Updated:** 2026-07-16

> **AI CONTEXT:** This document is the authoritative technical reference. Read this FIRST for any technical question. Do not guess architectural patterns — verify here.

---

## 1. Data-Driven Design & Kinematic Math (Toán học Động học)

- **Quy tắc tuyệt đối:** Không viết chết (hardcode) các thông số trong code.
- Mọi thông số vật lý của nhân vật được lưu trữ trong `PlayerData` (ScriptableObject).
- **Tính toán tự động (OnValidate):** Thay vì bắt Game Designer phải đoán mò các thông số vật lý trừu tượng như `gravityScale` hay `jumpForce`, họ chỉ cần nhập các giá trị trực quan như **Độ cao nhảy (Jump Height)** và **Thời gian đạt đỉnh (Time To Apex)**. Hàm `OnValidate()` sẽ tự động áp dụng công thức động học (Kinematics) để dịch ngược ra Lực và Trọng lực chính xác:
  - $Gravity = \frac{-(2 \times jumpHeight)}{timeToApex^2}$
  - $JumpForce = Gravity \times timeToApex$
- Việc này đảm bảo quỹ đạo nhảy là chính xác tuyệt đối theo thiết kế, bất chấp các biến số vật lý khác.

## 2. Editor-First Tooling

- Các ScriptableObject bắt buộc phải sử dụng triệt để `[Header]`, `[Tooltip]`, `[Range]` và `[Space]`. Điều này biến SO thành một bảng điều khiển (Dashboard) trực quan cho Designer chứ không chỉ là một cái kho chứa Data khô khan. Các Tooltip phải giải thích rõ **hậu quả** của việc thay đổi thông số (VD: "Tăng sẽ làm trượt như trên băng").

## 2. Finite State Machine (FSM) Strictness

- Quản lý luồng hoạt động của game (và cả nhân vật) chặt chẽ qua các Trạng thái (State) rõ ràng (VD: `Grounded`, `Jumping`, `Falling`, `Dashing`, `Sliding`).
- **Nghiêm cấm** sử dụng các cờ hiệu boolean (`bool isJumping`, `bool isRunning`) lồng chéo nhau vô tội vạ tạo thành code "Spaghetti".
- **Single Point of Entry:** Mọi thay đổi trạng thái bắt buộc phải đi qua một hàm trung gian duy nhất (VD: `TransitionToState()`) để bảo đảm xử lý triệt để logic `OnEnter` và `OnExit` cho mỗi trạng thái. Không bao giờ gán đè state trực tiếp.

## 3. Observer Pattern (Event System)

- Sử dụng hệ thống Event (C# Action hoặc UnityEvent) để giảm thiểu sự phụ thuộc trực tiếp (Tight Coupling) giữa các Class.
- Mẫu kiến trúc này bắt buộc dùng chủ yếu để giao tiếp giữa Code Gameplay/Logic với Code UI. (VD: Nhân vật mất máu -> Phát Event -> UI nghe Event tự động trừ thanh máu).

## 4. Physics & Collision Systems

- **Physics Anti-Tunneling:** Đối với các vật thể bay tốc độ cực cao (như Đạn), bắt buộc sử dụng `Physics2D.Linecast` trong `FixedUpdate` để quét va chạm thay vì dựa vào `OnTriggerEnter2D`, do Continuous Collision Detection (CCD) của Unity không hoạt động với Collider IsTrigger.
- **Physics Interpolation:**
  - Tuyệt đối không dùng chế độ `Extrapolate` trên Player vì sẽ làm lệch tọa độ Transform so với hộp vật lý, gây sai số cho các tia Raycast kiểm tra tường/đất.
  - Các vật thể cần chuyển động mượt trên màn hình (Player, Đạn) chỉ được dùng `Interpolate`.
- **Projectile Physics:** Tuyệt đối KHÔNG cộng đà di chuyển của nhân vật vào đạn (No Inherit Velocity). Hiện tượng Doppler (dồn/giãn khoảng cách đạn) được giải quyết ở tầng Game Design thông qua việc cân bằng thông số `fireRate` thay vì phức tạp hóa logic vật lý.
- **Momentum & Game Feel:** Việc bảo toàn đà (Momentum Conservation) khi vượt quá giới hạn tốc độ (ví dụ: bị knockback, sau khi Dash) chỉ được áp dụng trên không trung và yêu cầu người chơi phải chủ động giữ phím cùng chiều.

## 5. Time Management & Coroutines

- **Hit Stop / Freeze Frame:** Khi sử dụng `Time.timeScale = 0` để tạm dừng game tạo hiệu ứng giật khung hình (Hit Stop), mọi Coroutine dùng để canh thời gian thoát khỏi trạng thái này bắt buộc phải sử dụng `WaitForSecondsRealtime`. Việc dùng `WaitForSeconds` sẽ gây kẹt vô tận do bộ đếm thời gian in-game đã dừng hoạt động.

## 6. Unity Lifecycle Separation (Update vs FixedUpdate)

- **`Update()`:** Chỉ dùng cho việc:
  1. Cập nhật các bộ đếm thời gian (Timers).
  2. Đọc input từ người chơi (`Input.GetKeyDown`).
  3. Quét va chạm logic (Raycasts, Overlaps).
  4. Chạy các lệnh kiểm tra chuyển đổi trạng thái (State Checks).
- **`FixedUpdate()`:** Độc quyền dùng cho việc thực thi vật lý thực tế (`Rigidbody2D.AddForce`, gán `linearVelocity`). Tuyệt đối không đọc input ở đây.

## 7. Input Buffering & Coyote Time

- Không dùng kiểu check input thô ở frame hiện tại để quyết định nhảy/lướt.
- Luôn sử dụng Timer đếm ngược (VD: `LastPressedJumpTime -= Time.deltaTime;`, `LastOnGroundTime -= Time.deltaTime;`) để xử lý **Input Buffer** (ghi nhớ thao tác phím sớm) và **Coyote Time** (cho phép nán lại thao tác trễ).

## 8. Zero-GC Optimization (Chống rác bộ nhớ)

- Tuyệt đối hạn chế việc tạo object mới (`new WaitForSeconds(...)`, `new Vector2(...)`) bên trong các vòng lặp như `Update()` hay `FixedUpdate()`.
- Bắt buộc phải **Cache** (lưu đệm) mọi Coroutine Waiters, Materials, Variables ở hàm `Awake()` hoặc biến Global để tái sử dụng.

## 9. State Isolation & Guard Clauses (Bảo vệ luồng State)

- **Tắt kiểm tra chéo:** Các trạng thái đang thực thi quyền ưu tiên cao (như `Dashing`) có quyền "return sớm" để bỏ qua các logic cấp thấp (VD: đang Lướt thì không thèm check Nhảy `if (IsDashing) return;` trong `HandleJumpChecks`).
- **Phase-specific Collision:** Việc quét va chạm (Collision Check) có thể bật/tắt linh hoạt theo từng giai đoạn (Phase) của một kỹ năng.
  _Ví dụ:_ Quét va chạm Ground bị tắt hoàn toàn khi Dashing để tránh ép rớt về state Grounded. Nhưng quét va chạm Tường (Wall check) chỉ bị tắt ở Phase 1 (`_isDashAttacking`), sau đó được bật lại ở Phase 2 để nhân vật có thể bám tường sớm ngay khi vừa hết tốc độ lướt cao.

## 10. Anti-Waste Logic (Chống lãng phí kỹ năng)

- Hệ thống luôn bảo vệ người chơi khỏi các thao tác thừa hoặc thao tác lỗi làm tốn tài nguyên.
  _Ví dụ:_ Nếu nhân vật đang bám tường trái, nhưng người chơi lỡ bấm phím Lướt sang trái (lướt vào tường), hàm `HandleDashChecks()` sẽ đánh chặn: hủy lướt, **xóa bộ nhớ đệm phím** (`LastPressedDashTime = 0`) và thoát hàm. Người chơi không bị trừ mất số lần lướt.

## 11. Direction Fallback Chain (Chuỗi ưu tiên hướng)

- Khi xác định hướng cho một hành động (như Lướt), logic phải tuân theo chuỗi ưu tiên từ cụ thể đến tổng quát:
  1. Hướng bắt buộc của ngữ cảnh (VD: Đang trượt tường dọc -> Lướt dọc).
  2. Hướng phím bấm của người chơi (`_moveInput`).
  3. Hướng quay mặt mặc định của nhân vật (`IsFacingRight`).

## 12. Dynamic Gravity Pipeline (Trọng lực Động)

- Trọng lực không phải là hằng số vật lý, mà là **Công cụ Game Feel**. Trọng lực phải được cập nhật liên tục mỗi frame trong `Update` (qua hàm `HandleGravity()`) thông qua cơ chế If-Else ưu tiên:
  1. **Khóa trọng lực (Gravity = 0):** Dành cho Lướt (Dash) hoặc Trượt tường (Sliding).
  2. **Fast Fall (Rơi nhanh):** Nhấn phím xuống khi đang rơi -> Tăng vọt trọng lực + giới hạn `maxFastFallSpeed`.
  3. **Jump Cut (Nhả phím nhảy sớm):** Tăng trọng lực kéo nhân vật xuống ngay lập tức + giới hạn `maxFallSpeed`.
  4. **Hang Time (Đỉnh nhảy):** Khi vận tốc trục Y tiệm cận 0, giảm trọng lực để nhân vật "lơ lửng" trên không lâu hơn.
  5. **Fall (Rơi tự do):** Trọng lực khi rơi thường lớn hơn trọng lực nhảy lên để rớt xuống dứt khoát.

## 13. Steering Authority (Quyền bẻ lái & Nội suy tuyến tính)

- Việc di chuyển ngang (`HandleRun`) sử dụng khái niệm **Lerp Amount** để quyết định "Quyền bẻ lái" của người chơi:
  - `lerpAmount = 1f`: Cấp 100% quyền. Nhân vật tuân lệnh phím bấm ngay lập tức (Chạy bộ bình thường).
  - `lerpAmount = 0.4f`: Lấy lại một phần quyền (VD: Nhảy tường). Phím bấm chỉ bẻ lái nhân vật từ từ, không gắt.
  - `lerpAmount = 0f` (hoặc chặn gọi hàm Run): Tước 100% quyền. Hệ thống kiểm soát hoàn toàn quỹ đạo (VD: Dash Phase 1).

## 14. Input Callback Pattern

- Tuyệt đối không viết logic xử lý nhảy hay lướt thẳng vào hàm đọc Input (`Input.GetKeyDown`).
- Hàm đọc Input (như `OnJumpInput`) chỉ có duy nhất một nhiệm vụ: **Nạp đầy bình xăng cho bộ đệm** (VD: `LastPressedJumpTime = Data.jumpInputBufferTime`). Sau đó, các hàm `HandleChecks()` sẽ dựa vào bình xăng này để quyết định có thực thi hành động hay không.

## 15. Velocity Compensation (Bù trừ vận tốc)

- Trong các hành động áp dụng lực bộc phát (Impulse) như `Jump` hay `WallJump`, bắt buộc phải **triệt tiêu đà cản** trước khi cộng lực.
  _Ví dụ:_ Nếu nhân vật đang rơi xuống (`velocity.y < 0`) mà người chơi bấm Nhảy, ta phải bù thêm một lượng lực đúng bằng `velocity.y` đó (`force -= RB.linearVelocity.y`) để đảm bảo chiều cao bước nhảy luôn luôn bằng nhau, không bị lùn đi do đang rớt.

## 16. Two-Phase Dash Architecture (Kiến trúc Lướt 2 Giai đoạn)

- Coroutine Lướt (`StartDash`) được thiết kế chia làm 2 pha rõ rệt lấy cảm hứng từ game Celeste:
  - **Phase 1 (Dash Attack):** Vận tốc bị ép cứng ở mức cao (`dashSpeed`), trọng lực bằng 0. Người chơi không thể tương tác với tường.
  - **Phase 2 (Dash End / Hãm phanh):** Vận tốc giảm đột ngột (`dashEndSpeed`), trọng lực khôi phục.
  - **Cơ chế Thoát sớm (Early Exit):** Đặc biệt trong Phase 2, vòng lặp `while` sẽ liên tục kiểm tra xem nhân vật có đang ép sát tường không (`wallClingReady`). Nếu có, Coroutine sẽ lập tức bị hủy (`yield break`) và chuyển thẳng sang trạng thái Bám Tường (`Sliding`). Điều này giúp game cực kỳ "snappy", người chơi không bị khựng lại chờ hết animation lướt.

## 17. Physics Overshoot Prevention (Chống vọt lố gia tốc)

- Khi dùng `AddForce` để ép Rigidbody đạt một mốc tốc độ nhất định (VD: `Slide`, `WallClimb`), lực bơm vào phải được **Clamp** (giới hạn) bằng công thức: `Mathf.Clamp(..., -speedDif / Time.fixedDeltaTime, speedDif / Time.fixedDeltaTime)`.
  _Lý do:_ Lực tác dụng trong 1 frame fixedUpdate có thể làm thay đổi vận tốc cực mạnh. Nếu không Clamp, nhân vật sẽ liên tục vượt quá tốc độ mục tiêu rồi lại bị kéo giật lùi, gây ra hiện tượng rung lắc (jitter) liên tục trên tường.

## 18. Infinite Wall Climb Prevention (Chống leo một tường)

- Cờ `_lastWallJumpDir` lưu lại hướng của lần bật tường gần nhất. Điều kiện `CanWallJump()` bắt buộc người chơi phải chạm vào bức tường ngược hướng với lần bật trước thì mới được bật tiếp. Điều này ép người chơi phải nhảy qua lại giữa 2 bức tường (Zig-zag) chứ không thể spam nhảy để leo thẳng đứng trên 1 bức tường duy nhất.

## 19. Passive Skill Refill (Hồi kỹ năng thụ động)

- Các kỹ năng như Lướt (Dash) được hồi lại dựa trên cơ chế đánh giá thụ động: Hàm `CanDash()` không chỉ trả về bool mà còn ngầm kiểm tra môi trường (Đang chạm đất/chạm tường). Nếu đúng điều kiện, nó tự động kích hoạt Coroutine `RefillDash`. Cách này giúp không phải viết logic Update kiểm tra chạm đất liên tục chỉ để hồi lướt.

## 20. 2D Visual & Animation Architecture

- **2-Piece Visual Setup (Zero-Offset):** Nhân vật được chia thành 2 GameObjects duy nhất: `UpperBody` và `LowerBody` với tọa độ nội bộ luôn khóa ở mức `localPosition = (0,0,0)`. Các trạng thái toàn thân (Lướt, Leo tường, Chết) sẽ được phát trực tiếp trên `LowerBody`, đồng thời tắt (`SpriteRenderer.enabled = false`) cục `UpperBody`. Tuyệt đối không dùng Transform để bù trừ sự sai lệch ảnh.
- **Moonwalk Mechanism:**
  1. Hình ảnh toàn thân (Scale X) luôn lật theo hướng ngắm chuột (Aiming Direction) để giữ tự nhiên.
  2. Nếu hướng Di chuyển ngược chiều với hướng Ngắm, truyền `RunSpeed = -1` (thông qua Multiplier của Animator State) để phát ngược hoạt ảnh chân (Moonwalk).
- **Pivot Alignment Standard:** Đối với Sprite cắt sát viền khác size, bắt buộc phải dùng **Sprite Editor -> Custom Pivot** và ghim vòng tròn Pivot vào đúng vị trí cơ thể cố định (như vùng bụng) ở MỌI frame để chống giật (jitter).
- **Depth Sorting:** Sử dụng thuần túy `Sorting Layer` và `Order in Layer` (Hoặc trục Z để làm Parallax). Tuyệt đối KHÔNG cấu hình `Transparency Sort Mode: Y=1` (vì đó là trick của Top-Down 2D, không áp dụng cho Platformer).
