# ARCHITECTURE: Words on the Waves PH61823

**Last Updated:** 2026-07-04

## 1. Tổng quan hệ thống (System Overview)

Trò chơi sử dụng **Kiến trúc Hướng dữ liệu (Data-Driven Architecture)** kết hợp với **Máy trạng thái hữu hạn (FSM)** để quản lý luồng trò chơi và **Observer Pattern** để giao tiếp giữa các module.

```mermaid
graph TD
    subgraph KhoiDauVao ["Khối đầu vào"]
        ViTriBanDau["Vị trí ban đầu, kéo thả, snap"]
        Cham[Chạm]
    end

    subgraph KhoiLogic ["Khối logic"]
        HeThongLuuTai[Hệ thống lưu và tải bản lưu]
        HeThongDuLieu["Hệ thống dữ liệu<br>Dùng json"]

        subgraph SapXepSach ["Sắp xếp sách"]
            TrungOLuoiTrong{Trúng ô lưới trống}
            SoSachNhoHonSucChua{"Số sách trên kệ < sức chứa"}
            CapNhatViTriSach[Cập nhật vị trí sách]
        end

        subgraph ChonBanDo ["Chọn bản đồ"]
            ChonMap[Chọn 1 trong 4 map]
            ViTienPhiDiChuyen{"Ví tiền >= phí di chuyển"}
            TruPhiXangXe[Trừ phí xăng xe di chuyển]
        end

        subgraph CuaHang ["Cửa hàng"]
            GiaoDienCuaHang[Giao diện của hàng]
            MuaSach[Mua sách]
            ViTienGiaThungSach{"Ví tiền >= Giá thùng sách"}
            TruTienMuaSach[Trừ tiền trong ví]
            TinhToanGacha[Tính toán tỉ lệ gacha sách]
            ChoSachVaoKho[Cho sách vào kho]
            MuaDoTrangTriLogic[Mua đồ trang trí]
            ViTienGiaDoTrangTri{"Ví tiền >= Giá đồ trang trí"}
            TruTienMuaDoTrangTri[Trừ tiền trong ví]
            ChoDoTrangTriVaoKho[Cho đồ trang trí vào kho]
        end

        subgraph DocDuLieuKhiKhoiDong ["Đọc dữ liệu khi khởi động"]
            KhoiDongGame[Khởi động game]
            DocDuLieuJson[Đọc dữ liệu từ file json]
            HienThiMenu[Hiển thị menu chính]
        end

        subgraph HeThongThoai ["Hệ thống thoại"]
            HienThiThoai[Hiện thị thoại từ json]
            KiemTraTheLoaiSach{"Thể loại sách chọn == đáp án trong json"}
            ApDungBuff["Áp dụng buff % doanh thu/tip và từ đồ trang trí với kỷ vật"]
            TienSachVaThuong["Tiền sách niêm yết + thưởng"]
            KhachHangDacBiet{Khách hàng đặc biệt}
        end

        subgraph SinhKhachHang ["Sinh khách hàng"]
            TaiNhomKhachHang[Tải nhóm khách hàng và sở thích theo map]
            SinhNpc[SInh npc theo thời gian]
            KhachCanTuVan{Khách cần tư vấn}
            KhachTuChonSach[Khách tự chọn sách]
            CongTienBanSach[Cộng tiền bán sách theo giá niêm yết]
        end

        subgraph TongKetNgay ["Tổng kết ngày"]
            HetThoiGian{"Hết thời gian mở tiệm hoặc khách vắn"}
            TinhTongDoanhThu[Tính tổng doanh thu ngày và kỷ vật mới nhận]
            CapNhatDuLieuJson[Cập nhật dữ liệu lưu vào json]
        end
    end

    subgraph KhoiTruyenTin ["Khối truyền tin"]
        NhanKyVat[Nhận kỷ vật]
        ThayDoiVungBanDo[Thay đổi vùng bản đồ]
        TuVanSai["Tư vấn sai / khách từ chối"]
        TuVanChinhXac[Tư vấn chính xác]
        DatThanhCong["Đặt sách/đồ trang trí thành công"]
        DatSaiViTri["Đặt sai vị trí / đầy kệ"]
        KhachMuaSachThanhCong[Khách mua sách thành công]
        TruyenTinMuaDoTrangTri[Mua đồ trang trí]
        TruyenTinMuaThungSach[Mua thùng sách]
        KhongDuTienDiChuyen[Không đủ tiền di chuyển]
        KhongDuTienMuaHang[Không đủ tiền mua hàng]
        GhiFileJson[Ghi file lưu trữ json]
    end

    subgraph KhoiDauRa ["Khối đầu ra"]
        UIHUB["UI/HUB"]
        HieuUngVFX["Hiệu ứng/VFX"]
        AmThanh[Âm thanh]
        Tweening[Tweening và Animation]
    end

    ViTriBanDau --> TrungOLuoiTrong
    TrungOLuoiTrong -- Đúng --> SoSachNhoHonSucChua
    TrungOLuoiTrong -- Sai --> DatSaiViTri
    SoSachNhoHonSucChua -- Đúng --> CapNhatViTriSach
    SoSachNhoHonSucChua -- Sai --> DatSaiViTri
    CapNhatViTriSach --> DatThanhCong

    Cham --> ChonMap
    Cham --> HienThiThoai
    Cham --> GiaoDienCuaHang
    ChonMap --> ViTienPhiDiChuyen
    ViTienPhiDiChuyen -- Đúng --> TruPhiXangXe
    ViTienPhiDiChuyen -- Sai --> KhongDuTienDiChuyen
    TruPhiXangXe --> ThayDoiVungBanDo
    ThayDoiVungBanDo --> TaiNhomKhachHang

    GiaoDienCuaHang --> MuaSach
    GiaoDienCuaHang --> MuaDoTrangTriLogic
    MuaSach --> ViTienGiaThungSach
    ViTienGiaThungSach -- Đúng --> TruTienMuaSach
    ViTienGiaThungSach -- Sai --> KhongDuTienMuaHang
    TruTienMuaSach --> TinhToanGacha
    TinhToanGacha --> ChoSachVaoKho
    ChoSachVaoKho --> TruyenTinMuaThungSach

    MuaDoTrangTriLogic --> ViTienGiaDoTrangTri
    ViTienGiaDoTrangTri -- Đúng --> TruTienMuaDoTrangTri
    ViTienGiaDoTrangTri -- Sai --> KhongDuTienMuaHang
    TruTienMuaDoTrangTri --> ChoDoTrangTriVaoKho
    ChoDoTrangTriVaoKho --> TruyenTinMuaDoTrangTri

    KhoiDongGame --> DocDuLieuJson

    HeThongDuLieu --> HienThiThoai
    HeThongDuLieu --> ApDungBuff
    HeThongDuLieu --> ViTienPhiDiChuyen
    HeThongDuLieu --> GiaoDienCuaHang

    DocDuLieuJson --> HeThongLuuTai

    HeThongLuuTai --> HienThiMenu
    HeThongLuuTai --> GhiFileJson

    HienThiThoai --> KiemTraTheLoaiSach
    KiemTraTheLoaiSach -- Đúng --> ApDungBuff
    KiemTraTheLoaiSach -- Sai --> TuVanSai
    ApDungBuff --> TienSachVaThuong
    TienSachVaThuong --> KhachHangDacBiet
    KhachHangDacBiet -- Đúng --> NhanKyVat
    KhachHangDacBiet -- Sai --> TuVanChinhXac

    TaiNhomKhachHang --> SinhNpc
    SinhNpc --> KhachCanTuVan
    SinhNpc --> HetThoiGian
    KhachCanTuVan -- Đúng --> Cham
    KhachCanTuVan -- Sai --> KhachTuChonSach
    KhachTuChonSach --> CongTienBanSach
    CongTienBanSach --> KhachMuaSachThanhCong

    HetThoiGian -- Đúng --> TinhTongDoanhThu
    TinhTongDoanhThu --> CapNhatDuLieuJson
    CapNhatDuLieuJson --> HeThongLuuTai
    HienThiMenu --> GiaoDienCuaHang
    GhiFileJson --> HienThiMenu

    ThayDoiVungBanDo --> UIHUB
    ThayDoiVungBanDo --> AmThanh

    KhachMuaSachThanhCong --> AmThanh
    KhachMuaSachThanhCong --> UIHUB
    KhachMuaSachThanhCong --> CapNhatDuLieuJson

    TuVanChinhXac --> Tweening
    TuVanChinhXac --> AmThanh
    TuVanChinhXac --> HieuUngVFX
    TuVanChinhXac --> UIHUB
    TuVanChinhXac --> CapNhatDuLieuJson

    NhanKyVat --> CapNhatDuLieuJson
    NhanKyVat --> Tweening
    NhanKyVat --> AmThanh
    NhanKyVat --> UIHUB
    NhanKyVat --> HieuUngVFX

    TruyenTinMuaDoTrangTri --> CapNhatDuLieuJson
    TruyenTinMuaDoTrangTri --> Tweening
    TruyenTinMuaDoTrangTri --> AmThanh
    TruyenTinMuaDoTrangTri --> UIHUB

    TruyenTinMuaThungSach --> CapNhatDuLieuJson
    TruyenTinMuaThungSach --> AmThanh
    TruyenTinMuaThungSach --> UIHUB

    TuVanSai --> UIHUB
    TuVanSai --> AmThanh
    TuVanSai --> Tweening

    KhongDuTienDiChuyen --> UIHUB
    KhongDuTienDiChuyen --> AmThanh

    GhiFileJson --> UIHUB

    KhongDuTienMuaHang --> UIHUB
    KhongDuTienMuaHang --> AmThanh

    DatSaiViTri --> Tweening
    DatSaiViTri --> AmThanh

    DatThanhCong --> Tweening
    DatThanhCong --> UIHUB
    DatThanhCong --> AmThanh
```

