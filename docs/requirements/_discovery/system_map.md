# Bản Đồ Hệ Thống — Perfex CRM (khu vực Quản trị)

> **INDEX của tầng khám phá — tên tệp bất biến.** Bản đồ đã tách file: chi tiết từng module nằm trong `modules/`, xem mục **Bản đồ tài liệu** bên dưới.
>
> Tầng này **không cấp mã `REQ-XXX-NN`** — chỉ cấp **prefix**. Trạng thái recon là bản gốc ở [`../README.md`](../README.md), tệp này không nhân bản.

---

## 1. Bối cảnh khảo sát

| Mục | Giá trị |
|---|---|
| **Ngày khảo sát** | 19-09-2026 |
| **Mode** | **UI** — chỉ có hệ thống đang chạy, người dùng không cung cấp tài liệu nào |
| **Mặt đã chạy** | **Web** — khu vực quản trị `/admin` |
| **Hệ thống** | Perfex CRM `3.1.6` — ứng dụng PHP render phía máy chủ |
| **URL · tài khoản** | Lưu ở `.env`, **không** ghi vào tài liệu |
| **Vai trò đã dùng** | **01 tài khoản duy nhất** — quyền nghiệp vụ đầy đủ nhưng **không** có quyền vùng Setup |
| **Môi trường dùng chung** | **KHÔNG** — người dùng chốt 19-09-2026. Được phép ghi dữ liệu. Dù vậy đợt khám phá này vẫn **chỉ đọc**: không bấm Lưu ở bất kỳ biểu mẫu nào |
| **Công cụ** | Hai lượt:<br>1. **Khảo sát** — Browser pane của Claude (trình duyệt thật, headed, viewport `1600×750`, đọc DOM + Network + Console)<br>2. **Chụp evidence** — **Playwright + Google Chrome thật**, headed, viewport `1600×750`, chạy bằng script đăng nhập tự động |
| **Phạm vi crawl** | Toàn bộ sidebar (14 mục cấp 1 + 2 nhóm con), thanh công cụ trên cùng, menu tạo nhanh, menu hồ sơ; mở chi tiết 1 Dự án và 1 Khách hàng ở chế độ xem; thử 13 route vùng Setup |
| **Ngoài phạm vi** | Cổng Khách hàng/Người dùng (URL khác — người dùng cung cấp sau) · mặt Mobile · mặt API |

### Tầng network — kết luận quan trọng

Perfex **không có tầng REST API** để quan sát. Kiến trúc là:

- Trang HTML render sẵn phía máy chủ, mỗi màn hình là một request điều hướng đầy đủ
- Bảng dữ liệu nạp bằng `POST /admin/<module>/table` (ví dụ `/admin/clients/table`, `/admin/projects/table`) trả JSON đúng định dạng DataTables
- Một số bảng dùng đường dẫn riêng: `POST /admin/clients/all_contacts` · `POST /admin/misc/reminders_table` · `POST /admin/projects/staff_projects` · `POST /admin/staff/notifications`
- Lịch nạp dữ liệu bằng `GET /admin/utilities/get_calendar_data?start=...&end=...`
- Quản lý tệp dùng `GET /admin/utilities/media_connector?cmd=...` (giao thức elFinder)
- Mọi request ghi mang `csrf_token_name` — **automation phải lấy token từ trang, không hardcode**

**Hệ quả cho các bước sau:** mẹo *"phát hiện module có API nhưng chưa có UI"* **không dùng được** ở hệ thống này. Validation phía máy chủ chỉ lộ ra khi submit biểu mẫu thật — tầng recon cấp module bắt buộc phải bật Network **lúc submit**, không chỉ lúc tải trang.

---

## 2. Sơ đồ điều hướng toàn hệ thống

Cây menu **nguyên trạng** (nhãn tiếng Anh như UI hiển thị):

```
Sidebar
├── Dashboard ......................... /admin/
├── Customers ......................... /admin/clients
│   ├── New Customer .................. /admin/clients/client
│   ├── Import Customers .............. /admin/clients/import
│   └── Contacts ...................... /admin/clients/all_contacts
├── Projects .......................... /admin/projects
│   └── New Project ................... /admin/projects/project
├── Tasks ............................. /admin/tasks
├── Contracts ......................... /admin/contracts
│   └── New Contract .................. /admin/contracts/contract
├── Sales  (nhóm, href="#")
│   ├── Proposals ..................... /admin/proposals
│   ├── Estimates ..................... /admin/estimates
│   ├── Invoices ...................... /admin/invoices
│   │   └── Recurring Invoices ........ /admin/invoices/recurring
│   ├── Payments ...................... /admin/payments
│   ├── Credit Notes .................. /admin/credit_notes
│   └── Items ......................... /admin/invoice_items
├── Subscriptions ..................... /admin/subscriptions
├── Expenses .......................... /admin/expenses
├── Support ........................... /admin/tickets
├── Leads ............................. /admin/leads
├── Estimate Request .................. /admin/estimate_request
├── Knowledge Base .................... /admin/knowledge_base
├── Utilities  (nhóm, href="#")
│   ├── Media ......................... /admin/utilities/media          (tiêu đề trang: "Files")
│   ├── Bulk PDF Export ............... /admin/utilities/bulk_pdf_exporter
│   └── Calendar ...................... /admin/utilities/calendar
└── Reports  (nhóm, href="#")
    ├── Sales ......................... /admin/reports/sales
    ├── Expenses ...................... /admin/reports/expenses
    ├── Expenses vs Income ............ /admin/reports/expenses_vs_income
    ├── Leads ......................... /admin/reports/leads
    ├── Timesheets overview ........... /admin/staff/timesheets?view=all
    └── KB Articles ................... /admin/reports/knowledge_base_articles

Thanh công cụ trên cùng
├── Ô tìm kiếm toàn cục ............... widget #search_input (không có route riêng)
├── Nút tạo nhanh (+)
│   └── Invoice · Estimate · Proposal · Credit Note · Customer · Subscription
│       · Project · Task · Expense · Contract · Article · Ticket · Event
├── Bảng tin "Share documents, ideas.." ... panel mở tại chỗ (không có route riêng)
├── Todo items ........................ /admin/todo
├── Hồ sơ (avatar)
│   ├── My Profile .................... /admin/profile
│   ├── My Timesheets ................. /admin/staff/timesheets
│   ├── Edit Profile .................. /admin/staff/edit_profile
│   ├── Language ...................... /admin/staff/change_language/<28 ngôn ngữ>
│   └── Logout ........................ /admin/authentication/logout
├── Bộ đếm giờ (timer) ................ widget trong thanh công cụ
└── Thông báo (chuông) ................ /admin/profile?notifications=true

Route ngoài menu, ĐÃ xác minh vào được
├── Announcements ..................... /admin/announcements   (chỉ xem, không có nút tạo)
└── Reminders ......................... /admin/misc/reminders

Route ngoài menu, ĐÃ xác minh BỊ CHẶN → /admin/access_denied hoặc đẩy về Dashboard
└── /admin/staff · /admin/roles · /admin/settings · /admin/departments
    · /admin/taxes · /admin/currencies · /admin/goals · /admin/tickets/priorities
    · /admin/utilities/activity_log
```

