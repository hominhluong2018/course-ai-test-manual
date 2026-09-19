# Module 02 — Customers (Khách hàng)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `CUST` |
| **Tên trên website** | Customers |
| **Bí danh** | Clients (tên route) |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 55–70 — giảm từ 85–110 sau khi tách `CTC` ra module riêng |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/clients` | Danh sách khách hàng |
| `/admin/clients/client` | Tạo khách hàng mới |
| `/admin/clients/client/{id}` | Chi tiết / Sửa khách hàng — **19 tab** |
| `/admin/clients/import` | Nhập khách hàng từ tệp |
| `/admin/clients/all_contacts` | ➡️ Thuộc module **`CTC`** — xem [module_03_contacts.md](module_03_contacts.md) |

## Customers — màn hình danh sách

**Cột bảng:** `#` · `Company` · `Primary Contact` · `Primary Email` · `Phone` · `Active` · `Groups` · `Date Created`

**Nút thanh công cụ:** `New Customer` · `Import Customers` · `Contacts` · `Export` · `Bulk Actions`

Có khối **Customers Summary** (thống kê) phía trên bảng và hộp thoại **Bulk Actions** riêng.

## Contacts — đã tách sang module riêng

Người liên hệ có vòng đời riêng (tài khoản đăng nhập cổng khách hàng, cờ `Active`, `Last Login`) nên được cấp prefix **`CTC`** ngày 19-09-2026.

➡️ Toàn bộ nội dung ở [module_03_contacts.md](module_03_contacts.md).

Trong module `CUST` chỉ giữ lại **điểm giao**: nút `Contacts` trên thanh công cụ và tab `Contacts` trong hồ sơ khách hàng là **đường vào** của module `CTC`.

## Customer detail (chi tiết khách hàng) — 19 tab, đã đếm trên DOM

```
Profile · Contacts · Notes · Statement · Invoices · Payments · Proposals
Credit Notes · Estimates · Subscriptions · Expenses · Contracts · Projects
Tasks · Tickets · Files · Vault · Reminders · Map
```

Riêng tab **Profile** chia thành 3 tab con: `Customer Details` · `Billing & Shipping` · `Customer Admins`.

**Số trường biểu mẫu đếm được trên DOM: 37.**

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Nhập từ tệp + Hành động hàng loạt |
| Status flow | `Active` / `Inactive` (khách hàng) · `Active` (người liên hệ) |
| Số tab | 19 + 3 tab con |

## Lý do risk 🔴

- **Dữ liệu khách hàng** — thông tin cá nhân, địa chỉ thanh toán
- **10 module khác phụ thuộc** vào nó (xem bản đồ phụ thuộc ở index mục 4)
- Là **nút trung tâm**: hồ sơ khách hàng tổng hợp dữ liệu của 10 module khác qua 19 tab
- Biểu mẫu lớn nhất hệ thống (37 trường, 19 tab) → rất nhiều đường đi và validation
- Mức phủ tài liệu ⬜ Trắng

## ⚠️ Ranh giới với `CTC` — đọc trước khi viết REQ

| Thuộc `CUST` | Thuộc `CTC` |
|---|---|
| Hồ sơ doanh nghiệp: `Company`, `VAT Number`, `Website`, `Groups`, `Currency`, `Default Language`, địa chỉ thanh toán / giao hàng | Từng người liên hệ: họ tên, email, điện thoại, chức danh |
| Cờ `Active` **của khách hàng** | Cờ `Active` **của người liên hệ** — hai thứ khác nhau |
| Nhập khách hàng từ tệp | Tài khoản đăng nhập cổng khách hàng, `Last Login` |
| 19 tab tổng hợp dữ liệu module khác | Nội dung bên trong tab `Contacts` |

⚠️ Khối **Customers Summary** đếm **cả hai**: `2032 Total Customers` thuộc `CUST`, còn `367 Active Contacts` · `5 Inactive Contacts` · `0 Contacts Logged In Today` thuộc `CTC`. Viết REQ cho khối thống kê này phải neo đúng từng chỉ số về đúng module.

## Phát hiện tầng network của riêng module

| Endpoint | Ghi nhận |
|---|---|
| `POST /admin/clients/table` | Bảng danh sách khách hàng |
| `POST /admin/clients/all_contacts` | Bảng người liên hệ — ➡️ thuộc `CTC`. Ghi ở đây vì cùng controller `clients` |

Không có controller `/admin/contacts` — đã xác minh, trả **404**.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Nội dung từng tab trong 19 tab | Tầng khám phá chỉ đếm tab, không mở từng tab |
| Đặc tả 37 trường (bắt buộc · độ dài · kiểu · thông báo lỗi) | Việc của tầng recon cấp module |
| Tab **Vault** | Chưa mở — nghi chứa dữ liệu nhạy cảm (mật khẩu lưu trữ). 🔒 Cẩn trọng khi chụp ảnh evidence |
| Hộp thoại Bulk Actions có những hành động nào | Chưa mở |
| Luồng Nhập khách hàng từ tệp | Chưa mở |
| Nhóm khách hàng (`Groups`) cấu hình ở đâu | Nghi nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/clients` — có ảnh
- `/admin/clients/client/{id}` — có ảnh (mở 1 bản ghi ở chế độ xem)

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/clients/client` — biểu mẫu tạo mới
- `/admin/clients/import` — luồng nhập từ tệp

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`cust_list_viewport.png`](../evidence/cust_list_viewport.png) | Thanh công cụ `New Customer` · `Import Customers` · `Contacts`; khối Customers Summary (2032 Total / 2023 Active / 9 Inactive / 367 Active Contacts / 5 Inactive Contacts); đủ 8 cột bảng |
| [`cust_detail_tabs_viewport.png`](../evidence/cust_detail_tabs_viewport.png) | Danh sách tab dọc + 3 tab ngang `Customer Details` · `Billing & Shipping` · `Customer Admins`; badge `Projects 4`; trường `* Company` đánh dấu bắt buộc |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
