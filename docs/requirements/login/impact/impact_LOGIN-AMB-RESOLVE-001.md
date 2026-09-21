# Impact Report — `LOGIN-AMB-RESOLVE-001` · 21-09-2026

← [Tài liệu requirements module](../requirements_login.md) · [Danh mục toàn hệ thống](../../README.md)

| Mục | Giá trị |
|---|---|
| **Module** | Login (Đăng nhập & Phiên làm việc) — prefix `LOGIN` |
| **Nguồn thay đổi** | Quyết định của người dùng: **chốt toàn bộ 12 ambiguity theo Assumption tạm** đã ghi trong tài liệu |
| **Không phải ticket hệ thống** | Không có Jira ticket. Mã `LOGIN-AMB-RESOLVE-001` do workflow cấp để truy vết đợt thay đổi này |
| **Dải mã trước** | `REQ-LOGIN-01` → `40` · `RISK-LOGIN-01` → `06` |
| **Dải mã sau** | `REQ-LOGIN-01` → `43` · `RISK-LOGIN-01` → `07` · mã kế tiếp `REQ-LOGIN-44` · `AMB-LOGIN-13` · `RISK-LOGIN-08` |

---

## 1. Tóm tắt

| Nhóm | Số lượng | REQ |
|---|---|---|
| 🟢 Thêm mới | 3 | `REQ-LOGIN-41`, `42`, `43` — cả 3 ở trạng thái ⚪ |
| 🟡 Sửa — **đổi kỳ vọng, trái build hiện tại** | 3 | `REQ-LOGIN-30`, `31`, `38` |
| 🟡 Sửa — siết cách assert, không đổi hành vi | 6 | `REQ-LOGIN-10`, `18`, `19`, `25`, `35`, `36` |
| 🟡 Sửa — làm rõ phạm vi, vẫn ⚪ | 1 | `REQ-LOGIN-34` |
| ✏️ Biên tập | — | Field Spec 4.1 (`value="estimate"`) · Phạm vi · Ma trận phân quyền |
| 🔴 Bỏ | 0 | — |

**Tổng REQ: 40 → 43.** Không mã nào bị đánh lại, không mã nào bị xoá.

---

## 2. ⚠️ Ba yêu cầu nay trái với build hiện tại — đọc trước khi viết TC

Đây là hệ quả quan trọng nhất của đợt chốt này. Ba REQ dưới đây **không còn mô tả hành vi đang chạy** mà mô tả hành vi **đúng** đã được chốt:

| REQ | Yêu cầu đã chốt | Build hiện tại | AMB nguồn |
|---|---|---|---|
| `REQ-LOGIN-30` | Quên mật khẩu với email không tồn tại → thông báo **trung tính**, không lộ danh tính | Trả `Email not found` | `AMB-LOGIN-02` |
| `REQ-LOGIN-31` | Quên mật khẩu bỏ trống email → báo **thiếu trường**, thống nhất trang đăng nhập | Trả `Email not found` | `AMB-LOGIN-02` |
| `REQ-LOGIN-38` | Cookie `autologin` phải `HttpOnly = true` | `HttpOnly = false` | `AMB-LOGIN-03` |

**Hệ quả bắt buộc:**

1. **Raise 3 bug** trước khi thực thi TC, gắn đúng REQ ID — dùng `/create-bug-report`. Evidence đã có sẵn:
   `evidence/forgot_password_unknown_email_viewport.png` · `evidence/forgot_password_empty_submit_viewport.png` · số liệu cookie trong AC của `REQ-LOGIN-38`
2. TC viết theo REQ mới sẽ **FAIL thật** cho tới khi dev sửa → đánh nhãn `known-bug`, **không** tính vào tỉ lệ pass của đợt
3. ⛔ **CẤM hạ AC xuống theo hành vi hiện tại** để test xanh. Làm vậy là che bug — xem `RISK-LOGIN-07`

---

## 3. Test case cần xử lý

> 🔍 **Trạng thái rà soát:** module `LOGIN` **chưa có bộ test case nào** (`docs/testcases/login/` không tồn tại) và **chưa có RTM**. Không có TC nào bị stale — toàn bộ REQ cần viết TC mới.

| TC ID | REQ liên quan | Hành động | Lý do |
|---|---|---|---|
| — | `REQ-LOGIN-01` → `43` | ➕ **Viết mới toàn bộ** | Module chưa có TC nào |
| — | `REQ-LOGIN-30`, `31`, `38` | ➕ Viết mới, đánh `known-bug` | Trái build hiện tại — sẽ FAIL, xem mục 2 |
| — | `REQ-LOGIN-34`, `41`, `42`, `43` | ➕ Viết mới, đánh `skip` | Trạng thái ⚪ — chưa kiểm chứng được, xem mục 5 |

**Điểm cần chú ý khi viết TC cho 6 REQ vừa siết cách assert:**

| REQ | Ràng buộc mới với TC |
|---|---|
| `REQ-LOGIN-10` | Chỉ kiểm **có mặt cả hai** thông báo — **cấm** assert thứ tự |
| `REQ-LOGIN-18` | Phép thử trên tài khoản **có thật** chỉ được dùng **tài khoản phụ** (`RISK-LOGIN-06`) |
| `REQ-LOGIN-19` | Thời hạn ~62 ngày, chấp nhận sai số **±1 ngày** — cấm assert khớp tới giây |
| `REQ-LOGIN-25` | **Cấm assert mã trạng thái HTTP**; chỉ assert điểm dừng và nội dung hiển thị |
| `REQ-LOGIN-35`, `36` | AC chỉ đúng trên **môi trường demo** — ghi rõ điều kiện môi trường trong TC |