### ⚠️ Quy tắc đọc cây điều hướng trên — đọc trước khi dùng

**Cây này liệt kê mọi đường dẫn *phát hiện được*, không phải mọi màn hình *đã mở*.** Phần lớn đường dẫn ở đây đọc ra từ thuộc tính `href` trên DOM; việc mở từng màn hình là chuyện khác.

| Mức | Nghĩa | Được phép làm gì với nó |
|---|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, có ảnh evidence | Viết REQ/TC dựa trên quan sát |
| 🔗 **Mới thấy liên kết** | Đường dẫn đọc từ `href`, **chưa mở màn hình phía sau** | Chỉ được dùng làm *điểm vào cho recon sau*. **Không** được suy ra nội dung màn hình |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng mở không ra, hoặc cố ý không thử | Ghi lại, **không** đưa vào TC |

**Mức xác minh của từng route ghi trong chính tệp module** — mục `## Mức xác minh của từng route`. Đây là chỗ tra khi chuẩn bị recon một module.

> **Vì sao có mục này:** trong lượt evidence đầu tiên, ảnh trang đăng nhập cho thấy liên kết `Forgot Password?` và điều đó suýt bị hiểu thành "đã khảo sát luồng Quên mật khẩu". Thực tế màn hình phía sau chưa ai mở. Ảnh chứng minh **một liên kết tồn tại** không phải ảnh của **màn hình phía sau nó** — áp dụng cho mọi nút và liên kết còn lại.

---

## 3. Bảng module tổng — 28 module

| # | Module — tên trên website | Tên tiếng Việt | Bí danh | Prefix | Nền tảng | Tệp khám phá | Loại màn hình | Risk | Ước REQ |
|---|---|---|---|---|---|---|---|---|---|
| 01 | **Login** | Đăng nhập & Phiên làm việc | Authentication (tên route) | `LOGIN` | Web | [module_01](modules/module_01_login.md) | Biểu mẫu | 🔴 | 12–18 |
| 02 | **Customers** | Khách hàng | Clients (tên route) | `CUST` | Web | [module_02](modules/module_02_customers.md) | Danh sách + Chi tiết 19 tab | 🔴 | 55–70 |
| 03 | **Contacts** | Người liên hệ của khách hàng | Customer Contacts · Customer Admins | `CTC` | Web | [module_03](modules/module_03_contacts.md) | Danh sách (chỉ xem) + tab trong hồ sơ KH | 🔴 | 30–40 |
| 04 | **Leads** | Khách hàng tiềm năng | — | `LEAD` | Web | [module_04](modules/module_04_leads.md) | Danh sách + Kanban | 🔴 | 35–45 |
| 05 | **Estimate Request** | Yêu cầu báo giá | — | `ESTREQ` | Web | [module_05](modules/module_05_estimate_request.md) | Danh sách + Trình dựng biểu mẫu | 🟡 | 20–28 |
| 06 | **Estimates** | Báo giá | — | `EST` | Web | [module_06](modules/module_06_estimates.md) | Danh sách + Biểu mẫu | 🔴 | 30–40 |
| 07 | **Proposals** | Đề xuất báo giá | — | `PROP` | Web | [module_07](modules/module_07_proposals.md) | Danh sách + Biểu mẫu | 🔴 | 30–40 |
| 08 | **Contracts** | Hợp đồng | — | `CTR` | Web | [module_08](modules/module_08_contracts.md) | Danh sách + Biểu mẫu | 🔴 | 30–40 |
| 09 | **Items** | Sản phẩm & Dịch vụ | Invoice Items | `ITEM` | Web | [module_09](modules/module_09_items.md) | Danh sách + Nhóm | 🟡 | 20–26 |
| 10 | **Invoices** | Hoá đơn | Recurring Invoices | `INV` | Web | [module_10](modules/module_10_invoices.md) | Danh sách + Biểu mẫu + Định kỳ | 🔴 | 50–65 |
| 11 | **Payments** | Thanh toán | Payments Received · Batch Payments | `PAY` | Web | [module_11](modules/module_11_payments.md) | Danh sách (chỉ xem) | 🔴 | 18–25 |
| 12 | **Credit Notes** | Giấy báo có | — | `CN` | Web | [module_12](modules/module_12_credit_notes.md) | Danh sách + Biểu mẫu | 🔴 | 25–32 |
| 13 | **Subscriptions** | Đăng ký định kỳ | — | `SUB` | Web | [module_13](modules/module_13_subscriptions.md) | Danh sách + Biểu mẫu | 🔴 | 28–35 |
| 14 | **Expenses** | Chi phí | — | `EXP` | Web | [module_14](modules/module_14_expenses.md) | Danh sách + Biểu mẫu | 🔴 | 30–38 |
| 15 | **Projects** | Dự án | — | `PRJ` | Web | [module_15](modules/module_15_projects.md) | Danh sách + Chi tiết 18 tab | 🔴 | 60–80 |
| 16 | **Tasks** | Công việc | — | `TASK` | Web | [module_16](modules/module_16_tasks_timesheets.md) | Danh sách + Chi tiết | 🔴 | 40–55 |
| 17 | **Timesheets** | Chấm công | Timer | `TIME` | Web | [module_16](modules/module_16_tasks_timesheets.md) | Danh sách + Bộ đếm giờ | 🟡 | 18–24 |
| 18 | **Support** | Hỗ trợ / Ticket | Support Tickets (tiêu đề trang) · Tickets | `TICKET` | Web | [module_17](modules/module_17_support.md) | Danh sách + Biểu mẫu | 🟡 | 30–40 |
| 19 | **Knowledge Base** | Cơ sở tri thức | Articles | `KB` | Web | [module_18](modules/module_18_knowledge_base.md) | Danh sách + Nhóm | 🟢 | 15–20 |
| 20 | **Calendar** | Lịch | Events | `CAL` | Web | [module_19](modules/module_19_calendar_media_bulk_pdf.md) | Lịch | 🟡 | 15–20 |
| 21 | **Media / Bulk PDF Export** | Tệp & Xuất PDF hàng loạt | Files (tiêu đề trang của Media) | `UTIL` | Web | [module_19](modules/module_19_calendar_media_bulk_pdf.md) | Quản lý tệp + Biểu mẫu | 🟡 | 18–24 |
| 22 | **Reports** | Báo cáo | — | `RPT` | Web | [module_20](modules/module_20_reports.md) | Báo cáo (6 nhóm, riêng nhóm Bán hàng có 10 báo cáo con) | 🟡 | 40–55 |
| 23 | **Dashboard** | Bảng điều khiển | — | `DASH` | Web | [module_21](modules/module_21_dashboard_search.md) | Bảng điều khiển | 🟢 | 8–12 |
| 24 | **Search** | Tìm kiếm toàn cục | Website không đặt nhãn cho widget này — tên lấy từ `placeholder="Search..."` | `SEARCH` | Web | [module_21](modules/module_21_dashboard_search.md) | Widget | 🟡 | 8–12 |
| 25 | **To Do** | Việc cần làm | — | `TODO` | Web | [module_22](modules/module_22_todo_reminders_announcements.md) | Danh sách | 🟢 | 10–14 |
| 26 | **Reminders** | Nhắc nhở | — | `REM` | Web | [module_22](modules/module_22_todo_reminders_announcements.md) | Danh sách | 🟢 | 10–14 |
| 27 | **Announcements** | Thông báo nội bộ | — | `ANN` | Web | [module_22](modules/module_22_todo_reminders_announcements.md) | Danh sách (chỉ xem) | 🟢 | 6–10 |
| 28 | **My Profile** | Hồ sơ cá nhân & Ngôn ngữ | Profile (tiêu đề trang) · Edit Profile · Language | `PROFILE` | Web | [module_23](modules/module_23_my_profile.md) | Biểu mẫu | 🟡 | 15–20 |

