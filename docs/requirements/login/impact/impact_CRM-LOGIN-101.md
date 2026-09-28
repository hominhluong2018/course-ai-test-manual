# Impact Report — CRM-LOGIN-101 · 28-09-2026

> 🔄 **Đã cập nhật 5 lần trong ngày 28-09-2026.** Đọc từ trên xuống: **lần 5** (PO trả lời `AMB-LOGIN-28`) → **lần 4** (PO trả lời `AMB-LOGIN-27` + cấp tài khoản) → **lần 3** (`AMB-LOGIN-26`) → **lần 2** (`AMB-LOGIN-21`→`25`) → **lần 1** (phân tích ticket). Lần sau đè lên phần tương ứng của lần trước; lần trước giữ lại để truy vết.

## Cập nhật lần 5 — PO trả lời `AMB-LOGIN-28` · ticket hết ambiguity treo

| Câu hỏi | Trả lời PO | Tác động |
|---|---|---|
| (a) Bỏ trống email rồi bấm `Login` có tính? | **Không** — theo Giả định tạm | ✏️ `REQ-LOGIN-52` thêm AC ranh giới |
| (b) PM bị từ chối ở cổng khách hàng có tính vào bộ đếm PM? | **Có** — theo Giả định tạm | ✏️ `REQ-LOGIN-55` thêm AC chiều ngược |

- Không đổi số REQ: module vẫn **55 REQ** (24 🟢 · 17 🟡 · 14 ⚪) · 51 trong phạm vi. **Ambiguity treo: 0.**
- TC mới của `REQ-LOGIN-52` và `55` phải viết **cả** vế chính **lẫn** vế ranh giới / chiều ngược (để `skip` tới khi deploy).
- `.env` chuẩn hoá tên biến: `ADMIN_EMAIL` · `ADMIN_PASSWORD` · `PM_EMAIL` · `PM_PASSWORD` · `CUSTOMER_LOGIN_URL` · `CUSTOMER_EMAIL` · `CUSTOMER_PASSWORD` — khớp tên TC đang dùng.

### Danh sách tổng hợp cho `/update-testcases-from-impact`

| Loại | TC / REQ |
|---|---|
| ⚠️ Sửa TC có sẵn | `CRM_LOGIN_TC_014` (email không tồn tại) · `022`, `028`, `042` (đổi sang PM) · `026` (viết lại theo `REQ-41`, `skip`) · `027` (thêm bước đặt lại bộ đếm Customer) · `044` (PM + phạm vi < 5 lần + đặt lại bộ đếm Customer) |
| ➕ Viết TC mới (⚪ → `skip`) | `REQ-LOGIN-45` → `55` (11 REQ) |

---

## Cập nhật lần 4 — PO trả lời `AMB-LOGIN-27` + cấp tài khoản

### Tóm tắt thay đổi lần 4

| Câu hỏi | Trả lời PO | Tác động |
|---|---|---|
| Làm rõ `AMB-LOGIN-26` (a) | Chỉ tính khi **bấm `Login`** | Khớp cách hiểu đã ghi — `REQ-LOGIN-52` chỉ thêm câu chữ cho rõ |
| (a) Cổng khách hàng: ngưỡng/thời lượng/thông báo | **Giống `/admin`** | `REQ-LOGIN-54` viết lại AC đầy đủ |
| (b) Bộ đếm `/admin` và cổng khách hàng | **Dùng chung** | 🟢 Thêm `REQ-LOGIN-55` — ⚪ |
| Customer bị từ chối ở `/admin` có tính? | **Có tính** | Thuộc `REQ-LOGIN-55` · `TC_027`, `TC_044` tiêu hao bộ đếm Customer |
| (c) Tài khoản cho TC | PO cấp tài khoản PM + Customer | Lưu `.env` (`PM_*`, `CUSTOMER_*`, `CUSTOMER_LOGIN_URL`) — **không** ghi vào tài liệu. `REQ-LOGIN-54` **hết BLOCKED** |
| (d) Bỏ trống **email** | Chưa chốt | → `AMB-LOGIN-28` |

