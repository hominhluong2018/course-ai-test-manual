# Kế hoạch cập nhật TC — CRM-LOGIN-101 · module `login`

| Mục | Giá trị |
|---|---|
| Ticket | `CRM-LOGIN-101` — Khoá tài khoản khi đăng nhập sai nhiều lần |
| Ngày lập | 28-09-2026 |
| Impact Report nguồn | [`impact_CRM-LOGIN-101.md`](../../../requirements/login/impact/impact_CRM-LOGIN-101.md) — đọc đủ 5 lần cập nhật |
| Requirements hiện hành | [`REQUIREMENTS_LOGIN_SUMMARY.md`](../../../requirements/login/REQUIREMENTS_LOGIN_SUMMARY.md) · [`web/requirements_login_web.md`](../../../requirements/login/web/requirements_login_web.md) — 55 REQ |
| File TC sẽ sửa | `web/parts/part_01_web_smoke_chuc_nang.md` · `web/parts/part_02_web_ky_thuat_phi_chuc_nang.md` · index `TEST_CASES_LOGIN_SUMMARY.md` |
| Mốc git | ⚠️ **Cả thư mục `docs/testcases/login/` chưa từng được commit** — không có bản đối chiếu. Đề nghị commit trước khi APPLY |
| Độ hạt | **GỘP** — giữ nguyên, không tách biến thể |
| Mode | APPLY — chờ duyệt |

---

## 1. Ánh xạ REQ → TC

### ✅ Chắc chắn — theo cột `REQ ID`

| REQ | Trạng thái REQ | TC | Đổi gì |
|---|---|---|---|
| REQ-LOGIN-41 | 🟢 → ⚪ (đảo ngược) | TC_026 | Kỳ vọng **ngược hoàn toàn** — từ *không khoá* sang *khoá sau 5 lần sai* |
| REQ-LOGIN-15 | 🟢 → 🟡 (thu hẹp) | TC_022, TC_044 | Chỉ đảm bảo không lộ email khi **chưa** tới ngưỡng khoá |
| REQ-LOGIN-12 | ✏️ ghi chú | TC_014 | Bỏ trống mật khẩu nay **tính** vào bộ đếm (`REQ-LOGIN-52`) |
| REQ-LOGIN-22 | không đổi | TC_042 | Sai CSRF nay **tính** vào bộ đếm (`REQ-LOGIN-53`) |
| REQ-LOGIN-43 | không đổi | TC_027 | Customer bị từ chối ở `/admin` nay **tính** vào bộ đếm chung (`REQ-LOGIN-55`) |

### ⚠️ Suy luận — không map qua `REQ ID`, phát hiện qua **dữ liệu test** (cần xác nhận)

Ràng buộc mới của ticket (dòng 6 + `REQ-LOGIN-52`, `53`, `55`) làm mọi lần bấm `Login` với email có thật mà **không** đăng nhập thành công đều tiêu hao bộ đếm. Rà toàn bộ 52 TC theo cột `Test Data`:

| TC | REQ | Lần tiêu hao / lượt chạy | Tài khoản | Trong Impact Report? |
|---|---|---|---|---|
| TC_014 | 12 | 1 | Admin | ✅ |
| TC_022 | 14, 15 | 3 (`a`, `b`, `c`) | Admin | ✅ |
| **TC_023** | 14 | **4** (`a`→`d`) | Admin | ❌ **phát hiện thêm** |
| TC_026 | 41 | 10 | Admin | ✅ |
| TC_028 | 16 | 1 | Admin | ✅ |
| TC_042 | 22 | 1 | Admin | ✅ |
| TC_044 | 15 | 1 Admin + 1 Customer | Admin · Customer | ✅ |
| TC_027 | 43 | 1 | Customer | ✅ — bước 6 **đã** đăng nhập đúng ở cổng khách hàng, tự đặt lại bộ đếm |
| **Tổng trên Admin** | | **21 lần** | | → khoá tài khoản `Admin` của cả đội **4 lần liên tiếp** sau khi deploy |

**Đã rà và KHÔNG ảnh hưởng:**

| TC | Lý do không sửa |
|---|---|
| TC_013 | Bỏ trống **email** → không tính cho tài khoản nào (`AMB-LOGIN-28` ✅) |
| TC_015, TC_019, TC_048 (phần sai định dạng) | Trình duyệt chặn, **không** gửi biểu mẫu → không bấm `Login` thành công |
| TC_016, TC_020 | Email **không tồn tại** → không bao giờ bị khoá (`REQ-LOGIN-50`) |
| TC_017, TC_018 | Mọi biến thể **đăng nhập thành công** → đặt lại bộ đếm |
| TC_034 | **Không** bấm `Login` |
| TC_040-f | Mở thẳng URL, **không** gửi biểu mẫu đăng nhập |
| TC_003, 007, 008, 024, 025, 032, 035, 037, 043, 047, 049, 050 | Đăng nhập **đúng** |