**Tổng ước lượng: ~706–942 REQ** — cộng trực tiếp từ cột `Ước REQ` ở trên.

> ⚠️ Con số này **sửa lại** các mức ghi trước đó trong ngày (`620–800`, `635–825`, `648–840`) — đó là cộng nhẩm sai, không phải do đổi phạm vi. Tách `CTC` và `PAY` gần như **không** làm tổng thay đổi, vì khối lượng chỉ được chia lại chứ không sinh thêm: `CUST` giảm đúng phần chuyển sang `CTC`, `INV` giảm đúng phần chuyển sang `PAY`.

### Ghi chú ranh giới đã chốt với người dùng (19-09-2026)

| Quyết định | Lý do người dùng chốt |
|---|---|
| **Contacts TÁCH thành module riêng `CTC`** | Chốt lại ngày 19-09-2026 (ban đầu gộp vào `CUST`). Contact có **vòng đời riêng**: tài khoản đăng nhập cổng khách hàng, cờ `Active` độc lập, cột `Last Login`, danh sách toàn cục riêng và endpoint riêng → đủ điều kiện "1 entity nghiệp vụ có vòng đời riêng = 1 module" |
| **Payments TÁCH thành module riêng `PAY`** | Chốt lại ngày 19-09-2026 (ban đầu gộp vào `INV`). Payment có **mã chứng từ riêng** `Payment #`, màn hình danh sách riêng trong menu Sales và báo cáo riêng `Payments Received`. Việc "không có nút tạo trên danh sách" chỉ nói lên **đường vào** nằm ở Hoá đơn, không làm nó thôi là một entity |
| Giữ riêng `TODO` · `REM` · `ANN` · `SEARCH` · `PROFILE` | Người dùng không chọn gộp thành `MISC` |
| Prefix `CTC` chứ không phải `CONT` cho Contacts | `CONT` quá giống `CTR` (Contracts) — grep một mã ra hai nghĩa là đúng thứ quy ước đặt mã phải tránh |

---

## Bản đồ tài liệu

28 module chia vào **23 tệp**. Trạng thái recon là bản gốc ở [`../README.md`](../README.md).

