# Software Requirements Specification (SRS)
## Module: Login (Đăng nhập)
**Hệ thống:** Perfex CRM – Anh Tester Demo
**URL khảo sát:** https://crm.anhtester.com/admin/authentication
**Ngày khảo sát:** 2026-08-26
**Phiên bản tài liệu:** 1.0

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu này mô tả yêu cầu chức năng và phi chức năng của module **Login** trong hệ thống Perfex CRM, dùng làm cơ sở cho việc thiết kế test case, kiểm thử và phát triển.

### 1.2 Phạm vi
Module Login cho phép người dùng (Admin/Staff) xác thực để truy cập vào khu vực quản trị CRM. Phạm vi tài liệu bao gồm:
- Màn hình đăng nhập (Login)
- Chức năng quên mật khẩu (Forgot Password) — liên quan trực tiếp đến luồng đăng nhập
- Các thông báo lỗi, validation liên quan

Không bao gồm: chức năng đổi mật khẩu sau khi đăng nhập, quản lý phân quyền, đăng nhập 2FA (không phát hiện trên giao diện khảo sát).

### 1.3 Đối tượng sử dụng
- Nhân viên/Quản trị viên (Staff/Admin) của hệ thống CRM.

---

## 2. Mô tả tổng quan

Màn hình Login là điểm truy cập đầu tiên vào hệ thống quản trị. Người dùng cần cung cấp **Email** và **Password** hợp lệ để được cấp quyền truy cập. Trang cung cấp thêm tùy chọn "Remember me" và liên kết đến chức năng khôi phục mật khẩu.

### 2.1 Các thành phần giao diện (UI Elements)

| # | Thành phần | Loại | Bắt buộc | Ghi chú |
|---|-----------|------|----------|---------|
| 1 | Logo hệ thống | Image/Link | - | Liên kết về trang chủ |
| 2 | Tiêu đề "Login" | Heading | - | |
| 3 | Email Address | Input (type=email) | Có | Có validate định dạng email (HTML5) |
| 4 | Password | Input (type=password) | Có | Ẩn ký tự nhập |
| 5 | Remember me | Checkbox | Không | Ghi nhớ đăng nhập |
| 6 | Nút "Login" | Button (submit) | - | |
| 7 | Liên kết "Forgot Password?" | Link | - | Điều hướng đến trang khôi phục mật khẩu |

---

## 3. Yêu cầu chức năng (Functional Requirements)

### FR-01: Hiển thị form đăng nhập
Hệ thống phải hiển thị form đăng nhập gồm: Email Address, Password, checkbox Remember me, nút Login, và liên kết Forgot Password khi người dùng truy cập URL `/admin/authentication`.

### FR-02: Validate trường bắt buộc (Required Fields)
- Khi người dùng bấm **Login** mà để trống cả hai trường, hệ thống phải hiển thị đồng thời hai thông báo lỗi:
  - `The Email Address field is required.`
  - `The Password field is required.`
- Thông báo lỗi hiển thị ngay trên form, phía trên các trường nhập liệu.

### FR-03: Validate định dạng Email (Client-side)
- Trường Email sử dụng kiểu `input type="email"`, trình duyệt tự động chặn submit nếu giá trị không đúng định dạng email (ví dụ thiếu ký tự `@`).
- Thông báo lỗi client-side (native browser tooltip), ví dụ: `Please include an '@' in the email address. '<value>' is missing an '@'.`
- Form **không** được gửi lên server khi email sai định dạng.

### FR-04: Xác thực thông tin đăng nhập (Server-side)
- Khi Email đúng định dạng nhưng **Email hoặc Password không khớp** với tài khoản trong hệ thống, server trả về thông báo lỗi chung:
  - `Invalid email or password`
- Thông báo lỗi không được tiết lộ cụ thể trường nào sai (email hay password) — nhằm mục đích bảo mật, chống dò tài khoản (username enumeration).

### FR-05: Đăng nhập thành công
- Khi Email và Password khớp với tài khoản hợp lệ và tài khoản đang **active**, hệ thống phải:
  - Tạo phiên đăng nhập (session) cho người dùng.
  - Điều hướng người dùng đến trang **Dashboard** tại URL `https://crm.anhtester.com/admin/` (đã xác nhận qua ảnh chụp màn hình do người dùng cung cấp sau khi tự đăng nhập).
- Giao diện Dashboard sau đăng nhập gồm các thành phần chính:
  - Sidebar menu: Bảng tin, Khách hàng, Các dự án, Công việc, Hợp đồng, Doanh số, Thuê bao, Chi phí, Hỗ trợ, Khách tiềm năng, Yêu cầu báo giá, Kiến thức, Tiện ích, Báo cáo.
  - Thanh trên cùng: ô Tìm kiếm, nút thêm nhanh (+), icon chia sẻ, icon việc cần làm, avatar người dùng (kèm dropdown tài khoản), lịch sử, thông báo.
  - Các widget tổng quan: Hóa đơn đang chờ thanh toán, Mục tiêu đã chuyển đổi, Các dự án đang thực hiện, Phân công chưa hoàn thành.
  - Các bảng tổng quan: Tổng quan hóa đơn, Tổng quan báo giá, Tổng quan đề xuất dự án, Nhắc nhở công việc của tôi, Phân công của tôi.

### FR-06: Chức năng "Remember me"
- Khi checkbox "Remember me" được chọn trước khi đăng nhập, hệ thống phải duy trì phiên đăng nhập lâu hơn (qua cookie/token) sau khi đóng trình duyệt.
- Khi không chọn, phiên đăng nhập kết thúc theo thời gian timeout mặc định hoặc khi đóng trình duyệt (tùy cấu hình session).

