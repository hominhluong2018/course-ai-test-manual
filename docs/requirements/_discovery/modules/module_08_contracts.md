# Module 08 — Contracts (Hợp đồng)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `CTR` |
| **Tên trên website** | Contracts |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 30–40 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/contracts` | Danh sách hợp đồng |
| `/admin/contracts/contract` | Tạo hợp đồng mới |
| `/admin/contracts/contract/{id}` | Chi tiết hợp đồng |

## Quan sát được

**Cột bảng:** `#` · `Subject` · `Customer` · `Contract Type` · `Contract Value` · `Start Date` · `End Date` · `Project` · `Signature`

**Nút thanh công cụ:** `New Contract` · `Export`

**Khối Contract Summary** (đọc từ evidence) — 5 ô: `Active 108` · `Expired 11` · `About to Expire 0` · `Recently Added 8` · **`Trash 5`**

**Hai biểu đồ:** `Contracts by Type` và `Contracts Value by Type (USD)`

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + **xoá mềm** (có Thùng rác) |
| Status flow | Không có cột `Status` trên bảng, nhưng có cột **`Signature`** (luồng ký) và 5 phân loại ở khối tổng hợp, trong đó **`Trash`** là trạng thái đã xoá mềm |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Liên quan tiền** — `Contract Value`
- **Có chữ ký điện tử** (`Signature`) — giá trị pháp lý, sai là hậu quả ngoài phạm vi phần mềm
- Có khoảng hiệu lực (`Start Date` / `End Date`) → hành vi theo thời gian, có nhắc hết hạn
- Khách hàng bình luận được từ bên ngoài — quan sát thấy **15 thông báo** dạng *"New comment from customer on contract ..."* trong khay thông báo → có kênh tương tác hai chiều với cổng Khách hàng
- Mức phủ tài liệu ⬜ Trắng

## Phát hiện tầng network của riêng module

Khay thông báo chứa nhiều liên kết `/admin/contracts/contract/{id}` sinh từ bình luận của khách hàng — xác nhận **cổng Khách hàng có tương tác ngược vào hợp đồng**. Đây là đầu mối cho `AMB-SYS-03` (chưa khảo sát cổng Khách hàng).

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Tập giá trị `Signature` | Chưa mở chi tiết |
| Luồng ký điện tử hoạt động thế nào | Chưa mở |
| Danh mục `Contract Type` cấu hình ở đâu | Đã thử `/admin/contracts/contract_types` → **404**. Nhưng evidence cho thấy có nhiều loại do tester tự tạo (`Auto Type 1779369438`…) → **tạo được loại mới từ trong biểu mẫu hợp đồng**, không nhất thiết qua Setup. Cần xác minh |
| 🔴 **Thùng rác** — khôi phục / xoá vĩnh viễn hoạt động thế nào | Mới biết có `Trash 5` qua khối tổng hợp; chưa mở. **Bắt buộc có TC** cho xoá mềm và khôi phục |
| Cơ chế nhắc hết hạn hợp đồng | Chưa xác minh |
| Bình luận của khách hàng hiển thị ở đâu trong màn hình quản trị | Chưa mở chi tiết |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/contracts` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/contracts/contract` — biểu mẫu tạo mới
- `/admin/contracts/contract/{id}` — chi tiết (đường dẫn lấy từ khay thông báo)

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`ctr_list_viewport.png`](../evidence/ctr_list_viewport.png) | Contract Summary 5 ô gồm **`Trash 5`**; 2 biểu đồ `Contracts by Type` và `Contracts Value by Type (USD)` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