| Tệp | Module bao phủ — tên trên website (tiếng Việt) | Prefix |
|---|---|---|
| [modules/module_01_login.md](modules/module_01_login.md) | Login (Đăng nhập & Phiên làm việc) | `LOGIN` |
| [modules/module_02_customers.md](modules/module_02_customers.md) | Customers (Khách hàng) | `CUST` |
| [modules/module_03_contacts.md](modules/module_03_contacts.md) | Contacts (Người liên hệ của khách hàng) | `CTC` |
| [modules/module_04_leads.md](modules/module_04_leads.md) | Leads (Khách hàng tiềm năng) | `LEAD` |
| [modules/module_05_estimate_request.md](modules/module_05_estimate_request.md) | Estimate Request (Yêu cầu báo giá) | `ESTREQ` |
| [modules/module_06_estimates.md](modules/module_06_estimates.md) | Estimates (Báo giá) | `EST` |
| [modules/module_07_proposals.md](modules/module_07_proposals.md) | Proposals (Đề xuất báo giá) | `PROP` |
| [modules/module_08_contracts.md](modules/module_08_contracts.md) | Contracts (Hợp đồng) | `CTR` |
| [modules/module_09_items.md](modules/module_09_items.md) | Items (Sản phẩm & Dịch vụ) | `ITEM` |
| [modules/module_10_invoices.md](modules/module_10_invoices.md) | Invoices (Hoá đơn — gồm Recurring Invoices) | `INV` |
| [modules/module_11_payments.md](modules/module_11_payments.md) | Payments (Thanh toán) | `PAY` |
| [modules/module_12_credit_notes.md](modules/module_12_credit_notes.md) | Credit Notes (Giấy báo có) | `CN` |
| [modules/module_13_subscriptions.md](modules/module_13_subscriptions.md) | Subscriptions (Đăng ký định kỳ) | `SUB` |
| [modules/module_14_expenses.md](modules/module_14_expenses.md) | Expenses (Chi phí) | `EXP` |
| [modules/module_15_projects.md](modules/module_15_projects.md) | Projects (Dự án) | `PRJ` |
| [modules/module_16_tasks_timesheets.md](modules/module_16_tasks_timesheets.md) | Tasks (Công việc) · Timesheets (Chấm công) | `TASK` · `TIME` |
| [modules/module_17_support.md](modules/module_17_support.md) | Support (Hỗ trợ / Ticket) | `TICKET` |
| [modules/module_18_knowledge_base.md](modules/module_18_knowledge_base.md) | Knowledge Base (Cơ sở tri thức) | `KB` |
| [modules/module_19_calendar_media_bulk_pdf.md](modules/module_19_calendar_media_bulk_pdf.md) | Calendar (Lịch) · Media / Bulk PDF Export (Tệp & Xuất PDF hàng loạt) | `CAL` · `UTIL` |
| [modules/module_20_reports.md](modules/module_20_reports.md) | Reports (Báo cáo) | `RPT` |
| [modules/module_21_dashboard_search.md](modules/module_21_dashboard_search.md) | Dashboard (Bảng điều khiển) · Search (Tìm kiếm toàn cục) | `DASH` · `SEARCH` |
| [modules/module_22_todo_reminders_announcements.md](modules/module_22_todo_reminders_announcements.md) | To Do (Việc cần làm) · Reminders (Nhắc nhở) · Announcements (Thông báo nội bộ) | `TODO` · `REM` · `ANN` |
| [modules/module_23_my_profile.md](modules/module_23_my_profile.md) | My Profile (Hồ sơ cá nhân & Ngôn ngữ) | `PROFILE` |

**Tự kiểm chứng:** 15 tệp đơn + `module_16` (2) + 2 tệp đơn + `module_19` (2) + 1 tệp đơn + `module_21` (2) + `module_22` (3) + 1 tệp đơn = **28 module** — khớp bảng mục 3. ✅

---

## 4. Bản đồ entity & phụ thuộc

```
                    ┌──────────┐
                    │   LEAD   │  chuyển đổi →
                    └────┬─────┘
                         ▼
   ┌─────────────────────────────────────────────┐
   │                   CUST                      │  ← trung tâm, 19 tab tổng hợp
   └──┬──────────────────────────────────────┬───┘
      │                                      │
      ▼                                      │
    CTC  ← người liên hệ, CÓ đăng nhập riêng │
         vào cổng khách hàng (AMB-SYS-03)    │
      ┌──────────────────────────────────────┘
      │
      │   │   │   │   │   │   │   │   │   │
      ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼
    PRJ  CTR EST PROP INV  CN  SUB EXP TICKET ESTREQ
      │              ▲    ▲
      │              │    │
      ▼              │    │
    TASK ────────────┘    │   (task → tính giờ → hoá đơn)
      │                   │
      ▼                   │
    TIME ─────────────────┘

    PAY  ──→ ghi nhận tiền vào INV; nhiều PAY cho một INV quyết định
             trạng thái Paid / Partially Paid của INV
    ITEM ──→ dùng làm dòng hàng trong: EST · PROP · INV · CN
    REM  ──→ gắn vào: CUST · LEAD · INV · EST · PROP · CTR · EXP · TASK
    CAL  ──→ tổng hợp sự kiện từ: TASK · INV · EST · PROP · CTR · REM
    RPT  ──→ đọc dữ liệu từ: INV · EXP · LEAD · KB · TIME
    UTIL ──→ Xuất PDF hàng loạt đọc từ: INV · EST · PROP · CN
    SEARCH ─→ tìm xuyên: CUST · PRJ · TASK · INV · EST · PROP · CTR · TICKET · LEAD · KB
```

**Ràng buộc thứ tự khảo sát rút ra từ sơ đồ:**

- `CUST` là nút trung tâm — **phải recon trước** các module phụ thuộc, trong đó `CTC` phụ thuộc trực tiếp
- `ITEM` phải recon **trước** `EST` · `PROP` · `INV` · `CN` (dòng hàng lấy từ đây)
- `PRJ` phải recon **trước** `TASK`, `TASK` trước `TIME`
- `INV` phụ thuộc cả `CUST` lẫn `ITEM`; `PAY` phải recon **sau** `INV` vì bản ghi thanh toán chỉ tạo được từ trong hoá đơn

### Phát hiện tầng network cấp hệ thống

| Phát hiện | Ý nghĩa |
|---|---|
| Mẫu `POST /admin/<module>/table` lặp lại ở **mọi** module có bảng | Ranh giới entity trùng khớp với ranh giới module đã chia — xác nhận cách tách ở mục 3 là đúng với cách hệ thống tự tổ chức |
| `POST /admin/clients/all_contacts` là bảng **riêng**, không phải `/admin/contacts/table` | Contact là entity thật nhưng **không** có controller độc lập — củng cố quyết định gộp vào `CUST` |
| Mọi request mang `csrf_token_name` | Ràng buộc kỹ thuật cho automation: phải đọc token từ trang |
| **Không** có endpoint nào dạng `/api/*` | Không phát hiện được tính năng "có API chưa có UI" bằng tầng network ở hệ thống này |

---

## 5. Ma trận phân quyền sơ bộ cấp module

⚠️ **Toàn bộ bảng này là mức bằng chứng thấp.** Chỉ có **01 tài khoản**, và màn hình quản lý vai trò bị chặn nên **không biết hệ thống có những vai trò nào**.