Module: **55 REQ** (24 🟢 · 17 🟡 · 14 ⚪) · 51 trong phạm vi viết TC.

### Test case cần xử lý — bổ sung lần 4

| TC ID | REQ liên quan | Hành động | Lý do |
|---|---|---|---|
| CRM_LOGIN_TC_027 | REQ-LOGIN-43 | ⚠️ Thêm ghi chú + bước dọn | Gửi tài khoản Customer vào `/admin` nay **tiêu hao bộ đếm Customer** (`REQ-LOGIN-55`). Giữ số lần gửi < 5 và **kết thúc bằng 1 lần đăng nhập đúng ở cổng khách hàng** để đặt lại bộ đếm (`REQ-LOGIN-48`) |
| CRM_LOGIN_TC_044 | REQ-LOGIN-15 | ⚠️ Thêm vào việc đã có ở lần 2 | Bước 4 cũng gửi Customer vào `/admin` → cùng xử lý như `TC_027` |
| — | REQ-LOGIN-54 | ➕ Viết mới + để `skip` | Hết BLOCKED. Tài khoản Customer **dùng chung** → chạy cuối lượt, một luồng, báo đội trước |
| — | REQ-LOGIN-55 | ➕ Viết mới + để `skip` | 3 lần Customer vào `/admin` + 2 lần sai ở cổng khách hàng → khoá |

> ⚠️ **Thứ tự chạy các TC khoá sau khi deploy:** mỗi TC khoá một tài khoản 15 phút. Tài khoản PM (`41`, `45`→`49`, `51`→`53`) và Customer (`54`, `55`) độc lập với nhau → chạy **song song hai nhánh** được, nhưng **trong mỗi nhánh phải tuần tự** và chờ mở khoá giữa các TC.

### Ambiguity — lần 4

| Mã | Chuyển trạng thái | Kết luận |
|---|---|---|
| AMB-LOGIN-27 | ❓ → ✅ | Giống `/admin` · bộ đếm dùng chung · Customer ở `/admin` có tính · đã có tài khoản |
| AMB-LOGIN-28 | (mới) ❓ 🟡 | (a) Bỏ trống email có tính không, tính cho ai · (b) PM bị từ chối ở cổng khách hàng có tính vào bộ đếm PM không (chiều ngược của `REQ-LOGIN-55`) |

---

## Cập nhật lần 3 — PO trả lời `AMB-LOGIN-26`

### Tóm tắt thay đổi lần 3

| Câu hỏi | Trả lời PO | Tác động |
|---|---|---|
| (a) Bỏ trống ô / sai CSRF có tính vào bộ đếm? | **Có tính** — ⚠️ khác Giả định tạm | 🟢 Thêm `REQ-LOGIN-52` (bỏ trống mật khẩu) · `REQ-LOGIN-53` (sai CSRF) — ⚪ |
| (b) Thử lại lúc đang khoá có tính lại 15 phút? | **Không** — trùng Giả định tạm | ✏️ `REQ-LOGIN-47` gỡ cảnh báo; TC được thử lại giữa chừng |
| (c) Có tài khoản PM riêng? | **Không** | Giả định tạm thành **luật chạy bắt buộc**: TC khoá chạy cuối lượt, một luồng, báo đội trước. `RISK-LOGIN-03` giữ mở |
| (d) Cổng `/login` có áp khoá? | **Có** — ⚠️ khác Giả định tạm | 🟢 Thêm `REQ-LOGIN-54` — ⚪, **BLOCKED** (cần tài khoản `Customer` — `AMB-LOGIN-27`) |

Module: **54 REQ** (24 🟢 · 17 🟡 · 13 ⚪) · 50 trong phạm vi viết TC.

### Test case cần xử lý — bổ sung lần 3

