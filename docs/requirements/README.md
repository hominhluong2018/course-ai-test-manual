# Danh Mục Requirements Toàn Hệ Thống

> **Đây là điểm vào cấp hệ thống.** Mọi workflow đụng tới `docs/` đọc file này **đầu tiên** — để biết module nào đã có tài liệu, prefix nào đã bị chiếm, mã REQ kế tiếp bắt đầu từ đâu.

## Thuộc tính dự án

| Mục | Giá trị |
|---|---|
| **Hệ thống** | Perfex CRM — bản demo đào tạo của Anh Tester |
| **Phiên bản ứng dụng** | `3.1.6` (đọc từ query string `?v=` của asset) |
| **Kiến trúc** | Ứng dụng PHP render phía máy chủ (**không** phải SPA). Không có tầng REST API công khai — bảng dữ liệu nạp bằng `POST /admin/<module>/table` trả JSON cho DataTables |
| **Mặt đã khám phá** | **Web** — khu vực quản trị `/admin` |
| **Mặt chưa khám phá** | **Web — cổng Khách hàng/Người dùng** (URL khác, người dùng sẽ cung cấp sau) · Mobile · API |
| **Tiền tố TC ID** | **`CRM_`** — chốt 19-09-2026. Dạng đầy đủ: `CRM_<MODULE>_TC_<3 số>`, ví dụ `CRM_CUST_TC_001` |
| **Quy ước mã REQ** | `REQ-<MODULE>-<số>` — hệ thống mặc định của repo, **không** dùng namespace |
| **Môi trường dùng chung** | **KHÔNG** — chốt 19-09-2026. Được phép tạo/sửa/xoá dữ liệu khi khảo sát và chạy test |
| **URL · tài khoản** | Lưu ở `.env` (đã `.gitignore`). **KHÔNG** ghi vào bất kỳ tệp nào trong `docs/` |
| **Năng lực kiểm thử của QA** | Chốt 19-09-2026 — dùng cho nhánh Vòng 3 của **mọi** bộ TC:<br>• DevTools trình duyệt: ✅ có — đã kiểm chứng trong phiên khám phá (đọc được DOM, Network, Console)<br>• Gọi API: ❌ không có quyền — đội Dev xác minh<br>• Truy vấn CSDL: ❌ không có quyền — đội Dev xác minh<br>• Kiểm tầng tích hợp: ❌ không có quyền — đội Dev xác minh<br>• Xem nhật ký hoạt động: ❌ **đã đo** — tài khoản hiện tại bị chặn toàn bộ vùng Setup (`/admin/staff` → `/admin/access_denied`). Cần tài khoản Super Admin, đề nghị PO cấp |

### ⛔ Ràng buộc lớn nhất của dự án — đọc trước khi lập kế hoạch

Tài khoản đang dùng **không phải Super Admin**. Toàn bộ vùng **Setup** không truy cập được:

| Đã thử | Kết quả |
|---|---|
| `/admin/staff` | → chuyển hướng `/admin/access_denied` |
| `/admin/roles` · `/admin/settings` · `/admin/departments` · `/admin/taxes` · `/admin/currencies` · `/admin/goals` · `/admin/tickets/priorities` | → đều bị đẩy về Dashboard |
| `#setup-menu` trong DOM | Tồn tại nhưng **rỗng hoàn toàn** — máy chủ không render mục nào |

**Hệ quả:** Nhân viên · Vai trò & phân quyền · Cấu hình hệ thống · Danh mục (thuế, tiền tệ, phòng ban, loại hợp đồng) · Mục tiêu · Nhật ký hoạt động — **chưa khảo sát được**, chưa cấp prefix. Ma trận phân quyền của mọi module sẽ ở mức **suy diễn `⚠️`** cho tới khi có tài khoản Super Admin. Xem `AMB-SYS-01`.

---

## 1. Bảng danh mục module

Tổng: **28 module** · đã có tài liệu: **1** · còn trắng: **27**

