# Module 09 — Items (Sản phẩm & Dịch vụ)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `ITEM` |
| **Tên trên website** | Items |
| **Bí danh** | Invoice Items |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 20–26 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/invoice_items` | Danh sách sản phẩm/dịch vụ + quản lý Nhóm |

## Quan sát được

**Cột bảng:** `Description` · `Long Description` · `Rate` · `Tax 1` · `Tax 2` · `Unit` · `Group Name`

**Bảng Nhóm (Groups)** có cột riêng: `ID` · `Group Name`

**Nút thanh công cụ:** `New Item` · `Import Items` · `Groups` · `Export` · `Bulk Actions`

Có bộ chọn thuế trong biểu mẫu với giá trị quan sát được: `No Tax`.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Nhập từ tệp + Hành động hàng loạt |
| Status flow | Không |
| Entity con | **Nhóm sản phẩm** — quản lý ngay trong màn hình này, không có route riêng |

## Lý do risk 🟡

- **Không trực tiếp là giao dịch tiền**, nhưng là **danh mục nguồn** cho 4 module 🔴: `EST` · `PROP` · `INV` · `CN`
- Sai đơn giá hoặc sai thuế ở đây thì **lan sang mọi chứng từ** tạo sau đó
- CRUD thường, ít trường
- Mức phủ tài liệu ⬜ Trắng

## Thứ tự khảo sát

**Phải recon TRƯỚC** `EST` · `PROP` · `INV` · `CN` — 4 module đó lấy dòng hàng từ đây.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Biểu mẫu tạo sản phẩm — số trường, ràng buộc | Chưa mở |
| Danh sách thuế đầy đủ trong bộ chọn `Tax 1` / `Tax 2` | Chưa mở dropdown; danh mục thuế nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| Danh sách `Unit` (đơn vị tính) | Chưa mở |
| Luồng Nhập sản phẩm từ tệp | Chưa mở |
| Hành động hàng loạt có những gì | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/invoice_items` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- Màn hình sau nút `Groups`
- Màn hình sau nút `Import Items`

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`item_list_viewport.png`](../evidence/item_list_viewport.png) | Thanh công cụ `New Item` · `Import Items` · `Groups`; `Tax 1` / `Tax 2` hiển thị dạng phần trăm; cột `Unit` và `Group Name` đang trống |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
