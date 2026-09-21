# Master Test Plan — Perfex CRM · Release 1.0

## Kiểm soát tài liệu

### Thông tin tài liệu

| | |
|---|---|
| Mã tài liệu | `test_plan_release_1.0` |
| Phiên bản tài liệu | v1.0 |
| Trạng thái | 🟨 Draft — còn 27 ô chờ thông tin |
| Mức phân loại | ❓ Chờ QA Lead chốt |
| Ngày lập | 21-09-2026 |
| Ngày hiệu lực | — *(chưa duyệt)* |
| Người lập | Luong Ho — trưởng nhóm QA |
| Người review | ❓ Chờ QA Lead chỉ định |
| Người phê duyệt | Anh Tester (QA Lead) · ❓ Product Owner *(chưa có tên)* — chữ ký ở mục 11 |
| Hệ thống · Build | Perfex CRM `3.1.6` (bản demo đào tạo) · phiên bản đợt **v1.0.0** |
| Phiếu đầu vào | [`test_plan_release_1.0.input.yaml`](test_plan_release_1.0.input.yaml) (bản lưu của `plans/master-test-plan/test_plan.config.yaml`) |
| Cấu trúc tài liệu | Biên soạn **theo cấu trúc** ISO/IEC/IEEE 29119-3 — Test Plan · phủ đủ nội dung điển hình của ISTQB CTFL v4.0 mục 5.1.1 · ánh xạ ở mục 12 |

> **Trạng thái hợp lệ:** 🟨 Draft (đang soạn / còn ô treo) → 🟦 Chờ duyệt (đã review, không còn ô treo chặn) → 🟩 Đã duyệt (đủ chữ ký mục 11) → ⬛ Hết hiệu lực (có bản mới thay thế). Sửa nội dung bản 🟩 → quay về 🟨, tăng phiên bản.

### ⛔ Ba điều chặn việc bắt đầu thực thi — đọc trước mọi mục khác

| # | Sự việc | Hệ quả |
|---|---|---|
| 1 | **CUST và PRJ chưa được khảo sát** — 0 REQ, trạng thái ⬜ trong [danh mục](../requirements/README.md). Ước 55–70 và 60–80 REQ | 2/3 module trong phạm vi chưa có cơ sở kiểm thử. Tiêu chí vào #6 và #8 **không đạt** |
| 2 | **Toàn hệ thống có 0 test case** — `docs/testcases/` chưa tồn tại | Tiêu chí vào #8 **không đạt** cho cả 3 module, kể cả LOGIN |
| 3 | **Chỉ có một tài khoản test, không phải Super Admin** (`AMB-SYS-01`, `RISK-LOGIN-05`) | Vùng Setup bị chặn · ma trận phân quyền mọi module ở mức suy diễn · tiêu chí vào #3 **không đạt** |

### Ô còn treo (27)

**Cần QA Lead trả lời (13):**

- **1.** Mức phân loại tài liệu — *(Kiểm soát tài liệu)*
- **2.** Người review plan — *(Kiểm soát tài liệu)*
- **3.** Mục tiêu kiểm thử: 4 mục tiêu ở 1.1 là **agent đề xuất**, chờ duyệt — *(1.1)*
- **4.** Kiểm thử **Tương thích** có làm không — *(3.2 · 3.2.1)*
- **5.** Kiểm thử **Khả năng truy cập** có làm không — *(3.2 · 3.2.1)*
- **6.** Kiểm thử **Khả dụng** có làm không — *(3.2 · 3.2.1)*
- **7.** Kiểm thử **Độ tin cậy & phục hồi** có làm không — *(3.2 · 3.2.1)*
- **8.** Chiến lược tự động hoá: mục tiêu · phạm vi tự động · phần không tự động · tầng kiểm thử · tiêu chí chọn TC · kích hoạt chạy · người bảo trì — 7 ô — *(3.7)*
- **9.** Quy trình trạng thái lỗi: dùng bộ mặc định của repo hay workflow Jira riêng — *(9.1)*
- **10.** Thang Severity · thang Priority: dùng bộ mặc định hay thang riêng — *(9.2 · 9.3)*
- **11.** Người phân loại lỗi (triage) và tần suất họp phân loại — *(9.4)*
- **12.** Ước lượng ở 7.2 **chưa tính công sức recon** CUST và PRJ — cần ước lượng lại sau khi chốt cách xử lý rủi ro R1 — *(7.2 · 8.1)*
- **13.** Tên Product Owner ở mục Bên liên quan và mục Phê duyệt — *(2.4 · 11)*

**Cần Dev Lead trả lời (1):**

- **14.** Thời hạn phản hồi và thời hạn sửa xong theo từng mức Severity (SLA) — không có giá trị mặc định — *(9.4)*

**Cần đội DEV trả lời (7):**

- **15.** Dữ liệu nền của môi trường test: gồm những gì, bao nhiêu bản ghi — *(5.2)*
- **16.** Nguồn dữ liệu: tự sinh khi chạy · nạp sẵn · bản sao production đã che · nhập tay — *(5.2)*
- **17.** Môi trường test có chứa dữ liệu thật của khách hàng không — *(5.2)*
- **18.** Cách che dữ liệu nếu có dữ liệu thật — *(5.2)*
- **19.** Có dọn dữ liệu sau khi chạy không, ai dọn — *(5.2)*
- **20.** Tần suất làm mới dữ liệu nền — *(5.2)*
- **21.** Ai cung cấp dữ liệu kiểm thử — *(5.2)*

**Cần Product Owner / Quản trị hệ thống trả lời (3):**

- **22.** `AMB-SYS-01` — cấp tài khoản **Super Admin** để mở vùng Setup — *(4.1 · 8.1)*
- **23.** `AMB-SYS-02` — hệ thống có những vai trò (role) nào — *(4.1 · 8.1)*
- **24.** `AMB-SYS-03` — cổng Khách hàng nằm ở URL nào, tài khoản nào *(ngoài phạm vi đợt này nhưng chặn việc lập bản đồ hệ thống)* — *(2.2 · 8.1)*

**Cần chốt trước khi bắt đầu thực thi (3):**