| # | Module — tên trên website (tiếng Việt) | Prefix | Nền tảng | Trạng thái recon | Mức phủ tài liệu | Tài liệu | REQ đã dùng | Mã kế tiếp | AMB treo | Cập nhật |
|---|---|---|---|---|---|---|---|---|---|---|
| 01 | **Login** (Đăng nhập & Phiên làm việc) | `LOGIN` | Web ✅ | ✅ Đã có tài liệu | ⬜ Trắng | [requirements_login.md](login/requirements_login.md) | 01 → 40 | `REQ-LOGIN-41` | AMB-LOGIN-01, 02, 03, 04, 09 | 20-09-2026 |
| 02 | **Customers** (Khách hàng) | `CUST` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-CUST-01` | — | 19-09-2026 |
| 03 | **Contacts** (Người liên hệ của khách hàng) | `CTC` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-CTC-01` | — | 19-09-2026 |
| 04 | **Leads** (Khách hàng tiềm năng) | `LEAD` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-LEAD-01` | — | 19-09-2026 |
| 05 | **Estimate Request** (Yêu cầu báo giá) | `ESTREQ` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-ESTREQ-01` | — | 19-09-2026 |
| 06 | **Estimates** (Báo giá) | `EST` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-EST-01` | — | 19-09-2026 |
| 07 | **Proposals** (Đề xuất báo giá) | `PROP` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-PROP-01` | — | 19-09-2026 |
| 08 | **Contracts** (Hợp đồng) | `CTR` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-CTR-01` | — | 19-09-2026 |
| 09 | **Items** (Sản phẩm & Dịch vụ) | `ITEM` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-ITEM-01` | — | 19-09-2026 |
| 10 | **Invoices** (Hoá đơn) | `INV` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-INV-01` | — | 19-09-2026 |
| 11 | **Payments** (Thanh toán) | `PAY` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-PAY-01` | — | 19-09-2026 |
| 12 | **Credit Notes** (Giấy báo có) | `CN` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-CN-01` | — | 19-09-2026 |
| 13 | **Subscriptions** (Đăng ký định kỳ) | `SUB` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-SUB-01` | — | 19-09-2026 |
| 14 | **Expenses** (Chi phí) | `EXP` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-EXP-01` | — | 19-09-2026 |
| 15 | **Projects** (Dự án) | `PRJ` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-PRJ-01` | — | 19-09-2026 |
| 16 | **Tasks** (Công việc) | `TASK` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-TASK-01` | — | 19-09-2026 |
| 17 | **Timesheets** (Chấm công) | `TIME` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-TIME-01` | — | 19-09-2026 |
| 18 | **Support** (Hỗ trợ / Ticket) | `TICKET` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-TICKET-01` | — | 19-09-2026 |
| 19 | **Knowledge Base** (Cơ sở tri thức) | `KB` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-KB-01` | — | 19-09-2026 |
| 20 | **Calendar** (Lịch) | `CAL` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-CAL-01` | — | 19-09-2026 |
| 21 | **Media / Bulk PDF Export** (Tệp & Xuất PDF hàng loạt) | `UTIL` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-UTIL-01` | — | 19-09-2026 |
| 22 | **Reports** (Báo cáo) | `RPT` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-RPT-01` | — | 19-09-2026 |
| 23 | **Dashboard** (Bảng điều khiển) | `DASH` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-DASH-01` | — | 19-09-2026 |
| 24 | **Search** (Tìm kiếm toàn cục) | `SEARCH` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-SEARCH-01` | — | 19-09-2026 |
| 25 | **To Do** (Việc cần làm) | `TODO` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-TODO-01` | — | 19-09-2026 |
| 26 | **Reminders** (Nhắc nhở) | `REM` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-REM-01` | — | 19-09-2026 |
| 27 | **Announcements** (Thông báo nội bộ) | `ANN` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-ANN-01` | — | 19-09-2026 |
| 28 | **My Profile** (Hồ sơ cá nhân & Ngôn ngữ) | `PROFILE` | Web ⬜ | ⬜ Chưa khảo sát | ⬜ Trắng | — | — | `REQ-PROFILE-01` | — | 19-09-2026 |

### Quy ước đặt tên module

**Tên module luôn ghi theo dạng `Tên trên website (tiếng Việt)`.** Tên tiếng Anh lấy **nguyên văn nhãn hiển thị trên Perfex CRM** để đối chiếu nhanh giữa tài liệu và màn hình thật; phần tiếng Việt trong ngoặc chỉ để đọc hiểu, **không** dùng làm khoá tra cứu.

