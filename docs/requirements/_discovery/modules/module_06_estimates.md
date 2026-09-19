# Module 06 — Estimates (Báo giá)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `EST` |
| **Tên trên website** | Estimates |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 30–40 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/estimates` | Danh sách báo giá |
| `/admin/estimates/estimate` | Tạo báo giá mới |
| `/admin/estimates/list_estimates?status={1..5}` | Lọc theo trạng thái — **đã quan sát 5 giá trị** |
| `/admin/estimates/list_estimates?not_sent=1` | Lọc "chưa gửi" |

## Quan sát được

**Cột bảng:** `Estimate #` · `Amount` · `Total Tax` · `Customer` · `Project` · `Tags` · `Date` · `Expiry Date` · `Reference #` · `Status`

**Nút thanh công cụ:** `Create New Estimate` · `Export`

**Trạng thái đọc được từ Bảng điều khiển** (khối "Estimate overview"): `Draft` · `Not Sent` · `Sent` · `Expired` · `Declined` · `Accepted` — **6 trạng thái**.

✅ **Đã giải quyết bằng evidence.** Bảng điều khiển hiển thị đồng thời *"3 Not Sent = 100%"* **và** *"3 Expired = 100%"*, trong khi danh sách chỉ có 3 báo giá và **cả 3 đều mang `Status = Expired`**. Kết luận: `Not Sent` là **cờ trạng thái gửi riêng**, **không** nằm cùng tập với `Expired` — hai thứ chồng lấn trên cùng một bản ghi. Đây là lý do đường dẫn lọc có `status=1..5` **cộng thêm** `not_sent=1` tách biệt.

⚠️ Hệ quả cho TC: **không được** viết TC giả định một báo giá chỉ mang đúng một trạng thái.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ **Có** — 6 trạng thái |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Liên quan tiền** — `Amount`, `Total Tax`
- Có **luồng trạng thái phức tạp** với ngày hết hạn (`Expiry Date`) → có hành vi tự đổi trạng thái theo thời gian
- Có luồng chuyển đổi sang Hoá đơn
- Gửi ra ngoài cho khách hàng → sai là lộ ra bên ngoài
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` và `ITEM` đã recon xong — báo giá chọn khách hàng và lấy dòng hàng từ danh mục Sản phẩm & Dịch vụ.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Ánh xạ `status=1..5` ↔ tên trạng thái hiển thị | Chưa mở từng bộ lọc |
| Biểu mẫu tạo báo giá — số trường, bảng dòng hàng, cách tính thuế | Chưa mở |
| Luồng chuyển Báo giá → Hoá đơn | Chưa mở |
| Hành vi tự chuyển sang `Expired` khi quá hạn | Cần dữ liệu và thời gian |
| Cấu hình thuế (`Total Tax`) | Nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/estimates` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/estimates/estimate` — biểu mẫu tạo mới
- `/admin/estimates/list_estimates?status={1..5}` và `?not_sent=1` — 6 đường dẫn lọc, mới đọc từ liên kết

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`est_list_viewport.png`](../evidence/est_list_viewport.png) | 3 bản ghi, **cả 3 đều `Status = Expired`**; đủ 10 cột |
| [`dash_overview_viewport.png`](../evidence/dash_overview_viewport.png) | Khối "Estimate overview": đồng thời "3 Not Sent = 100%" và "3 Expired = 100%" → hai cờ **chồng lấn** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