### ❓ Chưa có TC → ngoài phạm vi DELTA

`REQ-LOGIN-45` → `55` (11 REQ, đều ⚪ chưa deploy) — xem mục 5.

---

## 2. Kế hoạch sửa từng TC

> Nguyên tắc chung cho mọi TC tiêu hao bộ đếm: **chỉ dùng tài khoản `Project Manager`** (ticket dòng 6) · tổng lần tiêu hao **< 5** trong một TC (trừ TC_026 cố ý khoá) · **kết thúc bằng một lần đăng nhập đúng rồi đăng xuất** để đặt lại bộ đếm (`REQ-LOGIN-48`) — lượt TC sau luôn bắt đầu từ bộ đếm = 0.

| # | TC · file:dòng | Vòng · Nhánh | Sửa ô nào | Nội dung sửa | Evidence? |
|---|---|---|---|---|---|
| 1 | **TC_014** · `part_01:62` | V2 · Required | `Test Data` · `Pre-Condition` | Email `admin@example.com` → **email không tồn tại** `notexist_20260928@auto.test` — TC chỉ cần banner *trường bắt buộc*, không cần tài khoản thật, nên không tiêu hao bộ đếm của ai. **Kiểm chứng trên UI thật trước khi ghi** (email không tồn tại + mật khẩu trống vẫn ra đúng 1 banner `The Password field is required.`) | Không — nhãn thông báo không đổi |
| 2 | **TC_022** · `part_01:70` | V2 · Validation · Business Rule | `Pre-Condition` · `Test Steps` · `Test Data` · `Expected Result` | Gỡ tiền đề *"`AMB-LOGIN-02` ✅ không khoá"* → thay bằng *"bộ đếm PM = 0, 3 biến thể < ngưỡng 5"*. Email → 🔒 `PM_EMAIL`, biến thể `b`/`c` dùng 🔒 `PM_PASSWORD`. Thêm **bước 6 dọn**: đăng nhập đúng bằng PM rồi đăng xuất | Không |
| 3 | **TC_023** · `part_01:71` | V2 · Validation | `Pre-Condition` · `Test Steps` · `Test Data` · `Expected Result` | Email → 🔒 `PM_EMAIL` (giữ email có thật để máy chủ **thật sự xử lý** mật khẩu dài/lạ — đổi sang email không tồn tại là làm mất mục đích TC). 4 biến thể < 5. Thêm **bước 6 dọn** | Không |
| 4 | **TC_026** · `part_01:79` | V2 · Business Rule · V3 · Security | **Viết lại** `Test Scenario` · `Pre-Condition` · `Test Steps` · `Test Data` · `Expected Result` · `Tags` | Theo `REQ-LOGIN-41` AC1 + AC2: **sai 4 lần → đúng vẫn vào** (chưa chạm ngưỡng) → đăng xuất → **sai 5 lần, cả 5 đều banner `Invalid email or password`** → lần thứ 6 mật khẩu đúng **bị từ chối**. Tài khoản PM. Tiền đề: ⛔ **tính năng đã deploy** (`AMB-LOGIN-21` — hiện **chưa**) → chạy khi chưa deploy chấm `BLOCKED`, không mở bug. Ghi rõ: PM bị khoá 15 phút sau TC, chạy cuối lượt, một luồng, báo đội trước. Thêm tag `@NotDeployed`. Kỳ vọng lần 6 **không** assert nguyên văn thông báo khoá (thuộc `REQ-LOGIN-45`, TC riêng) — chỉ assert *bị từ chối, không vào Dashboard* | Không — thông báo khoá chưa quan sát được, và không assert ở TC này |
| 5 | **TC_027** · `part_01:80` | V3 · Permission | `Pre-Condition` | Thêm ghi chú: bước 4 tiêu hao 1 lần bộ đếm Customer (`REQ-LOGIN-55`); bước 6 đăng nhập đúng ở cổng khách hàng **đã** đặt lại bộ đếm — **không đổi steps**. Cổng khách hàng: `/login` hoặc `/authentication/login` đều được | Không |
| 6 | **TC_028** · `part_01:81` | V4 · Regression (V2 · Business Rule) | `Test Data` · `Expected Result` · `Test Steps` | Email → 🔒 `PM_EMAIL`; kỳ vọng bước 6 đổi `admin@example.com` → *"email vừa nhập (🔒 `PM_EMAIL`)"*. Thêm **bước 7 dọn**. Giữ nguyên tính chất *thiết kế để FAIL* | Không |
| 7 | **TC_042** · `part_02:27` | V3 · Security | `Test Data` · `Test Steps` · `Expected Result` | Email/mật khẩu → 🔒 `PM_EMAIL` / `PM_PASSWORD`. Thêm **bước 6 dọn** (lần gửi sai CSRF tính 1 lần — `REQ-LOGIN-53`) | Không |
| 8 | **TC_044** · `part_02:29` | V3 · Security | `Pre-Condition` · `Test Steps` · `Test Data` · `Expected Result` | Gỡ tiền đề *"không khoá"* → thay bằng *"bộ đếm PM và Customer = 0; phạm vi REQ-15 **< 5 lần sai**"*. Bước 3: `admin@example.com` → 🔒 `PM_EMAIL`. Thêm **bước 6 dọn**: đăng nhập đúng PM ở `/admin` **và** Customer ở cổng khách hàng (bước 4 tiêu hao bộ đếm Customer — `REQ-LOGIN-55`). Ghi chú: *từ lần sai thứ 6 hai loại email sẽ khác nhau — PO đã chấp nhận (`RISK-LOGIN-10`), không thuộc TC này* | Không |