### FR-07: Chức năng "Forgot Password"
- Liên kết "Forgot Password?" điều hướng đến trang `/admin/authentication/forgot_password`.
- Trang này gồm: trường **Email Address** (bắt buộc) và nút **Confirm**.
- Khi người dùng nhập email đã đăng ký và bấm Confirm, hệ thống phải gửi email chứa liên kết đặt lại mật khẩu.
- Cần có validate:
  - Email bắt buộc (required).
  - Định dạng email hợp lệ.
  - Thông báo phù hợp khi email không tồn tại trong hệ thống (nên dùng thông báo trung lập để tránh lộ thông tin tài khoản).

### FR-08: Điều hướng Logo
- Bấm vào logo ở góc trên phải điều hướng người dùng về trang chủ (`https://crm.anhtester.com/`).

---

## 4. Yêu cầu phi chức năng (Non-Functional Requirements)

### NFR-01: Bảo mật
- Password phải được ẩn (masked) khi nhập, không hiển thị dạng plain text.
- Password không được truyền hoặc lưu dưới dạng plain text (phải mã hóa/hash phía server).
- Thông báo lỗi đăng nhập sai không được phân biệt rõ "sai email" hay "sai mật khẩu" (chống dò tài khoản).
- Nên có cơ chế giới hạn số lần đăng nhập sai (rate limiting/lockout/captcha) để chống tấn công brute-force. *(Chưa xác minh được trên hệ thống khảo sát — cần bổ sung test case riêng.)*
- Kết nối phải sử dụng HTTPS (đã xác nhận: `https://crm.anhtester.com`).

### NFR-02: Khả năng sử dụng (Usability)
- Thông báo lỗi phải rõ ràng, hiển thị ngay tại vị trí gần trường liên quan hoặc đầu form.
- Form phải hỗ trợ điều hướng bằng bàn phím (Tab, Enter để submit).

### NFR-03: Khả năng tương thích (Compatibility)
- Giao diện cần hiển thị đúng trên nhiều kích thước màn hình (responsive) — quan sát cho thấy layout co giãn theo viewport (đã kiểm tra ở độ phân giải 1280x720 và 529x678).

### NFR-04: Hiệu năng
- Thời gian phản hồi cho một request đăng nhập (thành công hoặc thất bại) nên dưới 3 giây trong điều kiện mạng bình thường.

---

## 5. Quy tắc nghiệp vụ (Business Rules)

| Mã | Quy tắc |
|----|---------|
| BR-01 | Email và Password là bắt buộc để đăng nhập. |
| BR-02 | Email phải đúng định dạng chuẩn (có ký tự `@` và domain). |
| BR-03 | Tài khoản không tồn tại hoặc sai mật khẩu → thông báo lỗi chung "Invalid email or password". |
| BR-04 | Tài khoản bị vô hiệu hóa (inactive/banned) không được phép đăng nhập. *(cần xác minh thêm)* |
| BR-05 | "Remember me" ảnh hưởng đến thời gian tồn tại của phiên đăng nhập. |

---

## 6. Các thông báo hệ thống đã xác nhận (Observed Messages)

| Tình huống | Thông báo |
|-----------|-----------|
| Bỏ trống cả 2 trường | `The Email Address field is required.` / `The Password field is required.` |
| Email sai định dạng | `Please include an '@' in the email address. '<value>' is missing an '@'.` (validate native của trình duyệt) |
| Email đúng định dạng, sai email/mật khẩu | `Invalid email or password` |

---

## 7. Giả định & Giới hạn (Assumptions & Limitations)

- Tài liệu được xây dựng dựa trên khảo sát giao diện thực tế (black-box), **không truy cập được mã nguồn hệ thống**.
- Hành vi đăng nhập thành công (điều hướng đến `/admin/` Dashboard) đã được xác nhận qua ảnh chụp màn hình do người dùng cung cấp sau khi tự đăng nhập.
- Không xác minh được: cơ chế khóa tài khoản sau nhiều lần đăng nhập sai, thời gian timeout của session, có/không có 2FA, hành vi cụ thể của "Remember me" (thời hạn cookie) — do giới hạn về việc nhập thông tin xác thực thật trong quá trình khảo sát tự động.
- Các mục trên cần được xác minh bổ sung bằng kiểm thử thủ công có kiểm soát trước khi dùng làm cơ sở phát triển/kiểm thử chính thức.

---

## 8. Đề xuất kịch bản kiểm thử (Test Scenarios – tham khảo)

1. Đăng nhập với email & password hợp lệ → thành công, chuyển đến Dashboard.
2. Đăng nhập với email hợp lệ, password sai → hiển thị `Invalid email or password`.
3. Đăng nhập với email không tồn tại → hiển thị `Invalid email or password`.
4. Để trống cả 2 trường → hiển thị đủ 2 thông báo required.
5. Nhập email sai định dạng (không có `@`) → chặn submit, hiển thị tooltip lỗi định dạng.
6. Nhập password đúng nhưng email sai định dạng → không submit được.
7. Chọn "Remember me" và đăng nhập thành công → kiểm tra cookie/token tồn tại lâu hơn.
8. Bấm "Forgot Password?" → điều hướng đúng trang, nhập email hợp lệ → nhận được email khôi phục.
9. Kiểm tra giới hạn số lần đăng nhập sai liên tiếp (brute-force protection).
10. Kiểm tra hiển thị responsive trên các độ phân giải khác nhau (desktop/mobile).
