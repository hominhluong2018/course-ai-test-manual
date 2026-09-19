# Module 07 — Proposals (Đề xuất báo giá)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `PROP` |
| **Tên trên website** | Proposals |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 30–40 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/proposals` | Danh sách đề xuất |
| `/admin/proposals/proposal` | Tạo đề xuất mới |
| `/admin/proposals/list_proposals?status={1..6}` | Lọc theo trạng thái — **đã quan sát 6 giá trị** |

## Quan sát được

**Cột bảng:** `Proposal #` · `Subject` · `To` · `Total` · `Date` · `Open Till` · `Project` · `Tags` · `Date Created` · `Status`

**Nút thanh công cụ:** `New Proposal` · `Export`

**Trạng thái đọc được từ Bảng điều khiển** (khối "Proposal overview"): `Draft` · `Sent` · `Open` · `Revised` · `Declined` · `Accepted` — **6 trạng thái**, khớp với `status=1..6`.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ **Có** — 6 trạng thái |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Liên quan tiền** — cột `Total`
- Luồng trạng thái 6 bước, có trạng thái `Revised` (sửa lại sau khi gửi) → dễ sinh lỗi chuyển trạng thái sai chiều
- Có ngày hết hiệu lực (`Open Till`) → hành vi theo thời gian
- Gửi ra ngoài cho khách hàng / người nhận (`To`)
- Mức phủ tài liệu ⬜ Trắng

## Khác biệt so với Báo giá (`EST`) — cần làm rõ khi recon

Hai module rất giống nhau về hình thức nhưng khác tập trạng thái:

| | `EST` Báo giá | `PROP` Đề xuất |
|---|---|---|
| Trạng thái | Draft · Not Sent · Sent · Expired · Declined · Accepted | Draft · Sent · **Open** · **Revised** · Declined · Accepted |
| Người nhận | `Customer` | `To` (có thể là Lead, không chỉ Customer) |
| Hạn | `Expiry Date` | `Open Till` |

⚠️ **Không được dùng tài liệu của module này để suy ra module kia** — khác tập trạng thái là khác nghiệp vụ.

## Phụ thuộc

Cần `CUST` và `ITEM` đã recon xong. Có thể gửi cho `LEAD` → nên recon sau `LEAD`.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Ánh xạ `status=1..6` ↔ tên trạng thái hiển thị | Chưa mở từng bộ lọc |
| Trường `To` nhận Khách hàng hay cả Khách hàng tiềm năng | Chưa mở biểu mẫu |
| Biểu mẫu tạo đề xuất — số trường, bảng dòng hàng | Chưa mở |
| Luồng chuyển Đề xuất → Báo giá / Hoá đơn | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/proposals` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/proposals/proposal` — biểu mẫu tạo mới
- `/admin/proposals/list_proposals?status={1..6}` — 6 đường dẫn lọc

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`prop_list_viewport.png`](../evidence/prop_list_viewport.png) | 5 bản ghi: 1 `Sent` + 4 `Open`; đủ 10 cột |
| [`dash_overview_viewport.png`](../evidence/dash_overview_viewport.png) | Khối "Proposal overview": 1 Sent 20% + 4 Open 80% = 100% → trạng thái **loại trừ nhau** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
