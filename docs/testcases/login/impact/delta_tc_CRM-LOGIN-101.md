# Delta TC List — CRM-LOGIN-101 · module `login`

| Mục | Giá trị |
|---|---|
| Ticket | `CRM-LOGIN-101` — Khoá tài khoản khi đăng nhập sai nhiều lần |
| Ngày áp | 28-09-2026 |
| Impact Report nguồn | [`impact_CRM-LOGIN-101.md`](../../../requirements/login/impact/impact_CRM-LOGIN-101.md) |
| Kế hoạch đã duyệt | [`impact_plan_CRM-LOGIN-101.md`](impact_plan_CRM-LOGIN-101.md) — user duyệt nguyên kế hoạch 28-09-2026 |
| Mốc git trước khi sửa | web → `web/parts/part_01_web_smoke_chuc_nang.md` @ `1f0491f` · `web/parts/part_02_web_ky_thuat_phi_chuc_nang.md` @ `1f0491f` · index `TEST_CASES_LOGIN_SUMMARY.md` @ `1f0491f` |
| Xem thay đổi | `git diff 1f0491f -- docs/testcases/login/web/parts/` |
| Trạng thái | ✅ 11 REQ mới đã có TC (`TC_053` → `TC_065`, bổ sung 28-09-2026) · còn 2 việc ngoài phạm vi ở mục dưới |

## TC đã xử lý

| TC ID | Nền tảng | Vòng · Nhánh | Hành động đã làm | Đổi cái gì (cho automation) |
|---|---|---|---|---|
| CRM_LOGIN_TC_026 | web | V2 · Business Rule · V3 · Security | ✏️ Đã sửa — **viết lại toàn bộ** | Kỳ vọng **đảo ngược**: 4 lần sai → đăng nhập đúng **vào được** · 5 lần sai (cả 5 lần banner `Invalid email or password`) → lần 6 mật khẩu đúng **bị chặn**, không có phiên. Tài khoản `PM_*` thay `ADMIN_*`. Tag `@NotDeployed` → script (nếu viết) phải **skip** tới khi deploy, và chạy **cuối suite**, không song song với script khác dùng `PM_*` (khoá 15 phút) |
| CRM_LOGIN_TC_022 | web | V2 · Validation · Business Rule | ✏️ Đã sửa | `ADMIN_*` → `PM_*` · thêm bước 6 dọn (đăng nhập đúng + đăng xuất) · biến thể `b` **không chạy được** (mật khẩu test chỉ gồm chữ số) → data-driven phải **bỏ** dòng `b` |
| CRM_LOGIN_TC_023 | web | V2 · Validation | ✏️ Đã sửa | Email `admin@example.com` → `PM_EMAIL` · thêm bước 6 dọn. Không có trong Impact Report — phát hiện qua dữ liệu test |
| CRM_LOGIN_TC_014 | web | V2 · Required | ✏️ Đã sửa | Email → `notexist_20260928@auto.test` (nên sinh động `notexist_<timestamp>@auto.test` trong script). Kỳ vọng **không đổi** — đã kiểm chứng trên UI 28-09-2026 |
| CRM_LOGIN_TC_028 | web | V4 · Regression | ✏️ Đã sửa | `admin@example.com` → `PM_EMAIL`; assert giá trị ô Email trong HTML máy chủ = `PM_EMAIL` · thêm bước 7 dọn. Vẫn **thiết kế để FAIL** (`@KnownBug`) |
| CRM_LOGIN_TC_042 | web | V3 · Security | ✏️ Đã sửa | `ADMIN_*` → `PM_*` · thêm bước 6 dọn (mở lại trang lấy mã chống giả mạo mới rồi đăng nhập đúng) |
| CRM_LOGIN_TC_044 | web | V3 · Security | ✏️ Đã sửa | Bước 3 `admin@example.com` → `PM_EMAIL` · thêm bước 6 dọn cho **cả** `PM_*` (ở `/admin/authentication`) và `CUSTOMER_*` (ở cổng khách hàng) · tiền đề ghi rõ phạm vi < 5 lần sai |
| CRM_LOGIN_TC_027 | web | V3 · Permission | ✏️ Đã sửa — chỉ ghi chú | **Không** đổi steps/data/kỳ vọng — chỉ thêm ghi chú bộ đếm Customer dùng chung. Script (nếu có) **không cần sửa** |

> Không TC nào `🗑️ Deprecated` · không TC nào `⏸️ @NeedsVerify` · không TC mới trong phạm vi REQ 🟡.
>
> **Automation hiện tại:** repo **chưa có** script nào tham chiếu các TC trên → `/update-automation-from-impact` **chưa cần chạy**. Khi sinh script cho module `LOGIN`, đọc file này để áp đúng quy tắc tài khoản và bước dọn.

## Ngoài phạm vi

| REQ | Việc còn lại | Command |
|---|---|---|
| ~~REQ-LOGIN-45 → 55~~ | ✅ **Đã xong 28-09-2026** — 13 TC mới `CRM_LOGIN_TC_053` → `065`. Đây là TC **mới**, không phải TC sửa: khi sinh script, lấy từ file part chứ **không** từ bảng *TC đã xử lý* ở trên | `/generate-testcases-from-requirements` chế độ BỔ SUNG |
| Tag `@NotDeployed` | `/execute-test-cases` chưa tự bỏ qua tag này — hiện dựa vào Pre-Condition để tester chấm `BLOCKED` | Bổ sung luật vào `.claude/commands/execute-test-cases.md` |
| `TC_022-b` | Cần tài khoản test có **chữ cái** trong mật khẩu mới chạy được biến thể phân biệt hoa thường | Xin PO / dev cấp tài khoản |

## Nhật ký

| Ngày | Thay đổi |
|---|---|
| 28-09-2026 | Ghi nhận lượt BỔ SUNG `TC_053` → `TC_065` cho `REQ-LOGIN-45` → `55` — không đổi danh sách TC đã sửa ở trên |
| 28-09-2026 | Áp lần đầu theo `impact_plan_CRM-LOGIN-101.md` đã duyệt — 8 TC sửa tại chỗ, TC ID giữ nguyên |