| TC ID | REQ liên quan | Hành động | Lý do |
|---|---|---|---|
| CRM_LOGIN_TC_014 | REQ-LOGIN-12 | ⚠️ Sửa dữ liệu | Bỏ trống mật khẩu với `admin@example.com` nay **tiêu hao bộ đếm** của Admin. TC chỉ cần banner `The Password field is required.` → đổi sang **email không tồn tại** (`notexist_<timestamp>@auto.test`) |
| CRM_LOGIN_TC_042 | REQ-LOGIN-22 | ⚠️ Sửa dữ liệu | Gửi CSRF giả với `admin@example.com` nay **tiêu hao bộ đếm** của Admin. TC cần thông tin đăng nhập **đúng** để chứng minh CSRF chặn được cả khi đúng mật khẩu → đổi sang tài khoản PM (🔒 `PM_EMAIL`/`PM_PASSWORD`) |
| CRM_LOGIN_TC_026 | REQ-LOGIN-41 | Không đổi so với lần 2 | Vẫn dùng lần gửi có đủ email + mật khẩu sai để đếm cho rõ |
| — | REQ-LOGIN-52, 53 | ➕ Viết mới + để `skip` | ⚪ chưa deploy |
| — | REQ-LOGIN-54 | ➕ Viết mới + `BLOCKED` | Cần tài khoản `Customer` — chỉ có 1 tài khoản dùng chung (`AMB-LOGIN-27`) |

> 🚨 **Cảnh báo cập nhật:** trước khi sửa TC, số lần tiêu hao bộ đếm trên `admin@example.com` là **7** — `TC_022` (3) + `TC_028` (1) + `TC_044` (1) + `TC_014` (1) + `TC_042` (1). Sau khi deploy, chạy nguyên bộ TC chắc chắn khoá tài khoản Admin. Phải sửa **cả 5 TC này** bằng `/update-testcases-from-impact` trước ngày deploy.

### Ambiguity — lần 3

| Mã | Chuyển trạng thái | Kết luận |
|---|---|---|
| AMB-LOGIN-26 | ❓ → ✅ | 4/4 vế đã trả lời — (a), (d) **khác** Giả định tạm |
| AMB-LOGIN-27 | (mới) ❓ 🟡 | Khoá ở `/login`: ngưỡng/thời lượng/thông báo có giống `/admin` · bộ đếm tách hay chung · dùng tài khoản `Customer` nào · bỏ trống **email** có tính không |

---

## Cập nhật lần 2 — PO trả lời Ambiguity

### Tóm tắt thay đổi lần 2

| Nhóm | REQ | Nội dung |
|---|---|---|
| ⚪ Chưa implement | REQ-LOGIN-41, 45 → 49 | `AMB-LOGIN-21`: tính năng **chưa deploy** → 6 REQ chuyển ⚪ (`41` từ 🟡, `45`→`49` từ 🟢). `41` AC2 sửa: lần sai thứ 5 **vẫn** hiện `Invalid email or password` (`AMB-LOGIN-22`). `45`: chuỗi `15 minutes` cố định (`AMB-LOGIN-23`). `49`: email thứ hai = `admin@example.com`, chỉ đăng nhập đúng (`AMB-LOGIN-25`) |
| 🟢 Thêm (⚪) | REQ-LOGIN-50 | Email không tồn tại không bao giờ bị khoá (`AMB-LOGIN-24` — **khác** Giả định tạm) |
| 🟢 Thêm (⚪) | REQ-LOGIN-51 | Hết thời gian khoá thì bộ đếm về 0 (`AMB-LOGIN-23`) |
| 🟡 Sửa | REQ-LOGIN-15 | Thu hẹp phạm vi: không lộ email chỉ được đảm bảo khi bộ đếm **chưa** tới ngưỡng khoá |

Module: **51 REQ** (24 🟢 · 17 🟡 · 10 ⚪) · 47 trong phạm vi viết TC.

### Test case cần xử lý — bản thay thế

