# Module 17 — Support (Hỗ trợ / Ticket)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `TICKET` |
| **Tên trên website** | Support |
| **Bí danh** | Support Tickets (tiêu đề trang) |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 30–40 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/tickets` | Danh sách phiếu hỗ trợ |
| `/admin/tickets/add` | Tạo phiếu mới |

## Quan sát được

**Cột bảng:** `#` · `Subject` · `Tags` · `Department` · `Service` · `Contact` · `Status` · `Priority` · `Last Reply` · `Created`

**Nút thanh công cụ:** `New Ticket` · `Export` · `Bulk Actions` · bộ lọc `Open`

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Hành động hàng loạt |
| Status flow | ✅ **Có** — trạng thái + mức ưu tiên |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🟡

- **Là kênh tiếp xúc trực tiếp với khách hàng** — trả lời sai người là lộ thông tin
- Có luồng trả lời hai chiều với cổng Khách hàng (cột `Last Reply`, `Contact`)
- Nhưng **không chạm tới tiền** và không có module nào phụ thuộc vào nó
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Gắn vào `CUST` qua cột `Contact` — nên recon sau `CUST`.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| ~~Tập trạng thái đầy đủ~~ | ✅ **Đã có đủ 5** từ khối Tickets Summary: `Open` · `In Progress` · `Answered` · `On Hold` · `Closed` — đọc được **không** cần quyền Setup |
| Tập **mức ưu tiên** đầy đủ | Đã thử `/admin/tickets/priorities` → **bị đẩy về Dashboard**. Danh mục nằm ở vùng Setup bị chặn — `AMB-SYS-01` |
| ⚠️ **Danh sách RỖNG** | Phải tự tạo dữ liệu trước khi recon |
| Danh mục `Department` (Phòng ban) | Đã thử `/admin/departments` → **bị đẩy về Dashboard** — `AMB-SYS-01` |
| Danh mục `Service` (Dịch vụ) | Nghi cùng vùng Setup — `AMB-SYS-01` |
| Luồng trả lời phiếu, đính kèm tệp | Chưa mở chi tiết |
| Khách hàng tạo phiếu từ cổng Khách hàng thế nào | `AMB-SYS-03` |
| Mẫu trả lời sẵn (canned response) | Chưa xác minh có hay không |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/tickets` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/tickets/add` — biểu mẫu tạo phiếu

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`ticket_list_viewport.png`](../evidence/ticket_list_viewport.png) | Danh sách **rỗng**; Tickets Summary đủ **5 trạng thái**; đủ 10 cột gồm `Department` và `Service` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