**Không TC nào bị `🗑️ Deprecated`** — không REQ nào 🔴.
**Không TC mới** trong phạm vi REQ 🟡 — `REQ-LOGIN-15` thu hẹp phạm vi, không sinh nhu cầu TC negative mới (vế *sau ngưỡng* là `REQ-LOGIN-50`, REQ mới).
**Cột `Automation` không hạ:** TC_026 giữ `Yes`, ghi `⏸️ Hoãn` ở mục Đối soát cột Automation (vướng tạm thời: chưa deploy). Các TC còn lại giữ nguyên giá trị.

---

## 3. Nhánh 4 vòng bị chạm

```
Nhánh bị chạm:   V2 · Required (TC_014) · V2 · Validation (TC_022, TC_023) · V2 · Business Rule (TC_022, TC_026)
                 V3 · Permission (TC_027 — chỉ ghi chú) · V3 · Security (TC_026, TC_042, TC_044) · V4 · Regression (TC_028)
Nhánh ghi chú:   V2 · State Transition — ticket thêm trạng thái "Bị khoá"; TC phủ trạng thái này thuộc REQ-45→55 (ngoài phạm vi DELTA)
Nhánh KHÔNG đụng: toàn bộ V1 · V2 (UI Behavior, EP, BVA, Decision Table, Dependency, Use Case, CRUD, Error Guessing) · V3 (API, DB, Integration, Logging) · V4 (Compatibility, Responsive, Accessibility, Performance, E2E)
```

**Vì sao V1 · UI cơ bản không đổi:** ticket **không** thêm/bớt/đổi nhãn thành phần nào trên màn hình đăng nhập hiện có. Thứ mới nhìn thấy được duy nhất là banner thông báo khoá — thuộc `REQ-LOGIN-45` (REQ mới, ⚪ chưa deploy, chưa có evidence) → TC riêng ở lượt BỔ SUNG.

---

## 4. Tác động lan toả

| Câu hỏi | Kết quả |
|---|---|
| Ticket đụng thành phần nhìn thấy trên màn hình? | Chỉ thêm banner khoá (REQ mới) — V1 hiện có không đổi |
| TC dùng tài khoản thật ở bước phụ? | ⚠️ **Có — nguồn tác động lớn nhất.** Phát hiện thêm `TC_023` ngoài Impact Report (mục 1) |
| TC lấy TC khác làm precondition? | Không TC nào phụ thuộc TC_026 |
| Thứ tự chạy | Sau khi deploy: mọi TC tiêu hao bộ đếm tự dọn về 0; riêng TC_026 **khoá PM 15 phút** → xếp **cuối lượt** |
| Vượt ngưỡng tách file? | Không — không thêm TC (part_01 35 TC, part_02 17 TC) |
| Automation có sẵn? | Không có script nào tham chiếu các TC này → `/update-automation-from-impact` **không** cần chạy |

---

## 5. Ngoài phạm vi

| REQ | Việc còn lại | Command |
|---|---|---|
| REQ-LOGIN-45 → 55 (11 REQ ⚪) | Chưa có TC — thông báo khoá · mật khẩu đúng vẫn bị chặn · mở khoá sau 15 phút · đặt lại bộ đếm · khoá theo email không theo IP · email không tồn tại không bị khoá · bộ đếm về 0 sau mở khoá · bỏ trống mật khẩu / sai CSRF tính 1 lần · cổng khách hàng khoá · bộ đếm dùng chung | `/generate-testcases-from-requirements` — chế độ **BỔ SUNG** (TC ID nối từ `CRM_LOGIN_TC_053`, độ hạt GỘP) |
| Tag bỏ qua TC chưa deploy | `/execute-test-cases` chỉ bỏ qua `@PersonalOnly`. TC của REQ ⚪ đang dựa vào Pre-Condition để chấm `BLOCKED`. Nên bổ sung luật bỏ qua `@NotDeployed` vào command | Sửa `.claude/commands/execute-test-cases.md` — ngoài phạm vi workflow này |
