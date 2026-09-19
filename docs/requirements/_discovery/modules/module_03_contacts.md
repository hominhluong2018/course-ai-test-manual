# Module 03 — Contacts (Người liên hệ của khách hàng)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `CTC` |
| **Tên trên website** | Contacts |
| **Bí danh** | Customer Contacts · Customer Admins (tab con trong hồ sơ khách hàng) |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 30–40 |

> Trạng thái recon là bản gốc ở [`../../README.md`](../../README.md) — tệp này không nhân bản.
>
> 📌 **Tách khỏi `CUST` ngày 19-09-2026** theo quyết định của người dùng. Contact là entity có **vòng đời riêng** (tài khoản đăng nhập cổng khách hàng, cờ `Active`, `Last Login`), nên được cấp prefix riêng thay vì coi là tab phụ của Customers.
>
> ⚠️ Vì sao prefix là `CTC` chứ không phải `CONT`: `CONT` quá giống `CTR` (Contracts), grep ra dễ lẫn.

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/clients/all_contacts` | Danh sách Người liên hệ **toàn hệ thống** — chỉ xem |
| `/admin/clients/client/{id}` › tab `Contacts` | Danh sách người liên hệ **của một khách hàng** — nơi tạo/sửa |
| `/admin/clients/client/{id}` › tab `Customer Admins` | Gán nhân viên phụ trách khách hàng — ❔ chưa mở |

⚠️ **Không có controller `/admin/contacts`** — đã xác minh, trả **404**. Entity này sống hoàn toàn dưới `clients`.

## Quan sát được — danh sách toàn hệ thống

**Cột bảng:** `First Name` · `Last Name` · `Email` · `Company` · `Phone` · `Position` · `Last Login` · `Active`

**Nút:** chỉ `Export`. **Không có nút tạo** trên màn hình này — người liên hệ chỉ tạo được từ trong hồ sơ khách hàng.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (tạo/sửa từ trong hồ sơ khách hàng) · ❌ tạo trực tiếp từ danh sách toàn hệ thống |
| Status flow | Cờ **`Active`** bật/tắt — đã thấy bản ghi đang tắt trên evidence |
| Số tab | 0 ở màn hình danh sách |

## Bằng chứng cho thấy đây là entity riêng, không phải tab phụ

| Dấu hiệu | Quan sát được trên evidence |
|---|---|
| **Có tài khoản đăng nhập riêng** | Cột `Last Login` mang giá trị thật (*one year ago*, *4 weeks ago*) → người liên hệ **đăng nhập được vào cổng khách hàng** |
| **Có trạng thái bật/tắt độc lập** | Cột `Active` là công tắc riêng từng người liên hệ; đã thấy một bản ghi **đang tắt** |
| **Có danh sách toàn cục riêng** | `/admin/clients/all_contacts` tách khỏi `/admin/clients` |
| **Có endpoint riêng** | `POST /admin/clients/all_contacts` — **không** theo mẫu `POST /<module>/table` như phần còn lại của hệ thống |
| **Được đếm riêng ở thống kê** | Khối Customers Summary đếm tách: `367 Active Contacts` · `5 Inactive Contacts` · `0 Contacts Logged In Today` |

## Lý do risk 🔴

- **Là điểm xác thực thứ hai của hệ thống** — sau `LOGIN` của nhân viên. Người liên hệ đăng nhập vào cổng khách hàng bằng tài khoản ở đây
- **Dữ liệu cá nhân**: họ tên, email, điện thoại, chức danh
- Tắt nhầm `Active` là **chặn khách hàng đăng nhập** mà không có cảnh báo rõ
- Cổng khách hàng hiện **chưa khảo sát** (`AMB-SYS-03`) nên không kiểm được đầu bên kia của luồng đăng nhập này
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Phải recon **sau `CUST`** — người liên hệ luôn thuộc một khách hàng, và màn hình tạo nằm trong hồ sơ khách hàng.

Liên quan `AMB-SYS-03`: xác minh đầy đủ luồng đăng nhập của người liên hệ chỉ làm được khi có URL và tài khoản cổng khách hàng.

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng chưa mở được, hoặc cố ý không thử |

**✅ Đã mở thật:**

- `/admin/clients/all_contacts` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- Tab `Contacts` trong hồ sơ khách hàng — mới thấy nhãn tab, chưa mở
- Tab `Customer Admins` trong hồ sơ khách hàng — mới thấy nhãn tab, chưa mở
- Biểu mẫu tạo / sửa người liên hệ

**❔ Chưa xác minh được:**

- Màn hình đăng nhập của người liên hệ ở **cổng khách hàng** — chưa có URL và tài khoản (`AMB-SYS-03`)

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Đặc tả trường của biểu mẫu người liên hệ (bắt buộc · độ dài · thông báo lỗi) | Chưa mở biểu mẫu |
| Quyền của người liên hệ — xem được gì trên cổng khách hàng | `AMB-SYS-03` |
| Một khách hàng có nhiều người liên hệ thì ai là `Primary Contact`, đổi thế nào | Chưa mở tab |
| Tắt `Active` thì người liên hệ còn đăng nhập được không | Cần cổng khách hàng để kiểm |
| Chỉ số `Contacts Logged In Today` tính theo quy tắc nào | Chưa xác minh |
| Người liên hệ có nhận email hệ thống không (hoá đơn, hợp đồng) | Chưa xác minh — liên quan `INV`, `CTR` |

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`ctc_list_viewport.png`](../evidence/ctc_list_viewport.png) | Danh sách toàn hệ thống: đủ 8 cột; cột `Last Login` **có giá trị thật**; có bản ghi đang **tắt** `Active`; chỉ có nút `Export`, **không có nút tạo** |
| [`cust_list_viewport.png`](../evidence/cust_list_viewport.png) | Khối Customers Summary đếm riêng người liên hệ: `367 Active Contacts` · `5 Inactive Contacts` · `0 Contacts Logged In Today` |
| [`cust_detail_tabs_viewport.png`](../evidence/cust_detail_tabs_viewport.png) | Hồ sơ khách hàng có tab `Contacts` và tab con `Customer Admins` — nơi tạo/sửa người liên hệ |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
