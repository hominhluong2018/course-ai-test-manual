# Execution Report — Đăng nhập / Xác thực (`LOGIN`) · TC cập nhật & bổ sung ngày 28-09-2026 (`CRM-LOGIN-101`)

| Thông tin | Nội dung |
|---|---|
| Run ID | run_1790606735 |
| Nền tảng | `web` |
| Nguồn TC | [part_01_web_smoke_chuc_nang.md](../../../../testcases/login/web/parts/part_01_web_smoke_chuc_nang.md) · [part_02_web_ky_thuat_phi_chuc_nang.md](../../../../testcases/login/web/parts/part_02_web_ky_thuat_phi_chuc_nang.md) · [part_03_web_khoa_tai_khoan.md](../../../../testcases/login/web/parts/part_03_web_khoa_tai_khoan.md) — index [TEST_CASES_LOGIN_SUMMARY.md](../../../../testcases/login/TEST_CASES_LOGIN_SUMMARY.md) |
| Phạm vi | **21 TC** — 8 TC sửa theo DELTA `CRM-LOGIN-101` (`TC_014`, `022`, `023`, `026`, `027`, `028`, `042`, `044`) + 13 TC bổ sung (`TC_053` → `TC_065`) · loại 0 TC `@Deprecated` |
| Môi trường | `https://crm.anhtester.com` — môi trường demo |
| Build / Version | Không cung cấp. Người dùng báo tính năng khoá tài khoản `CRM-LOGIN-101` **đã deploy** (28-09-2026) |
| Tài khoản | Không đăng nhập — 2 TC đã chạy chỉ dùng email **không tồn tại** |
| Người thực hiện | Agent (Playwright MCP, Google Chrome, headed, viewport theo `--viewport-size` lúc launch) |
| Bắt đầu → Kết thúc | 28-09-2026 21:49 → 21:51 (2 phút). Lần mở trang đầu tiên hết thời gian chờ mạng; thử lại thì vào được |
| Môi trường dùng chung? | Có — auto-skip TC phá huỷ đang BẬT (phạm vi này không có TC phá huỷ) |
| ⚠️ Giới hạn của lần chạy | Agent **không nhập mật khẩu thật** vào hệ thống bên ngoài (giới hạn an toàn của agent, **không** gỡ được bằng lời cho phép trong chat). 19/21 TC cần ít nhất một lần đăng nhập đúng → `BLOCKED`. Agent **cũng không chạy dở** các TC này: nhập sai mà không có bước đăng nhập đúng để dọn sẽ để lại bộ đếm lần sai dang dở hoặc khoá tài khoản `Project Manager` / `Customer` dùng chung |

## 1. Tổng kết

| Trạng thái | Số lượng | Tỷ lệ |
|---|---|---|
| ✅ PASS | 2 | 9.5% |
| ❌ FAIL | 0 | 0% |
| ⚠️ BLOCKED | 19 | 90.5% |
| ⏭️ SKIPPED | 0 | 0% |
| **Tổng** | **21** | 100% |

> **Pass rate (không tính SKIPPED):** 2/21 = 9.5% · **trên số TC thực chạy:** 2/2 = 100%.
>
> ⚠️ **Lần chạy này CHƯA chứng minh được tính năng khoá tài khoản hoạt động.** Hai TC PASS đều dùng email không tồn tại, một TC là nhánh bỏ trống mật khẩu. Hệ thống **chưa có** tính năng khoá cũng cho kết quả y hệt. Mọi TC chứng minh *có khoá* đều nằm trong nhóm BLOCKED.

## 2. Kết quả từng TC