| Prefix | Tên trên website | Lấy từ đâu trên UI |
|---|---|---|
| `LOGIN` | **Login** | Tiêu đề trang đăng nhập. `Authentication` chỉ là tên route |
| `CUST` | **Customers** | Sidebar |
| `CTC` | **Contacts** | Nút `Contacts` trên thanh công cụ Customers + tab `Contacts` trong hồ sơ khách hàng |
| `LEAD` | **Leads** | Sidebar |
| `ESTREQ` | **Estimate Request** | Sidebar |
| `EST` | **Estimates** | Sidebar › Sales |
| `PROP` | **Proposals** | Sidebar › Sales |
| `CTR` | **Contracts** | Sidebar |
| `ITEM` | **Items** | Sidebar › Sales. Tiêu đề trang: `Invoice Items` |
| `INV` | **Invoices** | Sidebar › Sales. Gồm `Recurring Invoices` |
| `PAY` | **Payments** | Sidebar › Sales |
| `CN` | **Credit Notes** | Sidebar › Sales |
| `SUB` | **Subscriptions** | Sidebar |
| `EXP` | **Expenses** | Sidebar |
| `PRJ` | **Projects** | Sidebar |
| `TASK` | **Tasks** | Sidebar |
| `TIME` | **Timesheets** | Menu hồ sơ › `My Timesheets`; tiêu đề trang: `Today` |
| `TICKET` | **Support** | Sidebar. Tiêu đề trang: `Support Tickets` |
| `KB` | **Knowledge Base** | Sidebar |
| `CAL` | **Calendar** | Sidebar › Utilities |
| `UTIL` | **Media / Bulk PDF Export** | Sidebar › Utilities. Tiêu đề trang của Media: `Files` |
| `RPT` | **Reports** | Sidebar |
| `DASH` | **Dashboard** | Sidebar |
| `SEARCH` | **Search** | Widget không có nhãn — tên lấy từ `placeholder="Search..."` |
| `TODO` | **To Do** | Biểu tượng `Todo items`; tiêu đề trang: `My To Do Items` |
| `REM` | **Reminders** | Không có trong sidebar — tiêu đề trang |
| `ANN` | **Announcements** | Không có trong sidebar — tiêu đề trang |
| `PROFILE` | **My Profile** | Menu hồ sơ. Tiêu đề trang: `Profile` |

📌 **Tên tệp trong `_discovery/modules/` cũng dùng slug tiếng Anh, KHÔNG kèm tiếng Việt** — `module_02_customers.md`, `module_10_invoices.md`. Người dùng chốt ngày 19-09-2026, để nhìn tên tệp là nhận ra ngay module trên website.

> ⚠️ **Đây là chỗ cố ý lệch với CLAUDE.md mục 6.5**, vốn quy định `<slug>` là *tiếng Việt không dấu*. Lệch có chủ đích, người dùng đã chốt. Mọi đợt khám phá sau của dự án này **phải theo slug tiếng Anh** cho nhất quán — đừng quay về tiếng Việt chỉ vì CLAUDE.md ghi thế.

Tệp gộp nhiều module thì slug **nêu đủ nhóm**, không giấu module nào:

| Tệp | Module bên trong |
|---|---|
| `module_10_invoices.md` | `INV` — gồm Recurring Invoices |
| `module_16_tasks_timesheets.md` | `TASK` · `TIME` |
| `module_19_calendar_media_bulk_pdf.md` | `CAL` · `UTIL` |
| `module_21_dashboard_search.md` | `DASH` · `SEARCH` |
| `module_22_todo_reminders_announcements.md` | `TODO` · `REM` · `ANN` |

### Danh sách prefix đã chiếm

```
ANN · CAL · CN · CTC · CTR · CUST · DASH · EST · ESTREQ · EXP · INV · ITEM
KB · LEAD · LOGIN · PAY · PRJ · PROFILE · PROP · REM · RPT · SEARCH · SUB
TASK · TICKET · TIME · TODO · UTIL
```

`SYS` là prefix **dành riêng** cho AMB/RISK cấp hệ thống — không cấp cho module.

**Module mới phải chọn prefix chưa có trong danh sách trên.**

---

## 2. Trạng thái REQ toàn hệ thống

Bảng được điền dần khi từng module chạy xong `/generate-requirements-from-website`.

| Module | 🟢 Rõ ràng | 🟡 Cần làm rõ | 🔴 Deprecated | ⚪ Chưa implement | Tổng |
|---|---|---|---|---|---|
| **Login** (`LOGIN`) | 39 | — | — | 1 | **40** |
| *(27 module còn lại)* | — | — | — | — | 0 |
| **Toàn hệ thống** | **39** | **—** | **—** | **1** | **40** |

