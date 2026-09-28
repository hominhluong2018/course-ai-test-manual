# Báo Cáo Review Test Cases — Module `LOGIN` · Web

## Tổng quan

| Mục | Giá trị |
|---|---|
| **Nguồn** | [TEST_CASES_LOGIN_SUMMARY.md](../TEST_CASES_LOGIN_SUMMARY.md) → [part_01_web_smoke_chuc_nang.md](../web/parts/part_01_web_smoke_chuc_nang.md) · [part_02_web_ky_thuat_phi_chuc_nang.md](../web/parts/part_02_web_ky_thuat_phi_chuc_nang.md) |
| **Requirements đối chiếu** | [REQUIREMENTS_LOGIN_SUMMARY.md](../../../requirements/login/REQUIREMENTS_LOGIN_SUMMARY.md) · [requirements_login_web.md](../../../requirements/login/web/requirements_login_web.md) — đánh giá được đủ 6/6 tiêu chí |
| **Mode** | REVIEW — **không** sửa file TC nào |
| **Ngày review** | 23-09-2026 |
| **Số TC review** | **52** (`CRM_LOGIN_TC_001` → `CRM_LOGIN_TC_052`) · 0 TC `@Deprecated` |
| **Kết quả** | 🟢 **49** tốt · 🟡 **3** cần sửa · 🔴 **0** nên viết lại |
| **Điểm trung bình** | **11,4/12** (595/624) |
| **Số biến thể** | 61 biến thể / mục bảng kiểm trên 15 TC — **khớp** con số công bố ở index. Không TC nào vượt trần 6 biến thể |

> **Nhận định chung:** bộ TC viết **rất tốt** ở mức từng dòng — nhãn nguyên văn, URL đích, số banner, dữ liệu 🔒 lấy từ `.env`, tách `🔧 Ghi chú kỹ thuật` đúng luật. Vấn đề thật nằm ở **mức bộ TC**: (1) các payload tiêm mã ở `TC_020` phần lớn **bị trình duyệt chặn trước khi tới máy chủ** nên nhánh Security đang được chấm ✅ quá tay; (2) thiếu kịch bản **Back sau khi đăng xuất**; (3) ô Email của trang **Quên mật khẩu** mới phủ 2/9 mục validation; (4) phần *Cảnh báo truy vết* ở index trỏ tới 8 file **không tồn tại trên đĩa**.

---

## Chi tiết từng TC

Thang điểm: 1 Rõ ràng · 2 Expected đo được · 3 Độc lập · 4 Test data · 5 Truy vết · 6 Đúng trọng tâm (mỗi tiêu chí 0–2).