| TC ID | Test Scenario | Kết quả | Bước fail | Ghi chú |
|---|---|---|---|---|
| CRM_LOGIN_TC_014 | Gửi biểu mẫu đăng nhập khi chỉ bỏ trống Mật khẩu | ✅ PASS | — | Email `notexist_20260928@auto.test`. Bước 4: trang nạp lại, vẫn ở `/admin/authentication`. Bước 5: đúng 1 banner `The Password field is required.`, không kèm `Invalid email or password` |
| CRM_LOGIN_TC_022 | Email có thật nhưng sai mật khẩu bị từ chối bằng đúng thông báo chung | ⚠️ BLOCKED | — | Bước 6 (Dọn) cần mật khẩu thật `PM_PASSWORD` |
| CRM_LOGIN_TC_023 | Ô Mật khẩu chịu được dữ liệu bất thường mà hệ thống không sập | ⚠️ BLOCKED | — | Bước 6 (Dọn) cần mật khẩu thật `PM_PASSWORD` |
| CRM_LOGIN_TC_026 | Tài khoản bị khoá khi đăng nhập sai mật khẩu 5 lần liên tiếp — sai 4 lần thì chưa khoá | ⚠️ BLOCKED | — | Bước 3, 6 cần mật khẩu thật `PM_PASSWORD` |
| CRM_LOGIN_TC_027 | Tài khoản cổng khách hàng không đăng nhập được vào khu quản trị | ⚠️ BLOCKED | — | Bước 3, 6 cần mật khẩu thật `CUSTOMER_PASSWORD` |
| CRM_LOGIN_TC_028 | 🐞 Máy chủ phải giữ lại email đã nhập sau khi đăng nhập thất bại | ⚠️ BLOCKED | — | Bước 7 (Dọn) cần mật khẩu thật `PM_PASSWORD` |
| CRM_LOGIN_TC_042 | Gửi biểu mẫu với mã chống giả mạo bị sửa thì bị từ chối | ⚠️ BLOCKED | — | Bước 3, 6 cần mật khẩu thật `PM_PASSWORD` |
| CRM_LOGIN_TC_044 | Thông báo lỗi đăng nhập không tiết lộ email nào có tài khoản thật | ⚠️ BLOCKED | — | Bước 4, 6 cần mật khẩu thật `CUSTOMER_PASSWORD`, `PM_PASSWORD` |
| CRM_LOGIN_TC_053 | Thông báo khoá tài khoản hiển thị đúng nguyên văn, đúng vị trí và không đếm ngược | ⚠️ BLOCKED | — | Bước 7 (Dọn) cần `PM_PASSWORD`. Không chạy dở bước 2–6 vì sẽ khoá PM mà không dọn được |
| CRM_LOGIN_TC_054 | Mật khẩu đúng vẫn bị từ chối trong lúc khoá | ⚠️ BLOCKED | — | Bước 3 (cả 5 biến thể) cần `PM_PASSWORD` |
| CRM_LOGIN_TC_055 | Tài khoản tự mở khoá sau 15 phút | ⚠️ BLOCKED | — | Bước 3, 4, 5 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_056 | Đăng nhập thành công trước ngưỡng thì số lần sai được xoá về 0 | ⚠️ BLOCKED | — | Bước 3, 6 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_057 | Khoá áp theo email — email khác đăng nhập từ cùng máy vẫn vào được | ⚠️ BLOCKED | — | Bước 3, 5 cần `ADMIN_PASSWORD`, `PM_PASSWORD` |
| CRM_LOGIN_TC_058 | Email không tồn tại gửi sai nhiều lần cũng không bao giờ bị khoá | ✅ PASS | — | Email `notexist_20260928_lock@auto.test` + `SaiMatKhau@2026`, gửi 7 lần: **cả 7 lần** đúng 1 banner `Invalid email or password`, vẫn ở `/admin/authentication`, không lần nào hiện thông báo khoá. Evidence: [CRM_LOGIN_TC_058_lan7_invalid.png](evidence/CRM_LOGIN_TC_058_lan7_invalid.png). ⚠️ Kết quả này **không phân biệt được** hệ thống đã có khoá hay chưa |
| CRM_LOGIN_TC_059 | Hết thời gian khoá thì số lần sai về 0 | ⚠️ BLOCKED | — | Bước 5 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_060 | Bấm Login khi bỏ trống mật khẩu cũng bị tính là một lần sai | ⚠️ BLOCKED | — | Bước 4 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_061 | Bấm Login khi bỏ trống email thì không bị tính vào số lần sai | ⚠️ BLOCKED | — | Bước 4 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_062 | Cổng khách hàng cũng khoá tài khoản sau 5 lần sai | ⚠️ BLOCKED | — | Bước 3, 4 cần `CUSTOMER_PASSWORD` |
| CRM_LOGIN_TC_063 | Tài khoản khách hàng bị từ chối ở khu quản trị cũng bị tính chung số lần sai | ⚠️ BLOCKED | — | Bước 2, 5 cần `CUSTOMER_PASSWORD` |
| CRM_LOGIN_TC_064 | Tài khoản quản trị bị từ chối ở cổng khách hàng cũng bị tính chung số lần sai | ⚠️ BLOCKED | — | Bước 2, 5 cần `PM_PASSWORD` |
| CRM_LOGIN_TC_065 | Gửi biểu mẫu với mã chống giả mạo bị sửa cũng bị tính là một lần sai | ⚠️ BLOCKED | — | Bước 3, 7 cần `PM_PASSWORD` |

