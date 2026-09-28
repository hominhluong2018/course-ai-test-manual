# Test Cases — Module Đăng nhập / Xác thực (`LOGIN`) — tổng **52 TC** · 1 nền tảng (Web)

| Thông tin | Nội dung |
|---|---|
| **Hệ thống** | Perfex CRM — Anh Tester Demo (`https://crm.anhtester.com`) |
| **Module · prefix** | Đăng nhập / Xác thực · `LOGIN` |
| **Nguồn requirement** | [REQUIREMENTS_LOGIN_SUMMARY.md](../../requirements/login/REQUIREMENTS_LOGIN_SUMMARY.md) — 44 REQ, **40 trong phạm vi viết TC** |
| **Mode sinh** | QUICK (`/generate-testcases-from-requirements`) · **độ hạt GỘP** |
| **Ngày sinh** | 21-09-2026 |
| **Dải TC ID đã dùng** | `CRM_LOGIN_TC_001` → `CRM_LOGIN_TC_052` — chung mọi nền tảng |
| **Mã kế tiếp** | `CRM_LOGIN_TC_053` — **KHÔNG đánh lại từ 001** |
| **Mức rủi ro · độ sâu** | `Cao` → **Đầy đủ** (đủ mọi nhánh của cả 4 vòng).<br>Căn cứ chấm Cao: chạm **xác thực và phân quyền** · chạm **dữ liệu cá nhân** · là **cổng vào** của 23 module còn lại (`RISK-LOGIN-06`).<br>**Hạ xuống Tiêu chuẩn khi:** không bao giờ — module xác thực luôn ở mức Cao |
| **Quy mô** | **52 TC · 61 biến thể / mục bảng kiểm** (15 TC dùng Bảng biến thể hoặc Bảng kiểm) |
| **Môi trường** | ⚠️ **Dùng chung** — mọi TC chỉ đọc hoặc hoàn tác được; không TC nào phá huỷ dữ liệu nghiệp vụ |
| **Trình duyệt chuẩn** | Google Chrome, viewport desktop `1600×750` |
| **Tài khoản** | 🔒 Lấy từ `.env` (`ADMIN_*`, `PM_*`, `CUSTOMER_*`) — **KHÔNG** ghi mật khẩu thật vào tài liệu |