| TC ID | REQ liên quan | Hành động | Lý do |
|---|---|---|---|
| CRM_LOGIN_TC_026 | REQ-LOGIN-41 | ⚠️ Viết lại toàn bộ + để `skip` | Kỳ vọng đảo ngược. AC2 nay **assert được** banner lần thứ 5 = `Invalid email or password`, lần gửi thứ 6 (mật khẩu đúng) = thông báo khoá. Dùng tài khoản PM. Gỡ `@AssumptionBased` về banner lần 5. REQ ⚪ → `skip` tới khi deploy |
| CRM_LOGIN_TC_044 | REQ-LOGIN-15 | ⚠️ Review & sửa | Đổi bước 3 sang tài khoản PM · ghi rõ phạm vi **< 5 lần sai** · **huỷ** biến thể đã dự kiến ở lần 1 ("email không tồn tại cũng hiện thông báo khoá") vì `AMB-LOGIN-24` chốt ngược lại |
| CRM_LOGIN_TC_022 | REQ-LOGIN-14, 15 | ⚠️ Review & sửa — đổi dữ liệu | Không đổi so với lần 1: chuyển sang tài khoản PM, gỡ tiền điều kiện "không khoá" |
| CRM_LOGIN_TC_028 | REQ-LOGIN-16 | ⚠️ Review & sửa — đổi dữ liệu | Không đổi so với lần 1: chuyển sang tài khoản PM |
| — | REQ-LOGIN-45 → 48 | ➕ Viết mới + để `skip` | REQ ⚪ — viết trước, chạy khi dev báo deploy |
| — | REQ-LOGIN-49 | ➕ Viết mới + để `skip` | **Hết BLOCKED** — email B = `admin@example.com` với mật khẩu đúng (🔒 `ADMIN_PASSWORD`). Tuyệt đối **không** nhập sai với tài khoản Admin |
| — | REQ-LOGIN-50 | ➕ Viết mới + để `skip` | 6 lần sai với email `notexist_<timestamp>@auto.test` → cả 6 lần `Invalid email or password`. ℹ️ Hiện hệ thống chưa có khoá nên TC này sẽ PASS "vì lý do sai" — vẫn để `skip` cho tới khi deploy mới có ý nghĩa |
| — | REQ-LOGIN-51 | ➕ Viết mới + để `skip` | Sau khi hết khoá: sai 4 lần → đúng → thành công. TC chạy ≥ 15 phút, tách khỏi smoke |

> 🚨 Cảnh báo cũ **vẫn đúng**: `TC_022` + `TC_028` + `TC_044` = 5 lần sai trên `admin@example.com`. Hiện **chưa** deploy nên chưa khoá thật — nhưng phải sửa TC **trước** ngày deploy, không đợi tới lúc đó.

### Ambiguity — lần 2

| Mã | Chuyển trạng thái | Kết luận |
|---|---|---|
| AMB-LOGIN-21 | ❓ → ✅ | Chưa deploy — trùng Giả định tạm |
| AMB-LOGIN-22 | ❓ → ✅ (vế 1) | Lần 5 vẫn hiện `Invalid email or password`. Vế 2 → `AMB-LOGIN-26` |
| AMB-LOGIN-23 | ❓ → ✅ (một phần) | Chuỗi cố định · hết khoá bộ đếm về 0 (`REQ-LOGIN-51`). Vế "gia hạn khi thử lại" → `AMB-LOGIN-26` |
| AMB-LOGIN-24 | ❓ → ✅ | ⚠️ **Khác Giả định tạm** — email không tồn tại không bị khoá (`REQ-LOGIN-50`, `RISK-LOGIN-10`) |
| AMB-LOGIN-25 | ❓ → ✅ (vế b) | Email thứ hai = `admin@example.com`. Vế (a), (c) → `AMB-LOGIN-26` |
| AMB-LOGIN-26 | (mới) ❓ 🟡 | Gom 4 vế chưa trả lời: lần gửi không tới bước kiểm mật khẩu có tính không · gia hạn khi thử lại lúc khoá · tài khoản PM riêng · cổng `/login` |

### Rủi ro — lần 2

| Mã | Thay đổi |
|---|---|
| RISK-LOGIN-10 | (mới) ⚠️ **PO chấp nhận** — thông báo khoá để lộ email nào có tài khoản (email có thật bị khoá, email không tồn tại thì không) |
| RISK-LOGIN-01 | Chưa deploy → hệ thống **vẫn** không khoá, rủi ro giữ mức xác nhận |