- **25.** Ngày build `v1.0.0` được deploy lên môi trường test — *(4.1 #9 · 7.1)*
- **26.** Bộ smoke test dùng cho tiêu chí vào #10 — chưa tồn tại — *(4.1 #10)*
- **27.** Đối chiếu lại tên mục ISO/IEC/IEEE 29119-3 ở bảng 12.1 bằng bản chuẩn thật, **trước khi đem tài liệu đi audit** — agent không có bản chuẩn để tra — *(12.1)*

### Lịch sử thay đổi

| Phiên bản | Ngày | Người sửa | Mục thay đổi | Nội dung | Người duyệt |
|---|---|---|---|---|---|
| v1.0 | 21-09-2026 | Luong Ho | Toàn bộ | Lập mới từ phiếu `test_plan.config.yaml`. Trước khi lập, 4 mâu thuẫn phiếu ↔ repo đã được QA Lead chốt và **sửa lại vào phiếu**: (1) danh sách ngoài phạm vi 20 → **25 module**, sửa 4 prefix sai `CONT→CTC` `TICK→TICKET` `REP→RPT` `PROF→PROFILE`, bổ sung 5 module thiếu `TIME` `CAL` `UTIL` `SEARCH` `ANN`; (2) giả định ước lượng sửa theo repo — **0 TC** chứ không phải 50 TC, CUST và PRJ **chưa recon**; (3) hệ thống CI `GitLab Actions` → **GitHub Actions**; (4) Người lập = **Luong Ho**, Anh Tester giữ vai trò phê duyệt | ❓ |

---

## 1. Mục tiêu & Cơ sở kiểm thử

### 1.1 Mục tiêu kiểm thử

> ⚠️ Phiếu để trống mục này. Bốn mục tiêu dưới đây là **agent đề xuất** dựa trên phạm vi và rủi ro đã ghi trong `docs/` — **chờ QA Lead và PO duyệt** (ô treo #3).

| # | Mục tiêu | Đo bằng |
|---|---|---|
| O1 | Xác nhận luồng đăng nhập, cấp phiên và bảo vệ route của khu vực quản trị `/admin` hoạt động đúng 43 REQ đã chốt của `LOGIN` | Tiêu chí exit #3, #4, #6 |
| O2 | Đưa hai module nghiệp vụ trọng yếu `CUST` và `PRJ` từ trạng thái chưa khảo sát lên có tài liệu requirements và bộ TC đã chạy | Tiêu chí exit #7 — tính theo cặp module × nền tảng |
| O3 | Không còn lỗi Critical đang mở ở luồng đăng nhập và hai module nghiệp vụ trước ngày phát hành | Tiêu chí exit #1 |
| O4 | Đưa ba sai lệch bảo mật đã xác định của `LOGIN` (`RISK-LOGIN-07`) thành bug có mã, có chủ, có trạng thái — thay vì để nằm trong tài liệu requirements | Chỉ số nhóm *Lỗi* ở 3.6 · tiêu chí exit #1, #2 |

### 1.2 Cơ sở kiểm thử (Test basis)

| Tài liệu | Phiên bản / ngày cập nhật | Module | Ghi chú |
|---|---|---|---|
| [`requirements_login.md`](../requirements/login/requirements_login.md) | Nhật ký thay đổi 21-09-2026 | `LOGIN` | 43 REQ (30 🟢 · 9 🟡 · 4 ⚪) · **0 AMB treo** (12/12 đã chốt) · 7 RISK |
| [`impact_LOGIN-AMB-RESOLVE-001.md`](../requirements/login/impact/impact_LOGIN-AMB-RESOLVE-001.md) | 21-09-2026 | `LOGIN` | Đợt chốt ambiguity — nguồn của 9 REQ 🟡 và `RISK-LOGIN-07` |
| [`_discovery/system_map.md`](../requirements/_discovery/system_map.md) | 19-09-2026 | Cấp hệ thống | Bản đồ 28 module · ước REQ · ranh giới đã chốt |
| [`requirements/README.md`](../requirements/README.md) | 21-09-2026 | Cấp hệ thống | Danh mục prefix · thuộc tính dự án · 3 `AMB-SYS` treo |
| Tài liệu requirements `CUST` | ❌ **Chưa có** | `CUST` | Phải sinh bằng `/generate-requirements-from-website` trước khi viết TC |
| Tài liệu requirements `PRJ` | ❌ **Chưa có** | `PRJ` | Như trên |

> ⚠️ **Không có tài liệu đặc tả nào được cung cấp cho dự án này.** Toàn bộ cơ sở kiểm thử sinh từ khảo sát UI thực tế — nghĩa là tài liệu mô tả *hệ thống đang chạy thế nào*, không phải *hệ thống phải chạy thế nào*, trừ 12 điểm đã được chốt lại ngày 21-09-2026.
>
> ⚠️ Cơ sở kiểm thử **đổi giữa đợt** (ticket sửa yêu cầu) → cập nhật bằng `/update-requirements-from-ticket` rồi tăng phiên bản plan — TC viết theo cơ sở cũ là TC sai.

## 2. Phạm vi

### 2.1 Trong phạm vi

> Mỗi dòng là **một module × một nền tảng** — đơn vị báo cáo tiến độ theo dõi và báo cáo tổng hợp chấm tiêu chí exit #7.

| Module | Prefix | Nền tảng | Số REQ | Số TC hiện có | Đã từng chạy? | Ghi chú |
|---|---|---|---|---|---|---|
| **Login** (Đăng nhập & Phiên làm việc) | `LOGIN` | Web | **43** — 30 🟢 · 9 🟡 · 4 ⚪ | **0** | ❌ Chưa | Đã có tài liệu. ⚠️ `REQ-LOGIN-30`, `31`, `38` **trái với build hiện tại** — TC viết theo chúng sẽ FAIL thật, xem `RISK-LOGIN-07` |
| **Customers** (Khách hàng) | `CUST` | Web | **0** — ⬜ chưa khảo sát *(ước 55–70)* | **0** | ❌ Chưa | Danh sách + chi tiết 19 tab · risk 🔴. Phải recon trước |
| **Projects** (Dự án) | `PRJ` | Web | **0** — ⬜ chưa khảo sát *(ước 60–80)* | **0** | ❌ Chưa | Danh sách + chi tiết 18 tab · risk 🔴. Phải recon trước |

**Tổng: 3 cặp module × nền tảng** — cả 3 đều **chưa có test case nào**, 2/3 **chưa có requirements**.

**REQ cần quyết định lại**

Phiếu khai `Đưa lại REQ bị loại vì môi trường: có`. Đối chiếu repo: môi trường của dự án **không dùng chung** ngay từ đầu (chốt 19-09-2026), nên **không có REQ nào bị loại vì lý do môi trường dùng chung**. Bốn REQ ⚪ của `LOGIN` bị loại vì lý do khác — liệt kê ở đây để người duyệt quyết định, **agent không tự đưa lại**:

| REQ | Nội dung | Lý do chưa kiểm chứng được | Quyết định theo phiếu |
|---|---|---|---|
| `REQ-LOGIN-34` | Quên mật khẩu với email có thật | Bấm `Confirm` sẽ **gửi email thật ra ngoài** | ❓ Không thuộc diện "môi trường dùng chung" — chờ QA Lead quyết |
| `REQ-LOGIN-41` | Thời hạn phiên làm việc | Chưa đo được — cần chờ hết hạn phiên thật | ❓ Như trên |
| `REQ-LOGIN-42` | Hành vi trên giao thức HTTPS đầy đủ | Cần **môi trường production**, môi trường test phục vụ qua HTTP | ❓ Như trên — là hạn chế môi trường, nhưng không phải môi trường dùng chung |
| `REQ-LOGIN-43` | Mọi vai trò dùng chung một luồng đăng nhập | Dự án chỉ có **một tài khoản** (`RISK-LOGIN-05`) | ❓ Như trên — gỡ được nếu `AMB-SYS-01` được giải |

### 2.2 NGOÀI phạm vi (out of scope)

| Không kiểm thử | Lý do | Ai chịu trách nhiệm | Nguồn quyết định |
|---|---|---|---|
| **25 module CRM còn lại** — `CTC` · `LEAD` · `ESTREQ` · `EST` · `PROP` · `CTR` · `ITEM` · `INV` · `PAY` · `CN` · `SUB` · `EXP` · `TASK` · `TIME` · `TICKET` · `KB` · `CAL` · `UTIL` · `RPT` · `DASH` · `SEARCH` · `TODO` · `REM` · `ANN` · `PROFILE` | Không thuộc Release 1.0 | Đợt sau | Anh Tester — QA Lead, 17-09-2026 · danh sách sửa lại đúng danh mục 21-09-2026 |
| **Hệ thống Book API** (namespace `_book-api/`) | Không thuộc Release 1.0. ⚠️ Namespace này **chưa tồn tại** trong repo — khai ở đây để giữ dấu vết quyết định | Đợt sau | Anh Tester — QA Lead, 17-09-2026 |
| **Cổng Khách hàng / Người dùng** (khu front-end ngoài `/admin`) | Loại khỏi phạm vi Release 1.0. Đã chốt là **hệ thống xác thực tách biệt**, thuộc module `CTC` (`AMB-LOGIN-11`) | Chưa xếp đợt — chờ PO chốt phạm vi cổng khách hàng | Anh Tester — QA Lead, 17-09-2026 |
| **Mặt Mobile** (app Android / iOS) | Hệ thống chưa khảo sát mặt này. Phiếu khai `Cách chạy mobile: ngoài phạm vi` | Đợt sau | `system_map.md` mục 1 · phiếu đợt này |
| **Mặt API** | Perfex CRM **không có tầng REST API công khai** — dữ liệu bảng nạp bằng `POST /admin/<module>/table` trả JSON cho DataTables. Phiếu khai `Cách chạy API: ngoài phạm vi` | Đợt sau | `README.md` — bảng thuộc tính dự án · phiếu đợt này |
| **Toàn bộ vùng Setup** — Nhân viên · Vai trò & phân quyền · Cấu hình hệ thống · Danh mục (thuế, tiền tệ, phòng ban, loại hợp đồng) · Mục tiêu · Nhật ký hoạt động | **Không truy cập được** — tài khoản hiện tại bị đẩy về `/admin/access_denied`. Chưa cấp prefix, chưa khảo sát | PO / Quản trị hệ thống — cấp tài khoản Super Admin | `AMB-SYS-01` · `README.md` mục ⛔ |
| **Kiểm thử hiệu năng** | Phiếu khai `Hiệu năng: không` | Chưa xếp đợt | Phiếu đợt này |
| **Kiểm thử bảo mật chuyên sâu** (pentest, quét lỗ hổng, thử khai thác) | Phiếu khai `Bảo mật chuyên sâu: không`. ⚠️ Các thuộc tính bảo mật **quan sát được** (`REQ-LOGIN-35` → `39`) vẫn nằm trong kiểm thử chức năng — xem 3.2.1 | Chưa xếp đợt | Phiếu đợt này |
| **Màn hình đặt lại mật khẩu** sau khi bấm liên kết trong email | Chưa kiểm chứng được — bấm `Confirm` gửi email thật ra ngoài (`REQ-LOGIN-34` ⚪) | QA — khi có hộp thư test | `requirements_login.md` mục 1 |
| **Cấu hình thời hạn phiên làm việc** | Nằm trong vùng Setup bị chặn | PO — cấp tài khoản Super Admin | `requirements_login.md` mục 1 |
| **Ma trận quyền chi tiết của từng chức năng** | Thuộc module tương ứng, không thuộc `LOGIN`. Hiện ở mức suy diễn `⚠️✅` vì chỉ có một tài khoản | QA — khi có tài khoản vai trò thứ hai | `requirements_login.md` mục 6 · `RISK-LOGIN-05` |

> ⚠️ **Bảng ISO/IEC 25010 và mục *Vùng loại khỏi phạm vi* chưa có nguồn để đọc** — `docs/testcases/` chưa tồn tại nên chưa có bảng 25010 của module nào, và `system_map.md` không có mục *Vùng loại khỏi phạm vi* riêng. Bảng trên tổng hợp từ mục *Ngoài phạm vi* của `system_map.md` mục 1, mục 1 của `requirements_login.md`, và phiếu đợt này. **Khi `/generate-testcases-manual-rbt` chạy xong cho 3 module, phải đối chiếu lại bảng này với các ô `➖` của bảng 25010** và tăng phiên bản plan nếu có chênh.
>
> Mục này đã được thống nhất với **Anh Tester — QA Lead** ngày 17-09-2026, danh sách module sửa lại đúng danh mục ngày 21-09-2026. Thay đổi phạm vi phải cập nhật tài liệu, tăng phiên bản và thông báo lại.

### 2.3 Giả định & Ràng buộc

| Loại | Nội dung | Ảnh hưởng tới kiểm thử | Nguồn |
|---|---|---|---|
| Ràng buộc | Tài khoản test **không phải Super Admin** — toàn bộ vùng Setup bị chặn | Ma trận phân quyền của mọi module chỉ ở mức suy diễn `⚠️`. Không xem được nhật ký hoạt động để đối chiếu | `AMB-SYS-01` · `README.md` |
| Ràng buộc | Dự án chỉ có **một tài khoản** | Không kiểm được khác biệt giữa các vai trò. Không dám thử kịch bản khoá tài khoản — hỏng là mất quyền truy cập toàn dự án | `RISK-LOGIN-05` · `RISK-LOGIN-06` |
| Ràng buộc | Năng lực kiểm thử của QA: chỉ có **DevTools trình duyệt**. Không có quyền gọi API, truy vấn CSDL, kiểm tầng tích hợp, xem nhật ký hoạt động | Nhánh **Vòng 3 (kỹ thuật)** của mọi bộ TC phải chuyển cho đội Dev xác minh — đây **không** phải vùng trắng | `README.md` — bảng thuộc tính dự án, chốt 19-09-2026 |
| Ràng buộc | Perfex CRM là ứng dụng PHP render phía máy chủ, **không** phải SPA · **không** có tầng REST API công khai | Không có mặt API để kiểm độc lập. Mọi xác minh phải đi qua UI | `README.md` — bảng thuộc tính dự án |
| Ràng buộc | Hệ thống phục vụ qua **HTTP**, không có HSTS | `REQ-LOGIN-42` (hành vi trên HTTPS đầy đủ) không kiểm được trên môi trường test | `RISK-LOGIN-03` |
| Giả định | Môi trường test **riêng cho Release 1.0**, không dùng chung với đội khác | Được phép tạo / sửa / xoá dữ liệu khi khảo sát và chạy test. Thao tác phá huỷ được phép | Phiếu đợt này · khớp `README.md` (chốt 19-09-2026) |
| Giả định | Đội DEV dựng xong môi trường test đúng hạn 05-10-2026 | Trễ một ngày là trễ ngày bắt đầu thực thi tương ứng — xem `R2` ở 8.1 | Phiếu đợt này |
| Giả định | `CUST` và `PRJ` recon được bằng `/generate-requirements-from-website` trong khung thời gian trước 02-10-2026 | Nếu không kịp, không có cơ sở kiểm thử để viết TC — xem `R1` ở 8.1 | Phiếu đợt này — giả định ước lượng |
| Giả định | 10 module ghi nhận *đang không có dữ liệu* không bao gồm `CUST` và `PRJ` ở mức chặn recon | Nếu hai module này rỗng dữ liệu, QA phải tự tạo trước khi khảo sát — xem `R8` ở 8.1 | `system_map.md` mục 7 — *Dữ liệu trống* |

### 2.4 Các bên liên quan & Giao tiếp

| Bên | Vai trò trong đợt | Liên quan tới kiểm thử | Nhận gì | Tần suất | Kênh |
|---|---|---|---|---|---|
| **Anh Tester** | QA Lead — duyệt plan | Phê duyệt kế hoạch · chấp nhận dừng khi hết thời gian/ngân sách (cùng PO) | Kế hoạch · báo cáo tiến độ · báo cáo tổng hợp | Hằng tuần **thứ Sáu** · cuối đợt | Jira · repo |
| **❓ *(chưa có tên)*** | Product Owner / BA — duyệt plan · thực hiện UAT | Nguồn quyết định phạm vi · thực hiện nghiệm thu 30-11 → 04-12-2026 | Kế hoạch · báo cáo tiến độ · báo cáo tổng hợp | Hằng tuần **thứ Sáu** · cuối đợt | Email · Jira |
| **Đội DEV** | Sửa bug · dựng và duy trì môi trường test riêng | Nhận bug · dựng môi trường hạn 05-10-2026 · xác minh nhánh Vòng 3 thay QA | Báo cáo lỗi | Khi phát sinh | Jira |
| **Luong Ho** | Trưởng nhóm QA — người lập plan · kiêm tự động hoá | Lập kế hoạch · điều phối 3 kiểm thử viên · viết và bảo trì script | *(người lập)* | — | Repo |

**Mẫu tài liệu dùng trong đợt** — phiếu khai `Dùng mẫu có sẵn của repo: có`:

| Tài liệu | Mẫu | Workflow sinh |
|---|---|---|
| Requirements | Mẫu của `skills-requirements-analyzer` | `/generate-requirements-from-website` |
| Test case | Mẫu của `skills-rbt-manual-testing` | `/generate-testcases-manual-rbt` |
| Execution report | Mẫu của `skills-manual-test-executor` | `/execute-test-cases` |
| Bug report | Mẫu của `skills-bug-reporter` | `/create-bug-report` |
| Báo cáo tiến độ | Mẫu của `skills-test-progress-reporter` | `/generate-test-progress-report` |
| Báo cáo tổng hợp | Mẫu của `skills-test-summary-reporter` | `/generate-test-summary-report` |

## 3. Chiến lược kiểm thử (Test approach)

### 3.1 Cấp độ kiểm thử

| Cấp độ | Trong đợt? | Ai thực hiện | Tiêu chí vào/ra |
|---|---|---|---|
| Component (unit) | ❌ ngoài phạm vi QA | Đội Dev | Theo quy trình Dev |
| Component integration | ❌ ngoài phạm vi QA | Đội Dev | Theo quy trình Dev |
| System | ✅ | QA | Bộ chung mục 4 |
| System integration | ✅ | QA | Bộ chung mục 4 |
| Acceptance (UAT) | ✅ | PO/BA nội bộ, QA hỗ trợ | Bộ chung mục 4 |

> ISTQB CTFL v4.0 khuyến nghị tiêu chí vào/ra **theo từng cấp độ**. Phiếu để trống ô `Tiêu chí riêng` của cả 5 cấp độ → ba cấp độ trong đợt đều dùng **bộ chung mục 4**.
>
> ⚠️ **System integration ở đợt này bị giới hạn nặng:** QA không có quyền gọi API, truy vấn CSDL hay kiểm tầng tích hợp (2.3). Kiểm tích hợp chỉ thực hiện được ở mức **UI liên module** — xem dòng *Tích hợp liên module* ở 3.2.

### 3.2 Loại kiểm thử

| Loại test | Nền tảng | Có làm? | Cách làm | Ghi chú |
|---|---|---|---|---|
| Kiểm thử chức năng | Web | ✅ | Manual theo TC — `/execute-test-cases` | Toàn bộ TC của 3 module chạy tay |
| Kiểm thử chức năng | Mobile | ❌ | — | Ngoài phạm vi — xem 2.2 |
| Kiểm thử chức năng | API | ❌ | — | Ngoài phạm vi — hệ thống không có REST API công khai |
| Hồi quy | Web | ✅ | Vòng hồi quy 20-11 → 25-11-2026 · bộ automation hỗ trợ | 4 ngày làm việc |
| Kiểm thử lại lỗi (retest) | Web | ✅ | `/retest-fixed-bugs` | Critical/Major chạy **mode FULL**, Minor/Trivial mode RETEST |
| Tích hợp liên module | Web | ✅ | `/generate-cross-module-test-plan` — mode BROWSER | `LOGIN` là cổng vào của cả `CUST` và `PRJ`; `PRJ` gắn với khách hàng của `CUST` |
| Tự động hoá | Web | ✅ | `/generate-automation-framework` → `/generate-automation-web` | ⚠️ Repo **chưa có** project automation lẫn CI — phải dựng từ đầu. Chi tiết 3.7 |
| Nghiệm thu (UAT) | Web | ✅ | PO/BA nội bộ thực hiện, QA hỗ trợ | 30-11 → 04-12-2026 |
| **Vòng 3 — kỹ thuật** | Web | ⚠️ Một phần | DevTools ✅ QA tự làm · gọi API / CSDL / tích hợp / nhật ký hoạt động ❌ → **đội Dev xác minh** | Đây **không** phải vùng trắng — có người chịu trách nhiệm. Nguồn: `README.md` *Năng lực kiểm thử của QA* |
| Hiệu năng | — | ❌ | — | Ngoài phạm vi — xem 2.2 · 3.2.1 |
| Bảo mật chuyên sâu | — | ❌ | — | Ngoài phạm vi — xem 2.2 · 3.2.1 |
| Tương thích · Khả năng truy cập · Khả dụng · Độ tin cậy & phục hồi | — | ❓ | ❓ | Phiếu để trống — ô treo #4 → #7 |

**Tỷ trọng thủ công / tự động** *(theo phiếu)*: chạy manual **toàn bộ** TC trên web · automate bộ **Smoke** và **regression** của `LOGIN`, `CUST`, `PRJ` bằng Playwright.

**Thứ tự ưu tiên thực thi** — theo rủi ro sản phẩm (8.2):

1. **`LOGIN` × web** trước tiên — là **cổng vào duy nhất** của khu vực quản trị. Hỏng ở đây thì 27 module còn lại không tiếp cận được, và cả `CUST` lẫn `PRJ` đều không chạy được TC nào
2. **`CUST` × web** — `PRJ` tham chiếu khách hàng, nên dữ liệu khách hàng phải có trước
3. **`PRJ` × web**

#### 3.2.1 Kiểm thử phi chức năng

> Đủ **6 dòng**, kể cả loại không làm. Loại `có` phải có **ngưỡng đo được**. Phiếu không khai dòng nào trong nhóm `Phi chức năng` → không có ngưỡng nào được điền, agent **không** tự đặt số.

| Loại | Có làm? | Mục tiêu đo | Ngưỡng chấp nhận | Cách làm · công cụ | Môi trường | Ai thực hiện | TC đã có |
|---|---|---|---|---|---|---|---|
| Hiệu năng | ❌ | — | — | — | — | — | 0 — chưa có TC nào |
| Bảo mật | ❌ *(chuyên sâu)* | — | — | — | — | — | 0 — chưa có TC nào |
| Tương thích | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | 0 — chưa có TC nào |
| Khả năng truy cập | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | 0 — chưa có TC nào |
| Khả dụng | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | 0 — chưa có TC nào |
| Độ tin cậy & phục hồi | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | 0 — chưa có TC nào |

> ⚠️ **Bảo mật chuyên sâu nằm ngoài phạm vi, nhưng `LOGIN` đã có 4 rủi ro bảo mật được ghi nhận** (`RISK-LOGIN-01` → `04`) và 3 yêu cầu bảo mật mà build hiện tại vi phạm (`REQ-LOGIN-30`, `31`, `38`). Các điểm này kiểm bằng **kiểm thử chức năng theo AC quan sát được** (DevTools đọc cookie, đọc thông báo) — **không** phải bằng pentest. Mục 2.2 loại trừ pentest, **không** loại trừ nhóm REQ này.
>
> ⚠️ Môi trường khai **2 trình duyệt** (Chrome chính, Firefox) trong khi ô `Tương thích` để trống. Nếu ý định là kiểm trên 2 trình duyệt thì đó **là** kiểm thử tương thích và cần ngưỡng — chờ QA Lead trả lời ô treo #4.
>
> Ngưỡng của loại `có` là ứng viên cho **bảng tiêu chí ra bổ sung** (4.2) — gợi ý cho người duyệt, agent **không** tự thêm.

### 3.3 Kỹ thuật thiết kế test

> Chỉ liệt kê kỹ thuật mà bộ TC trong phạm vi **thực sự đã dùng**. `docs/testcases/` chưa tồn tại → chưa có bộ TC nào để đọc.

| Kỹ thuật | Áp dụng ở đâu |
|---|---|
| ❓ Xác định khi sinh TC | `LOGIN` × web — `/generate-testcases-manual-rbt` |
| ❓ Xác định khi sinh TC | `CUST` × web — sau khi recon xong |
| ❓ Xác định khi sinh TC | `PRJ` × web — sau khi recon xong |

> Mục này **phải cập nhật** sau khi 3 bộ TC được sinh, kèm tăng phiên bản plan.

### 3.4 Mức độc lập của kiểm thử

| | |
|---|---|
| Mức độ | **Đội QA riêng trong tổ chức** *(theo phiếu)* |
| Thể hiện ở đâu | Nhóm QA 4 người (1 trưởng nhóm + 3 kiểm thử viên) tách khỏi đội DEV. QA viết và chạy TC, đội DEV sửa bug và dựng môi trường — hai vai trò không chồng lên nhau |
| Giới hạn | **Hai điểm làm giảm độc lập trên thực tế:** (1) nhánh Vòng 3 (gọi API · CSDL · tích hợp · nhật ký hoạt động) phải **nhờ chính đội Dev xác minh** vì QA không có quyền — người viết mã tự kiểm phần mã của mình; (2) UAT do **PO/BA nội bộ** thực hiện, không phải khách hàng bên ngoài |

### 3.5 Retest & Regression

- Bug đã fix → `/retest-fixed-bugs`: **Critical / Major** chạy **mode FULL** (verify bug + regression quanh vùng fix), **Minor / Trivial** chạy mode RETEST
- Mỗi build mới → chạy bộ **Smoke** trước khi thực thi tiếp *(bộ smoke chưa tồn tại — ô treo #26)*
- Vòng regression trước phát hành: **20-11 → 25-11-2026** (4 ngày làm việc), dùng bộ regression của 3 module + suite automation
- ⚠️ Ba REQ trái build (`RISK-LOGIN-07`) → TC gắn nhãn `known-bug`, **FAIL là kết quả đúng** cho tới khi dev sửa. ⛔ **CẤM hạ AC xuống theo hành vi hiện tại** để test xanh

### 3.6 Chỉ số theo dõi

> Nhóm theo ISTQB CTFL v4.0 mục 5.3.1. `/generate-test-progress-report` báo cáo **đúng các chỉ số này** mỗi kỳ, gửi **thứ Sáu hằng tuần**.

| Nhóm | Chỉ số | Nguồn | Dùng để |
|---|---|---|---|
| Tiến độ kiểm thử | TC đã viết / đã review · TC đã chạy / chưa chạy · PASS · FAIL · BLOCKED · SKIPPED | `docs/testcases/` · `execution_report.md` | Báo cáo tiến độ · tiêu chí exit #3, #4, #5, #7 |
| Tiến độ kiểm thử | **REQ đã sinh cho `CUST` và `PRJ`** — chỉ số riêng của đợt này vì 2 module bắt đầu từ 0 | `docs/requirements/README.md` mục 2 | Theo dõi rủi ro `R1` ở 8.1 |
| Tiến độ dự án | Công sức thực tế so với ước lượng 7.2 | Báo cáo tiến độ | Phát hiện trễ sớm |
| Lỗi | Bug mới / đã fix / đang mở theo Severity · regression phát sinh | `docs/bugs/` · Jira | Tiêu chí exit #1, #2 |
| Độ phủ | REQ có TC · REQ Critical có TC PASS | `traceability_matrix.md` | Tiêu chí exit #6 |
| Rủi ro | Trạng thái từng rủi ro ở 8.1 | Báo cáo tiến độ | Kiểm soát rủi ro |

### 3.7 Chiến lược tự động hoá

| | |
|---|---|
| Mục tiêu | ❓ *(ô treo #8)* |
| Hiện trạng | ⚠️ **Chưa có gì** — đọc từ repo: không có `package.json` / `pom.xml` / `pyproject.toml`, không có `.github/workflows/` / `.gitlab-ci.yml` / `Jenkinsfile`, **0 script**. Phải dựng framework từ đầu trong đợt này |
| Tầng kiểm thử (kim tự tháp) | ❓ *(ô treo #8)* — ⚠️ Hệ thống **không có REST API công khai** nên tầng API gần như không dùng được; kim tự tháp sẽ **dồn hết lên tầng UI**, là tầng chậm và dễ vỡ nhất (ISTQB CTFL v4.0 mục 5.1.6). Cần cân nhắc khi đặt kỳ vọng |
| Tiêu chí chọn TC để tự động | ❓ *(ô treo #8)* |
| Framework · report | **Playwright + TypeScript + Allure report** *(theo phiếu)* · output gom trong `reports/` theo `reporting_rules.md` |
| Hệ thống CI | **GitHub Actions** (`.github/workflows/`) — chốt 21-09-2026. ⚠️ Repo chưa có file cấu hình, cần dựng |
| Người bảo trì | ❓ *(ô treo #8)* — mục Nhân lực ghi **Luong Ho** kiêm vai trò tự động hoá cho cả 3 module |

**Phạm vi:**

| Tự động | Không tự động | Lý do không tự động |
|---|---|---|
| Bộ **Smoke** của `LOGIN` · `CUST` · `PRJ` × web *(theo ô `Tỷ trọng thủ công / tự động`)* | ❓ *(ô treo #8)* | ❓ |
| Bộ **regression** của `LOGIN` · `CUST` · `PRJ` × web *(theo ô `Tỷ trọng thủ công / tự động`)* | | |

**Kích hoạt chạy:**

| Bộ chạy | Khi nào | Môi trường | Ai xem kết quả | Fail thì |
|---|---|---|---|---|
| Smoke | ❓ *(ô treo #8)* | ❓ | ❓ | Chặn thực thi manual — tiêu chí tạm dừng 4.3 |
| Regression | ❓ *(ô treo #8)* | ❓ | ❓ | Phân loại bằng `/run-and-fix-tests` — **không** sửa test để né bug |

> ⚠️ **Lịch 7.1 chưa có mốc nào cho việc dựng framework và CI.** Bộ regression phải chạy được **trước** ngày `Hồi quy đến` 25-11-2026, mà tính tới ngày lập plan chưa có dòng code nào. Xem rủi ro `R5` ở 8.1.

**Nguyên tắc:**
- Script chỉ tính là xong khi đạt Definition of Done của `CLAUDE.md` — PASS ổn định ≥ 2 lần liên tiếp, đủ Allure metadata (tên Tiếng Việt · Description · Severity · Tags · TC ID) và screenshot cuối mọi test
- Kết quả automation **báo riêng**, không cộng vào pass rate manual của tiêu chí exit #3, #4
- Test chập chờn → `/analyze-flaky-tests`, **không** chạy lại tới khi xanh · UI đổi → `/heal-locators` · yêu cầu đổi → `/update-automation-from-impact`

## 4. Tiêu chí Vào / Ra

### 4.1 Tiêu chí VÀO (Entry) — chưa đủ thì CHƯA bắt đầu test

> Nhóm theo ISTQB CTFL v4.0 mục 5.1.3: nguồn lực · testware · chất lượng ban đầu. **Trạng thái tại ngày lập plan 21-09-2026.**

| # | Nhóm | Điều kiện | Trạng thái |
|---|---|---|---|
| 1 | Nguồn lực | Nhân lực ở mục 6 đã được phân công, đủ người cho mọi cặp module × nền tảng | ✅ **Đạt** — 3/3 cặp có người phụ trách |
| 2 | Nguồn lực | Môi trường test sẵn sàng, có dữ liệu nền | ❓ Dự kiến **05-10-2026**, đội DEV dựng. Dữ liệu nền chưa khai *(ô treo #15)* |
| 3 | Nguồn lực | Tài khoản test đủ mọi vai trò trong phạm vi | ❌ **Không đạt** — chỉ có **một** tài khoản, không phải Super Admin (`AMB-SYS-01`, `RISK-LOGIN-05`) |
| 4 | Nguồn lực | Công cụ sẵn sàng: quản lý bug · quản lý kết quả · automation | 🟨 **Một phần** — Jira + repo markdown đã có · **automation framework và CI chưa tồn tại** |
| 5 | Nguồn lực | Ngân sách đã duyệt | ➖ Không áp dụng — không có ngân sách riêng cho kiểm thử (7.3) |
| 6 | Testware | Tài liệu requirements của mọi module × nền tảng trong phạm vi đã có | ❌ **Không đạt** — 1/3 (`LOGIN` ✅ · `CUST` ⬜ · `PRJ` ⬜) |
| 7 | Testware | AMB 🔴 đã được giải đáp hoặc người duyệt chấp nhận treo | ❌ **Không đạt** — `AMB-SYS-01` · `AMB-SYS-02` · `AMB-SYS-03` còn treo. *(`LOGIN` sạch — 12/12 đã chốt ✅)* |
| 8 | Testware | Test case đã viết và đã review — đủ từng nền tảng | ❌ **Không đạt** — **0 TC** toàn hệ thống, `docs/testcases/` chưa tồn tại |
| 9 | Chất lượng ban đầu | Build `v1.0.0` đã deploy và truy cập được | ❓ Chưa có ngày deploy *(ô treo #25)* |
| 10 | Chất lượng ban đầu | Smoke test đã pass | ❓ **Bộ smoke chưa tồn tại** *(ô treo #26)* |
| 11 | Chất lượng ban đầu | *(mobile)* Bản build app cài được trên thiết bị | ➖ Không áp dụng — mobile ngoài phạm vi |
| 12 | Chất lượng ban đầu | *(API)* Snapshot spec khớp build đang test · có quyền gọi thử | ➖ Không áp dụng — API ngoài phạm vi |

**Tổng kết: 1 đạt · 4 không đạt · 3 chờ thông tin · 2 một phần · 2 không áp dụng.**

> ⚠️ Bắt đầu test khi chưa đạt tiêu chí vào là nguyên nhân số một khiến kết quả kiểm thử không dùng được — BLOCKED tràn lan, phải chạy lại từ đầu. Ngày `Bắt đầu thực thi` **06-10-2026** chỉ giữ được nếu **#3, #6, #7, #8** được xử lý trước đó — xem `R1` · `R2` · `R3` ở 8.1.

### 4.2 Tiêu chí RA (Exit)

> Bảng mặc định lấy **nguyên văn** từ `skills-test-summary-reporter`. `/generate-test-summary-report` chấm lại **cả bảng mặc định lẫn bảng bổ sung**.

| # | Tiêu chí | Ngưỡng |
|---|---|---|
| 1 | Bug **Critical** đang mở | **0** |
| 2 | Bug **Major** đang mở | 0, hoặc có workaround được PM chấp nhận bằng văn bản |
| 3 | Pass rate TC **Priority High** | **≥ 95%** |
| 4 | Pass rate toàn bộ TC đã chạy | ≥ 90% |
| 5 | Tỷ lệ **BLOCKED** | ≤ 5% |
| 6 | REQ mức Critical có ít nhất 1 TC **PASS** | 100% |
| 7 | Module trong phạm vi release đã có TC và đã chạy — tính trên **từng cặp module × nền tảng** trong phạm vi | 100% |

**Tiêu chí bổ sung của dự án:** phiếu khai `Bộ tiêu chí ra: mặc định` và không khai tiêu chí bổ sung nào → **không có bảng bổ sung**.

☑ **Bộ mặc định**  ☐ Bộ mặc định + bổ sung  ☐ Bộ tiêu chí riêng của dự án

> Gợi ý ISTQB CTFL v4.0 mục 5.1.3 cho bảng bổ sung nếu dự án muốn thêm sau: mật độ lỗi · đã thực hiện kiểm thử tĩnh (review requirements/TC) · mọi lỗi tìm thấy đã được báo cáo · toàn bộ regression đã được automate.

> ⚠️ **Tiêu chí #7 là tiêu chí khó đạt nhất của đợt này** — cả 3 cặp module × nền tảng hiện đều ở mức 0 TC, và 2/3 chưa có requirements.
>
> ⚠️ **Dừng kiểm thử khi hết thời gian hoặc ngân sách** (ISTQB CTFL v4.0 mục 5.1.3): được coi là hợp lệ **chỉ khi** **Product Owner cùng Anh Tester — QA Lead** đã xem xét và **chấp nhận bằng văn bản** rủi ro phát hành mà chưa đạt đủ tiêu chí. Báo cáo tổng hợp khi đó ghi rõ tiêu chí nào chưa đạt và ai chấp nhận — **không** chấm lại thành "Đạt".

### 4.3 Tiêu chí TẠM DỪNG (Suspension) & tiếp tục

**Tạm dừng kiểm thử khi:**

- Môi trường sập > **4 giờ**
- Build lỗi không đăng nhập được *(với hệ thống này là chặn toàn bộ — `LOGIN` là cổng vào duy nhất)*
- \> **30% TC BLOCKED** cùng một nguyên nhân
- Phát hiện bug **Critical** chặn luồng chính

**Tiếp tục khi:** nguyên nhân đã xử lý, có build mới, và đã chạy lại smoke.

> ✅ Ngưỡng trên **đã được xác nhận** — phiếu khai `Dùng ngưỡng tạm dừng đề xuất: có`.
>
> *(Dòng ngưỡng dành cho mobile không áp dụng — mobile ngoài phạm vi.)*

## 5. Môi trường, Dữ liệu & Công cụ

### 5.1 Môi trường kiểm thử

| | |
|---|---|
| Môi trường | **Môi trường test riêng cho Release 1.0** — URL lưu ở `.env`, **không** ghi vào tài liệu này |
| Dùng chung với đội khác? | **Không** — được phép tạo / sửa / xoá dữ liệu, thao tác phá huỷ được phép. Khớp với `docs/requirements/README.md` (chốt 19-09-2026) |
| Khác môi trường đã khảo sát? | ❓ Chưa rõ — khảo sát ngày 19-09-2026 chạy trên bản demo Perfex CRM `3.1.6`. Nếu môi trường mới dựng khác bản đó thì tài liệu requirements có thể lệch → cần đối chiếu lại khi môi trường sẵn sàng (`R10` ở 8.1) |
| Web — trình duyệt | **Google Chrome** (chính) · **Firefox**. ⚠️ Khảo sát chỉ chạy trên Chrome — mọi AC dựa trên **thông báo mặc định của trình duyệt** chỉ đúng với Chrome và **không được dùng làm assertion** |
| Giao thức | ⚠️ Hệ thống phục vụ qua **HTTP**, không có HSTS (`RISK-LOGIN-03`). `REQ-LOGIN-42` không kiểm được trên môi trường này |
| Người dựng · ngày sẵn sàng | **Đội DEV** · **05-10-2026** |

### 5.2 Quản lý dữ liệu kiểm thử

| | |
|---|---|
| Dữ liệu nền | ❓ *(ô treo #15)* |
| Nguồn dữ liệu | ❓ *(ô treo #16)* |
| Tài khoản test | ⚠️ **Một tài khoản duy nhất**, **không** phải Super Admin — chỉ tên vai trò được ghi, mật khẩu ở `.env`. Không đủ để kiểm ma trận phân quyền (`RISK-LOGIN-05`) |
| Dữ liệu thật của khách hàng | ❓ *(ô treo #17)* — chưa che được vì chưa biết có hay không *(ô treo #18)* |
| Quy tắc sinh dữ liệu | Random + traceable theo `CLAUDE.md` mục 7 — nhìn bản ghi biết test nào tạo. Ví dụ: `auto_cust_<timestamp>@test.local` |
| Dọn dữ liệu sau khi chạy | ❓ *(ô treo #19)* |
| Làm mới dữ liệu nền | ❓ *(ô treo #20)* |
| Người cung cấp | ❓ *(ô treo #21)* |

> ⚠️ **Toàn bộ nhóm dữ liệu kiểm thử đang trống.** Với môi trường **không dùng chung**, đợt này được phép thao tác phá huỷ — càng cần chốt ai dọn và làm mới ra sao, nếu không dữ liệu sẽ bẩn dần qua 33 ngày thực thi.
>
> ⚠️ Nhật ký khám phá ghi nhận **10 module đang không có dữ liệu** — nếu `CUST` hoặc `PRJ` nằm trong số đó thì phải **tự tạo dữ liệu trước khi recon**, công sức này chưa nằm trong ước lượng 7.2. Xem `R8` ở 8.1.
>
> 🔒 Dữ liệu thật của khách hàng lọt vào evidence (ảnh chụp, bug report) là rủi ro lộ dữ liệu.

### 5.3 Công cụ

| Mục đích | Công cụ | Ghi chú |
|---|---|---|
| Quản lý lỗi | **Jira** · file markdown trong `docs/bugs/` | **Nguồn chính khi lệch:** Jira cho **trạng thái bug** |
| Quản lý kết quả kiểm thử | **Jira** · file markdown trong `docs/executions/` | **Nguồn chính khi lệch:** repo cho **execution report** |
| Tự động hoá | **Playwright + TypeScript + Allure report** | ⚠️ Chưa tồn tại trong repo — chi tiết 3.7 |
| CI | **GitHub Actions** (`.github/workflows/`) | ⚠️ Chưa tồn tại trong repo |
| Khác | — | |

## 6. Nhân lực & Phân công

| Vai trò | Người | Module × nền tảng phụ trách | Ghi chú |
|---|---|---|---|
| Trưởng nhóm QA | **Luong Ho** | Toàn bộ | Người lập plan · điều phối · kiêm vai trò tự động hoá |
| Kiểm thử viên | **Hồng** | `LOGIN` × web | Module cổng vào — chạy trước tiên |
| Kiểm thử viên | **Lan** | `CUST` × web | Module chưa recon — phải khảo sát trước khi viết TC |
| Kiểm thử viên | **Huệ** | `PRJ` × web | Module chưa recon — phải khảo sát trước khi viết TC |
| Tự động hoá | **Luong Ho** *(kiêm)* | `LOGIN` × web · `CUST` × web · `PRJ` × web | ⚠️ Một người vừa điều phối toàn đợt vừa dựng framework từ đầu — điểm nghẽn, xem `R9` ở 8.1 |

**Tổng: 4 người** *(Luong Ho giữ 2 vai trò)*. Phủ đủ **3/3** cặp module × nền tảng.

**Nhu cầu đào tạo:** không khai ở phiếu — hiểu là **không có**.
**Nhu cầu tuyển thêm:** **Không** — phiếu khai `Cần tuyển thêm người: không`.

## 7. Lịch trình, Ước lượng & Ngân sách

### 7.1 Lịch trình & Mốc

| Mốc | Ngày | Thứ | Điều kiện hoàn thành |
|---|---|---|---|
| Duyệt plan | **21-09-2026** | Thứ 2 | Mục 11 có chữ ký |
| Hoàn tất viết & review TC | **02-10-2026** | Thứ 6 | Đủ 3 module × web. ⚠️ Chỉ **9 ngày làm việc** kể từ ngày duyệt plan, mà `CUST` và `PRJ` **chưa có REQ nào** — xem `R1` |
| Môi trường sẵn sàng | **05-10-2026** | Thứ 2 | Tiêu chí vào #2, #3 |
| Bắt đầu thực thi | **06-10-2026** | Thứ 3 | Đạt **toàn bộ** tiêu chí vào 4.1 — hiện 4 tiêu chí không đạt |
| Báo cáo tiến độ | **Hằng tuần · thứ Sáu** | Thứ 6 | `/generate-test-progress-report` — slug `release_1.0` |
| Đóng băng mã nguồn (code freeze) | **19-11-2026** | Thứ 5 | — |
| Hồi quy | **20-11 → 25-11-2026** | Thứ 6 → Thứ 4 | 4 ngày làm việc · bộ regression + automation |
| Nghiệm thu (UAT) | **30-11 → 04-12-2026** | Thứ 2 → Thứ 6 | 5 ngày làm việc · PO/BA nội bộ, QA hỗ trợ |
| Báo cáo tổng hợp | **10-12-2026** | Thứ 5 | `/generate-test-summary-report` — slug `release_1.0` |
| Phát hành | **15-12-2026** | Thứ 3 | Đạt tiêu chí ra 4.2, hoặc có văn bản chấp nhận rủi ro |

> ✅ **Thứ tự các mốc hợp lệ**, không mốc nào rơi vào cuối tuần.
>
> ⚠️ **Lịch thiếu hai mốc cần thiết:** (1) ngày hoàn tất **recon `CUST` và `PRJ`** — phải trước ngày hoàn tất viết TC; (2) ngày **framework automation và CI sẵn sàng** — phải trước ngày bắt đầu hồi quy 20-11-2026.
>
> ⚠️ **Khoảng trống 26-11 → 27-11** (2 ngày làm việc) giữa kết thúc hồi quy và bắt đầu UAT không được khai mục đích — nếu là đệm sửa bug thì nên ghi rõ.

### 7.2 Ước lượng công sức

| | |
|---|---|
| Kỹ thuật (ISTQB CTFL v4.0 mục 5.1.4) | **Three-point estimation** (ước lượng ba điểm) |
| Công thức | `E = (a + 4m + b) / 6` · `SD = (b − a) / 6` · **E tổng** = cộng E từng hạng mục · **SD tổng** = cộng SD từng hạng mục *(cách cộng thận trọng)* |
| Giả định của ước lượng | Toàn hệ thống hiện có **0 TC** — phải viết TC cho cả 3 module · `CUST` và `PRJ` **chưa recon**, 0 REQ (ước 55–70 và 60–80 REQ), phải chạy `/generate-requirements-from-website` trước khi viết TC · **bảng dưới KHÔNG bao gồm công sức recon hai module đó** · không tính công sức PO/BA làm UAT · không tính thời gian Dev sửa bug · không tính công sức dựng framework automation và CI |

| Hạng mục | a (lạc quan) | m (khả năng nhất) | b (bi quan) | E = (a+4m+b)/6 | SD = (b−a)/6 |
|---|---|---|---|---|---|
| Viết & review TC cho `LOGIN` · `CUST` · `PRJ` × web | 7 | 8 | 9 | **8.0** | ±0.3 |
| Thực thi manual trên Web | 5 | 6 | 7 | **6.0** | ±0.3 |
| Retest bug và regression | 2 | 3 | 4 | **3.0** | ±0.3 |
| Hỗ trợ UAT, lập báo cáo và quản lý đợt | 1 | 2 | 3 | **2.0** | ±0.3 |
| **Tổng** | **15** | **19** | **23** | **19.0 người-ngày** | **±1.3** |

**Đối chiếu năng lực:**

| Giai đoạn | Ngày làm việc | Người | Năng lực | Công sức ước | Kết luận |
|---|---|---|---|---|---|
| Viết TC (22-09 → 02-10-2026) | 9 | 4 | 36 người-ngày | 8.0 | Dư năng lực **nếu chỉ tính việc viết TC** |
| Thực thi (06-10 → 19-11-2026) | 33 | 4 | 132 người-ngày | 11.0 *(thực thi + retest/regression + quản lý)* | Dư năng lực rất nhiều |
| Hồi quy (20-11 → 25-11-2026) | 4 | 4 | 16 người-ngày | *(đã nằm trong 3.0 ở trên)* | — |

> ⚠️ **Con số 19.0 người-ngày KHÔNG phản ánh khối lượng thật của đợt này** — nó bỏ qua ba khối công việc lớn nhất:
>
> | Việc bị bỏ khỏi ước lượng | Khối lượng gợi ý |
> |---|---|
> | Recon `CUST` — khảo sát UI, sinh **55–70 REQ**, màn hình chi tiết **19 tab** | Chưa ước lượng |
> | Recon `PRJ` — khảo sát UI, sinh **60–80 REQ**, màn hình chi tiết **18 tab** | Chưa ước lượng |
> | Dựng framework Playwright + Allure + GitHub Actions **từ đầu**, viết script Smoke và regression cho 3 module | Chưa ước lượng |
>
> **Dư năng lực ở bảng trên là dư giả.** Ước lượng cần làm lại sau khi QA Lead chốt cách xử lý rủi ro `R1` *(ô treo #12)*. Ước lượng luôn có sai số và dựa trên giả định — ghi rõ giả định để người duyệt đánh giá được.

### 7.3 Ngân sách

**Không có ngân sách riêng cho kiểm thử** — chi phí nằm trong ngân sách dự án. *(Phiếu khai `Có ngân sách riêng: không`.)*

| Hạng mục | Số tiền | Ghi chú |
|---|---|---|
| — | — | Không áp dụng |
| **Tổng** | **—** | |

## 8. Rủi ro

### 8.1 Rủi ro DỰ ÁN & biện pháp

> Rủi ro **của việc kiểm thử** — nhóm theo ISTQB CTFL v4.0 mục 5.2.2: tổ chức · con người · kỹ thuật · nhà cung cấp. Khả năng / Ảnh hưởng là **đề xuất của agent** — người duyệt xác nhận. `/generate-test-progress-report` theo dõi trạng thái từng dòng.

| # | Nhóm | Rủi ro | Khả năng | Ảnh hưởng | Biện pháp | Nguồn phát hiện |
|---|---|---|---|---|---|---|
| **R1** | Kỹ thuật · Tổ chức | **`CUST` và `PRJ` chưa recon — 0 REQ.** Hạn hoàn tất viết TC là 02-10-2026, chỉ còn **9 ngày làm việc**, trong khi hai module này cần sinh ước **115–150 REQ** rồi mới viết được TC. Ước lượng 7.2 **không** tính công sức recon | 🔴 Cao | 🔴 Cao | (1) Chạy `/generate-requirements-from-website CUST` và `PRJ` **ngay tuần này**; (2) ước lượng lại mục 7.2 sau khi biết khối lượng thật; (3) nếu không kịp — đề xuất PO rút `PRJ` khỏi Release 1.0 hoặc dời mốc 02-10 | `docs/requirements/README.md` — trạng thái ⬜ · phiếu ước lượng |
| **R2** | Nhà cung cấp · Tổ chức | **`AMB-SYS-01` — không có tài khoản Super Admin.** Toàn bộ vùng Setup bị chặn. Ma trận phân quyền của **mọi** module chỉ ở mức suy diễn `⚠️`; không xem được nhật ký hoạt động để đối chiếu kết quả | 🟠 Trung bình | 🔴 Cao | Đề nghị PO / quản trị hệ thống cấp tài khoản Super Admin **trước 05-10-2026**. Tới khi có, **không** làm tròn `⚠️✅` thành `✅` khi báo độ phủ | `AMB-SYS-01` · `README.md` mục ⛔ |
| **R3** | Kỹ thuật | **Chỉ có một tài khoản test.** Không kiểm được khác biệt giữa các vai trò (`REQ-LOGIN-43` ⚪). Nguy hiểm hơn: thử kịch bản khoá tài khoản có thể **khoá chính tài khoản duy nhất** → mất quyền truy cập toàn dự án | 🟠 Trung bình | 🔴 Cao | ⛔ **CẤM** thử kịch bản khoá tài khoản trên tài khoản chính. Xin tài khoản vai trò thứ hai cùng lúc với `R2` | `RISK-LOGIN-05` · `RISK-LOGIN-06` |
| **R4** | Kỹ thuật | **Ba REQ trái với build hiện tại** (`REQ-LOGIN-30`, `31`, `38`). TC viết theo chúng sẽ **FAIL thật** — dễ bị hiểu nhầm là TC sai và bị "sửa cho xanh", tức là che bug | 🟠 Trung bình | 🟠 Trung bình | Raise **3 bug trước khi thực thi TC**, gắn đúng REQ ID. TC đánh nhãn `known-bug`. ⛔ **CẤM hạ AC** xuống theo hành vi hiện tại | `RISK-LOGIN-07` |
| **R5** | Kỹ thuật | **Chưa có framework automation lẫn CI** — 0 script, không file cấu hình nào. Phạm vi đợt lại khai có tự động hoá Smoke + regression cho 3 module, phải chạy được trước 20-11-2026 | 🔴 Cao | 🟠 Trung bình | Thêm mốc *framework sẵn sàng* vào lịch 7.1. Chạy `/generate-automation-framework` sớm. Nếu trượt — hồi quy chạy tay hoàn toàn, cần tính lại công sức | Đọc repo: không có `package.json` · `.github/workflows/` |
| **R6** | Tổ chức | **Chiến lược tự động hoá chưa khai** — 7 ô trống. Không rõ automate cái gì, chạy khi nào, ai bảo trì, fail thì xử lý ra sao | 🟠 Trung bình | 🟠 Trung bình | QA Lead điền nhóm `Chiến lược tự động hoá` của phiếu, chạy lại workflow, tăng phiên bản plan *(ô treo #8)* | Phiếu — nhóm trống |
| **R7** | Tổ chức | **Quản lý lỗi chưa chốt** — quy trình trạng thái, thang Severity/Priority, người triage, SLA đều trống. Tiêu chí exit **#1 và #2 đếm bug theo Severity** — chưa chốt thang là chưa chấm được tiêu chí ra | 🟠 Trung bình | 🔴 Cao | Chốt trước ngày bắt đầu thực thi 06-10-2026. Tạm dùng bộ mặc định của repo ở mục 9, **đánh dấu chưa xác nhận** *(ô treo #9 → #11, #14)* | Phiếu — nhóm trống |
| **R8** | Kỹ thuật | **Dữ liệu kiểm thử chưa khai gì** (7 ô trống). Nhật ký khám phá ghi nhận **10 module đang không có dữ liệu** — nếu `CUST` hoặc `PRJ` rỗng, QA phải tự tạo dữ liệu trước khi recon | 🟠 Trung bình | 🟠 Trung bình | Đội DEV trả lời nhóm `Dữ liệu kiểm thử` *(ô treo #15 → #21)*. Kiểm tra trạng thái dữ liệu của `CUST` và `PRJ` ngay khi môi trường sẵn sàng | Phiếu — nhóm trống · `system_map.md` mục 7 |
| **R9** | Con người | **Luong Ho kiêm 2 vai trò** — vừa trưởng nhóm QA phụ trách toàn bộ, vừa là người duy nhất làm tự động hoá cho cả 3 module. Nghỉ ốm hoặc bận điều phối là nhánh automation dừng hẳn | 🟡 Thấp | 🟠 Trung bình | Phiếu khai không tuyển thêm người → cần xác định người thứ hai đọc được script, hoặc chấp nhận automation là hạng mục *nice-to-have* của đợt | Phiếu mục Nhân lực |
| **R10** | Kỹ thuật | **Môi trường khảo sát có thể khác môi trường test.** Tài liệu requirements sinh ngày 19-09-2026 trên bản demo Perfex CRM `3.1.6`; môi trường test do DEV dựng ngày 05-10-2026 chưa xác nhận cùng phiên bản | 🟡 Thấp | 🟠 Trung bình | Khi môi trường sẵn sàng, đối chiếu phiên bản (`?v=` của asset) và chạy lại vài REQ mốc của `LOGIN` trước khi thực thi diện rộng | `README.md` — phiên bản `3.1.6` · phiếu môi trường |
| **R11** | Kỹ thuật | **Hệ thống không có REST API công khai** → kim tự tháp kiểm thử dồn hết lên tầng UI, là tầng chậm và dễ vỡ nhất. Bộ regression tự động sẽ chậm và tốn công bảo trì | 🟠 Trung bình | 🟡 Thấp | Chọn lọc TC tự động theo tiêu chí ở 3.7 thay vì automate diện rộng. Đặt kỳ vọng đúng với PO ngay từ đầu | `README.md` — kiến trúc · 3.7 |

### 8.2 Rủi ro SẢN PHẨM — tóm tắt

> **Nguồn chính** vẫn là tài liệu requirements (`RISK-<MODULE>-xx`) và tài liệu test case (đánh giá RBT) của từng module — bảng này chỉ **tóm tắt rủi ro cao nhất mỗi module** để người duyệt plan thấy ngay, kèm link. Sửa rủi ro ở tài liệu nguồn, **không** sửa ở đây.

| Module | Rủi ro | Mức | Kiểm soát bằng | Nguồn |
|---|---|---|---|---|
| `LOGIN` | **Module là cổng vào duy nhất** — hỏng ở đây thì 27 module còn lại không tiếp cận được | 🔴 Cao | Chạy `LOGIN` **trước tiên** trong thứ tự thực thi (3.2). Bộ Smoke chạy mọi build | [`requirements_login.md`](../requirements/login/requirements_login.md) — Tổng quan |
| `LOGIN` | **Không có hàng rào chống thử mật khẩu hàng loạt** — 6 lần sai liên tiếp không sinh khoá, không CAPTCHA, không độ trễ. Đã xác nhận, không còn là nghi vấn | 🔴 Cao | TC bảo mật theo `REQ-LOGIN-18`, báo dev. ⛔ **Không** tự chạy kịch bản hàng nghìn lần trên môi trường này | [`RISK-LOGIN-01`](../requirements/login/requirements_login.md) |
| `LOGIN` | **Token ghi nhớ đăng nhập bị JavaScript đọc được** — `autologin` có `HttpOnly = false`, sống ~62 ngày, **một mình đủ để vào hệ thống**. Một lỗ XSS ở bất kỳ module nào cũng lấy được token này | 🔴 Cao | Raise bug theo `REQ-LOGIN-38`. Ưu tiên cao khi test XSS ở các module có ô nhập tự do (`CUST`, `PRJ` đều có) | [`RISK-LOGIN-02`](../requirements/login/requirements_login.md) · `RISK-LOGIN-07` |
| `LOGIN` | **Màn hình Quên mật khẩu để lộ email nào chưa đăng ký** — `Email not found` cho phép dò ngược danh sách email hợp lệ | 🟠 Trung bình | Raise bug theo `REQ-LOGIN-30`. TC viết theo REQ mới và **sẽ FAIL** tới khi dev sửa | [`RISK-LOGIN-04`](../requirements/login/requirements_login.md) · `RISK-LOGIN-07` |
| `LOGIN` | **Trang đăng nhập phục vụ qua HTTP, không có HSTS** — kẻ tấn công ở giữa có thể sửa trang, đổi `form.action` | 🟠 Trung bình | Ghi nhận; giảm nhẹ nhờ `form.action` tuyệt đối HTTPS (`REQ-LOGIN-37`). Đề nghị bật ép HTTPS + HSTS | [`RISK-LOGIN-03`](../requirements/login/requirements_login.md) |
| `CUST` | ⬜ **Chưa đánh giá** — module chưa recon. `system_map.md` xếp risk sơ bộ 🔴 | 🔴 *(sơ bộ)* | Phải recon trước — xem `R1` ở 8.1 | [`module_02_customers.md`](../requirements/_discovery/modules/module_02_customers.md) |
| `PRJ` | ⬜ **Chưa đánh giá** — module chưa recon. `system_map.md` xếp risk sơ bộ 🔴 | 🔴 *(sơ bộ)* | Phải recon trước — xem `R1` ở 8.1 | [`module_15_projects.md`](../requirements/_discovery/modules/module_15_projects.md) |

## 9. Quản lý lỗi

> Nội dung theo ISTQB CTFL v4.0 mục 5.5. Mục này là **phần mở rộng** so với khung 29119-3 — xem 12.1.
>
> ⚠️ **Toàn bộ nhóm `Quản lý lỗi` của phiếu để trống.** Mục 9.1 → 9.3 dưới đây là **bộ mặc định của repo**, **chưa được QA Lead xác nhận** *(ô treo #9, #10)*. Tiêu chí exit #1 và #2 đếm bug theo thang này — chưa chốt thang là chưa chấm được tiêu chí ra (`R7`).

### 9.1 Quy trình trạng thái lỗi — ❓ *bộ mặc định, chưa xác nhận*

```text
TC FAIL ──/create-bug-report──→ 🔴 Đang mở ──Dev sửa──→ 🟡 Đã fix — chờ retest
                                    ▲                           │
                                    │                   /retest-fixed-bugs
                                    │                           │
                     NOT_FIXED ─────┤           ┌───────────────┼────────────────┐
                     PARTIAL   ─────┘         FIXED                      CANNOT_VERIFY
                                                │                   (giữ trạng thái, ghi lý do)
                                                ▼
                                            ⬛ Đóng
```

| Trạng thái | Ai chuyển | Điều kiện |
|---|---|---|
| 🔴 Đang mở | Tester | Bug report đủ Build/Version · TC ID · REQ ID · evidence |
| 🟡 Đã fix — chờ retest | Dev | Có build chứa bản sửa |
| ⬛ Đóng | Tester | Retest `FIXED` — lặp ≥ 2 lần theo Steps gốc |
| 🔴 Mở lại | Tester | Retest `NOT_FIXED` hoặc `PARTIAL` — **không** tạo bug trùng |

> ⚠️ Công cụ quản lý lỗi là **Jira** (5.3). Nếu Jira của dự án dùng workflow riêng (có *Từ chối* · *Trùng* · *Hoãn*), phải khai ở ô `Quy trình riêng` của phiếu và thay sơ đồ này. Bug *Hoãn* vẫn tính là **đang mở** khi chấm tiêu chí exit #1, #2 — trừ khi người có quyền ở 4.2 chấp nhận bằng văn bản.

### 9.2 Thang Severity — ❓ *bộ mặc định, chưa xác nhận*

> Chép **nguyên văn** `skills-bug-reporter` — *Severity & Priority Guide*.

| Severity | Định nghĩa | Ví dụ |
|---|---|---|
| 🔴 **Critical** | Chặn luồng chính, mất data, crash, security | Không login được, thanh toán sai tiền |
| 🟠 **Major** | Chức năng chính sai nhưng có workaround | Filter sai kết quả, export thiếu cột |
| 🟡 **Minor** | Chức năng phụ sai, UI lệch ảnh hưởng sử dụng | Validation message sai, sort không đúng |
| 🟢 **Trivial** | Lỗi hiển thị nhỏ, không ảnh hưởng chức năng | Sai chính tả, lệch margin |

### 9.3 Thang Priority — ❓ *bộ mặc định, chưa xác nhận*

| Priority | Định nghĩa |
|---|---|
| **P1** | Fix ngay trong sprint hiện tại / hotfix |
| **P2** | Fix trong sprint kế tiếp |
| **P3** | Fix khi có thời gian (backlog) |

> Severity đánh giá theo **mức ảnh hưởng kỹ thuật**; Priority theo **mức khẩn cấp business**. Hai giá trị độc lập nhau. Tester đề xuất Severity; **Priority do người phân loại lỗi chốt**.

### 9.4 Phân loại lỗi & thời hạn xử lý

| | |
|---|---|
| Người phân loại lỗi (triage) | ❓ *(ô treo #11)* |
| Họp phân loại lỗi | ❓ *(ô treo #11)* |
| Bug đang mở tại ngày lập | **0** — `docs/bugs/` chưa tồn tại. ⚠️ Nhưng **3 bug cần được raise ngay** theo `RISK-LOGIN-07`: `REQ-LOGIN-30`, `REQ-LOGIN-31` (thông báo Quên mật khẩu lộ email), `REQ-LOGIN-38` (`autologin` thiếu `HttpOnly`) |

| Severity | Thời hạn phản hồi | Thời hạn sửa xong |
|---|---|---|
| Critical | ❓ | ❓ |
| Major | ❓ | ❓ |
| Minor | ❓ | ❓ |
| Trivial | ❓ | ❓ |

> Thời hạn xử lý **không có giá trị mặc định** — phiếu trống → ❓ *(ô treo #14, cần Dev Lead)*. Bug quá hạn là dữ liệu cho mục *trở ngại* của báo cáo tiến độ.

## 10. Sản phẩm bàn giao

| Sản phẩm | Nơi lưu | Workflow sinh ra |
|---|---|---|
| Master Test Plan + bản lưu phiếu | [`test_plan_release_1.0.md`](test_plan_release_1.0.md) · [`test_plan_release_1.0.input.yaml`](test_plan_release_1.0.input.yaml) | `/generate-master-test-plan` |
| Tài liệu requirements | `docs/requirements/<module>/` — `LOGIN` ✅ · `CUST` ⬜ · `PRJ` ⬜ | `/generate-requirements-from-website` |
| Test cases | `docs/testcases/<module>/web/` — **chưa có thư mục nào** | `/generate-testcases-manual-rbt` |
| Execution report | `docs/executions/<module>/web/run_*/` | `/execute-test-cases` |
| Retest report | `docs/executions/<module>/web/retest_*/` | `/retest-fixed-bugs` |
| Bug report | `docs/bugs/<module>/web/` | `/create-bug-report` |
| Automation script + report | Project automation *(chưa có)* · `reports/` | `/generate-automation-framework` → `/generate-automation-web` |
| Ma trận truy vết | `traceability_matrix.md` | `/generate-traceability-matrix` |
| **Báo cáo tiến độ** | `docs/executions/test_progress_release_1.0_<YYYYMMDD>.md` | `/generate-test-progress-report` — hằng tuần thứ Sáu |
| **Báo cáo tổng hợp** | `docs/executions/test_summary_release_1.0_*.md` | `/generate-test-summary-report` — 10-12-2026 |

> Chỉ giữ dòng của nền tảng **web** — mobile và API ngoài phạm vi.

## 11. Phê duyệt

| Vai trò | Tên | Phiên bản duyệt | Ngày | Ý kiến |
|---|---|---|---|---|
| QA Lead | **Anh Tester** | v1.0 | ❓ | ❓ |
| Product Owner | ❓ *(chưa có tên — ô treo #13)* | v1.0 | ❓ | ❓ |

> Tài liệu đang ở trạng thái 🟨 **Draft** với **27 ô treo**, trong đó **4 tiêu chí vào không đạt**. Không nên ký duyệt trước khi ít nhất `R1` (recon `CUST`, `PRJ`), `R7` (chốt quản lý lỗi) và nhóm ô treo về dữ liệu kiểm thử được xử lý.

## 12. Ánh xạ chuẩn tài liệu

### 12.1 Đối chiếu mục

> Tài liệu này biên soạn **theo cấu trúc** ISO/IEC/IEEE 29119-3 — Test Plan và phủ đủ nội dung điển hình của test plan theo **ISTQB CTFL v4.0 mục 5.1.1**. Cột IEEE 829 theo **khung Test Plan bản 1998**. Bảng dưới để người duyệt đối chiếu; **không** phải tuyên bố đã được đánh giá tuân thủ.

| Mục | ISO/IEC/IEEE 29119-3 — Test Plan | ISTQB CTFL v4.0 — 5.1.1 | IEEE 829-1998 — Test Plan |
|---|---|---|---|
| Kiểm soát tài liệu | Document-specific information — Unique identification · Issuing organization · Approval authority · Change history | — | Test plan identifier |
| 1.1 Mục tiêu kiểm thử | Introduction — Scope · Context of the testing — Project/test sub-process | Context of testing — test objectives | Introduction |
| 1.2 Cơ sở kiểm thử | Context of the testing — Test item(s) | Context of testing — test basis | Introduction |
| 2.1 Trong phạm vi | Context of the testing — Test item(s) · Test scope | Context of testing — scope | Test items · Features to be tested |
| 2.2 Ngoài phạm vi | Context of the testing — Test scope (phần loại trừ) | Context of testing — scope | Features not to be tested |
| 2.3 Giả định & ràng buộc | Context of the testing — Assumptions and constraints | Assumptions and constraints of the test project · Context of testing — constraints | — |
| 2.4 Các bên liên quan & giao tiếp | Context of the testing — Stakeholders · Testing communication | Stakeholders — roles, relevance to testing · Communication — forms and frequency of communication, documentation templates | — |
| 3.1 Cấp độ kiểm thử | Test strategy — Test sub-processes | Test approach — test levels | Approach |
| 3.2 Loại kiểm thử | Test strategy — Test sub-processes | Test approach — test types | Approach |
| 3.2.1 Kiểm thử phi chức năng | Test strategy — Test sub-processes · Test design techniques | Test approach — test types | Approach |
| 3.3 Kỹ thuật thiết kế test | Test strategy — Test design techniques | Test approach — test techniques | Approach |
| 3.4 Mức độc lập của kiểm thử | Staffing — Roles, activities, and responsibilities | Test approach — independence of testing | Responsibilities |
| 3.5 Retest & regression | Test strategy — Retesting and regression testing | Test approach — test types | Approach |
| 3.6 Chỉ số theo dõi | Test strategy — Metrics to be collected | Test approach — metrics to be collected | — |
| 3.7 Chiến lược tự động hoá | Test strategy — Test sub-processes | Test approach — test types *(kim tự tháp kiểm thử: CTFL v4.0 mục 5.1.6)* | Approach |
| 4.1 Tiêu chí vào | Test strategy | Test approach — entry criteria | — |
| 4.2 Tiêu chí ra | Test strategy — Test completion criteria | Test approach — exit criteria | Item pass/fail criteria |
| 4.3 Tạm dừng & tiếp tục | Test strategy — Suspension and resumption criteria | — | Suspension criteria and resumption requirements |
| 5.1 Môi trường kiểm thử | Test strategy — Test environment requirements | Test approach — test environment requirements | Environmental needs |
| 5.2 Quản lý dữ liệu kiểm thử | Test strategy — Test data requirements | Test approach — test data requirements | Environmental needs |
| 5.3 Công cụ | Test strategy — Test environment requirements | — *(công cụ: CTFL v4.0 chương 6)* | Environmental needs |
| 6. Nhân lực & phân công | Staffing — Roles, activities, and responsibilities · Hiring needs · Training needs | Stakeholders — responsibilities, hiring and training needs | Responsibilities · Staffing and training needs |
| 7.1 Lịch trình & mốc | Schedule | Budget and schedule | Schedule |
| 7.2 Ước lượng công sức | Testing activities and estimates | Budget and schedule | Testing tasks |
| 7.3 Ngân sách | Testing activities and estimates | Budget and schedule | — |
| 8.1 Rủi ro dự án | Risk register — Project risks | Risk register — project risks | Risks and contingencies |
| 8.2 Rủi ro sản phẩm (tóm tắt) | Risk register — Product risks | Risk register — product risks | Risks and contingencies |
| 9. Quản lý lỗi | — *(không có mục riêng trong Test Plan; báo cáo sự cố là tài liệu riêng — Incident Report)* | — *(quản lý lỗi: CTFL v4.0 mục 5.5)* | — *(Test incident report là tài liệu riêng)* |
| 10. Sản phẩm bàn giao | Test strategy — Test deliverables | Test approach — test deliverables | Test deliverables |
| 11. Phê duyệt | Document-specific information — Approval authority | — | Approvals |
| 12.2 Điểm làm khác chính sách & chiến lược chung | Test strategy — Deviations from the Organizational Test Strategy | Test approach — deviations from the organizational test policy and test strategy | — |

**Rủi ro sản phẩm:** plan chỉ tóm tắt ở 8.2 — nguồn chính là tài liệu requirements và test case của từng module.

**Phần mở rộng ngoài khung chuẩn:** **3.7 Chiến lược tự động hoá** · **9 Quản lý lỗi**. Hai mục này thêm vì PM/khách cần thấy ngay trong plan *lỗi được phân loại, xử lý thế nào* và *tự động hoá tới đâu*. Không mục nào của 29119-3 bị bỏ; chỉ thêm.

> ⚠️ **Hai bản IEEE 829 có cấu trúc khác nhau.** Khung 16 mục phẳng ở cột cuối là **829-1998**. Bản **829-2008** tách thành Master Test Plan và Level Test Plan với dàn mục lồng nhau — khách yêu cầu đúng 829-2008 thì xin file template của khách rồi ánh xạ theo file đó.
>
> ⚠️ **Tên mục 29119-3 ghi theo khung Test Plan của chuẩn; giữa các bản phát hành có điều chỉnh tên gọi.** Người có bản chuẩn phải **đối chiếu lại cột này trước khi đem đi audit** *(ô treo #27)*. Cột ISTQB đã đối chiếu nguyên văn giáo trình v4.0 (phát hành 21-04-2023); bản sửa lỗi v4.0.1 **chưa** đối chiếu.

### 12.2 Điểm làm khác chính sách & chiến lược kiểm thử chung (Deviations)

**Không áp dụng — tổ chức chưa có Test Policy và Test Strategy.** *(Phiếu khai `Có chính sách kiểm thử: không` · `Có chiến lược kiểm thử: không`.)*

| Làm khác ở đâu | Test Policy / Test Strategy quy định | Đợt này làm | Lý do | Ai duyệt |
|---|---|---|---|---|
| — | — | — | — | — |