> ⚠️ **Bộ TC này thay thế bộ `LOGIN` cũ đã mất khỏi đĩa — TC ID KHÔNG tương thích ngược.** Xem mục [Cảnh báo truy vết](#cảnh-báo-truy-vết--bug-và-execution-report-cũ) ở cuối trước khi đối chiếu với bug hay execution report có sẵn.

---

## Bản đồ tài liệu

| Nền tảng | File | Nhóm chức năng | Số TC | TC ID | REQ bao phủ |
|---|---|---|---|---|---|
| Web | [web/parts/part_01_web_smoke_chuc_nang.md](web/parts/part_01_web_smoke_chuc_nang.md) | V1 Smoke · V2 Functional (bắt buộc · kiểm tra dữ liệu · thất bại · quên mật khẩu · đoán lỗi) | 35 | `001`–`035` | REQ-LOGIN-01 → 16 · 23 → 26 · 28 · 29 · 31 → 33 · 36 → 38 · 41 · 43 |
| Web | [web/parts/part_02_web_ky_thuat_phi_chuc_nang.md](web/parts/part_02_web_ky_thuat_phi_chuc_nang.md) | V3 Technical (phân quyền · bảo mật) · V4 Non-functional (đáp ứng · bàn phím · tương thích · hết phiên) | 17 | `036`–`052` | REQ-LOGIN-01 · 02 · 03 · 13 · 15 · 17 → 22 · 29 · 30 · 39 · 42 · 43 · 44 |

> Mobile · API: **chưa có** — module mới khảo sát ở nền tảng web.

---

## Bảng Đối Soát Coverage (toàn module)

40/40 REQ trong phạm vi đều có ≥1 TC. **Không có dòng 🔴.**

| REQ ID | Mô tả ngắn | Số TC | TC IDs | Đủ Positive / Negative / Boundary? |
|---|---|---|---|---|
| REQ-LOGIN-01 | Truy cập trang đăng nhập | 3 | TC_001, TC_034, TC_046 | ✅ |
| REQ-LOGIN-02 | Thành phần biểu mẫu đăng nhập | 4 | TC_001, TC_021, TC_046, TC_047 | ✅ |
| REQ-LOGIN-03 | Con trỏ tự vào ô Email | 2 | TC_001, TC_047 | ✅ |
| REQ-LOGIN-04 | Logo dẫn về trang chủ | 1 | TC_005 | ✅ (chỉ có luồng thuận) |
| REQ-LOGIN-05 | Không có CAPTCHA | 1 | TC_001 | ✅ (khẳng định phủ định) |
| REQ-LOGIN-06 | Đăng nhập bằng thông tin hợp lệ | 4 | TC_007, TC_008, TC_032, TC_035 | ✅ |
| REQ-LOGIN-07 | Email không phân biệt hoa thường | 1 | TC_017 (3 biến thể) | ✅ |
| REQ-LOGIN-08 | Tích Remember me → cấp cookie | 1 | TC_024 | ✅ (cặp với TC_025) |
| REQ-LOGIN-09 | Không tích → không cấp cookie | 1 | TC_025 | ✅ |
| REQ-LOGIN-10 | Bỏ trống cả hai trường | 1 | TC_012 | ✅ |
| REQ-LOGIN-11 | Bỏ trống riêng Email | 1 | TC_013 | ✅ |
| REQ-LOGIN-12 | Bỏ trống riêng Mật khẩu | 1 | TC_014 | ✅ |
| REQ-LOGIN-13 | Chặn email sai định dạng tại trình duyệt | 3 | TC_015 (5 bt), TC_019 (3 bt · BVA), TC_048 (3 bt) | ✅ |
| REQ-LOGIN-14 | Thông báo chung khi sai thông tin | 4 | TC_016, TC_020, TC_022, TC_023 | ✅ |
| REQ-LOGIN-15 | Không tiết lộ email nào có thật | 3 | TC_016, TC_022, TC_044 | ✅ |
| REQ-LOGIN-16 | Giữ lại email sau khi đăng nhập lỗi | 1 | TC_028 🐞 **thiết kế để FAIL** | ✅ |
| REQ-LOGIN-17 | Chặn URL nội bộ khi chưa đăng nhập | 1 | TC_036 | ✅ |
| REQ-LOGIN-18 | Không ghi nhớ URL đích | 1 | TC_037 | ✅ |
| REQ-LOGIN-19 | Đã đăng nhập không vào lại trang đăng nhập | 2 | TC_033, TC_038 | ✅ |
| REQ-LOGIN-20 | Đã đăng nhập không vào trang Quên mật khẩu | 1 | TC_039 | ✅ |
| REQ-LOGIN-21 | Biểu mẫu mang mã chống CSRF | 1 | TC_041 | ✅ |
| REQ-LOGIN-22 | Từ chối yêu cầu có mã CSRF sai | 1 | TC_042 | ✅ |
| REQ-LOGIN-23 | Truy cập trang Quên mật khẩu | 2 | TC_004, TC_006 | ✅ |
| REQ-LOGIN-24 | Không có lối quay lại đăng nhập | 1 | TC_004 | ✅ |
| REQ-LOGIN-25 | Bỏ trống email ở Quên mật khẩu | 1 | TC_029 🐞 **thiết kế để FAIL** | ✅ |
| REQ-LOGIN-26 | Email không tồn tại ở Quên mật khẩu | 1 | TC_030 | ✅ |
| REQ-LOGIN-28 | Liên kết đặt lại với mã khoá sai | 1 | TC_031 | ✅ |
| REQ-LOGIN-29 | Mỗi viewport một lối đăng xuất | 2 | TC_009 (desktop), TC_051 (mobile, `@NeedsVerify`) | ✅ |
| REQ-LOGIN-30 | Cảnh báo khi còn bộ đếm giờ chạy | 1 | TC_052 (`@NeedsVerify`) | ✅ |
| REQ-LOGIN-31 | Đăng xuất ngay khi không có bộ đếm giờ | 1 | TC_009 | ✅ (cặp đối lập với TC_052) |
| REQ-LOGIN-32 | Kết thúc phiên và về trang đăng nhập | 1 | TC_010 | ✅ |
| REQ-LOGIN-33 | URL nội bộ bị chặn sau đăng xuất | 1 | TC_011 | ✅ |
| REQ-LOGIN-36 | Không có đăng nhập mạng xã hội | 1 | TC_002 | ✅ (khẳng định phủ định) |
| REQ-LOGIN-37 | Trang không nạp tệp JavaScript nào | 1 | TC_003 | ✅ |
| REQ-LOGIN-38 | Email bỏ qua khoảng trắng thừa | 1 | TC_018 (3 bt) | ✅ |
| REQ-LOGIN-39 | Cookie ghi nhớ không tự đăng nhập lại | 1 | TC_043 | ✅ |
| REQ-LOGIN-41 | Không khoá tài khoản sau nhiều lần sai | 1 | TC_026 | ✅ |
| REQ-LOGIN-42 | Phiên hết hạn sau 1 giờ | 2 | TC_049 (AC1), TC_050 (AC2 — gia hạn) | ✅ cả hai vế |
| REQ-LOGIN-43 | Tài khoản khách hàng không vào được `/admin` | 2 | TC_027, TC_040 | ✅ |
| REQ-LOGIN-44 | Ép truy cập qua HTTPS | 1 | TC_045 🐞 **thiết kế để FAIL** | ✅ |

### Ngoài phạm vi — cố ý không có TC

| REQ ID | Nội dung | Quyết định |
|---|---|---|
| REQ-LOGIN-27 | Gửi mail đặt lại mật khẩu với email có thật | ⏭️ PO 18-08-2026 (`AMB-LOGIN-04`) — không kiểm chứng luồng gửi mail trên môi trường dùng chung |
| REQ-LOGIN-34 | Cookie ghi nhớ không bị xoá khi đăng xuất | ⏭️ PO 18-08-2026 (`AMB-LOGIN-06`) — ghi nhận hiện trạng, không viết TC |
| REQ-LOGIN-35 | Cookie ghi nhớ đọc được bằng JavaScript | ⏭️ PO 18-08-2026 (`AMB-LOGIN-07`) — `RISK-LOGIN-02` đã chấp nhận |
| REQ-LOGIN-40 | Hành vi cookie khi phiên hết hạn tự nhiên | ⏭️ PO 18-08-2026 (`AMB-LOGIN-15`) — PO xác nhận tính năng Ghi nhớ đăng nhập **không hoạt động** |

### Phép thử chiều ngược (Gate 6b)

Đã chạy trên cả 2 part. **Không TC nào** gánh ≥ 2 REQ mà các REQ đó không có TC khác chống lưng. 5 TC đa-REQ đều hợp lệ:

| TC | REQ gánh | REQ chỉ có mỗi TC này |
|---|---|---|
| TC_001 | 01, 02, 03, 05 | **1** — REQ-05 (được giữ) |
| TC_004 | 23, 24 | **1** — REQ-24 (được giữ) |
| TC_009 | 29, 31 | **1** — REQ-31 (được giữ) |
| TC_016 · TC_022 | 14, 15 | **0** |
| TC_046 · TC_047 | 01/02/03 | **0** |

---

## Bảng Đối Soát Evidence

Đã mở **10/10** ảnh trong `docs/requirements/login/web/evidence/`.

| Ảnh evidence | Màn hình / trạng thái | TC dựa vào | Đầy đủ? |
|---|---|---|---|
| `login_form_default_fullpage.png` | Đăng nhập — mặc định, các ô rỗng | TC_001, TC_002, TC_005, TC_021, TC_034 | ✅ full-page |
| `login_form_filled_remember_checked_fullpage.png` | Đăng nhập — đã nhập đủ, Remember me **đang tích** | TC_021, TC_024 | ✅ full-page |
| `login_form_empty_submit_error_fullpage.png` | Đăng nhập — gửi rỗng, 2 banner lỗi đúng thứ tự | TC_012, TC_013, TC_014 | ✅ full-page |
| `login_form_wrong_credentials_fullpage.png` | Đăng nhập — sai thông tin, banner `Invalid email or password` | TC_016, TC_020, TC_022, TC_023, TC_026, TC_028, TC_044 | ✅ full-page |
| `login_csrf_invalid_403_fullpage.png` | Đăng nhập — mã CSRF bị sửa, trang `419 Page Expired!` | TC_042 | ✅ full-page |
| `login_success_dashboard_viewport.png` | Dashboard ngay sau đăng nhập (menu trái 14 mục) | TC_007, TC_008, TC_032, TC_033, TC_035 | ✅ viewport — đúng phạm vi |
| `logout_menu_open_viewport.png` | Dashboard — menu ảnh đại diện **đang mở**, 5 mục, Logout cuối | TC_009, TC_043 | ✅ viewport — đúng phạm vi |
| `forgot_password_form_default_fullpage.png` | Quên mật khẩu — mặc định | TC_004, TC_005, TC_006 | ✅ full-page |
| `forgot_password_empty_submit_error_fullpage.png` | Quên mật khẩu — gửi rỗng, banner `Email not found` | TC_029, TC_030 | ✅ full-page |
| `login_form_375x700_fullpage.png` | Đăng nhập — khung hiển thị `375×700` | TC_046-d | ✅ full-page |
| *(không có)* | Đăng xuất ở khung hiển thị điện thoại — menu thu gọn đang mở | TC_051 | 🔴 **THIẾU** — TC gắn `@NeedsVerify` |
| *(không có)* | Hộp thoại cảnh báo bộ đếm giờ khi đăng xuất | TC_052 | 🔴 **THIẾU** — TC gắn `@NeedsVerify` |
| *(không có)* | Trang lỗi khi mở liên kết đặt lại mật khẩu với mã khoá sai | TC_031 | 🟡 Không có ảnh, nhưng AC mô tả rõ và `AMB-LOGIN-03` ⏭️ đã chấp nhận hiện trạng |
| *(không có)* | Trang `access_denied` khi mở khu Setup | TC_040-d, TC_040-e | 🟡 Không có ảnh; nguồn là ma trận phân quyền **đã kiểm chứng 100%** ngày 18-08-2026 |

### Vùng chưa có evidence

| Vùng | TC bị ảnh hưởng | Đề xuất |
|---|---|---|
| Đăng xuất ở khung hiển thị điện thoại (`div.mobile-navbar`) | TC_051 `@NeedsVerify` | Recon ở viewport `375×700` sau khi đăng nhập, mở menu thu gọn, chụp ảnh. Nguồn hiện tại **chỉ là quyết định PO** (`AMB-LOGIN-16`), chưa có lượt recon nào |
| Hộp thoại cảnh báo bộ đếm giờ khi đăng xuất | TC_052 `@NeedsVerify` | Thuộc module `TASK` (`AMB-LOGIN-14` ⏭️). Khi recon `TASK` phải chạy thật rồi **cập nhật ngược** `REQ-LOGIN-30` và TC này |
| Không có xung đột tài liệu ↔ ảnh | — | 10/10 ảnh **khớp** với mô tả trong requirements. Điểm dễ hiểu nhầm duy nhất (ô Email có nền xanh sau lỗi) đã ghi thành `ASM-01` |

---

## Đối soát loại kiểm thử (4 vòng)

| Vòng | Nhánh | Trạng thái | TC ID / Lý do |
|---|---|---|---|
| 1 | **UI cơ bản** | ✅ | TC_001, TC_004 (2 TC · **11 mục bảng kiểm**) — nhãn nguyên văn, thứ tự field, trạng thái mặc định, vị trí con trỏ |
| 1 | Open form | ✅ | TC_006 (mở bằng liên kết), TC_005 (logo), TC_036/TC_038 (mở thẳng địa chỉ) |
| 1 | Display | ✅ | TC_001, TC_004, TC_007 (menu trái 14 mục), TC_008 (menu trái 9 mục) |
| 1 | Input valid data | ✅ | TC_007, TC_008 (2 vai trò) |
| 1 | Save | ➖ | Module xác thực **không có** thao tác lưu dữ liệu — hành động tương đương là tạo phiên, đã phủ ở nhánh *Input valid data* |
| 1 | Verify data | ✅ | TC_007, TC_008 — đối chiếu vai trò qua số mục menu trái |
| 2 | UI Behavior | ✅ | TC_021 (5 mục: nút luôn bật · che ký tự · dán được · không nút hiện/ẩn · tích/bỏ tích), TC_034 |
| 2 | **Required** | ✅ | TC_012, TC_013, TC_014 (3 TC — bỏ trống cả hai / từng trường) |
| 2 | **Validation** | ✅ | TC_015–TC_023 (9 TC · **33 biến thể**) — đối soát **đủ 9/9 mục** bảng Email và **đủ 9/9 mục** bảng Password (5 mục Password `➖` vì `AMB-LOGIN-09` ✅ chốt không có chính sách mật khẩu, xem đối soát chi tiết đầu Part 01) |
| 2 | Equivalence Partitioning | ✅ | TC_015 (sai định dạng) · TC_016 (đúng định dạng, không tồn tại) · TC_017/TC_018 (đúng định dạng, tồn tại) — 3 phân vùng của ô Email |
| 2 | Boundary Value Analysis | ✅ | TC_019 (`63` / `64` / `65` ký tự phần trước @ · `ASM-02`), TC_023-a/b (1 và 1000 ký tự) |
| 2 | Business Rule | ✅ | TC_017 (không phân biệt hoa thường), TC_018 (bỏ khoảng trắng), TC_022-b/c (mật khẩu **có** phân biệt hoa thường và **không** bỏ khoảng trắng), TC_026 (không khoá tài khoản) |
| 2 | Decision Table | ➖ | Không có output nào phụ thuộc ≥3 điều kiện kết hợp. Biểu mẫu chỉ có 2 điều kiện (email đúng/sai × mật khẩu đúng/sai) và đã phủ đủ 4 tổ hợp: TC_007 (đúng-đúng) · TC_016 (sai-sai) · TC_022 (đúng-sai) · TC_013/TC_014 (thiếu) |
| 2 | State Transition | ✅ | Phủ đủ ma trận trạng thái phiên (mục 7 của requirements): Chưa đăng nhập→Đã đăng nhập TC_007 · Chưa→Chưa (lỗi) TC_016 · Đã→Đã đăng xuất TC_009/TC_010 · Đã đăng xuất→Chưa TC_011 · Đã đăng nhập chặn quay lại TC_038/TC_039 · còn cookie nhưng không tự vào TC_043 · hết hạn tự nhiên TC_049/TC_050 |
| 2 | Dependency | ✅ | TC_024 ↔ TC_025 (tích/không tích Remember me → có/không cookie) · TC_009 ↔ TC_052 (không có/có bộ đếm giờ → đi thẳng/hiện cảnh báo) |
| 2 | Use Case / Scenario | ✅ | TC_043 (chuỗi đăng nhập có ghi nhớ → đăng xuất → thử vào lại) · TC_037 (bị chặn → đăng nhập → kiểm đích đến) |
| 2 | Save / Edit / Delete | ➖ | Module xác thực **không có** thực thể nghiệp vụ nào để tạo/sửa/xoá |
| 2 | **Error Guessing** | ✅ | TC_032 (bấm Login 2 lần liên tiếp) · TC_033 (Back sau khi đăng nhập) · TC_034 (F5 khi gõ dở) · TC_035 (2 tab cùng đăng nhập) |
| 3 | **Permission** | ✅ | TC_008, TC_027, TC_036, TC_038, TC_039, TC_040 (**6 mục bảng kiểm** = 3 vai trò + khách × 2 khu vực) — bám ma trận phân quyền đã kiểm chứng 100% |
| 3 | Security | ✅ | TC_002 (không lối bên thứ ba) · TC_011 (chặn sau đăng xuất) · TC_020/TC_023 (tiêm mã) · TC_026 (không khoá) · TC_041/TC_042 (CSRF) · TC_043 (cookie không tự đăng nhập) · TC_044 (không lộ email có thật) · TC_045 (ép HTTPS) |
| 3 | API | ➖ | **QA không có quyền gọi API** (`docs/requirements/README.md`, chốt 11-09-2026) — **đội Dev** xác minh |
| 3 | Database | ➖ | **QA không có quyền truy vấn CSDL** (nguồn như trên) — **đội Dev** xác minh |
| 3 | Integration | ➖ | Module xác thực **không** tích hợp bên thứ ba nào (TC_002 chứng minh không có SSO/OAuth). Luồng gửi mail đặt lại — điểm tích hợp duy nhất — ra ngoài phạm vi theo `AMB-LOGIN-04` ⏭️ |
| 3 | Logging / Audit | ➖ | **QA không xem được nhật ký hoạt động** — đã đo 11-09-2026: `Utilities → Activity Log` trả **Từ chối truy cập** với tài khoản Admin demo. Cần Super Admin, **đề nghị PO cấp** — **đội Dev** xác minh trong lúc chờ |
| 4 | Compatibility | ✅ | TC_048 (3 biến thể: Chrome · Edge · Firefox). **Không** kiểm: Safari (không có máy macOS — **đội QA, mở lại khi có thiết bị**) · trình duyệt di động |
| 4 | Responsive / UI Stability | ✅ | TC_046 (4 breakpoint đã chốt: `1920×1080` · `1366×768` · `768×1024` · `375×700`), TC_051 |
| 4 | Accessibility | ✅ | TC_047 (thứ tự Tab · `Space` tích ô · `Enter` gửi biểu mẫu · viền focus nhìn thấy được).<br>Rà WCAG đầy đủ ➖ — cần công cụ chuyên dụng, **đội QA đề xuất đưa vào đợt riêng** |
| 4 | Performance | ⏭️ Cố ý bỏ | Requirements **không cam kết** ngưỡng thời gian nào cho module này. TC_023-b và TC_044-d đã có mức quan sát thô (không treo quá 10 giây · không lệch thời gian phản hồi giữa các nhánh lỗi). **Quyết định: QA lead, 21-09-2026.** Rà lại khi hệ thống công bố SLA cho trang đăng nhập, hoặc khi có báo cáo người dùng về độ trễ |
| 4 | Regression | ✅ | TC_028 (`REQ-16`), TC_029 (`REQ-25`), TC_045 (`REQ-44`) — 3 TC chống tái phát cho 3 lỗi đã được PO xác nhận, trỏ về `AMB-LOGIN-10`, `AMB-LOGIN-05`, `AMB-LOGIN-20` |
| 4 | E2E | ➖ | Luồng xuyên module **chưa chạy được** — mới có 2 module (`LOGIN`, `CUST`) có requirements. Khi module thứ ba có tài liệu thì dùng `/generate-cross-module-test-plan` |

> **Ba nhánh không bao giờ được `➖`** — `UI cơ bản` ✅ · `Validation` ✅ · `Permission` ✅ — đều đã có TC.

---

## Rà soát đặc tính chất lượng (ISO/IEC 25010:2023)

| Đặc tính | Trạng thái | TC ID / Lý do |
|---|---|---|
| Functional Suitability | ✅ Có TC | TC_001–TC_045 — toàn bộ V1–V3 |
| Performance Efficiency | ➖ Ngoài phạm vi | Không có công cụ đo tải và requirements không cam kết ngưỡng nào — **đội Hạ tầng**, đợt sau. Mức quan sát thô đã có ở TC_023-b, TC_044-d |
| Compatibility | ✅ Có TC | TC_048 (Chrome · Edge · Firefox). Safari và trình duyệt di động ➖ — **đội QA**, mở lại khi có thiết bị macOS |
| Interaction Capability | ✅ Có TC | TC_001, TC_004 (nhãn, bố cục) · TC_012–TC_014 (thông báo lỗi trỏ đúng trường) · TC_021 (hành vi biểu mẫu) · TC_029 (thông báo lỗi **sai bản chất** — đang là bug) · TC_047 (điều hướng bàn phím) |
| Reliability | ✅ Có TC | TC_032 (bấm 2 lần liên tiếp) · TC_034 (nạp lại khi gõ dở) · TC_035 (2 tab) · TC_049, TC_050 (hết phiên và gia hạn phiên) · TC_020, TC_023 (không sập với dữ liệu bất thường) |
| Security | ✅ Có TC | TC_002, TC_011, TC_020, TC_023, TC_026, TC_027, TC_036, TC_040, TC_041, TC_042, TC_043, TC_044, TC_045.<br>Pentest và quét lỗ hổng ➖ — **chưa có đội bảo mật được phân công**; `RISK-LOGIN-01` (không có lớp chống thử vét cạn) đã báo lên và **PO chấp nhận** |
| Maintainability | ➖ Không áp dụng | Đặc tính của mã nguồn — không kiểm được bằng manual TC. Thuộc code review / phân tích tĩnh của **đội Dev** |
| Flexibility | ✅ Có TC | TC_046 (4 breakpoint) · TC_051 (đăng xuất ở màn hình hẹp).<br>Đổi ngôn ngữ ➖ — trang đăng nhập **chỉ có tiếng Anh**; bộ chọn ngôn ngữ nằm sau đăng nhập, thuộc module `PROF` |
| Safety | ➖ Không áp dụng | App nghiệp vụ — lỗi không gây thiệt hại vật lý. Module không có thao tác không hồi lại được nào |

---

## Đối soát cột Automation

| Nền tảng | Yes | Partial | No | ⏸️ Hoãn | Tổng |
|---|---|---|---|---|---|
| Web | 30 | 17 | 5 | 0 | **52** |

### TC `Partial` — điều kiện để nâng lên `Yes`

| Nhóm | TC | Điều kiện cần chuẩn bị |
|---|---|---|
| Cần đọc cookie / mã nguồn máy chủ | TC_003, TC_024, TC_025, TC_028, TC_041, TC_043 | Tự động hoá phần chính được ngay; phần `🔧 Ghi chú kỹ thuật` cần API context của trình điều khiển trình duyệt (đọc cookie, lấy HTML gốc) — **Playwright hỗ trợ sẵn**, chỉ cần dựng helper |
| Cần đổi cài đặt trình duyệt giữa chừng | TC_045 | Không tự động hoá được qua UI. Thay bằng bước gọi `curl -I` trong script và assert phản hồi — cần quyền chạy lệnh ngoài trong CI |
| Cần sửa trường ẩn trước khi gửi | TC_042 | Playwright sửa được giá trị trường ẩn; cần chốt cách tái tạo mã CSRF sai mà không phụ thuộc DevTools |
| Cần đo thời gian phản hồi so sánh | TC_044 | Phần a/b/c tự động hoá được; phần `d` (so sánh thời gian phản hồi) cần ngưỡng cụ thể — **chưa ai chốt ngưỡng** |
| Thao tác chuột/bàn phím tinh · điều hướng nhiều tab | TC_019, TC_031, TC_032, TC_033, TC_034, TC_035, TC_046, TC_047 | Tự động hoá được nhưng cần chạy thật xác nhận độ ổn định — nhất là double click ở TC_032, thứ tự Tab ở TC_047, và hai ngữ cảnh trình duyệt ở TC_035 |

> **Tổng 17 TC `Partial`:** TC_003, TC_019, TC_024, TC_025, TC_028, TC_031, TC_032, TC_033, TC_034, TC_035, TC_041, TC_042, TC_043, TC_044, TC_045, TC_046, TC_047.

### TC `No` — lý do

| TC | Lý do |
|---|---|
| TC_048 | Chạy đa trình duyệt là **việc của cấu hình CI**, không phải của một script. Trong bộ automation sẽ thành ma trận `projects` của Playwright, không sinh test riêng |
| TC_049, TC_050 | ⏱️ Chạy **trên 1 giờ** — chiếm slot CI quá lâu, không đáng tự động hoá. Gắn `@PersonalOnly`, QA tự chạy định kỳ |
| TC_051, TC_052 | ⚠️ `@NeedsVerify` — **chưa có evidence**, chưa biết locator thật. Không sinh script trước khi recon xong (cấm đoán locator) |

---

## Cảnh báo truy vết — bug và execution report cũ

> 🚨 **Đọc trước khi đối chiếu bộ TC này với bug hoặc execution report có sẵn trong repo.**

Module `LOGIN` **đã từng có** một bộ TC 51 TC (dải `001`→`051`). File đó **không còn trên đĩa** và **không có trong git** ở trạng thái mới nhất — bản gần nhất tìm lại được là **41 TC** ở commit `9631f42` (`docs/testcases/login/test_cases_login.md`), thiếu hẳn dải `042`→`051`.

Bộ TC hiện tại được **sinh mới hoàn toàn** bằng Mode QUICK ngày 21-09-2026 theo quyết định của người dùng, TC ID đánh lại từ `001`. Hệ quả:

| Tài liệu bị ảnh hưởng | Mã TC đang trỏ tới | Trạng thái |
|---|---|---|
| `docs/bugs/login/web/BUG_login_1785678750_TC039.md` | `CRM_LOGIN_TC_039` | 🔴 **Trỏ sai** — nội dung bug là *ép HTTPS*, nay là **`CRM_LOGIN_TC_045`** |
| `docs/bugs/login/web/BUG_login_1787226513_TC004.md` | `CRM_LOGIN_TC_004` | 🔴 **Trỏ sai** — cần đối chiếu nội dung bug với bộ TC mới |
| `docs/bugs/login/web/BUG_login_1787226514_TC016.md` | `CRM_LOGIN_TC_016` | 🔴 **Trỏ sai** — nội dung là *giữ email sau lỗi*, nay là **`CRM_LOGIN_TC_028`** |
| `docs/bugs/login/web/BUG_login_1787226515_TC018.md` | `CRM_LOGIN_TC_018` | 🔴 **Trỏ sai** — ⚠️ nhật ký danh mục 11-09-2026 đã ghi bug này có thể là **ranh giới chuẩn RFC, không phải lỗi**; nay ứng với `TC_019` |
| `docs/bugs/login/web/BUG_login_1787226516_TC028.md` | `CRM_LOGIN_TC_028` | 🔴 **Trỏ sai** |
| `docs/bugs/login/web/BUG_login_1787226517_TC029.md` | `CRM_LOGIN_TC_029` | 🔴 **Trỏ sai** |
| `docs/executions/login/web/run_1787215085/execution_report.md` | `TC_001`→`TC_041` | 🔴 **Không đối chiếu được** — thuộc bộ TC cũ |
| `docs/executions/login/web/run_1789759574/execution_report.md` | `TC_001`→`TC_051` | 🔴 **Không đối chiếu được** — thuộc bộ TC cũ |

**Việc cần làm (ngoài phạm vi lệnh sinh TC này):**

1. Rà từng bug, đối chiếu **nội dung** (không đối chiếu mã) với bộ TC mới, sửa cột TC ID. Ba bug đã map sẵn ở bảng trên.
2. Hai execution report cũ giữ nguyên làm **hồ sơ lịch sử** — thêm một dòng ghi chú rằng chúng thuộc bộ TC đã bị thay thế.
3. Chạy lại `/execute-test-cases` trên bộ TC mới để có mốc kết quả tương ứng.

**Từ nay trở đi:** module đã có TC thì requirements đổi phải dùng `/update-testcases-from-impact` (Mode DELTA), **không** chạy lại QUICK — chạy lại là lặp đúng lần gãy truy vết này.

---

## Nhật ký thay đổi

| Ngày | Nguồn | Thay đổi | TC ảnh hưởng |
|---|---|---|---|
| 21-09-2026 | `/generate-testcases-from-requirements` Mode QUICK, độ hạt GỘP | **Sinh mới toàn bộ bộ TC** — 52 TC · 61 biến thể · 2 part, phủ 40/40 REQ trong phạm vi. Rủi ro `Cao` → độ sâu **Đầy đủ**. Bộ TC cũ (51 TC) đã mất khỏi đĩa và không khôi phục được ở trạng thái mới nhất → TC ID đánh lại từ `001` theo quyết định người dùng. 3 TC thiết kế để FAIL (`TC_028`, `TC_029`, `TC_045`) · 2 TC `@NeedsVerify` (`TC_051`, `TC_052`) · 2 TC `@PersonalOnly` chạy trên 1 giờ (`TC_049`, `TC_050`) | Toàn bộ `TC_001`→`TC_052` |
