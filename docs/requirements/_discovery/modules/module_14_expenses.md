# Module 14 — Expenses (Chi phí)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `EXP` |
| **Tên trên website** | Expenses |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 30–38 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/expenses` | Danh sách chi phí |
| `/admin/expenses/expense` | Ghi nhận chi phí mới |

## Quan sát được

**Cột bảng:** `Category` · `Amount` · `Name` · `Receipt` · `Date` · `Project` · `Customer` · `Invoice` · `Reference #` · `Payment Mode`

**Nút thanh công cụ:** `Record Expense` · `Import Expenses` · `Export` · `Bulk Actions`

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Nhập từ tệp + Hành động hàng loạt |
| Status flow | Không có cột trạng thái |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Liên quan tiền** — chi tiền ra
- Có cột **`Invoice`** → chi phí **tái lập hoá đơn cho khách** (billable expense): lỗi ở đây là tính tiền sai cho khách hàng
- Có **tệp đính kèm chứng từ** (`Receipt`) → luồng tải tệp lên, cần kiểm loại tệp và kích thước
- Gắn vào cả `PRJ` lẫn `CUST` → ảnh hưởng số liệu của hai module khác
- Là nguồn dữ liệu của 2 báo cáo (`Expenses`, `Expenses vs Income`)
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` và `INV` đã recon xong. Liên quan `PRJ` nhưng không chặn.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Danh mục `Category` cấu hình ở đâu | Nghi nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| Luồng tái lập hoá đơn cho khách từ chi phí | Chưa mở |
| Ràng buộc tệp `Receipt` — loại tệp, kích thước tối đa | Chưa mở biểu mẫu |
| Chi phí lặp lại (recurring expense) | Chưa xác minh có hay không |
| Luồng Nhập chi phí từ tệp | Chưa mở |
| Danh sách `Payment Mode` | Nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/expenses` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/expenses/expense` — biểu mẫu ghi nhận chi phí
- Màn hình sau nút `Import Expenses`

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`exp_list_viewport.png`](../evidence/exp_list_viewport.png) | Danh sách **rỗng**; đủ 10 cột gồm `Receipt` và `Invoice`; nút `Record Expense` · `Import Expenses` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
