# Module 05 — Estimate Request (Yêu cầu báo giá)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `ESTREQ` |
| **Tên trên website** | Estimate Request |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 20–28 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/estimate_request` | Danh sách yêu cầu báo giá |

## Quan sát được

**Cột bảng:** `#` · `Email` · `Tags` · `Assigned` · `Status` · `Created`

**Nút thanh công cụ:** `New Form` · `Export`

Nút `New Form` cho thấy module có **trình dựng biểu mẫu** — biểu mẫu công khai để người ngoài gửi yêu cầu vào hệ thống.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (trên biểu mẫu) |
| Status flow | ✅ **Có** — cột `Status` |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🟡

- Là **cửa vào từ bên ngoài** — dữ liệu do người ngoài hệ thống nhập, cần kiểm validation kỹ
- Nhưng chưa chạm tới tiền, và số trường ít hơn hẳn các module bán hàng
- Mức phủ tài liệu ⬜ Trắng

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Trình dựng biểu mẫu có những loại trường nào | Chưa mở `New Form` |
| Biểu mẫu công khai hiển thị ở URL nào cho người ngoài | Chưa xác minh — có thể thuộc cổng Khách hàng (`AMB-SYS-03`) |
| Luồng chuyển Yêu cầu → Báo giá (`EST`) | Chưa mở |
| Danh sách trạng thái đầy đủ | Chưa mở bộ lọc |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/estimate_request` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- Màn hình sau nút `New Form` — trình dựng biểu mẫu

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`estreq_list_viewport.png`](../evidence/estreq_list_viewport.png) | Danh sách **rỗng**; đủ 6 cột; nút `New Form` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