| Vùng | Tài khoản hiện có (`user-id-2`, hiển thị "Admin Example") | Các vai trò khác |
|---|---|---|
| 28 module nghiệp vụ ở mục 3 | ✅ **Đã kiểm chứng** — truy cập được danh sách của cả 28 module | ❔ Chưa có căn cứ |
| Vùng Setup (Nhân viên · Vai trò · Cấu hình · Danh mục · Mục tiêu · Nhật ký hoạt động) | ❌ **Đã kiểm chứng** — `/admin/staff` → `/admin/access_denied`; 8 route khác bị đẩy về Dashboard; `#setup-menu` rỗng | ❔ Chưa có căn cứ |
| Announcements — tạo mới | ❌ **Đã kiểm chứng** — vào `/admin/announcements` được nhưng **không có nút tạo**, chỉ có Export | ❔ Chưa có căn cứ |
| Payments — tạo mới từ danh sách | ❌ **Đã kiểm chứng** — không có nút tạo trên `/admin/payments` | ❔ Chưa có căn cứ |

**Tổng: Đã kiểm chứng 4 ô · Suy diễn 0 ô · Chưa rõ 4 ô.**

Mỗi vai trò chưa có tài khoản → xem `AMB-SYS-01` và `AMB-SYS-02` ở [`../README.md`](../README.md) mục 3.

---

## 6. Thứ tự khảo sát đã chốt

Xếp theo **phụ thuộc trước, risk sau** — không theo bảng chữ cái.

| Vòng | Module | Lý do xếp ở đây |
|---|---|---|
| **1 — Nền tảng** | `LOGIN` → `CUST` → `CTC` | Không đăng nhập được thì không khảo sát được gì. `CUST` là nút trung tâm; `CTC` ngay sau vì người liên hệ chỉ tạo được từ hồ sơ khách hàng |
| **2 — Bán hàng** | `LEAD` → `ESTREQ` → `EST` → `PROP` → `CTR` | Đi theo đúng dòng chảy nghiệp vụ: tiềm năng → yêu cầu → báo giá → đề xuất → hợp đồng |
| **3 — Tài chính** | `ITEM` → `INV` → `PAY` → `CN` → `SUB` → `EXP` | `ITEM` trước vì các module sau dùng nó làm dòng hàng. `PAY` ngay sau `INV` vì thanh toán chỉ tạo được từ hoá đơn. Toàn vòng đều 🔴 vì liên quan tiền |
| **4 — Vận hành** | `PRJ` → `TASK` → `TIME` → `TICKET` | Quan hệ cha–con chặt, phải theo đúng thứ tự này |
| **5 — Phụ trợ** | `KB` → `CAL` → `RPT` → `UTIL` → `DASH` → `SEARCH` → `TODO` → `REM` → `ANN` → `PROFILE` | Risk thấp, ít module khác phụ thuộc. `RPT` để sau cùng nhóm vì nó đọc dữ liệu của các module trước |

### Module BLOCKED

| Module | Bị chặn bởi | Gỡ chặn bằng cách |
|---|---|---|
| *(chưa cấp prefix)* Nhân viên · Vai trò & Phân quyền · Cấu hình hệ thống · Danh mục · Mục tiêu · Nhật ký hoạt động | `AMB-SYS-01` — tài khoản không có quyền Setup | Xin tài khoản Super Admin. Có rồi thì chạy `/discover-system` **Mode ADD** |
| *(chưa cấp prefix)* Cổng Khách hàng/Người dùng | `AMB-SYS-03` — chưa có URL và tài khoản | Người dùng cung cấp. Có rồi thì chạy `/discover-system` **Mode ADD** |

⚠️ Mục **Ma trận Phân quyền** của **mọi** module trong 5 vòng trên sẽ ở mức suy diễn `⚠️` cho tới khi gỡ được `AMB-SYS-01`. Đây không phải lý do hoãn recon — cứ recon, đánh dấu ô suy diễn cho đúng.

---

## 6b. Danh mục Evidence

**34 ảnh**, phủ **28/28 module**. Tất cả chụp ngày 19-09-2026 bằng Playwright + Chrome thật, viewport `1600×750`, **đã mở lại xác minh từng ảnh** trước khi ghi bảng này.

📸 **Vì sao chụp viewport chứ không full-page:** ở tầng khám phá, thứ cần chứng minh là *module tồn tại và có thanh công cụ / cột bảng gì* — phần đó nằm trọn trong viewport. Ảnh full-page của màn hình danh sách sẽ kéo theo hàng chục dòng dữ liệu nghiệp vụ (tên, email, số tiền) **không liên quan** tới việc chứng minh module tồn tại, mà `docs/` thì được commit. Chuẩn evidence đầy đủ (ảnh theo từng trạng thái, từng tab) áp dụng ở **tầng recon cấp module**, không phải ở đây.

