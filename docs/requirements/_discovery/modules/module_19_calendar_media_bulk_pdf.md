# Module 19 — Calendar (Lịch) · Media / Bulk PDF Export (Tệp & Xuất PDF hàng loạt)

← [Về bản đồ hệ thống](../system_map.md)

> Tệp này chứa **2 module**. Gộp vì cùng nằm trong nhóm menu `Utilities` và mỗi module chỉ có 1–2 màn hình.
>
> ⚠️ **Gộp tệp KHÔNG gộp prefix.** `CAL` và `UTIL` vẫn là 2 prefix riêng.

---

## 19a. Calendar (Lịch) — `CAL`

| Mục | Giá trị |
|---|---|
| **Prefix** | `CAL` |
| **Tên trên website** | Calendar |
| **Bí danh** | Events (Sự kiện — entity của module này) |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 15–20 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/utilities/calendar` | Lịch |
| `/admin/utilities/calendar?new_event=true&date=DD-MM-YYYY` | Tạo sự kiện mới — **tham số ngày nằm trên URL** |

### Quan sát được

**Nút / điều khiển:** `Clear` · `Apply` (bộ lọc) · `Minutes` (đơn vị nhắc trước) · `Save` (hộp thoại sự kiện)

Lịch **tổng hợp sự kiện từ nhiều module** — theo menu tạo nhanh, `Event` là một loại bản ghi tạo được trực tiếp.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (trên entity Sự kiện) |
| Status flow | Không |
| Số tab | 0 — dùng chế độ xem tháng/tuần/ngày |

### Phát hiện tầng network của riêng module

| Endpoint | Ghi nhận |
|---|---|
| `GET /admin/utilities/get_calendar_data?start=<ISO>&end=<ISO>` | Nạp dữ liệu lịch theo khoảng thời gian. **Đây là endpoint duy nhất trong hệ thống nhận tham số khoảng ngày trên query string** — rất tiện cho việc kiểm biên ngày |

### Lý do risk 🟡

- **Phụ thuộc ngày giờ và múi giờ** — loại lỗi kinh điển, khó thấy
- Tổng hợp dữ liệu từ nhiều module → hiển thị sai là nghi ngờ nhầm module khác
- Nhưng không chạm tiền, không module nào phụ thuộc vào nó

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Lịch gom sự kiện từ **những module nào** | Chưa mở bộ lọc `Apply` |
| Biểu mẫu sự kiện — số trường, cách đặt nhắc trước | Chưa mở |
| Múi giờ hệ thống | Cấu hình nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| ~~Các chế độ xem~~ | ✅ **Đã xác minh 3 chế độ**: `Month` · `Week` · `Day`, cộng nút `Filter By`. **Không** có chế độ danh sách |
| Nhãn `+30 more` khi sự kiện dồn trong một ô ngày | Thấy trên evidence, chưa bấm thử |

---

## 19b. Media / Bulk PDF Export (Tệp & Xuất PDF hàng loạt) — `UTIL`

| Mục | Giá trị |
|---|---|
| **Prefix** | `UTIL` |
| **Tên trên website** | Media · Bulk PDF Export |
| **Bí danh** | Files (tiêu đề trang của Media) |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 18–24 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/utilities/media` | Quản lý tệp — tiêu đề trang hiển thị **"Files"** |
| `/admin/utilities/bulk_pdf_exporter` | Xuất PDF hàng loạt |

### Quan sát được — Quản lý tệp

Không có nút thanh công cụ chuẩn — màn hình dùng **trình quản lý tệp nhúng** (giao thức elFinder), điều khiển nằm trong khung riêng.

### Quan sát được — Xuất PDF hàng loạt

**Số phần tử biểu mẫu đếm được trên DOM: 33.**

**Nhãn đọc được:** `* Select Type` · `From Date:` · `To Date:` · `Include Tag` · `Status`

Bộ trạng thái hiển thị theo loại chứng từ được chọn — quan sát thấy tập của Báo giá: `All` · `Draft` · `Sent` · `Expired` · `Declined` · `Accepted`.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (tải lên / xoá tệp) · Xuất PDF là thao tác đọc |
| Status flow | Không |
| Số tab | 0 |

### Phát hiện tầng network của riêng module

| Endpoint | Ghi nhận |
|---|---|
| `GET /admin/utilities/media_connector?cmd=open&target=&init=1&tree=1` | Giao thức elFinder — **không** theo mẫu `POST /<module>/table` như phần còn lại của hệ thống |
| `media_connector?cmd=editor&name[]=ZohoOffice&name[]=ZipArchive&name[]=OnlineConvert` | Request này **thất bại** (`net::ERR_ABORTED`) trong phiên khảo sát. ⚠️ Cần xác minh lại — có thể là lỗi thật, có thể chỉ là trình soạn thảo tuỳ chọn không bật |

### Lý do risk 🟡

- **Tải tệp lên** là bề mặt tấn công quen thuộc (loại tệp, kích thước, tên tệp)
- Xuất PDF hàng loạt **đọc dữ liệu của 4 module tài chính** → lọc sai là xuất nhầm chứng từ của khách hàng khác
- Nhưng không tự sinh dữ liệu nghiệp vụ, không module nào phụ thuộc vào nó

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Ràng buộc tải tệp — loại, kích thước tối đa | Chưa thử |
| `Select Type` có những loại chứng từ nào | Chưa mở dropdown |
| Bộ trạng thái đổi thế nào theo từng loại chứng từ | Chỉ quan sát được tập của Báo giá |
| Nguyên nhân request `cmd=editor` thất bại | Chưa điều tra |
| Quyền xem tệp của người khác | Evidence cho thấy trình quản lý có **2 gốc thư mục**: `admin-example` (đặt theo tên tài khoản) và `public` (dùng chung). Đây là **ranh giới quyền trên tệp** — cần TC kiểm người này có thấy thư mục riêng của người kia không. Chưa kiểm được vì chỉ có 1 tài khoản — `AMB-SYS-01` |

---

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `CAL` — `/admin/utilities/calendar` — có ảnh
- `UTIL` — `/admin/utilities/media` — có ảnh
- `UTIL` — `/admin/utilities/bulk_pdf_exporter` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `CAL` — `/admin/utilities/calendar?new_event=true&date=…` — hộp thoại tạo sự kiện
- `CAL` — chế độ `Week` và `Day` (mới thấy nút, chưa bấm)

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`cal_month_view_viewport.png`](../evidence/cal_month_view_viewport.png) | `CAL` — tháng `September 2026`; nút chuyển **`Month` · `Week` · `Day`** + `Filter By`; sự kiện dồn kèm nhãn `+30 more` |
| [`util_media_files_viewport.png`](../evidence/util_media_files_viewport.png) | `UTIL` — trình quản lý tệp nhúng với **2 gốc thư mục**: `admin-example` (riêng theo tài khoản) và `public` (dùng chung) |
| [`util_bulk_pdf_export_viewport.png`](../evidence/util_bulk_pdf_export_viewport.png) | `UTIL` — `* Select Type` bắt buộc (đang `Nothing selected`) · `From Date` · `To Date` · `Include Tag` · nút `Export`. Khối `Status` **chưa hiện** vì chưa chọn loại chứng từ |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