---

## 4. Ambiguity

**Toàn bộ 12/12 chuyển `❓ Chờ trả lời` → `✅ Đã trả lời 21-09-2026`. Không mở AMB mới.**

| Mã | Chuyển trạng thái | Kết luận đã chốt | Sinh ra gì |
|---|---|---|---|
| `AMB-LOGIN-01` | ❓ → ✅ | Không có khoá tài khoản, không giới hạn tần suất | `REQ-LOGIN-18` 🟡 |
| `AMB-LOGIN-02` | ❓ → ✅ | `Email not found` là **sơ suất** → phải thống nhất không lộ danh tính | `REQ-LOGIN-30`, `31` 🟡 · `RISK-LOGIN-07` |
| `AMB-LOGIN-03` | ❓ → ✅ | `HttpOnly = false` là **sơ suất cấu hình** → phải sửa | `REQ-LOGIN-38` 🟡 · `RISK-LOGIN-07` |
| `AMB-LOGIN-04` | ❓ → ✅ | Chấp nhận ở demo; production bắt buộc ép HTTPS + HSTS | `REQ-LOGIN-35`, `36` 🟡 · `REQ-LOGIN-42` ⚪ |
| `AMB-LOGIN-05` | ❓ → ✅ | ~62 ngày là mặc định sản phẩm | `REQ-LOGIN-19` 🟡 |
| `AMB-LOGIN-06` | ❓ → ✅ | Ứng dụng không đặt thời hạn phiên riêng, theo mặc định PHP | `REQ-LOGIN-41` ⚪ |
| `AMB-LOGIN-07` | ❓ → ✅ | `value="estimate"` là lỗi sao chép, cấm dùng làm locator | Ghi chú Field Spec 4.1 |
| `AMB-LOGIN-08` | ❓ → ✅ | Mã 307 không cố ý → cấm assert mã trạng thái | `REQ-LOGIN-25` 🟡 |
| `AMB-LOGIN-09` | ❓ → ✅ | Mọi vai trò dùng chung một luồng đăng nhập | `REQ-LOGIN-43` ⚪ · ma trận 10 ô `❔` → `⚠️✅` |
| `AMB-LOGIN-10` | ❓ → ✅ | Có gửi email kèm liên kết đặt lại | `REQ-LOGIN-34` giữ ⚪, AC làm rõ |
| `AMB-LOGIN-11` | ❓ → ✅ | Cổng khách hàng là hệ thống xác thực **tách biệt** | Cập nhật Phạm vi · chuyển sang module `CTC` |
| `AMB-LOGIN-12` | ❓ → ✅ | Thứ tự thông báo không thuộc yêu cầu | `REQ-LOGIN-10` 🟡 |

---

## 5. Cảnh báo — hai kết luận KHÔNG mang giá trị đo được

Hai AMB dưới đây được chốt ở mức **chính sách**, nhưng Assumption tạm không chứa con số nào để viết AC. Tôi **không suy ra giá trị** — phần thiếu nằm trong REQ ⚪ kèm chỉ dẫn phải đo:

| Mã | Chốt được gì | Còn thiếu gì | Cách lấp |
|---|---|---|---|
| `AMB-LOGIN-06` | Ứng dụng không có logic hết hạn phiên riêng | **Thời hạn cụ thể** (phút/giờ) | Đo thực nghiệm: để phiên nhàn rỗi rồi mở route bảo vệ · hoặc hỏi dev. Cho tới lúc đó `REQ-LOGIN-41` giữ ⚪ |
| `AMB-LOGIN-10` | Có gửi email kèm liên kết đặt lại | **Hạn dùng của liên kết** · nội dung email · thông báo sau khi gửi | Cần hộp thư kiểm soát được. `REQ-LOGIN-34` giữ ⚪ |

**Các cảnh báo khác:**

- ⚠️ `RISK-LOGIN-05` **vẫn mở** — dự án chỉ có một tài khoản. 10/20 ô ma trận phân quyền đang ở mức `⚠️✅` **suy diễn**, chưa ô nào được đăng nhập thử. `REQ-LOGIN-43` không kiểm chứng được cho tới khi có tài khoản vai trò thứ hai. **Không** báo cáo 10 ô này như đã kiểm chứng
- ⚠️ `RISK-LOGIN-06` **vẫn mở** — `AMB-LOGIN-01` chốt là không có khoá, nhưng chưa ai kiểm trên tài khoản **có thật**. Chỉ thử bằng tài khoản phụ
- ⚠️ `REQ-LOGIN-42` chỉ chạy được trên **môi trường production** — TC đánh `skip` trên demo
- ℹ️ `STORY-LOGIN-04` **hết BLOCKED** sau khi chốt `AMB-LOGIN-01`; `STORY-LOGIN-08` vẫn BLOCKED một phần vì `REQ-LOGIN-34`

---

## 6. Bước kế tiếp

| Thứ tự | Việc | Lệnh |
|---|---|---|
| 1 | Raise 3 bug cho `REQ-LOGIN-30`, `31`, `38` | `/create-bug-report` |
| 2 | Sinh bộ TC đầu tiên cho module (43 REQ) | `/generate-testcases-manual-rbt docs/requirements/login/requirements_login.md` |
| 3 | Sinh RTM sau khi đã có TC | `/generate-traceability-matrix` |

> ℹ️ **Không chạy `/update-testcases-from-impact`** cho đợt này — workflow đó dùng để sửa TC **đã có**. Module chưa có TC nào nên phải sinh mới từ đầu.
