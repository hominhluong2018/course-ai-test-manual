# Module 12 — Credit Notes (Giấy báo có)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `CN` |
| **Tên trên website** | Credit Notes |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 25–32 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/credit_notes` | Danh sách giấy báo có |
| `/admin/credit_notes/credit_note` | Tạo giấy báo có mới |

## Quan sát được

**Cột bảng:** `Credit Note #` · `Credit Note Date` · `Customer` · `Status` · `Project` · `Reference #` · `Amount` · `Remaining Amount`

**Nút thanh công cụ:** `New Credit Note` · `Export`

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ **Có** — cột `Status` |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Liên quan tiền, và là nghiệp vụ hoàn/khấu trừ** — sai là trả tiền nhầm cho khách
- Có **số dư còn lại** (`Remaining Amount`) — giá trị **dẫn xuất**, giảm dần khi áp vào hoá đơn. Cùng loại lỗi khó thấy như `Partially Paid` của `INV`
- Gắn ngược vào Hoá đơn → thay đổi ở đây ảnh hưởng số liệu module khác
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` · `ITEM` · `INV` đã recon xong — giấy báo có áp vào hoá đơn.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Tập trạng thái đầy đủ | Không có tầng API để đọc enum; chưa mở bộ lọc |
| Cách `Remaining Amount` được tính và giảm | Chưa mở chi tiết |
| Luồng áp giấy báo có vào hoá đơn | Chưa mở |
| Luồng hoàn tiền (refund) | Chưa xác minh có hay không |
| Biểu mẫu tạo — số trường, bảng dòng hàng | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/credit_notes` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/credit_notes/credit_note` — biểu mẫu tạo mới

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`cn_list_viewport.png`](../evidence/cn_list_viewport.png) | Danh sách **rỗng**; đủ 8 cột gồm `Remaining Amount` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