| Tệp ảnh | Module | Màn hình — tên trên website | Trạng thái đã xác minh trong ảnh |
|---|---|---|---|
| `login_form_default_viewport.png` | `LOGIN` | Login | Chưa đăng nhập, trường rỗng; thấy `Email Address` · `Password` · `Remember me` · nút `Login` · liên kết `Forgot Password?` |
| `login_forgot_password_default_viewport.png` | `LOGIN` | Forgot Password (Quên mật khẩu) | Trạng thái mặc định: tiêu đề `Forgot Password`, ô `Email Address` rỗng, nút `Confirm`; **không có liên kết quay lại đăng nhập**. Chưa bấm `Confirm` (sẽ gửi email thật) |
| `sys_access_denied_viewport.png` | *(cấp hệ thống)* | `/admin/staff` → `/admin/access_denied` | Toast **"Access denied"** + thân trang **"Something went wrong. Try again"**; sidebar đầy đủ kết thúc ở `Reports`, **không có mục Setup**. Bằng chứng của `AMB-SYS-01` |
| `dash_overview_viewport.png` | `DASH` | Dashboard | 3 khối overview đủ 6 trạng thái mỗi khối; bộ chọn năm `2026`; 3 ô chỉ số tiền; nút `Dashboard Options` |
| `search_widget_default_element.png` | `SEARCH` | Search widget — chụp **phần tử** `#top_search` | Ô `Search...` + nút kính lúp, trạng thái rỗng |
| `cust_list_viewport.png` | `CUST` | Customers — danh sách | Thanh công cụ `New Customer` · `Import Customers` · `Contacts`; khối Customers Summary (2032 Total / 2023 Active / 9 Inactive / 367 Active Contacts); 8 cột bảng |
| `cust_detail_tabs_viewport.png` | `CUST` | Customer detail — chi tiết | Danh sách tab dọc (Profile → Tickets…) + 3 tab ngang `Customer Details` · `Billing & Shipping` · `Customer Admins`; badge `Projects 4`; trường `* Company` bắt buộc |
| `ctc_list_viewport.png` | `CTC` | Contacts — danh sách toàn hệ thống | 8 cột; cột `Last Login` **có giá trị thật**; có bản ghi đang **tắt** `Active`. Chỉ có nút `Export`, **không có nút tạo** |
| `lead_list_viewport.png` | `LEAD` | Leads — danh sách | **Rỗng** (`No entries found`); 12 cột; có **2 nút chuyển chế độ xem** cạnh `New Lead` |
| `estreq_list_viewport.png` | `ESTREQ` | Estimate Request — danh sách | **Rỗng**; 6 cột; nút `New Form` |
| `est_list_viewport.png` | `EST` | Estimates — danh sách | 3 bản ghi, **cả 3 đều `Status = Expired`**; 10 cột |
| `prop_list_viewport.png` | `PROP` | Proposals — danh sách | 5 bản ghi: 1 `Sent` + 4 `Open`; 10 cột |
| `ctr_list_viewport.png` | `CTR` | Contracts — danh sách | Contract Summary 5 ô gồm **`Trash 5`**; 2 biểu đồ `Contracts by Type` và `Contracts Value by Type (USD)` |
| `item_list_viewport.png` | `ITEM` | Items — danh sách | Thanh công cụ `New Item` · `Import Items` · `Groups`; cột `Tax 1` / `Tax 2` hiển thị dạng phần trăm |
| `inv_list_viewport.png` | `INV` | Invoices — danh sách | Thanh công cụ `Create New Invoice` · `Batch Payments` · `Recurring Invoices` · `Filter by status`; badge `Paid` / `Unpaid`; "Showing 1 to 6 of 6 entries" |
| `inv_recurring_list_viewport.png` | `INV` | Recurring Invoices (hoá đơn định kỳ) | **Rỗng**; cột `Frequency` · `Cycles Remaining` · `Last Child Invoice Date` · `Next Invoice Date`; nút `Go Back` |
| `pay_list_viewport.png` | `PAY` | Payments — danh sách | 1 bản ghi, `Payment Mode = Bank`; **không có thanh công cụ tạo mới** |
| `cn_list_viewport.png` | `CN` | Credit Notes — danh sách | **Rỗng**; 8 cột gồm `Remaining Amount` |
| `sub_list_viewport.png` | `SUB` | Subscriptions — danh sách | **Rỗng**; Subscriptions Summary đủ **8 trạng thái**, gắn logo **`stripe`** |
| `exp_list_viewport.png` | `EXP` | Expenses — danh sách | **Rỗng**; 10 cột gồm `Receipt` và `Invoice`; nút `Record Expense` · `Import Expenses` |
| `prj_list_viewport.png` | `PRJ` | Projects — danh sách | Projects Summary đủ **5 trạng thái** (73/70/17/2/1); 8 cột; cột `Members` hiển thị avatar |
| `prj_detail_tabs_viewport.png` | `PRJ` | Project detail — chi tiết | 12 tab ngang với `Sales ▾` là **tab nhóm**; badge trạng thái `On Hold`; nút `New Task` · `Invoice Project` · `More`; ô **`0 / -699 Days Left`** |
| `task_list_viewport.png` | `TASK` | Tasks — danh sách | Tasks Summary đủ **5 trạng thái**; cột `Status` và `Priority` là dropdown **sửa tại chỗ**; nhãn `Recurring Task` trên dòng |
| `time_timesheets_viewport.png` | `TIME` | My Timesheets (chấm công của tôi) | 5 ô chỉ số giờ; nút `View all timesheets`; biểu đồ; bộ lọc `Today` · `Customer` · `Project` |
| `ticket_list_viewport.png` | `TICKET` | Support Tickets — danh sách | **Rỗng**; Tickets Summary đủ **5 trạng thái**; 10 cột gồm `Department` và `Service` |
| `kb_list_viewport.png` | `KB` | Knowledge Base — danh sách bài viết | **Rỗng**; 3 cột; nút `New Article` · `Groups` |
| `cal_month_view_viewport.png` | `CAL` | Calendar — chế độ `Month` | Tháng `September 2026`; nút chuyển `Month` · `Week` · `Day` · `Filter By`; sự kiện dồn kèm nhãn `+30 more` |
| `util_media_files_viewport.png` | `UTIL` | Media / Files — trình quản lý tệp | Trình quản lý nhúng với **2 gốc thư mục**: `admin-example` và `public` |
| `util_bulk_pdf_export_viewport.png` | `UTIL` | Bulk PDF Export | Trường `* Select Type` (bắt buộc, đang `Nothing selected`) · `From Date` · `To Date` · `Include Tag` · nút `Export`. Khối `Status` **chưa hiện** vì chưa chọn loại chứng từ |
| `rpt_sales_viewport.png` | `RPT` | Sales Reports (báo cáo bán hàng) | **7 báo cáo con** + **3 báo cáo biểu đồ**; dòng cảnh báo *"Cancelled invoices are excluded from the report"* |
| `todo_list_viewport.png` | `TODO` | My To Do Items | **2 khối**: "Unfinished to do's" (2 mục) và "Latest finished to do's"; có tay cầm kéo–thả, nút sửa/xoá từng dòng |
| `rem_list_viewport.png` | `REM` | Reminders | **Rỗng**; câu mô tả phạm vi *"Showing your reminders and reminders created by you."*; 5 cột |
| `ann_list_viewport.png` | `ANN` | Announcements | **Rỗng**; 2 cột; chỉ có nút `Export`, **không có nút tạo** |
| `profile_view_viewport.png` | `PROFILE` | My Profile | 5 ô chỉ số giờ; thẻ nhân viên (**khối liên hệ đã làm mờ có chủ đích** — xem mục 7, Sự cố); bảng Projects; khay Notifications với `Mark all as read` |