| TC ID | Điểm | Xếp loại | Vấn đề chính | Đề xuất sửa |
|---|---|---|---|---|
| CRM_LOGIN_TC_001 | 11/12 | 🟢 | (6) Bảng kiểm Kiểu B nhưng mục `c` *"Gõ vào ô Password thì hiện dấu chấm che"* và `d` *"bấm vào thì tích/bỏ tích được"* là **thao tác đổi trạng thái**, không phải quan sát tĩnh — và trùng `TC_021-b`, `TC_021-e`. Mục `b` không nói hai ô đang **rỗng** | Bỏ nửa sau của `c`/`d` (đã có ở `TC_021`), giữ phần tĩnh: `c` *"Ô Password rỗng, không có chữ gợi ý"* · `d` *"Ô tích `Remember me` không được tích sẵn"*. Thêm vào `b`: *"cả hai ô nhập đều rỗng, không có chữ gợi ý mờ bên trong"* (ảnh `login_form_default_fullpage.png` xác nhận không có placeholder) |
| CRM_LOGIN_TC_002 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_003 | 11/12 | 🟢 | (2) Bước 4 *"Đăng nhập vẫn thành công, vào Dashboard"* — Dashboard **cần** JavaScript, khi JS đang bị chặn trang Dashboard có thể vỡ bố cục → tester dễ chấm FAIL nhầm | Đổi Expected bước 4: *"Thanh địa chỉ dừng ở `https://crm.anhtester.com/admin/`, thanh tiêu đề chứa `Dashboard`. Bố cục Dashboard có thể thiếu thành phần do JS đang tắt — **không** chấm phần này"* |
| CRM_LOGIN_TC_004 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_005 | 10/12 | 🟢 | (1) Bước 3 gộp 2 hành động *"Bấm nút Back, mở …/forgot_password"*. (6) Kiểm logo trên **hai màn hình** trong 1 TC; logo trang Quên mật khẩu thuộc `REQ-LOGIN-24`, không thuộc `REQ-LOGIN-04` | Tách bước 3 thành *"3. Bấm nút Back của trình duyệt"* · *"4. Gõ `…/forgot_password` vào thanh địa chỉ, nhấn Enter"*. Thêm `REQ-LOGIN-24` vào cột REQ ID, hoặc chuyển nửa sau sang `TC_004-d` (*"bấm logo → dừng ở `https://crm.anhtester.com/`"*) |
| CRM_LOGIN_TC_006 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_007 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_008 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_009 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_010 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_011 | 10/12 | 🟢 | (3) Pre-Condition *"chạy ngay sau `TC_009` hoặc `TC_010`"* — phụ thuộc TC khác. (4) Expected *"mặc dù cookie ghi nhớ vẫn còn trong trình duyệt"* nhưng `TC_009`/`TC_010` **không** tích `Remember me` → không có cookie ghi nhớ nào để "còn" | Pre-Condition tự đủ: *"Đăng nhập tài khoản Admin (không tích `Remember me`) → bấm ảnh đại diện → `Logout`"*. Bỏ vế *"mặc dù cookie ghi nhớ vẫn còn"* — vế đó đã do `TC_043` (`REQ-LOGIN-39`) gánh |
| CRM_LOGIN_TC_012 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_013 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_014 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_015 | 12/12 | 🟢 | — | Tag `@Boundary` không khớp nội dung (đây là EP sai định dạng, không có biên) — đề xuất bỏ tag |
| CRM_LOGIN_TC_016 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_017 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_018 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_019 | 10/12 | 🟢 | (2) Biến thể `c` *"Ghi nhận hiện trạng thật … không chấm FAIL; bị chặn ngay ở ô Email → ghi nhận hệ thống có áp mốc"* — **không có kết quả nào làm TC FAIL**, đây là quan sát chứ không phải kiểm thử. (6) Một biến thể mang hai loại phản hồi | Chốt một kỳ vọng từ lượt khảo sát (hoặc mở `AMB` hỏi PO), VD: *"`c` Biểu mẫu vẫn gửi đi, hiện banner `Invalid email or password` — hệ thống không áp mốc 64"*. Chưa chốt được thì gắn `@NeedsVerify` cho biến thể `c` |
| CRM_LOGIN_TC_020 | **8/12** | 🟡 | **(2)(6)** Expected *"hoặc trình duyệt chặn ngay tại ô Email … hoặc trang nạp lại và hiện đúng banner"* — hai loại phản hồi trong một TC (vi phạm bảng CẤM gộp). Nghiêm trọng hơn: `a` `admin@example.com' OR '1'='1` và `b` (có khoảng trắng / ký tự sau tên miền), `c` `<script>…@auto.test` đều **bị ô Email của trình duyệt chặn** → payload **không bao giờ tới máy chủ**, TC PASS nhưng **không kiểm được gì về tiêm SQL/XSS**. (4) Dữ liệu viết bằng thực thể HTML (`&#39;`, `&lt;`, `&amp;`) — đọc file thô hoặc viewer không giải mã thì tester dán nguyên `&#39;` | Đổi payload sang dạng **qua được** kiểm tra định dạng của trình duyệt để thật sự tới máy chủ (dấu `'`, `=`, `-` hợp lệ ở phần trước `@`): `a` `admin'or'1'='1@example.com` · `b` `admin'--@example.com` · `c` `admin'/*@example.com` · `d` giữ nguyên. ✅ **Đã sửa tại chỗ 23-09-2026** — 4 payload kiểm trên Chrome: qua được ô Email, máy chủ trả đúng 1 banner `Invalid email or password`. Expected còn **một** loại phản hồi: *"Trang nạp lại, hiện đúng 1 banner `Invalid email or password`, không đăng nhập được, không lộ câu lệnh"*. Payload `<script>` chuyển sang TC mới (xem Gap #3). Ghi dữ liệu bằng ký tự thật trong code span, không dùng thực thể HTML |
| CRM_LOGIN_TC_021 | 11/12 | 🟢 | (6) Ghi là Bảng kiểm nhưng các mục đều là **thao tác** (gõ, dán, bấm) — không phải Kiểu B tĩnh. Chấp nhận được vì cùng một nhóm "hành vi giao diện của biểu mẫu", rủi ro thấp | Đổi tiêu đề ô thành *"Bảng hành vi — mọi mục phải đạt"* để không nhầm với Kiểu B; giữ nguyên nội dung |
| CRM_LOGIN_TC_022 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_023 | 11/12 | 🟢 | (4) Biến thể `d` viết bằng thực thể HTML `&lt;&gt;&amp;&quot;&#39;--;DROP` | Ghi ký tự thật trong code span: `` `<>&"'--;DROP` `` |
| CRM_LOGIN_TC_024 | 11/12 | 🟢 | (2) Che dòng `🔧` đi thì phần chính chỉ còn *"đăng nhập thành công"* — **giống hệt `TC_007`**, không chứng minh được `REQ-LOGIN-08`. Chấp nhận vì PO đã xác nhận ghi nhớ đăng nhập không có hệ quả nhìn thấy (`AMB-LOGIN-15`) | Thêm một dòng vào Expected chính: *"⚠️ TC chỉ PASS khi phần 🔧 Ghi chú kỹ thuật đạt — phần nhìn thấy không phân biệt được với `TC_007`"*, để người lập bộ chạy giao đúng người có DevTools |
| CRM_LOGIN_TC_025 | 11/12 | 🟢 | Như `TC_024` | Như `TC_024` |
| CRM_LOGIN_TC_026 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_027 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_028 | 11/12 | 🟢 | (2) Bước 5–6 ở **phần TC chính** bắt tester *"xem mã nguồn trang … Tìm tới dòng khai báo ô nhập Email"* — ngôn ngữ mã nguồn, lẽ ra thuộc `🔧` | Tìm hệ quả nhìn thấy được: chạy trong **cửa sổ ẩn danh mới** (không có dữ liệu tự điền) → Expected chính: *"Sau khi trang nạp lại, ô `Email Address` phải còn `admin@example.com`"* → hiện trạng ô rỗng → FAIL. Chuyển bước `View Page Source` xuống `🔧 Ghi chú kỹ thuật` |
| CRM_LOGIN_TC_029 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_030 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_031 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_032 | 11/12 | 🟢 | (2) *"Vào Dashboard **đúng một lần**"* — tester không có cách nhìn thấy "số lần" | Thay bằng điều đo được: *"Vào Dashboard, **không** hiện trang `419 Page Expired!`, không hiện banner. Bấm Back **một** lần → không quay về trang đăng nhập có banner lỗi"* |
| CRM_LOGIN_TC_033 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_034 | 12/12 | 🟢 | — | Ghi chú nhỏ: Pre-Condition nên chốt *"Chrome"* — Firefox **khôi phục** giá trị đã gõ khi nhấn F5, sẽ ra kết quả khác |
| CRM_LOGIN_TC_035 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_036 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_037 | 11/12 | 🟢 | (3) Pre-Condition *"chạy ngay sau `TC_036`"*, bước 1 *"Sau khi bị đưa về trang đăng nhập từ `TC_036`"* | Tự dựng: *"1. Xoá cookie của `crm.anhtester.com` · 2. Gõ `https://crm.anhtester.com/admin/clients`, nhấn Enter · 3. Đọc kỹ thanh địa chỉ"* rồi tiếp các bước còn lại |
| CRM_LOGIN_TC_038 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_039 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_040 | **9/12** | 🟡 | **(6)** Gộp 3 loại phản hồi khác nhau trong một TC `Critical` — *vào được* (`b`, `c`) · *bị đưa tới `access_denied`* (`d`, `e`) · *bị đưa về đăng nhập* (`a`, `f`) — vi phạm bảng CẤM gộp (khác loại phản hồi · một cái thành công một cái lỗi). `a` trùng hoàn toàn `TC_036`. **(5)** Cột REQ chỉ ghi `REQ-LOGIN-43` (khách hàng) nhưng `b`–`e` là quyền của Admin/PM với `/admin/clients`, `/admin/settings` — không có REQ `LOGIN` nào nêu | Tách theo loại phản hồi: **`TC_040`** giữ `d`, `e` — *"Vai trò nội bộ không vào được khu Setup"*. **TC mới `TC_053`** nhận `b`, `c` — *"Vai trò nội bộ vào được danh sách khách hàng"*. **TC mới `TC_054`** nhận `f` (`REQ-LOGIN-43`). Bỏ `a` (trùng `TC_036`). Cột REQ của `TC_040`/`TC_053` trỏ về ma trận phân quyền ở `REQUIREMENTS_LOGIN_SUMMARY.md` (hoặc mở REQ mới qua `/update-requirements-from-ticket`) |
| CRM_LOGIN_TC_041 | 11/12 | 🟢 | (2) Expected bước 4 *"Người dùng thường không nhìn thấy mã này — TC chấm ở phần kỹ thuật"* — che `🔧` đi thì TC không chấm được | Thêm hệ quả nhìn thấy: *"5. Sau 2 lần F5, nhập email/mật khẩu đúng, bấm `Login` → vào Dashboard bình thường (mã cũ vẫn hợp lệ trong phiên)"* |
| CRM_LOGIN_TC_042 | 11/12 | 🟢 | (2) Bước 2 ở phần chính *"Mở DevTools, tìm trường ẩn `csrf_token_name` … sửa giá trị"* | Có cách tái tạo không cần DevTools: *"1. Mở trang đăng nhập · 2. Trong Cài đặt Chrome xoá cookie của `crm.anhtester.com` (tab vẫn mở) · 3. Quay lại tab, nhập thông tin đúng, bấm `Login`"* → mã trên trang không còn khớp phiên. Cách sửa trường ẩn chuyển xuống `🔧`. **Cần chạy thử xác nhận** cho ra cùng trang `419 Page Expired!` trước khi đổi |
| CRM_LOGIN_TC_043 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_044 | 11/12 | 🟢 | (2) Mục `d` *"không có trường hợp nào chậm hơn hẳn … chênh lệch thấy rõ bằng mắt"* — không đo được, index cũng ghi *"chưa ai chốt ngưỡng"*. Mục `a`–`c` lặp lại kết quả của `TC_016`, `TC_022`, `TC_027` | Chuyển `d` xuống `🔧` với ngưỡng cụ thể: *"tab Network, cột Time, mỗi nhánh 5 lần — chênh trung bình giữa các nhánh < 200 ms"* (ngưỡng do QA lead chốt). Xem mục trùng lặp bên dưới |
| CRM_LOGIN_TC_045 | 12/12 | 🟢 | — | Ghi chú: Expected trỏ tới bug `BUG_login_1785678750_TC039` — **file không tồn tại** (thư mục `docs/bugs/` không có trên đĩa) |
| CRM_LOGIN_TC_046 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_047 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_048 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_049 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_050 | 12/12 | 🟢 | — | — |
| CRM_LOGIN_TC_051 | 11/12 | 🟢 | (1) *"biểu tượng ba gạch"* chưa được khảo sát thật — đã gắn đúng `@NeedsVerify` | Giữ nguyên tới khi có lượt recon ở `375×700` |
| CRM_LOGIN_TC_052 | **9/12** | 🟡 | **(1)** Bước 1 *"Khởi động một bộ đếm giờ công việc (thuộc module `TASK`)"* — người chưa biết module `TASK` không làm được. **(3)** Phụ thuộc dữ liệu của module khác. **(4)** *"dùng công việc sẵn có, không tạo mới"* — không chỉ định công việc nào, trên môi trường dùng chung có thể đụng việc của người khác | Viết bước dựng dữ liệu cụ thể (sau khi recon `TASK`): *"1. Vào `Tasks` → mở công việc do chính tài khoản test tạo, tên bắt đầu bằng `auto_timer_` · 2. Bấm `Start Timer`…"*. Test Data: *"Công việc `auto_timer_<timestamp>` do QA tạo riêng — không dùng công việc của người khác"*. Giữ `@NeedsVerify` tới lúc đó |

---

## Đối soát loại kiểm thử (4 vòng)

| Vòng | Nhánh | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | UI cơ bản | ✅ | TC_001 (6 mục), TC_004 (5 mục). Lưu ý nhỏ: TC_001 chưa khẳng định hai ô **rỗng, không có chữ gợi ý** — xem đề xuất ở TC_001 |
| 1 | Open form | ✅ | TC_005, TC_006, TC_036, TC_038 |
| 1 | Display | ✅ | TC_001, TC_004, TC_007, TC_008 |
| 1 | Input valid data | ✅ | Bộ tối thiểu TC_007 · bộ đầy đủ (có tích Remember me) TC_024 |
| 1 | Save | ➖ | Hợp lệ — không có thao tác lưu |
| 1 | Verify data | ✅ | TC_007, TC_008 |
| 2 | UI Behavior | ✅ | TC_021 (5 mục), TC_034 |
| 2 | Required | ✅ | Đăng nhập: TC_012–TC_014 · Quên mật khẩu: TC_029 |
| 2 | **Validation** | 🟡 Nông | Ô Email trang **đăng nhập** đạt 9/9 mục. Ô Email trang **Quên mật khẩu** mới có 2/9 mục (bỏ trống TC_029 · không tồn tại TC_030) — thiếu *sai định dạng* (xem Gap #2). *Max length* của Email chỉ kiểm phần trước `@` (TC_019), chưa kiểm độ dài toàn chuỗi (Gap #4). Ô tích `Remember me` (loại Checkbox) **chưa có dòng đối soát** ở đầu Part 01 dù đã phủ ở TC_001-d, TC_021-e |
| 2 | Equivalence Partitioning | ✅ | TC_015 · TC_016 · TC_017/TC_018 |
| 2 | Boundary Value Analysis | 🟡 Nông | TC_019 biến thể `c` không có tiêu chí FAIL (xem TC_019). TC_023-a/b hợp lệ |
| 2 | Business Rule | ✅ | TC_017, TC_018, TC_022-b/c, TC_026 |
| 2 | Decision Table | ➖ | Hợp lệ — 2 điều kiện, đủ 4 tổ hợp |
| 2 | State Transition | ✅ | Đủ ma trận phiên — trừ chuyển *Đã đăng xuất → Back* (Gap #1) |
| 2 | Dependency | ✅ | TC_024↔TC_025, TC_009↔TC_052 |
| 2 | Use Case / Scenario | ✅ | TC_037, TC_043 |
| 2 | Save / Edit / Delete | ➖ | Hợp lệ |
| 2 | Error Guessing | ✅ | TC_032–TC_035. Trang Quên mật khẩu chưa có (Gap #7, Low) |
| 3 | **Permission** | 🟡 | Có đủ ô ma trận nhưng TC_040 gộp sai (3 loại phản hồi, 1 mục trùng) — xem TC_040 |
| 3 | Security | 🟡 Nông | Index chấm ✅ dựa vào TC_020/TC_023 cho tiêm mã, nhưng 3/4 payload ở TC_020 **bị trình duyệt chặn**, không tới máy chủ. Thiếu **nút Back sau đăng xuất** — mục có tên trong nhánh Security của Bản Đồ 4 Vòng (Gap #1) |
| 3 | API · Database · Integration · Logging/Audit | ➖ | Hợp lệ — theo *Năng lực kiểm thử của QA* chốt 11-09-2026 ở `docs/requirements/README.md`, có ghi đội chịu trách nhiệm |
| 4 | Compatibility | ✅ | TC_048 (Chrome · Edge · Firefox), ghi rõ Safari không kiểm |
| 4 | Responsive / UI Stability | 🟡 Nông | TC_046 (4 breakpoint), TC_051 `@NeedsVerify`. Thiếu **phóng to 125%** — mục có tên trong nhánh (Gap #5) |
| 4 | Accessibility | 🟡 Nông | TC_047 phủ Tab/Space/Enter/viền focus. Thiếu: logo có văn bản thay thế · Tab tới liên kết `Forgot Password?` và Enter mở được · trang Quên mật khẩu dùng được bằng bàn phím (Gap #6) |
| 4 | Performance | ⏭️ | Hợp lệ — có lý do, người quyết (QA lead 21-09-2026), điều kiện rà lại. ⚠️ **Lệch nhãn**: bảng ISO 25010 ở index ghi cùng mục này là `➖ Ngoài phạm vi` — phải thống nhất về `⏭️` |
| 4 | Regression | ✅ | TC_028, TC_029, TC_045 |
| 4 | E2E | ⚠️ Lỗi ghi nhãn | Index ghi `➖` với lý do *"chưa chạy được — mới có 2 module"* — đó là **quyết định hoãn**, không phải *"không áp dụng"* (đăng nhập là cửa vào mọi luồng xuyên module). Đổi sang `⏭️` kèm người quyết + điều kiện rà lại (VD *"khi module thứ ba có requirements"*) |

---

## Coverage Gaps (TC còn thiếu)

Mã TC mới đề xuất nối tiếp dải từ `CRM_LOGIN_TC_053` (sau phần tách của TC_040 ở trên thì bắt đầu từ `TC_055`).

| # | Kịch bản thiếu | Vòng / Nhánh | Priority đề xuất |
|---|---|---|---|
| 1 | **Bấm Back sau khi đăng xuất**: đăng nhập Admin → mở `/admin/clients` → `Logout` → bấm Back của trình duyệt 1–2 lần → **không** thấy lại danh sách khách hàng hay Dashboard có dữ liệu; bấm vào bất kỳ liên kết nào trên trang cũ (nếu trình duyệt hiện bản lưu tạm) → bị đưa về `https://crm.anhtester.com/admin/authentication` | V3 · Security · V2 · State Transition | **High** |
| 2 | Trang **Quên mật khẩu** — ô Email sai định dạng bị trình duyệt chặn: `abc` · `abc@` · `a@b@example.com` → bong bóng cảnh báo cạnh ô Email, trang không nạp lại, không hiện banner `Email not found` (Kiểu A, 3 biến thể) | V2 · Validation (Email) | Medium |
| 3 | Tiêm mã **thật sự tới máy chủ** qua ô Email: tắt kiểm định dạng của trình duyệt (đổi ô sang dạng văn bản bằng DevTools) rồi gửi `admin@example.com' OR '1'='1`, `<script>alert(1)</script>` → banner `Invalid email or password`, không có hộp thoại, không đăng nhập được. Gắn `@TechCheck` — hoặc gộp vào TC_020 sau khi đổi payload theo đề xuất | V3 · Security | **High** |
| 4 | Email rất dài toàn chuỗi: `254` · `255` · `320+` ký tự (phần trước `@` ≤ 64) → biểu mẫu gửi đi, trang không sập, hiện `Invalid email or password` | V2 · Validation (Max length) · BVA | Medium |
| 5 | Trang đăng nhập ở mức phóng to trình duyệt `125%` và `200%` (viewport `1600×750`) → biểu mẫu không tràn ngang, không đè chữ, nút `Login` bấm được | V4 · Responsive | Low |
| 6 | Bàn phím & nội dung thay thế: Tab từ nút `Login` tới liên kết `Forgot Password?`, nhấn Enter → mở trang Quên mật khẩu · trang Quên mật khẩu: Tab vào ô Email, gõ, Enter gửi được · logo có văn bản thay thế (kiểm bằng trình đọc màn hình hoặc tắt ảnh — `@TechCheck`) | V4 · Accessibility | Low |
| 7 | Trang Quên mật khẩu: bấm `Confirm` hai lần liên tiếp · nhấn F5 sau khi đã nhận banner `Email not found` → không hiện hộp thoại gửi lại, không sập | V2 · Error Guessing | Low |

---

## TC trùng lặp — đề xuất merge

| Nhóm trùng | Đề xuất |
|---|---|
| `TC_001-c`, `TC_001-d` ≈ `TC_021-b`, `TC_021-e` | Giữ ở `TC_021` (hành vi); `TC_001` chỉ giữ phần quan sát tĩnh — xem đề xuất ở TC_001. **Không** Deprecated TC nào |
| `TC_040-a` ≈ `TC_036` | Bỏ biến thể `a` khỏi `TC_040`, ghi lý do ngay trong Expected của `TC_040`: *"(`a` cũ đã gỡ — trùng `TC_036`, 23-09-2026)"* |
| `TC_044-a/b/c` ≈ `TC_016` + `TC_022` + `TC_027` | Giữ `TC_044` vì giá trị của nó là **so sánh cạnh nhau** trong cùng một lượt (bắt được khác biệt mà chạy rời sẽ lọt) — nhưng làm rõ tiêu chí `d` như đề xuất. Nếu PO không chốt được ngưỡng thời gian → cân nhắc gắn `@Deprecated` với lý do *"trùng TC_016/TC_022/TC_027, không có ngưỡng thời gian"* |
| `TC_011` ≈ một phần `TC_043` | Không merge (khác REQ: 33 vs 39). Sửa `TC_011` bỏ vế cookie ghi nhớ để hai TC không chồng lên nhau |

---

## Ưu tiên (Priority)

Nhìn chung hợp lý với rủi ro. Hai điểm lệch nhỏ: `TC_004` và `TC_006` có `Risk = Medium` nhưng `Priority = High` — hoặc nâng Risk lên High (trang Quên mật khẩu là lối khôi phục duy nhất), hoặc hạ Priority xuống Medium cho nhất quán.

---

## Vấn đề ở file index & truy vết (ngoài rubric)

| # | Vấn đề | Căn cứ | Đề xuất |
|---|---|---|---|
| 1 | Mục **Cảnh báo truy vết** liệt kê 6 bug report và 2 execution report — **cả 8 file không tồn tại**: thư mục `docs/bugs/` và `docs/executions/` không có trên đĩa | `ls docs` chỉ còn `requirements`, `test-plans`, `testcases`, `user-guides` | Hỏi người dùng các file đó đã bị xoá có chủ đích hay chưa khôi phục. Nếu đã bỏ → rút bảng, ghi 1 dòng Nhật ký; `TC_045` bỏ tham chiếu `BUG_login_1785678750_TC039` |
| 2 | **Không đối chiếu được kết quả chạy** — không có execution report nào của module | Như trên | Hai TC `@NeedsVerify` (`TC_051`, `TC_052`) **vẫn còn hiệu lực**, không có ghi chú nào cũ cần dọn. Chạy `/execute-test-cases` trên bộ TC này để có mốc đầu tiên |
| 3 | Bảng đối soát ô Password ở đầu Part 01 ghi *"Đủ 9/9 mục"* — bảng 15 loại field của skill có **7** mục cho Password (hai mục *che ký tự*, *giá trị sai* không thuộc bảng) | `skills-rbt-manual-testing` · Bảng Field-Level | Ghi lại *"7/7 mục — 4 mục `➖` (AMB-LOGIN-09), 1 mục `➖` (không có ô xác nhận)"* + dòng riêng cho 2 mục bổ sung |
| 4 | Viewport ghi `1600×750` trong mọi TC; `CLAUDE.md` hiện quy định headed debug `1600×770` | `CLAUDE.md` mục Browser Rules | Không phải lỗi TC (khảo sát đo ở `1600×750`), nhưng nên thống nhất một con số để người chạy khỏi phân vân |
| 5 | Thư mục `docs/testcases/login/` **chưa được git theo dõi** | `git status --short docs/testcases/login` → `??` | Trước khi chạy Mode FIX phải **commit bộ TC hiện tại** — không có mốc git thì không lấy lại được bản trước khi sửa |

---

## Kết luận & Khuyến nghị

1. **Sửa TC_020 trước tiên (High):** đổi payload tiêm mã sang dạng qua được kiểm định dạng của trình duyệt, để nhánh Security thật sự kiểm tới máy chủ. Hiện tại TC PASS nhưng không chứng minh được điều nó tuyên bố.
2. **Bổ sung Gap #1 (Back sau đăng xuất) và Gap #3 (tiêm mã tới máy chủ)** — hai lỗ duy nhất ở mức High của module xác thực.
3. **Tách TC_040** theo loại phản hồi (→ `TC_040`, `TC_053`, `TC_054`), bỏ biến thể trùng `TC_036`.
4. **Làm TC tự đủ:** `TC_011`, `TC_037` bỏ phụ thuộc TC khác; `TC_052` viết bước dựng dữ liệu cụ thể sau khi recon module `TASK`.
5. **Dọn index:** xử lý 8 tham chiếu tới file không tồn tại, thống nhất nhãn Performance (`⏭️`) và E2E (`⏭️`), sửa đếm mục Password. Commit bộ TC trước nếu muốn chạy Mode FIX.
