# Module 13 — Subscriptions (Đăng ký định kỳ)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `SUB` |
| **Tên trên website** | Subscriptions |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 28–35 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/subscriptions` | Danh sách đăng ký định kỳ |
| `/admin/subscriptions/create` | Tạo đăng ký mới |

## Quan sát được

**Cột bảng:** `#` · `Subscription Name` · `Customer` · `Project` · `Status` · `Next Billing Cycle` · `Date Subscribed` · `Last Sent`

**Nút thanh công cụ:** `New Subscription` · `Export`

**Khối Subscriptions Summary** (đọc từ evidence) mang logo **`stripe`** và liệt kê đủ **8 trạng thái**:

`Not Subscribed` · `Active` · `Future` · `Past Due` · `Unpaid` · `Incomplete` · `Canceled` · `Incomplete Expired`

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ **Có — 8 trạng thái**, nhiều nhất hệ thống. Tên trạng thái (`Past Due`, `Incomplete`, `Incomplete Expired`) trùng đúng mô hình subscription của **Stripe** |
| Tích hợp ngoài | **Stripe** — xác nhận bằng logo trên khối tổng hợp |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Thu tiền lặp lại tự động** — lỗi ở đây thu sai nhiều lần, không phải một lần
- Có **tiến trình chạy nền theo lịch** (`Next Billing Cycle`, `Last Sent`) → hành vi phụ thuộc thời gian, khó kiểm bằng thao tác UI thuần
- Sinh ra hoá đơn → lỗi lan sang `INV`
- Chạm tới cổng thanh toán bên ngoài
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` · `ITEM` · `INV` đã recon xong.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| ~~Tập trạng thái đầy đủ~~ | ✅ **Đã có đủ 8** từ khối tổng hợp |
| 🔴 Điều kiện chuyển giữa 8 trạng thái | Chưa xác minh. Phụ thuộc Stripe nên **không** kiểm được hoàn toàn từ UI |
| 🔴 Kiểm thử cần môi trường Stripe sandbox nào | Chưa xác minh — cần hỏi dev |
| ⚠️ **Danh sách RỖNG** | Phải tự tạo dữ liệu trước khi recon |
| Chu kỳ thanh toán có những lựa chọn nào (tháng/quý/năm…) | Chưa mở biểu mẫu |
| Điều kiện dừng / huỷ đăng ký | Chưa mở |
| Quan hệ với Hoá đơn định kỳ của `INV` — **hai cơ chế này khác nhau thế nào** | ⚠️ Câu hỏi quan trọng, chưa trả lời được từ UI. Nghi là hai tính năng chồng lấn |
| Cách kiểm chứng tiến trình chạy nền mà không chờ hết chu kỳ | Cần quyền CSDL hoặc quyền chạy lệnh — QA **không** có (xem Năng lực kiểm thử ở danh mục) |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/subscriptions` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/subscriptions/create` — biểu mẫu tạo mới

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`sub_list_viewport.png`](../evidence/sub_list_viewport.png) | Danh sách **rỗng**; Subscriptions Summary đủ **8 trạng thái**; khối mang logo **`stripe`** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