**Đối chiếu phủ module:** 28/28 module đều có ≥ 1 ảnh. `LOGIN` có 3 ảnh (gồm trang từ chối truy cập) · `CUST` có 2 ảnh · `INV` có 2 ảnh · `PRJ` có 2 ảnh · `UTIL` có 2 ảnh. Ảnh `ctc_list_viewport.png` và `pay_list_viewport.png` đã đổi tên theo prefix mới khi tách module.

⚠️ **Ảnh chứng minh một liên kết tồn tại KHÔNG phải ảnh của màn hình phía sau nó.** Lần chụp đầu chỉ có `login_form_default_viewport.png` (thấy liên kết `Forgot Password?`) và điều đó từng bị hiểu nhầm thành "đã khảo sát luồng Quên mật khẩu". Màn hình thật được bổ sung sau, ở dòng thứ hai của bảng trên. Áp dụng cùng nguyên tắc cho mọi nút/liên kết còn đang `❔` trong các tệp module.

---

## 7. Nhật ký khám phá

| Ngày | Mode · Mặt | Phạm vi | Kết quả | Nguồn |
|---|---|---|---|---|
| 19-09-2026 | **UI** · Web `/admin` | Toàn bộ sidebar + thanh công cụ + menu tạo nhanh + menu hồ sơ; mở chi tiết 1 Dự án, 1 Khách hàng; thử 13 route vùng Setup | Phát hiện **26 module**, cấp 26 prefix. Xác minh vùng Setup **bị chặn** → `AMB-SYS-01`, `AMB-SYS-02`. Xác nhận hệ thống **không có tầng REST API** | `UI thực tế` |
| 19-09-2026 | **UI** · Web `/admin` — lượt chụp evidence | 33 ảnh phủ 26/26 module + trang từ chối truy cập; đăng nhập tự động bằng Playwright + Chrome thật | Thu **33 ảnh**, đã mở lại xác minh **từng ảnh**. Giải được 8 nghi vấn trạng thái (xem bảng dưới). Phát hiện thêm trạng thái `Cancelled` của Hoá đơn, `Trash` của Hợp đồng, tích hợp **Stripe** ở Đăng ký định kỳ | `Kiểm chứng thực tế` |
| 19-09-2026 | **UI** · Web — bổ sung | Màn hình **Quên mật khẩu** (`/admin/authentication/forgot_password`) | Người dùng chỉ ra thiếu: lượt trước chỉ chụp *liên kết* `Forgot Password?` chứ chưa mở màn hình phía sau. Đã mở thật, đọc DOM và chụp → **34 ảnh**. Ghi nhận màn hình **không có liên kết quay lại đăng nhập** và tiêu đề tab vẫn là `... - Login` | `Kiểm chứng thực tế` — người dùng phát hiện thiếu |

| 19-09-2026 | **UI** · Web — tái cấu trúc | Tách `Contacts` khỏi `CUST` và `Payments` khỏi `INV` | Người dùng chốt tách. Cấp 2 prefix mới **`CTC`** và **`PAY`** → **28 module / 23 tệp**. Đánh số lại tệp module theo thứ tự khảo sát, đổi tên 2 ảnh theo prefix mới. Ước REQ của `CUST` giảm 85–110 → 55–70, `INV` giảm 70–90 → 50–65 | Người dùng chốt |
| 20-09-2026 | Recon cấp module — `LOGIN` | Phát hành `docs/requirements/login/` | ⚠️ **Lệch lớn so với bản đồ khám phá:** ước ban đầu 12–18 REQ, thực tế **40 REQ**. Nguyên nhân: tầng khám phá mới đếm được biểu mẫu đăng nhập, chưa tính nhánh Quên mật khẩu, cookie ghi nhớ đăng nhập, bảo vệ route và tầng giao thức. **Ước REQ của các module còn lại nhiều khả năng cũng thấp hơn thực tế** — dùng con số ở mục 3 như mức sàn, không phải mức trần | `/generate-requirements-from-website LOGIN` |

### Nghi vấn đã được ảnh evidence giải quyết

| Trước đó ghi | Ảnh đã chứng minh |
|---|---|
| `LOGIN` — luồng Quên mật khẩu ❔ chưa xác minh | ✅ Màn hình `/admin/authentication/forgot_password` **đã mở và chụp**: tiêu đề `Forgot Password`, ô `Email Address`, nút `Confirm`. ⚠️ Phần **sau khi bấm `Confirm`** vẫn chưa kiểm — sẽ gửi email thật |
| `EST` — lệch giữa 6 trạng thái và `status=1..5` | ✅ `Not Sent` là **cờ gửi riêng**, không cùng tập với `Expired`: 3 báo giá đều mang `Status = Expired`, trong khi Bảng điều khiển tính đồng thời "3 Not Sent = 100%" và "3 Expired = 100%" |
| `PROP` — tập trạng thái ❔ | ✅ Trạng thái **loại trừ nhau** (1 Sent + 4 Open = 5 bản ghi, 20%/80%) — **ngược** với `EST` |
| `TASK` — tập trạng thái ❔ | ✅ Đủ **5**: `Not Started` · `In Progress` · `Testing` · `Awaiting Feedback` · `Complete` |
| `TICKET` — tập trạng thái ❔ (tưởng bị chặn bởi Setup) | ✅ Đủ **5**: `Open` · `In Progress` · `Answered` · `On Hold` · `Closed` |
| `PRJ` — tập trạng thái ❔ | ✅ Đủ **5**: `Not Started 73` · `In Progress 70` · `On Hold 17` · `Cancelled 2` · `Finished 1` |
| `SUB` — tập trạng thái ❔ | ✅ Đủ **8**: `Not Subscribed` · `Active` · `Future` · `Past Due` · `Unpaid` · `Incomplete` · `Canceled` · `Incomplete Expired` |
| `CAL` — các chế độ xem ❔ | ✅ `Month` · `Week` · `Day` + `Filter By` |
| `TODO` — vì sao gọi `POST /admin/todo` 2 lần ❔ | ✅ Trang có **2 khối** riêng: "Unfinished to do's" và "Latest finished to do's" |

