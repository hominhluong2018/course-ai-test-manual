# Module 11 — Payments (Thanh toán)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `PAY` |
| **Tên trên website** | Payments |
| **Bí danh** | Payments Received (tên trong Reports) · Batch Payments (chức năng ghi nhận hàng loạt) |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 18–25 |

> Trạng thái recon là bản gốc ở [`../../README.md`](../../README.md) — tệp này không nhân bản.
>
> 📌 **Tách khỏi `INV` ngày 19-09-2026** theo quyết định của người dùng. Payment là chứng từ có mã riêng (`Payment #`), có màn hình danh sách riêng và có báo cáo riêng, nên được cấp prefix riêng.

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/payments` | Danh sách Thanh toán — **chỉ xem**, không có nút tạo |
| `/admin/reports/sales` › `Payments Received` | Báo cáo thanh toán (thuộc module `RPT`) |
| ❔ Batch Payments | Nút có thật trên thanh công cụ của Invoices; route **chưa biết** |

## Quan sát được

**Cột bảng:** `Payment #` · `Invoice #` · `Payment Mode` · `Transaction ID` · `Customer` · `Amount` · `Date`

**Nút:** chỉ `Export`. **Không có thanh công cụ tạo mới** — đây là điểm khác biệt rõ nhất so với mọi module chứng từ khác.

Dữ liệu quan sát được: 1 bản ghi, `Payment Mode = Bank`, cột `Transaction ID` **để trống**.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ❌ tạo trực tiếp — bản ghi **chỉ sinh ra từ màn hình Hoá đơn**. Sửa/xoá: ❔ chưa xác minh |
| Status flow | Không có cột trạng thái |
| Số tab | 0 |

## Quan hệ với `INV` — đọc kỹ trước khi viết TC

Payment **không tồn tại độc lập**: mỗi bản ghi luôn trỏ về một hoá đơn qua cột `Invoice #`. Nhưng nó **không phải** một thuộc tính của hoá đơn:

| Dấu hiệu | Ý nghĩa |
|---|---|
| Có **mã chứng từ riêng** `Payment #` | Là entity, không phải trường của hoá đơn |
| Có **màn hình danh sách riêng** trong menu Sales | Người dùng tra cứu thanh toán độc lập với hoá đơn |
| Có **báo cáo riêng** `Payments Received` | Được coi là đối tượng nghiệp vụ ở tầng báo cáo |
| Một hoá đơn có **nhiều** thanh toán | Là nguồn gốc của trạng thái `Partially Paid` bên `INV` |

⚠️ **Lằn ranh khi viết TC:** trạng thái `Paid` / `Partially Paid` / `Unpaid` thuộc về **`INV`** (là kết quả tính toán), còn hành vi *ghi nhận một khoản tiền* thuộc về **`PAY`**. Test tính đúng của trạng thái hoá đơn thì neo REQ vào `INV`; test biểu mẫu ghi nhận thanh toán thì neo vào `PAY`.

## Lý do risk 🔴

- **Là nơi tiền thật được ghi nhận vào hệ thống** — sai số tiền hoặc sai hoá đơn là sai sổ sách
- Có `Transaction ID` và `Payment Mode` → **chạm tới cổng thanh toán bên ngoài**
- Ghi sai một khoản làm **trạng thái hoá đơn tính sai theo** (`Partially Paid`, `Paid`) → lỗi lan sang `INV`
- Không xoá/sửa được từ danh sách nên **khó khắc phục khi ghi nhầm** — càng cần test kỹ đường vào
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Phải recon **sau `CUST` và `INV`** — không có hoá đơn thì không tạo được thanh toán để quan sát.

⚠️ Môi trường hiện chỉ có **1 bản ghi thanh toán**. Muốn kiểm thanh toán một phần, nhiều lần thanh toán, hoặc thanh toán vượt số tiền hoá đơn thì **phải tự tạo dữ liệu** — môi trường không dùng chung nên được phép.

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng chưa mở được, hoặc cố ý không thử |

**✅ Đã mở thật:**

- `/admin/payments` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- Màn hình ghi nhận thanh toán **bên trong hoá đơn** — đường vào duy nhất để tạo bản ghi
- Báo cáo `Payments Received` trong `/admin/reports/sales` — mới thấy tiêu đề

**❔ Chưa xác minh được:**

- **Batch Payments** — nút có thật trên thanh công cụ của Invoices (thấy trên `inv_list_viewport.png`), nhưng route đoán `/admin/payments/batch_payments` trả **404**. Đường dẫn thật **chưa biết**

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Biểu mẫu ghi nhận thanh toán — số trường, ràng buộc số tiền | Chưa mở; nằm trong màn hình hoá đơn |
| Sửa / xoá một bản ghi thanh toán có được không | Danh sách không có nút; chưa mở chi tiết |
| Danh sách `Payment Mode` đầy đủ | Danh mục nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| `Transaction ID` do ai điền — người dùng nhập hay cổng thanh toán trả về | Bản ghi duy nhất đang để **trống** ô này |
| Hành vi khi thanh toán **vượt** số tiền hoá đơn | Cần tự tạo dữ liệu để thử |
| Nhiều thanh toán cho một hoá đơn → `Partially Paid` tính thế nào | Cần tự tạo dữ liệu; kết quả neo REQ về `INV` |
| Thanh toán có sinh email gửi khách hàng không | Chưa xác minh — liên quan `CTC` và `AMB-SYS-03` |

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`pay_list_viewport.png`](../evidence/pay_list_viewport.png) | Danh sách Payments: 1 bản ghi, `Payment Mode = Bank`, `Transaction ID` trống; đủ 7 cột; **không có thanh công cụ tạo mới**, chỉ có `Export` |
| [`inv_list_viewport.png`](../evidence/inv_list_viewport.png) | Nút **`Batch Payments`** có thật trên thanh công cụ của Invoices — cơ sở để mở `❔` ở trên |
| [`rpt_sales_viewport.png`](../evidence/rpt_sales_viewport.png) | Báo cáo `Payments Received` tồn tại trong nhóm Sales Report |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
