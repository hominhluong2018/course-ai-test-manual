# Module 01 — Login (Đăng nhập & Phiên làm việc)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `LOGIN` |
| **Tên trên website** | Authentication |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 12–18 |

> Trạng thái recon là bản gốc ở [`../../README.md`](../../README.md) — tệp này không nhân bản.

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/authentication` | Trang đăng nhập |
| `/admin/authentication/forgot_password` | Quên mật khẩu — **đã mở và chụp** |
| `/admin/authentication/logout` | Đăng xuất |
| `/admin/access_denied` | Trang từ chối truy cập — **đã xác minh**, xuất hiện khi gõ `/admin/staff` |

## Loại màn hình & hành vi quan sát được

**Biểu mẫu đăng nhập** — đã chụp ở trạng thái chưa đăng nhập. Thành phần đọc được trên DOM thật:

| Phần tử | Định danh | Ghi chú |
|---|---|---|
| Ô email | `#email`, `name="email"`, `type="email"` | Nhãn hiển thị: `Email Address` |
| Ô mật khẩu | `#password`, `name="password"`, `type="password"` | Nhãn hiển thị: `Password` |
| Ghi nhớ đăng nhập | `#remember`, `name="remember"`, `type="checkbox"` | Nhãn hiển thị: `Remember me` |
| Nút gửi | `button[type="submit"]` | Nhãn hiển thị: `Login` |
| Chống giả mạo | `input[type="hidden"][name="csrf_token_name"]` | **Automation phải lấy token từ trang** |
| Quên mật khẩu | Liên kết `Forgot Password?` | ✅ Đã xác minh tồn tại |

Biểu mẫu `POST` về chính `/admin/authentication`.

**Màn hình Quên mật khẩu** — đã mở thật tại `/admin/authentication/forgot_password` và chụp ở trạng thái mặc định:

| Phần tử | Định danh | Ghi chú |
|---|---|---|
| Tiêu đề màn hình | `Forgot Password` | Là `<h…>` trên trang |
| Ô email | `#email`, `name="email"`, `type="email"` | Nhãn hiển thị: `Email Address` |
| Nút gửi | `button[type="submit"]` | Nhãn hiển thị: **`Confirm`** |
| Chống giả mạo | `input[type="hidden"][name="csrf_token_name"]` | |

Biểu mẫu `POST` về chính `/admin/authentication/forgot_password`.

⚠️ Hai điểm ghi nhận được ngay ở tầng khám phá, đáng thành REQ/TC:

| Quan sát | Vì sao đáng chú ý |
|---|---|
| **Không có liên kết quay lại trang đăng nhập** — quét toàn bộ `<a>` trên trang trả về **danh sách rỗng** | Người dùng vào nhầm thì chỉ còn cách bấm Back của trình duyệt hoặc gõ URL. Là vấn đề khả dụng, cần hỏi PO cố ý hay thiếu sót |
| **Tiêu đề tab vẫn là `... - Login`** trong khi tiêu đề màn hình là `Forgot Password` | TC assert `document.title` mà kỳ vọng "Forgot Password" sẽ đỏ. Ghi lại để không viết sai kỳ vọng |

🚫 **Chưa bấm `Confirm`** — submit sẽ **gửi email đặt lại mật khẩu thật**. Việc này để tầng recon cấp module quyết định, có chuẩn bị hộp thư nhận.

**Đăng xuất** — mục trong menu hồ sơ gọi hàm `logout()` của trang, **không** phải liên kết điều hướng thuần.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | Không áp dụng |
| Status flow | Không |
| Số tab | 0 |

## Lý do risk 🔴

- Là cổng vào duy nhất của **toàn bộ** hệ thống — hỏng là chặn 25 module còn lại
- Liên quan trực tiếp tới quyền và danh tính người dùng
- Mức phủ tài liệu ⬜ Trắng

## Phát hiện tầng network của riêng module

- Đăng xuất chạy qua hàm JavaScript, nên automation **phải bấm đúng phần tử** — gọi thẳng URL rồi kết luận là sai
- Mọi biểu mẫu trong hệ thống mang `csrf_token_name`, áp cho cả biểu mẫu đăng nhập

## Vùng chưa xác minh

| Hạng mục | Lý do | Cách gỡ |
|---|---|---|
| Validation và thông báo lỗi nguyên văn của biểu mẫu đăng nhập | Chưa submit sai bao giờ — tầng khám phá không trigger validation | Việc của `/generate-requirements-from-website LOGIN` |
| Luồng Quên mật khẩu — **phần sau khi bấm `Confirm`**: thông báo hiển thị, email gửi đi, liên kết đặt lại, màn hình đặt mật khẩu mới, hạn dùng của liên kết | ✅ Màn hình nhập email **đã mở và chụp**. Chưa submit vì sẽ **gửi email thật** | Việc của `/generate-requirements-from-website LOGIN`, khi đã chuẩn bị hộp thư nhận. 🔒 Ghi **hình thái** chuỗi ký trong liên kết đặt lại, **không** ghi giá trị thật |
| Ghi nhớ đăng nhập (Remember me) | Như trên | Như trên |
| Khoá tài khoản sau N lần sai | Như trên. ⚠️ Môi trường **không** dùng chung nên được phép thử | Như trên |
| Hết hạn phiên | Cấu hình thời hạn nằm ở vùng Setup đang bị chặn | Gỡ `AMB-SYS-01` |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/authentication` — có ảnh
- `/admin/authentication/forgot_password` — có ảnh
- `/admin/access_denied` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/authentication/logout` — chưa bấm đăng xuất trong đợt khảo sát

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`login_form_default_viewport.png`](../evidence/login_form_default_viewport.png) | Chưa đăng nhập, trường rỗng; đủ 4 điều khiển + liên kết `Forgot Password?` |
| [`login_forgot_password_default_viewport.png`](../evidence/login_forgot_password_default_viewport.png) | Màn hình **Quên mật khẩu** ở trạng thái mặc định: tiêu đề `Forgot Password`, ô `Email Address` rỗng, nút `Confirm`; **không có liên kết quay lại đăng nhập** |
| [`sys_access_denied_viewport.png`](../evidence/sys_access_denied_viewport.png) | Trang từ chối truy cập: toast **"Access denied"** + **"Something went wrong. Try again"** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