---

## Lần 1 — phân tích ticket ban đầu (giữ để truy vết)

> **Ticket:** `CRM-LOGIN-101` — Khoá tài khoản khi đăng nhập sai nhiều lần · PO chốt 28-09-2026
> **Module:** `LOGIN` — [REQUIREMENTS_LOGIN_SUMMARY.md](../REQUIREMENTS_LOGIN_SUMMARY.md) · REQ chi tiết ở [web/requirements_login_web.md](../web/requirements_login_web.md) mục 3.3
> **Nguồn TC đã rà:** [docs/testcases/login/](../../../testcases/login/TEST_CASES_LOGIN_SUMMARY.md) — tra theo cột `REQ ID` và theo dữ liệu test (email có thật + mật khẩu sai). Chưa có RTM, chưa có automation script cho module này
> **Workflow kế tiếp:** `/update-testcases-from-impact` (TC 🟡 + ràng buộc tài khoản) · `/generate-testcases-manual-rbt` hoặc `/generate-testcases-from-requirements` (TC mới cho REQ 🟢)

## Tóm tắt

| Nhóm | Số lượng | REQ |
|---|---|---|
| 🟢 Thêm mới | 5 | REQ-LOGIN-45 → 49 |
| 🟡 Sửa | 1 | REQ-LOGIN-41 — **đảo ngược** từ "không khoá" sang "khoá sau 5 lần sai" |
| 🔴 Bỏ | 0 | — |
| ⚪ Chưa build | 0 | — (trạng thái deploy chưa biết → `AMB-LOGIN-21`, chưa hạ REQ về ⚪) |
| ⏸️ Trùng, không tác động nội dung | 3 | REQ-LOGIN-14, 15, 16 — nội dung REQ giữ nguyên, **nhưng TC phải đổi dữ liệu** vì ràng buộc tài khoản (dòng 6 của ticket) |

**Đối chiếu từng dòng ticket → REQ:**

| Dòng ticket | Nội dung (nguyên văn rút gọn) | Nhóm | REQ |
|---|---|---|---|
| 1 | Sai mật khẩu 5 lần liên tiếp cùng email → khoá 15 phút | 🟡 Sửa (ngưỡng) + 🟢 Thêm (thời lượng) | REQ-LOGIN-41 · REQ-LOGIN-47 |
| 2 | Hiển thị `Your account is locked. Please try again in 15 minutes.` | 🟢 Thêm | REQ-LOGIN-45 |
| 3 | Đang khoá, nhập đúng mật khẩu cũng không đăng nhập được | 🟢 Thêm | REQ-LOGIN-46 |
| 4 | Đăng nhập thành công trước khi đủ 5 lần → bộ đếm về 0 | 🟢 Thêm | REQ-LOGIN-48 |
| 5 | Chỉ khoá theo email, không khoá theo IP | 🟢 Thêm (tách khỏi AC cũ của REQ-41) | REQ-LOGIN-49 |
| 6 | Môi trường dùng chung: TC dùng tài khoản Project Manager, KHÔNG dùng Admin | Ràng buộc kiểm thử — không phải REQ | Metadata index · `RISK-LOGIN-03` |

> Dòng 1 tách thành 2 REQ theo luật *một REQ = một rule kiểm độc lập*: ngưỡng 5 lần (`41`) và thời lượng 15 phút (`47`) hỏng độc lập với nhau.

## Test case cần xử lý