REQ ⚪ duy nhất là `REQ-LOGIN-34` (gửi yêu cầu đặt lại mật khẩu cho email có thật) — chưa kiểm chứng vì thao tác đó **gửi email thật ra ngoài**. Xem `AMB-LOGIN-10`.

---

## 3. Ambiguity 🔴 High còn treo

| Mã | Nội dung | Ảnh hưởng | Cần ai trả lời |
|---|---|---|---|
| `AMB-SYS-01` | Tài khoản trong `.env` không có quyền vào vùng Setup (`/admin/staff` → `/admin/access_denied`). Toàn bộ Vai trò · Phân quyền · Cấu hình · Danh mục hệ thống **chưa khảo sát được**, và ma trận phân quyền của **mọi** module sẽ chỉ ở mức suy diễn `⚠️` | Chặn 28/28 module ở mục Ma trận Phân quyền | PO / Quản trị hệ thống — xin tài khoản Super Admin |
| `AMB-SYS-02` | Hệ thống có những vai trò (role) nào? Không đọc được vì màn hình quản lý vai trò bị chặn | Chặn việc lập ma trận phân quyền cấp hệ thống | PO |
| `AMB-SYS-03` | Cổng Khách hàng/Người dùng nằm ở URL nào, tài khoản nào? Người dùng đã xác nhận có nhưng chưa cung cấp | Chưa lập được bản đồ mặt thứ hai của hệ thống | Người dùng |
| `AMB-LOGIN-01` | Hệ thống có khoá tài khoản sau N lần đăng nhập sai không? Chỉ kiểm được trên email không tồn tại (6 lần không bị chặn); không dám thử trên tài khoản thật vì đó là tài khoản **duy nhất** | `STORY-LOGIN-04` đang `BLOCKED` | Dev / PO — và cần **tài khoản phụ** |
| `AMB-LOGIN-02` | Màn hình Quên mật khẩu trả `Email not found` (lộ email nào chưa đăng ký), trong khi trang đăng nhập cố tình **không** lộ. Cố ý hay sơ suất? | Lỗ hổng dò danh tính nếu là sơ suất | PO / Dev |
| `AMB-LOGIN-03` | Vì sao cookie `autologin` đặt `HttpOnly = false`, trong khi cookie này **một mình đủ để vào hệ thống** và sống ~62 ngày? | XSS ở bất kỳ module nào cũng lấy được token đăng nhập dài ngày | Dev |
| `AMB-LOGIN-04` | Vì sao không ép HTTPS và không đặt header HSTS? | Trang đăng nhập phục vụ được qua kênh không mã hoá | Dev / Quản trị hạ tầng |
| `AMB-LOGIN-09` | Hệ thống có những vai trò nào, mỗi vai trò đăng nhập có khác biệt gì? | 10/20 ô ma trận phân quyền của `LOGIN` là `❔` | PO — hệ quả của `AMB-SYS-01` |

---

## 4. Cấu trúc thư mục chuẩn

```
docs/
├── requirements/
│   ├── README.md                              ← TỆP NÀY — danh mục
│   ├── _discovery/                            ← TẦNG KHÁM PHÁ — cấp hệ thống
│   │   ├── system_map.md                      ← INDEX — TÊN TỆP BẤT BIẾN
│   │   ├── modules/module_NN_<slug>.md        ← chi tiết từng module
│   │   └── evidence/*.png                     ← 1 ảnh tổng quan mỗi module
│   └── <module>/
│       ├── requirements_<module>.md           ← INDEX — TÊN TỆP BẤT BIẾN
│       ├── web/ · mobile/ · api/              ← tầng nền tảng
│       ├── analysis/ · impact/
├── testcases/
│   ├── README.md
│   └── <module>/test_cases_<module>.md
├── executions/
├── bugs/
└── user-guides/
```

---

## 5. Quy trình sử dụng

| Tình huống | Workflow | Ghi vào đâu |
|---|---|---|
| Chưa biết hệ thống có module nào | `/discover-system` | `_discovery/` + tệp này |
| Cần requirements chi tiết cho 1 module web | `/generate-requirements-from-website <module>` | `<module>/requirements_<module>.md` |
| Có ticket sửa đổi module đã có tài liệu | `/update-requirements-from-ticket` | `<module>/impact/` + Nhật ký thay đổi |
| Cần test case | `/generate-testcases-manual-rbt` hoặc `/generate-testcases-from-requirements` | `docs/testcases/<module>/` |
| Phát hiện module bị sót | `/discover-system` **Mode ADD** | Bổ sung dòng vào bảng mục 1 |
| Có mặt mới (app, API) | `/discover-system` **Mode ADD** | Cột `Nền tảng` + `api_map.md` |

