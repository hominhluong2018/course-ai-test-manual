# Module 20 — Reports (Báo cáo)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `RPT` |
| **Tên trên website** | Reports |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | **40–55** — nâng từ ước lượng ban đầu 25–32 sau khi evidence cho thấy riêng báo cáo Bán hàng đã có 10 báo cáo con |

## Route — 6 báo cáo

| Đường dẫn | Báo cáo | Đọc dữ liệu từ module |
|---|---|---|
| `/admin/reports/sales` | Sales Reports — tiêu đề trang "Sales Reports" | `INV` · `EST` · `PROP` · `CN` · `ITEM` |
| `/admin/reports/expenses` | Expenses | `EXP` |
| `/admin/reports/expenses_vs_income` | Expenses vs Income | `EXP` · `INV` |
| `/admin/reports/leads` | Leads | `LEAD` |
| `/admin/staff/timesheets?view=all` | Timesheets overview | `TIME` |
| `/admin/reports/knowledge_base_articles` | KB Articles | `KB` |

⚠️ "Timesheets overview" nằm trong menu Reports nhưng route thuộc `/admin/staff/` — **không** phải `/admin/reports/`. Ghi nhận để không nhầm khi viết locator.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ❌ — chỉ đọc |
| Status flow | Không |
| Số tab | ❔ chưa mở từng báo cáo |

## Lý do risk 🟡

- **Không ghi dữ liệu** — hỏng thì hiển thị sai chứ không làm hỏng dữ liệu
- Nhưng là **nơi lãnh đạo ra quyết định** — số sai ở đây gây hậu quả gián tiếp lớn
- Phụ thuộc **5 module khác**: chỉ kiểm được sau khi những module đó đã recon xong, nếu không thì không biết số đúng là bao nhiêu mà đối chiếu

## Thứ tự khảo sát

**Recon SAU** `INV` · `EXP` · `LEAD` · `KB` · `TIME` — không có số gốc thì không kiểm chứng được báo cáo.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Nội dung cụ thể từng báo cáo — bộ lọc, biểu đồ, cột | Chỉ mở `reports/sales`, chưa mở 5 báo cáo còn lại. ⚠️ Riêng `reports/sales` đã chứa **7 báo cáo con** (Invoices · Items · Payments Received · Credit Notes · Proposals · Estimates · Customers) + **3 báo cáo biểu đồ** (Total Income · Payment Modes · Total Value By Customer Groups) — 5 báo cáo còn lại có thể cũng lớn tương tự |
| 🔴 Quy tắc loại trừ dữ liệu khỏi báo cáo | Evidence ghi nguyên văn *"Cancelled invoices are excluded from the report"*. **Phải có TC** kiểm đúng quy tắc loại trừ này — và nó tiết lộ trạng thái `Cancelled` của `INV` |
| Báo cáo có xuất tệp được không | Chưa xác minh |
| Bộ lọc khoảng thời gian có những lựa chọn nào | Chưa mở |
| Số liệu có khớp với dữ liệu gốc không | Cần recon module nguồn trước |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/reports/sales` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/reports/expenses`
- `/admin/reports/expenses_vs_income`
- `/admin/reports/leads`
- `/admin/reports/knowledge_base_articles`
- `/admin/staff/timesheets?view=all`
- **10 báo cáo con** bên trong `reports/sales` — mới thấy tiêu đề, chưa bung nội dung

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`rpt_sales_viewport.png`](../evidence/rpt_sales_viewport.png) | **7 báo cáo con** (Invoices · Items · Payments Received · Credit Notes · Proposals · Estimates · Customers) + **3 báo cáo biểu đồ** (Total Income · Payment Modes · Total Value By Customer Groups); dòng cảnh báo về hoá đơn `Cancelled` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