## 2. Các Module Cốt lõi

### 2.1. FSM (Máy trạng thái hữu hạn)

Vòng lặp trò chơi được quản lý bởi các lớp State chuyên biệt kế thừa từ State cơ sở:

- `MainMenuState` (Menu chính)
- `CargoState` (Nhập sách)
- `PreparationState` (Sắp xếp kệ sách)
- `MapTravelState` (Di chuyển giữa các địa điểm)
- `ServiceState` (Phục vụ khách hàng)
- `DialogueListeningState` (Phân tích lời thoại khách hàng)
- `DaySummaryState` (Tổng kết cuối ngày)

_Quy tắc: Không được sử dụng cờ boolean lồng nhau để quản lý trạng thái trò chơi._

### 2.2. Quản lý dữ liệu (Data-Driven)

- Toàn bộ thông số kinh tế (kiện hàng, phí di chuyển, sách, chỉ số buff, lời thoại) đều được lưu trữ trong tệp `.json`.
- `DataManager` phân tích cú pháp và cung cấp dữ liệu này trên toàn hệ thống.
- Cấm tuyệt đối việc hardcode (viết chết) thông số vào mã nguồn.

### 2.3. Hệ thống Sự kiện (Observer Pattern)

- Giao tiếp giữa Gameplay cốt lõi, UI và Âm thanh sử dụng `Action` hoặc `UnityEvent`.
- Đảm bảo tính liên kết lỏng lẻo (loose coupling) giữa các thành phần.