---

## 6. Nhật ký danh mục

| Ngày | Thay đổi | Nguồn |
|---|---|---|
| 19-09-2026 | Khởi tạo danh mục. Thêm 26 module, cấp 26 prefix. Chốt tiền tố TC ID `CRM_`, môi trường **không** dùng chung, năng lực QA chỉ có DevTools. Mở `AMB-SYS-01` → `AMB-SYS-03` | `/discover-system` mode UI — mặt Web `/admin` |
| 19-09-2026 | Bổ sung **33 ảnh evidence** phủ 26/26 module (đã mở lại xác minh từng ảnh). Giải 9 nghi vấn trạng thái; phát hiện thêm trạng thái `Cancelled` của `INV`, Thùng rác của `CTR`, tích hợp **Stripe** của `SUB`. Nâng ước REQ của `RPT` từ 25–32 lên 40–55. Ghi nhận **10 module đang không có dữ liệu** — phải tự tạo trước khi recon | `/discover-system` — lượt chụp evidence bằng Playwright + Chrome thật |
| 19-09-2026 | Bổ sung ảnh màn hình **Quên mật khẩu** của `LOGIN` → **34 ảnh**. Lượt trước chỉ chụp *liên kết* chứ chưa mở màn hình phía sau — người dùng phát hiện thiếu. Thêm mục `## Mức xác minh của từng route` vào **cả 21 tệp module**, tách rõ ✅ đã mở thật / 🔗 mới thấy liên kết / ❔ chưa xác minh được | `/discover-system` — bổ sung |
| 19-09-2026 | **Đổi quy ước đặt tên module sang `Tên trên website (tiếng Việt)`** theo yêu cầu người dùng, áp cho toàn bộ `README.md`, `system_map.md` và 21 tệp module — gồm cả tên màn hình trong Danh mục Evidence. Thêm bảng đối chiếu Anh–Việt ở mục 1. **Prefix giữ nguyên tuyệt đối** | `/discover-system` — chuẩn hoá cách đặt tên |
| 19-09-2026 | **Đổi tên 21 tệp module sang slug tiếng Anh** (`module_02_khach_hang.md` → `module_02_customers.md`), cập nhật 72 tham chiếu trong `system_map.md`. Cố ý lệch CLAUDE.md mục 6.5 — người dùng chốt. **Prefix, số thứ tự module và nội dung tệp không đổi** | `/discover-system` — chuẩn hoá tên tệp |
| 19-09-2026 | **Tách `Contacts` khỏi `CUST` và `Payments` khỏi `INV`** theo yêu cầu người dùng. Cấp 2 prefix mới **`CTC`** · **`PAY`** → **28 module / 23 tệp**. Đánh số lại tệp module theo thứ tự khảo sát; đổi tên 2 ảnh evidence theo prefix mới. Ước REQ: `CUST` 85–110 → 55–70, `INV` 70–90 → 50–65. **Sửa lại tổng ước lượng toàn hệ thống thành ~706–942 REQ** — các con số ghi trước đó trong ngày là cộng nhẩm sai | Người dùng chốt |
| 20-09-2026 | Phát hành tài liệu requirements module **`LOGIN`** — 40 REQ, 12 AMB, 6 RISK, 9 Story, 18 ảnh evidence. Cập nhật dòng `LOGIN`: `Nền tảng` → Web ✅, `Trạng thái recon` → ✅, `REQ đã dùng` 01 → 40, `Mã kế tiếp` `REQ-LOGIN-41`, `AMB treo` 5 mã 🔴. Bổ sung bảng trạng thái REQ (mục 2) và 5 ambiguity 🔴 (mục 3) | `/generate-requirements-from-website LOGIN` |
| 20-09-2026 | **Đối chiếu danh mục ↔ thư mục thực tế:** glob `docs/requirements/*/requirements_*.md` ra đúng 1 module (`login`), khớp với dòng duy nhất đang ở trạng thái ✅. Không phát hiện module thiếu dòng, dòng mồ côi, lệch `Mã kế tiếp`, hay trùng prefix | Đối chiếu bắt buộc của workflow |