### Phát hiện MỚI chỉ lộ ra nhờ ảnh evidence

| Phát hiện | Ảnh | Ý nghĩa |
|---|---|---|
| 🔴 Hoá đơn có trạng thái **`Cancelled`** | `rpt_sales_viewport.png` — dòng *"Cancelled invoices are excluded from the report"* | Rất có thể chính là `status=5` còn khuyết. Trạng thái này **không** hiện trên Bảng điều khiển → dễ bị bỏ sót khi viết TC |
| 🔴 Hợp đồng có **Thùng rác (`Trash`)** | `ctr_list_viewport.png` — Contract Summary: `Active 108` · `Expired 11` · `About to Expire 0` · `Recently Added 8` · `Trash 5` | Tồn tại **xoá mềm** — phải có TC cho khôi phục / xoá vĩnh viễn |
| 🔴 Đăng ký định kỳ gắn **Stripe** | `sub_list_viewport.png` — logo `stripe` trên khối tổng hợp | Chạm cổng thanh toán bên ngoài; kiểm thử cần môi trường sandbox |
| 🟡 Báo cáo lớn hơn nhiều so với ước lượng | `rpt_sales_viewport.png` — riêng "Sales Report" có **7 báo cáo con** + **3 báo cáo biểu đồ** | Ước REQ của `RPT` nâng từ 25–32 lên **40–55** |
| 🟡 Quản lý tệp có **2 gốc thư mục** | `util_media_files_viewport.png` — `admin-example` (riêng theo tài khoản) và `public` (dùng chung) | Ranh giới quyền trên tệp — cần TC kiểm người này có thấy thư mục người kia không |
| 🟡 Nhắc nhở nêu rõ quy tắc hiển thị | `rem_list_viewport.png` — *"Showing your reminders and reminders created by you."* | Quy tắc phạm vi hiển thị đã được hệ thống tự khai — chép nguyên văn vào REQ |
| 🟡 Hoá đơn định kỳ theo mô hình **cha–con** | `inv_recurring_list_viewport.png` — cột `Frequency` · `Cycles Remaining` · `Last Child Invoice Date` · `Next Invoice Date` | Làm rõ quan hệ sinh hoá đơn con |
| 🟠 Dự án hiển thị **số ngày âm** | `prj_detail_tabs_viewport.png` — ô `0 / -699 Days Left` với `Deadline 14-06-2024` | Nghi lỗi hiển thị khi quá hạn. Ghi lại để kiểm ở tầng module |
| 🟠 Bảng điều khiển đếm **7 hoá đơn**, danh sách chỉ có **6** | `dash_overview_viewport.png` (1 Draft = 14.29% → mẫu số 7) vs `inv_list_viewport.png` ("Showing 1 to 6 of 6 entries") | Nghi bản ghi thứ 7 bị lọc khỏi danh sách mặc định (có thể là bản `Cancelled`). Cần đối chiếu ở tầng module |

### Dữ liệu trống — ảnh hưởng kế hoạch recon

Các module sau **không có bản ghi nào** tại thời điểm khảo sát (`No entries found`): `LEAD` · `TICKET` · `EXP` · `CN` · `SUB` · `KB` · `ESTREQ` · `REM` · `ANN` · Hoá đơn định kỳ.

⚠️ Recon những module này **bắt buộc phải tự tạo dữ liệu trước** — môi trường **không** dùng chung nên được phép. Không có dữ liệu thì không quan sát được status flow, hành động trên dòng, hay phân trang.

### Sự cố & hạn chế của đợt khảo sát

| Hạng mục | Chi tiết | Xử lý |
|---|---|---|
| ✅ **Evidence — đã giải quyết** | Lượt khảo sát đầu không chụp được ảnh vì dự án thiếu `.mcp.json`. Đã chụp bù bằng **Playwright + Chrome thật** chạy qua script, không cần Playwright MCP | Đã tạo `.mcp.json` ở gốc dự án cho các workflow sau. **Cần khởi động lại Claude Code** để nạp |
| 🔒 **Che dữ liệu trong ảnh** | `profile_view_viewport.png` ban đầu lộ email tài khoản test và chuỗi điện thoại trùng mật khẩu trong `.env`. Đã **chụp lại với khối thông tin liên hệ bị làm mờ** | Ảnh hiện tại không đọc được định danh tài khoản |
| ⚠️ **Ảnh danh sách chứa dữ liệu nghiệp vụ của môi trường demo** | `cust_list` · `cust_contacts_list` · `prop_list` chứa tên, email, số điện thoại của bản ghi demo | Đã chụp **viewport** thay vì full-page để giảm lượng dữ liệu kéo theo. Nếu repo sẽ công khai, cân nhắc lược bớt 3 ảnh này |
| ❔ **Batch Payments** | Nút có thật trên trang Hoá đơn (`inv_list_viewport.png`), nhưng route đoán `/admin/payments/batch_payments` trả **404** | Ghi `❔ Nghi có, chưa xác minh` ở [module_09](modules/module_10_invoices.md). **Không** đưa vào danh mục module |
| ❔ **Bảng tin (Newsfeed)** | Phần tử `.open_newsfeed` có thật trong DOM; route đoán `/admin/newsfeed` trả **404** → là panel mở tại chỗ | Ghi `❔ Chưa mở thử` ở [module_19](modules/module_21_dashboard_search.md) |
| ❔ **Leads — chế độ Kanban** | Chỉ quan sát được chế độ bảng | Ghi ở [module_03](modules/module_04_leads.md) |
| ⚠️ **Sidebar bị ẩn ở viewport đang đo** | Ở `1600×750` trong browser pane, `body` mang class `page-small` và sidebar nằm ngoài màn hình (`left: -224px`) cho tới khi bấm nút `.hide-menu` | Ghi lại vì ảnh hưởng tới automation: **script phải mở sidebar trước khi bấm menu** ở viewport nhỏ hơn ngưỡng của Perfex |