| TC ID | REQ liên quan | Hành động | Lý do |
|---|---|---|---|
| CRM_LOGIN_TC_026 | REQ-LOGIN-41 | ⚠️ Review & sửa — **viết lại toàn bộ kỳ vọng** | TC đang kỳ vọng *không khoá sau 10 lần sai* và *đăng nhập đúng ngay sau đó thành công* — nay **ngược hoàn toàn**. Dùng `admin@example.com` → đổi sang tài khoản PM (🔒 `PM_EMAIL`/`PM_PASSWORD`). Viết theo AC biên của REQ-41: 4 lần sai → đúng vẫn vào · 5 lần sai → đúng bị từ chối. Gỡ tiền điều kiện `AMB-LOGIN-02 ✅ không khoá`. Gắn `@AssumptionBased` (`AMB-LOGIN-22`) và chưa chạy tới khi `AMB-LOGIN-21` trả lời |
| CRM_LOGIN_TC_022 | REQ-LOGIN-14, 15 | ⚠️ Review & sửa — đổi dữ liệu | 3 biến thể sai mật khẩu trên `admin@example.com` → vi phạm dòng 6; đổi sang tài khoản PM. Tiền điều kiện đang dựa vào *"hệ thống không khoá"* → thay bằng *"bộ đếm = 0, tổng lần sai < 5, kết thúc bằng 1 lần đăng nhập đúng để đặt lại bộ đếm"* |
| CRM_LOGIN_TC_028 | REQ-LOGIN-16 | ⚠️ Review & sửa — đổi dữ liệu | 1 lần sai mật khẩu trên `admin@example.com` → đổi sang tài khoản PM. Kỳ vọng (giữ lại email — đang FAIL, bug mở) không đổi |
| CRM_LOGIN_TC_044 | REQ-LOGIN-15 | ⚠️ Review & sửa — đổi dữ liệu + cân nhắc biến thể mới | Bước 3 dùng `admin@example.com` + mật khẩu sai → đổi sang PM, gỡ tiền điều kiện *"không khoá"*. Nếu `AMB-LOGIN-24` chốt theo Giả định tạm → thêm biến thể: 5 lần sai với email không tồn tại cũng phải hiện thông báo khoá giống email có thật |
| CRM_LOGIN_TC_016 | REQ-LOGIN-14 | ✅ Không sửa | Chỉ dùng email **không tồn tại** — không đụng tài khoản thật. Chạy liên tiếp nhiều biến thể vẫn an toàn trừ khi `AMB-LOGIN-24` chốt là email không tồn tại cũng bị "khoá" → khi đó xem lại số biến thể dùng cùng email |
| CRM_LOGIN_TC_027 | REQ-LOGIN-43 | ✅ Không sửa | Tài khoản `Customer` gửi **mật khẩu đúng** ở `/admin` — không phải sai mật khẩu của tài khoản `/admin`. Theo dõi nếu `AMB-LOGIN-22` chốt là mọi lần bị từ chối đều tính vào bộ đếm |
| — | REQ-LOGIN-45 | ➕ Viết mới | Thông báo khoá nguyên văn |
| — | REQ-LOGIN-46 | ➕ Viết mới | Mật khẩu đúng vẫn bị từ chối trong lúc khoá |
| — | REQ-LOGIN-47 | ➕ Viết mới | Tự mở khoá sau 15 phút — TC chạy **≥ 15 phút**, tách khỏi bộ smoke (giống `REQ-LOGIN-42`) |
| — | REQ-LOGIN-48 | ➕ Viết mới | Đặt lại bộ đếm — 4 sai → đúng → 4 sai → đúng vẫn vào |
| — | REQ-LOGIN-49 | ➕ Viết mới | Khoá theo email không theo IP — ⛔ `BLOCKED` cho tới khi có email thứ hai không phải Admin (`AMB-LOGIN-25`) |

**Tài liệu TC cần đồng bộ thêm (không phải dòng TC):** `TEST_CASES_LOGIN_SUMMARY.md` dòng *REQ-LOGIN-41 · Không khoá tài khoản* (bảng phủ REQ) và 2 dòng bảng đối soát loại kiểm thử đang ghi `TC_026 (không khoá)`.

**Automation:** chưa có script nào tham chiếu các TC trên → `/update-automation-from-impact` **không** cần chạy cho ticket này.

## Ràng buộc chạy TC — BẮT BUỘC đọc trước khi thực thi