## 3. Chi tiết TC FAIL

Không có TC FAIL.

## 4. TC BLOCKED

| TC ID | Nguyên nhân chặn | Cần gì để chạy được |
|---|---|---|
| CRM_LOGIN_TC_022, 023, 026, 027, 028, 042, 044 | Cần đăng nhập đúng bằng tài khoản thật (bước chính hoặc bước Dọn). Agent không nhập mật khẩu thật vào hệ thống bên ngoài | Tester chạy tay, **hoặc** chạy phối hợp: tester tự gõ mật khẩu ở bước đăng nhập đúng, agent làm các bước còn lại và chấm |
| CRM_LOGIN_TC_053 → TC_057, TC_059 → TC_065 | Như trên. Thêm: nhóm này khoá tài khoản `Project Manager` / `Customer` dùng chung — chạy dở không dọn được là để tài khoản ở trạng thái khoá | Như trên, theo **lịch chạy nhóm khoá** ở đầu Part 03 (nhóm PM nối tiếp ≈ 2,5 giờ, nhóm Customer song song). Báo đội trước khi chạy |

## 5. Dữ liệu đã tạo & dọn dẹp

| Dữ liệu | ID | Nơi tạo | Đã xoá? |
|---|---|---|---|
| Không tạo dữ liệu | — | — | — |

> Không bản ghi nào được tạo. Không tài khoản thật nào bị nhập sai mật khẩu → bộ đếm khoá của `Admin`, `Project Manager`, `Customer` **không** bị tiêu hao trong lần chạy này. Email không tồn tại không có bộ đếm (`REQ-LOGIN-50`).

## 6. Đề xuất bước tiếp theo

- **Chạy lại 19 TC BLOCKED** với tester tự nhập mật khẩu, theo thứ tự ở đầu Part 03: `TC_056`, `TC_061` (không khoá ai) → nhóm DELTA `TC_022`, `023`, `027`, `028`, `042`, `044` → nhóm khoá PM nối tiếp (`053`, `054`, `055`, `057`, `059`, `060`, `064`, `065`, và `026` cuối cùng) · nhóm khoá Customer (`062`, `063`) song song.
- **Ưu tiên chạy `TC_053` sớm trong nhóm khoá:** đây là TC duy nhất xác nhận tính năng thật sự đã deploy (thông báo khoá xuất hiện). Nếu không thấy thông báo khoá, nhóm khoá sẽ FAIL hàng loạt vì cùng một nguyên nhân → dừng và hỏi lại dev trước khi chạy tiếp.
- Sau khi `TC_053` chạy được: chụp ảnh thông báo khoá bổ sung vào `docs/requirements/login/web/evidence/` để gỡ `@NeedsVerify` (xem mục *Vùng chưa có evidence* của index).
- Không có TC FAIL → chưa cần `/create-bug-report`.
