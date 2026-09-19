# Module 10 — Invoices (Hoá đơn)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `INV` |
| **Tên trên website** | Invoices |
| **Bí danh** | Recurring Invoices (Hoá đơn định kỳ) — vẫn thuộc module này |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 50–65 — giảm từ 70–90 sau khi tách `PAY` ra module riêng |

## Route

| Đường dẫn | Màn hình | Ghi chú |
|---|---|---|
| `/admin/invoices` | Danh sách hoá đơn | |
| `/admin/invoices/invoice` | Tạo hoá đơn mới | |
| `/admin/invoices/recurring` | **Hoá đơn định kỳ** | ✅ đã xác minh vào được |
| `/admin/invoices/list_invoices?status={1,2,3,4,6}` | Lọc theo trạng thái | **đã quan sát 5 giá trị, khuyết `status=5`** |
| `/admin/invoices/list_invoices?filter=not_sent` | Lọc "chưa gửi" | |
| `/admin/payments` | ➡️ Thuộc module **`PAY`** — xem [module_11_payments.md](module_11_payments.md) | |

## Invoices — màn hình danh sách

**Cột bảng:** `Invoice #` · `Amount` · `Total Tax` · `Date` · `Customer` · `Project` · `Tags` · `Due Date` · `Status`

**Nút thanh công cụ:** `Create New Invoice` · `Batch Payments` · `Recurring Invoices` · `Filter by status` · `Export`

Có bộ lọc lưu được với tổ hợp quan sát thấy: `Unpaid, Paid, Partially Paid` và nút `Apply and Save`.

**Trạng thái đọc được từ Bảng điều khiển** (khối "Invoice overview"): `Draft` · `Not Sent` · `Unpaid` · `Partially Paid` · `Overdue` · `Paid` — **6 trạng thái**.

⚠️ Đường dẫn lọc khuyết `status=5`. Evidence của module `RPT` cho manh mối mạnh: trang Báo cáo Bán hàng ghi nguyên văn *"Cancelled invoices are excluded from the report"* → tồn tại trạng thái **`Cancelled`** **không** hiển thị trên Bảng điều khiển. Nhiều khả năng `status=5` chính là nó. **Phải xác minh ở tầng recon** — trạng thái không xuất hiện ở đâu cả là thứ dễ bị bỏ sót nhất khi viết TC.

⚠️ Theo mẫu `EST`, `Not Sent` nhiều khả năng cũng là **cờ gửi riêng** chứ không cùng tập với `Paid`/`Unpaid`. Cần kiểm riêng cho `INV`, **không** suy từ `EST`.

⚠️ **Lệch số liệu cần điều tra:** Bảng điều khiển tính theo mẫu số **7** hoá đơn (1 Draft = 14.29%), nhưng danh sách báo *"Showing 1 to 6 of 6 entries"*. Nghi bản ghi thứ 7 bị lọc khỏi danh sách mặc định — có thể chính là bản `Cancelled`.

## Payments — đã tách sang module riêng

Thanh toán có mã chứng từ riêng (`Payment #`), màn hình danh sách riêng và báo cáo riêng, nên được cấp prefix **`PAY`** ngày 19-09-2026.

➡️ Toàn bộ nội dung ở [module_11_payments.md](module_11_payments.md).

⚠️ **Lằn ranh khi viết REQ:** trạng thái `Paid` / `Partially Paid` / `Unpaid` là **kết quả tính toán của hoá đơn** → neo về **`INV`**. Hành vi *ghi nhận một khoản tiền* → neo về **`PAY`**. Bản ghi thanh toán vẫn **chỉ tạo được từ trong màn hình hoá đơn**, nên đường vào nằm ở `INV` còn entity nằm ở `PAY`.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ **Có** — 6 trạng thái, có trạng thái phụ thuộc thời gian (`Overdue`) và phụ thuộc số tiền đã trả (`Partially Paid`) |
| Số tab | ❔ chưa mở chi tiết |

## Lý do risk 🔴

- **Là module tiền quan trọng nhất hệ thống** — thu tiền thật
- Trạng thái **dẫn xuất từ dữ liệu khác**: `Partially Paid` phụ thuộc tổng tiền đã thanh toán, `Overdue` phụ thuộc `Due Date` và thời gian hiện tại → loại lỗi khó thấy nhất
- Có **hoá đơn định kỳ** — tự sinh bản ghi theo lịch, chạy nền
- Là module trung tâm của nhóm tài chính — `PAY`, `CN`, `SUB`, `EXP` đều trỏ về hoá đơn
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` và `ITEM` đã recon xong. `CN` (Giấy báo có) và `EXP` (Chi phí) đều trỏ ngược về hoá đơn.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| ❔ **Batch Payments** | Nút nằm trên thanh công cụ của Invoices nhưng nghiệp vụ thuộc `PAY` → ghi ở [module_11_payments.md](module_11_payments.md) |
| Ánh xạ `status=1,2,3,4,6` ↔ tên trạng thái, và `status=5` là gì | Chưa mở từng bộ lọc |
| Biểu mẫu tạo hoá đơn — số trường, bảng dòng hàng, cách tính thuế và chiết khấu | Chưa mở |
| Cấu hình Hoá đơn định kỳ — chu kỳ, điều kiện dừng | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng chưa mở được, hoặc cố ý không thử |

**✅ Đã mở thật:**

- `/admin/invoices` — có ảnh
- `/admin/invoices/recurring` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/invoices/invoice` — biểu mẫu tạo mới
- `/admin/invoices/list_invoices?status={1,2,3,4,6}` và `?filter=not_sent` — 6 đường dẫn lọc

**❔ Chưa xác minh được:**

- **Batch Payments** — nút có thật trên thanh công cụ nhưng route đoán `/admin/payments/batch_payments` trả **404**. Đường dẫn thật **chưa biết**

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`inv_list_viewport.png`](../evidence/inv_list_viewport.png) | Thanh công cụ đủ 4 nút gồm `Batch Payments` và `Recurring Invoices`; badge `Paid` / `Unpaid`; "Showing 1 to 6 of 6 entries" |
| [`inv_recurring_list_viewport.png`](../evidence/inv_recurring_list_viewport.png) | **Rỗng**; cột `Frequency` · `Cycles Remaining` · `Last Child Invoice Date` · `Next Invoice Date` |
| [`rpt_sales_viewport.png`](../evidence/rpt_sales_viewport.png) | Dòng *"Cancelled invoices are excluded from the report"* → tồn tại trạng thái **`Cancelled`** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
