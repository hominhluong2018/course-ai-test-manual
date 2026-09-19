# Module 04 — Leads (Khách hàng tiềm năng)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `LEAD` |
| **Tên trên website** | Leads |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 35–45 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/leads` | Danh sách |
| `/admin/leads/index` | Cùng màn hình — truy cập trực tiếp được (đã xác minh) |

## Quan sát được

**Cột bảng:** `#` · `Name` · `Company` · `Email` · `Phone` · `Value` · `Tags` · `Assigned` · `Status` · `Source` · `Last Contact` · `Created`

**Nút thanh công cụ:** `New Lead` · `Export` · `Bulk Actions` · `Save` (lưu bộ lọc)

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Hành động hàng loạt |
| Status flow | ✅ **Có** — cột `Status`, kèm cột `Source` (nguồn khách) |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Dữ liệu cá nhân** của người chưa phải khách hàng (email, điện thoại)
- Mang **giá trị tiền** — cột `Value`
- Có **luồng chuyển đổi sang Khách hàng**: thao tác một chiều, sai là mất dấu dữ liệu
- Có phân công người phụ trách (`Assigned`) → chạm tới phân quyền
- Mức phủ tài liệu ⬜ Trắng

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| **Chế độ Kanban** | ✅ Đã xác minh **có 2 nút chuyển chế độ xem** cạnh `New Lead`; chưa mở chế độ Kanban vì danh sách rỗng |
| ⚠️ **Danh sách RỖNG** (`No entries found`) | Không quan sát được status flow, hành động trên dòng, phân trang. **Phải tự tạo dữ liệu trước khi recon** — môi trường không dùng chung nên được phép |
| Danh sách trạng thái đầy đủ | Hệ thống không có tầng API để đọc enum — phải mở bộ lọc hoặc màn hình chi tiết |
| Luồng chuyển đổi Lead → Customer | Chưa mở |
| Nơi cấu hình Trạng thái và Nguồn khách | Nghi nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| Biểu mẫu web thu Lead từ bên ngoài | Chưa xác minh |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/leads` — có ảnh
- `/admin/leads/index`

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`lead_list_viewport.png`](../evidence/lead_list_viewport.png) | Danh sách **rỗng** (`No entries found`); đủ 12 cột; **2 nút chuyển chế độ xem** cạnh `New Lead` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