## 3. Tương tác & Trải nghiệm (Interaction & Game Feel)

- **Kéo & Thả (Drag & Drop):** Sử dụng `Physics.Raycast` để tương tác với kệ sách 3D. Bao gồm việc hiển thị bóng mờ (Ghost Mesh preview) và tự động bắt dính (Snapping).
- **Tweening:** Phóng to (Scale Up) khi nhấc, Co giãn (Squash & Stretch) khi đặt xuống, Rung lắc (Shake) khi bị lỗi.
- **Âm thanh (Audio):** Thay đổi cao độ (Combo Pitch Shifting) khi bán hàng thành công liên tiếp. Âm lượng nhạc nền thay đổi tự động dựa trên khoảng cách đến bờ biển.

## 4. Tiêu chuẩn Hiệu năng (Performance Standards)

- **Zero GC:** Tuyệt đối không dùng `Find`, `GetComponent`, hoặc từ khóa `new` (tạo mới object/list/string) bên trong hàm lặp `Update`.
- **Object Pooling:** Bắt buộc sử dụng cho mô hình NPC, bong bóng thoại, icon sách và hiệu ứng chữ bay (floating text).
- **Số lượng Batches < 50:**
  - Sử dụng **Sprite Atlas** cho toàn bộ UI.
  - Áp dụng **Static Batching** cho môi trường tĩnh.
  - Kích hoạt **GPU Instancing / Dynamic Batching** cho các mô hình sách trên kệ.
  - Tách biệt UI Canvas tĩnh và động để ngăn tình trạng tính toán vẽ lại (rebuild) gây giật lag.

## 5. Quy chuẩn Quản lý Mã nguồn (Git & VCS)

- **Chuẩn Conventional Commits (Dành cho Solo Developer):**
  - **Cấu trúc linh hoạt:** `<type>: <description>` (Không cần quá khắt khe với phần `<scope>`, có thể bỏ qua cho nhanh gọn).
  - **3 loại (type) dùng 90% thời gian:**
    - `feat`: Khi code một cơ chế/tính năng mới (Ví dụ: `feat: làm chức năng kéo thả sách`).
    - `fix`: Khi game bị lỗi và bạn sửa nó (Ví dụ: `fix: sửa lỗi khách hàng sinh ra bị kẹt`).
    - `chore`: Khi không sửa code mà chỉ import thêm asset, đổi tên thư mục, chỉnh thông số trên Inspector (Ví dụ: `chore: import bộ asset âm thanh sóng biển`).
  - **Mục đích:** Giúp tìm lại code cực nhanh khi có bug (chỉ cần search `fix` hoặc `feat`) và tạo thói quen tốt để chuẩn bị cho môi trường studio lớn.
- **Lưu ý Cấu hình Unity & Git:**
  - Bắt buộc thiết lập **Asset Serialization** thành **Force Text** (`Edit > Project Settings > Editor`) để Git đọc được nội dung các file `.meta` hoặc Scene dưới dạng chữ.
  - _(Agent ghi nhớ: Chỉ viết commit message hỗ trợ copy-paste khi người dùng yêu cầu rõ ràng, tuyệt đối không tự ý chạy git commit hoặc sinh file gitignore)._