| # | Ràng buộc | Nguồn |
|---|---|---|
| 1 | Mọi TC nhập sai mật khẩu với email có thật **chỉ** dùng tài khoản `Project Manager` (`.env`: `PM_EMAIL`, `PM_PASSWORD`). **Cấm** dùng `Admin` | Ticket dòng 6 |
| 2 | TC chỉ cần lấy thông báo lỗi → ưu tiên email **không tồn tại**, không tiêu hao bộ đếm của tài khoản thật | `RISK-LOGIN-03` |
| 3 | TC chủ động khoá tài khoản (`41` AC2, `45`, `46`, `47`, `49`) chạy **cuối lượt, một luồng**, báo đội trước; sau đó **chờ đủ 15 phút** trước khi bất kỳ TC nào khác dùng lại tài khoản PM | `RISK-LOGIN-03` · `AMB-LOGIN-25` |
| 4 | TC khoá **chưa chạy** cho tới khi dev xác nhận đã deploy (`AMB-LOGIN-21`) — chạy trước sẽ FAIL giả | `AMB-LOGIN-21` |

## Ambiguity

| Mã | Chuyển trạng thái | Ghi chú |
|---|---|---|
| AMB-LOGIN-02 | ✅ → ✅ (ghi chú *bị thay thế*) | Kết luận 18-08-2026 "không có cơ chế khoá" **bị ticket đảo ngược**. Giữ trạng thái, thêm ghi chú bị thay thế — không mở lại vì ticket đã trả lời rõ |
| AMB-LOGIN-21 | (mới) ❓ 🔴 | Tính năng đã deploy lên môi trường test chưa? |
| AMB-LOGIN-22 | (mới) ❓ 🟡 | Lần sai thứ 5 hiện banner nào · lần gửi không tới bước kiểm mật khẩu có tính vào bộ đếm không |
| AMB-LOGIN-23 | (mới) ❓ 🟡 | 15 phút có bị gia hạn khi thử lại · chuỗi `15 minutes` cố định hay đếm ngược |
| AMB-LOGIN-24 | (mới) ❓ 🔴 | Email không tồn tại có hiện thông báo khoá không — nếu không, lộ email nào có tài khoản (mâu thuẫn `REQ-LOGIN-15`) |
| AMB-LOGIN-25 | (mới) ❓ 🟡 | Tài khoản PM riêng cho TC khoá · email thứ hai cho `REQ-LOGIN-49` · cổng `/login` có áp khoá không |

## Rủi ro

| Mã | Thay đổi |
|---|---|
| RISK-LOGIN-01 | Cập nhật — "không có lớp chống vét cạn nào" **giảm một phần** khi tính năng deploy; vẫn không CAPTCHA, không giới hạn tần suất, không khoá IP |
| RISK-LOGIN-03 | 🔁 **Mở lại** — 4 TC hiện có dùng `admin@example.com` với mật khẩu sai, chạy nối tiếp đủ 5 lần → khoá tài khoản `Admin` của cả đội |
| RISK-LOGIN-09 | (mới) — khoá chỉ theo email bị lạm dụng để chặn người dùng hợp lệ 15 phút, lặp vô hạn |

## Cảnh báo

- 🚨 **Chưa sửa TC nào mà đã chạy lại bộ TC hiện có** thì `TC_022` (3 lần sai) + `TC_028` (1) + `TC_044` (1) = **5 lần sai liên tiếp trên `admin@example.com`** → nếu tính năng đã deploy, **khoá tài khoản Admin** của cả đội 15 phút. Chạy `/update-testcases-from-impact` **trước** lượt thực thi kế tiếp.
- ⚠️ `STORY-LOGIN-03` BLOCKED một phần (phần khoá tài khoản) bởi `AMB-LOGIN-21` 🔴 và `AMB-LOGIN-25`.
- ⚠️ 6 REQ của ticket (`41`, `45`→`49`) **chưa kiểm chứng trên UI thật** — nguồn chỉ là ticket. Không tự thử khoá trên môi trường dùng chung để kiểm chứng; chờ `AMB-LOGIN-21`.
