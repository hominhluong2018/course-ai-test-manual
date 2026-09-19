# Requirements — Login (Đăng nhập & Phiên làm việc) · Nền tảng Web

← [Về tài liệu index của module](../requirements_login.md) · [Danh mục toàn hệ thống](../../README.md)

| Mục | Giá trị |
|---|---|
| **Module** | Login (Đăng nhập & Phiên làm việc) — prefix `LOGIN` |
| **Nền tảng** | Web |
| **Dải mã đã dùng** | `REQ-LOGIN-01` → `REQ-LOGIN-40` |
| **Mã kế tiếp** | Đợt phân tích sau bắt đầu từ `REQ-LOGIN-41` — **KHÔNG đánh lại từ 01** |
| **Trình duyệt khảo sát** | **Google Chrome** (điều khiển bằng Playwright), viewport `1600×750`. Mọi AC dựa trên **thông báo mặc định của trình duyệt chỉ đúng với trình duyệt này** và **không được dùng làm assertion** |
| **Ngày khảo sát** | 19-09-2026 |
| **Nguồn** | UI thực tế — không có tài liệu nào được cung cấp |

---

## 1. Phạm vi

| Trong phạm vi | Ngoài phạm vi |
|---|---|
| Trang đăng nhập `/admin/authentication` | Cổng đăng nhập của **khách hàng** (người liên hệ) — URL khác, xem `AMB-LOGIN-11` |
| Màn hình Quên mật khẩu `/admin/authentication/forgot_password` | Màn hình **đặt lại mật khẩu** sau khi bấm liên kết trong email — chưa kiểm chứng |
| Đăng xuất, bảo vệ route, điều hướng theo trạng thái phiên | Cấu hình thời hạn phiên (nằm ở vùng Setup bị chặn) |
| Cookie phiên và cookie ghi nhớ đăng nhập | Quản lý tài khoản nhân viên (vùng Setup bị chặn) |
| Trang từ chối truy cập `/admin/access_denied` | Ma trận quyền chi tiết của từng chức năng — thuộc module tương ứng |

## 2. Bản đồ phủ tài liệu

**Không có tài liệu nào được cung cấp cho module này — toàn bộ REQ sinh từ khảo sát UI thực tế.**

---

## 3. Yêu cầu Chức năng

> Cột `Nguồn`: **`Kiểm chứng thực tế`** = đã tương tác và xác nhận kết quả · **`UI thực tế`** = chỉ quan sát, chưa tương tác.

### 3.1. Giao diện & cấu trúc trang đăng nhập

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-01 | Trang đăng nhập hiển thị tại route riêng | Người dùng chưa đăng nhập mở `/admin/authentication` thì thấy biểu mẫu đăng nhập | Mở `/admin/authentication` ở trạng thái chưa đăng nhập → trang có tiêu đề màn hình **`Login`** (thẻ heading) và biểu mẫu gồm 3 điều khiển nhập liệu + nút gửi | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-02 | Con trỏ tự vào ô Email khi mở trang | Giảm một thao tác cho người dùng | Mở trang → `document.activeElement` có `id = "email"`. Thuộc tính `autofocus` có trên ô email | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-03 | Ô mật khẩu che ký tự khi nhập | Không để lộ mật khẩu trên màn hình | Ô `#password` có `type="password"`. **Không** có nút hiện/ẩn mật khẩu trên giao diện | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-04 | Tuỳ chọn ghi nhớ đăng nhập, mặc định TẮT | Người dùng chủ động chọn mới được ghi nhớ | Mở trang → checkbox `#remember` **không** ở trạng thái tick. Nhãn hiển thị: `Remember me` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-05 | Lối vào chức năng Quên mật khẩu | Người quên mật khẩu tự khôi phục được | Trên trang đăng nhập có liên kết nhãn `Forgot Password?` trỏ tới `/admin/authentication/forgot_password`. Phần tử **dùng được**: `offsetParent` khác `null`, kích thước `119×17` px tại viewport `1600×750` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-06 | Biểu mẫu đăng nhập có chống giả mạo yêu cầu | Chặn tấn công CSRF | Biểu mẫu `POST` về chính `/admin/authentication`, kèm trường ẩn `name="csrf_token_name"` giá trị dạng **32 ký tự hex**. Automation **phải đọc token từ trang**, cấm gán cứng | 🟢 | — | Kiểm chứng thực tế |

### 3.2. Đăng nhập thành công & cấp phiên

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-07 | Đăng nhập đúng thì vào Bảng điều khiển | Đường vào hệ thống | Nhập email + mật khẩu hợp lệ → bấm `Login` → điểm dừng là `/admin/` và tiêu đề tab **chứa** `Dashboard` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-08 | Đăng nhập thành công cấp cookie phiên | Duy trì trạng thái đăng nhập | Sau khi đăng nhập, trình duyệt có cookie `sp_session` — hình thái: **độ dài 40**, `HttpOnly = true`, `Secure = true`, `SameSite = Lax`, có thời hạn (không phải cookie phiên trình duyệt) | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-09 | Gửi biểu mẫu theo mẫu POST→chuyển hướng→GET | Refresh sau đăng nhập không gửi lại biểu mẫu | Quan sát tầng mạng khi đăng nhập: `POST /admin/authentication` trả **303**, ngay sau đó `GET /admin/` trả **200** | 🟢 | — | Kiểm chứng thực tế |

### 3.3. Kiểm tra dữ liệu nhập

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-10 | Bỏ trống cả hai ô thì báo thiếu cả hai | Người dùng biết còn thiếu gì | Để trống cả 2 ô → bấm `Login` → trang hiện **đồng thời 2 thông báo**, nguyên văn: `The Password field is required.` và `The Email Address field is required.` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-11 | Bỏ trống riêng ô Email thì báo thiếu Email | | Nhập mật khẩu, để trống email → bấm `Login` → hiện `The Email Address field is required.` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-12 | Bỏ trống riêng ô Mật khẩu thì báo thiếu Mật khẩu | | Nhập email hợp lệ, để trống mật khẩu → bấm `Login` → hiện `The Password field is required.` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-13 | Ràng buộc bắt buộc nhập nằm ở máy chủ, không ở HTML | Quyết định cách viết automation: không dựa vào validation của trình duyệt để chặn | Đọc DOM: ô `#email` và `#password` **không** có thuộc tính `required`. Khi để trống cả hai, `form.checkValidity()` trả **`true`** → yêu cầu vẫn được gửi đi và máy chủ mới là nơi từ chối | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-14 | Email sai định dạng bị chặn ngay tại trình duyệt | Không gửi yêu cầu vô nghĩa lên máy chủ | Nhập `abc` vào ô email → bấm `Login` → `email.checkValidity()` trả **`false`**, **không** có yêu cầu mạng nào được gửi, điểm dừng vẫn là `/admin/authentication`.<br>⚠️ Chuỗi thông báo Chrome hiển thị (*"Please include an '@' in the email address…"*) do **trình duyệt** sinh — ghi để tham khảo, **CẤM dùng làm assertion** | 🟢 | — | Kiểm chứng thực tế |

### 3.4. Đăng nhập thất bại & chống dò danh tính

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-15 | Sai mật khẩu thì báo lỗi chung | | Nhập email **có thật** + mật khẩu sai → hiện đúng một thông báo, nguyên văn: `Invalid email or password`. Điểm dừng vẫn là `/admin/authentication` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-16 | Email không tồn tại báo **cùng** thông báo với sai mật khẩu | Không để kẻ tấn công dò ra email nào đã đăng ký | Nhập email **không tồn tại** + mật khẩu bất kỳ → thông báo nguyên văn **giống hệt** REQ-LOGIN-15: `Invalid email or password`. So hai ảnh evidence: nội dung và bố cục trùng khớp | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-17 | Máy chủ không trả lại giá trị email sau khi đăng nhập lỗi | Người dùng phải gõ lại email | Sau khi đăng nhập lỗi, đọc DOM: `#email` có `value` rỗng và **không** có thuộc tính `value` trong HTML trả về.<br>⚠️ **CẤM** assert "ô email rỗng trên màn hình" — trình duyệt có thể tự điền lại từ bộ nhớ tự động điền | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-18 | Không giới hạn số lần đăng nhập sai liên tiếp | Mô tả **hành vi hiện tại** của hệ thống, không phải hành vi mong muốn | Gửi **6 lần** đăng nhập sai liên tiếp cùng một email → cả 6 lần đều trả `Invalid email or password`, **không** xuất hiện thông báo khoá tài khoản, **không** có CAPTCHA, **không** thấy độ trễ tăng dần.<br>⚠️ Chỉ kiểm trên **email không tồn tại** — hành vi trên tài khoản **có thật** chưa xác minh, xem `AMB-LOGIN-01` | 🟢 | — | Kiểm chứng thực tế |

### 3.5. Ghi nhớ đăng nhập (Remember me)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-19 | Tick Remember me thì cấp thêm cookie ghi nhớ | *Tác tạo được tạo ra đúng hình thái* | Phép thử **trạng thái sạch**: (1) xoá toàn bộ cookie → (2) xác nhận danh sách cookie **rỗng** → (3) đăng nhập **có tick** `Remember me` → (4) xuất hiện cookie `autologin`. Hình thái: **độ dài 110**, `HttpOnly = false`, `Secure = true`, `SameSite = Lax`, thời hạn còn **khoảng 62 ngày** | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-20 | Cookie ghi nhớ tự đăng nhập lại khi mất cookie phiên | *Tác tạo dùng được đúng mục đích* — vế thứ hai của REQ-LOGIN-19 | (1) Đăng nhập có tick `Remember me` → (2) xoá **riêng** cookie `sp_session`, giữ `autologin` → (3) mở `/admin/clients` → **vào thẳng được**, điểm dừng là `/admin/clients`, tiêu đề `Customers`, **không** bị đẩy về trang đăng nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-21 | Không tick thì không cấp cookie ghi nhớ | | Phép thử **trạng thái sạch**: (1) xoá toàn bộ cookie → (2) xác nhận rỗng → (3) đăng nhập **không tick** → (4) danh sách cookie chỉ có `csrf_cookie_name` và `sp_session`, **không** có `autologin` | 🟢 | — | Kiểm chứng thực tế |

### 3.6. Đăng xuất

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-22 | Đăng xuất đưa người dùng về trang đăng nhập | | Bấm `Logout` trong menu hồ sơ → điểm dừng là `/admin/authentication`, tiêu đề tab **chứa** `Login` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-23 | Đăng xuất vô hiệu hoá phiên ở máy chủ | Đây mới là điều kiện an toàn thật sự | Sau khi đăng xuất, mở `/admin/clients` → bị đẩy về `/admin/authentication`. Kiểm ở **cùng ngữ cảnh trình duyệt**, không xoá cookie thủ công | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-24 | Cookie ghi nhớ còn lại trong trình duyệt sau đăng xuất nhưng hết tác dụng | Mô tả hành vi hiện tại — xem `RISK-LOGIN-02` | (1) Đăng nhập có tick `Remember me` → (2) đăng xuất → (3) cookie `autologin` **vẫn còn** trong danh sách cookie → (4) chỉ giữ `autologin`, xoá phần còn lại, mở `/admin/clients` → **vẫn bị đẩy về** trang đăng nhập → token đã bị vô hiệu phía máy chủ | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-25 | Yêu cầu đăng xuất trả mã chuyển hướng 307 | Ghi nhận vì lệch quy ước — xem `AMB-LOGIN-08` | Quan sát tầng mạng khi bấm `Logout`: `GET /admin/authentication/logout` trả **307**, sau đó `GET /admin/authentication` trả **200**.<br>⚠️ Chưa loại trừ được khả năng công cụ đo ghi nhận sai → **AC chỉ assert điểm dừng và nội dung hiển thị**, không assert con số 307 | 🟢 | — | Kiểm chứng thực tế |

### 3.7. Bảo vệ route & điều hướng theo trạng thái phiên

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-26 | Chưa đăng nhập thì mọi route quản trị bị chặn | | Ngữ cảnh trình duyệt **sạch cookie** → mở `/admin/clients` → điểm dừng là `/admin/authentication` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-27 | Hệ thống không ghi nhớ trang người dùng định vào | Mô tả hành vi hiện tại | Chưa đăng nhập, mở `/admin/clients/client/9091` → bị đẩy về `/admin/authentication` **không kèm tham số truy vấn nào**. Sau khi đăng nhập, điểm dừng là `/admin/`, **không** phải trang đã yêu cầu ban đầu | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-28 | Đã đăng nhập mà mở lại trang đăng nhập thì về Bảng điều khiển | | Đang đăng nhập → mở `/admin/authentication` → điểm dừng là `/admin/`, tiêu đề **chứa** `Dashboard` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-40 | Truy cập chức năng ngoài quyền thì hiện trang từ chối | | Đang đăng nhập bằng tài khoản không có quyền Setup → mở `/admin/staff` → điểm dừng là `/admin/access_denied`; trang hiện thông báo nổi nguyên văn `Access denied` và nội dung thân trang `Something went wrong. Try again` | 🟢 | — | Kiểm chứng thực tế |

### 3.8. Quên mật khẩu

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-29 | Màn hình Quên mật khẩu có route và biểu mẫu riêng | | Mở `/admin/authentication/forgot_password` → tiêu đề màn hình **`Forgot Password`**, một ô nhập nhãn `Email Address` (`#email`, `type="email"`), nút nhãn **`Confirm`**. Biểu mẫu `POST` về chính route đó kèm `csrf_token_name` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-30 | Email không tồn tại thì báo thẳng là không tìm thấy | Mô tả hành vi hiện tại — xem `RISK-LOGIN-04` | Nhập email đúng định dạng nhưng **không tồn tại** → bấm `Confirm` → hiện thông báo nguyên văn `Email not found` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-31 | Bỏ trống email cũng báo "không tìm thấy" | Không có thông báo bắt buộc nhập riêng — **khác** trang đăng nhập | Để trống ô email → bấm `Confirm` → hiện `Email not found`, **không** hiện `The Email Address field is required.` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-32 | Màn hình Quên mật khẩu giữ lại email đã nhập sau khi lỗi | **Khác** hành vi của trang đăng nhập (REQ-LOGIN-17) | Sau khi submit lỗi, ô `#email` **vẫn chứa** giá trị vừa nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-33 | Màn hình Quên mật khẩu không có lối quay lại đăng nhập | Mô tả hành vi hiện tại | Quét toàn bộ thẻ `<a>` trên trang → danh sách **rỗng**. Người dùng chỉ quay lại được bằng nút Back của trình duyệt hoặc gõ URL | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-34 | Gửi yêu cầu đặt lại mật khẩu cho email có thật | Chưa kiểm chứng — xem `AMB-LOGIN-10` | **Chưa xác minh.** Không submit email có thật vì thao tác này **gửi email đặt lại mật khẩu ra ngoài**. Cần: thông báo hiển thị sau khi gửi · nội dung email · hình thái liên kết đặt lại · hạn dùng của liên kết · màn hình đặt mật khẩu mới.<br>🔒 Khi kiểm chứng, ghi **hình thái** chuỗi ký trong liên kết, **không** ghi giá trị thật | ⚪ | — | Chưa kiểm chứng |

### 3.9. Giao thức & thuộc tính bảo mật quan sát được

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-35 | Trang đăng nhập mở được qua HTTP, không bị ép sang HTTPS | Mô tả hành vi hiện tại — xem `RISK-LOGIN-03` | Mở `http://crm.anhtester.com/admin/authentication` → trả **200**, giao thức của trang vẫn là `http:`, biểu mẫu hiển thị đầy đủ. **Không** có chuyển hướng sang `https:` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-36 | Phản hồi không kèm header ép dùng HTTPS | | Phản hồi của trang đăng nhập qua HTTP **không** có header `Strict-Transport-Security` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-37 | Thông tin đăng nhập vẫn gửi qua HTTPS kể cả khi trang mở bằng HTTP | Yếu tố giảm nhẹ của REQ-LOGIN-35 | Trên trang mở bằng `http:`, đọc `form.action` → là **đường dẫn tuyệt đối bắt đầu bằng `https://`** → dữ liệu biểu mẫu không đi qua kênh không mã hoá | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-38 | Cookie ghi nhớ đăng nhập đọc được bằng JavaScript | Mô tả hành vi hiện tại — xem `RISK-LOGIN-02` | Cookie `autologin` có `HttpOnly = false`. Kết hợp REQ-LOGIN-20 (cookie này **một mình** đủ để vào hệ thống) và thời hạn ~62 ngày | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-39 | Cookie phiên và cookie chống giả mạo đều đặt cờ bảo vệ | | Cookie `sp_session` và `csrf_cookie_name` đều có `Secure = true` và `SameSite = Lax`; riêng `sp_session` có `HttpOnly = true` | 🟢 | — | Kiểm chứng thực tế |

---

## 4. Đặc tả Trường Dữ liệu

### 4.1. Biểu mẫu đăng nhập — `/admin/authentication`

| Field (Label) | Loại UI | Required | Ràng buộc đọc được từ DOM | REQ liên quan | Ghi chú |
|---|---|---|---|---|---|
| Email Address | `input[type=email]` `#email` `name=email` | **Có — chỉ ở máy chủ** | **Không** có `required` · **không** có `maxlength` · **không** có `minlength` · **không** có `pattern` · **không** có `placeholder` · có `autofocus="1"` | REQ-LOGIN-02, 11, 13, 14 | Định dạng email do `type=email` của trình duyệt kiểm |
| Password | `input[type=password]` `#password` `name=password` | **Có — chỉ ở máy chủ** | **Không** có `required` · **không** có `maxlength` · **không** có `minlength` · **không** có `autocomplete` | REQ-LOGIN-03, 12, 13 | Không có nút hiện/ẩn mật khẩu |
| Remember me | `input[type=checkbox]` `#remember` `name=remember` | Không | Mặc định **không tick** · `value="estimate"` | REQ-LOGIN-04, 19, 21 | ⚠️ `value="estimate"` không liên quan nghiệp vụ ghi nhớ đăng nhập — xem `AMB-LOGIN-07` |
| *(ẩn)* | `input[type=hidden]` `name=csrf_token_name` | — | Giá trị dạng **32 ký tự hex** | REQ-LOGIN-06 | `display:none`, kích thước `0×0` — không phải điều khiển người dùng |
| Login | `button[type=submit]` | — | Kích thước `336×34` px tại viewport `1600×750` | REQ-LOGIN-07 | Nhãn: `Login` |

### 4.2. Biểu mẫu Quên mật khẩu — `/admin/authentication/forgot_password`

| Field (Label) | Loại UI | Required | Ràng buộc đọc được từ DOM | REQ liên quan | Ghi chú |
|---|---|---|---|---|---|
| Email Address | `input[type=email]` `#email` `name=email` | **Không có ràng buộc bắt buộc ở cả hai tầng** | **Không** có `required`; bỏ trống vẫn gửi được và máy chủ trả `Email not found` | REQ-LOGIN-29, 31, 32 | Giữ lại giá trị sau khi lỗi |
| *(ẩn)* | `input[type=hidden]` `name=csrf_token_name` | — | **32 ký tự hex** | REQ-LOGIN-29 | |
| Confirm | `button[type=submit]` | — | — | REQ-LOGIN-30 | Nhãn: `Confirm` |

### 4.3. Cookie hệ thống cấp — ghi hình thái, KHÔNG ghi giá trị

| Cookie | Khi nào có | Độ dài giá trị | HttpOnly | Secure | SameSite | Thời hạn | REQ |
|---|---|---|---|---|---|---|---|
| `csrf_cookie_name` | Ngay khi mở trang | 32 | ✅ | ✅ | Lax | Có hạn | REQ-LOGIN-39 |
| `sp_session` | Sau khi đăng nhập | 40 | ✅ | ✅ | Lax | Có hạn | REQ-LOGIN-08, 39 |
| `autologin` | **Chỉ khi** tick Remember me | 110 | ❌ | ✅ | Lax | ~62 ngày | REQ-LOGIN-19, 20, 38 |

---

## 5. Business Rules & Validation Messages

| REQ ID | Rule / Trigger | Thông báo mong đợi (nguyên văn) | Tầng kiểm |
|---|---|---|---|
| REQ-LOGIN-10 | Bỏ trống cả email và mật khẩu | `The Password field is required.` **và** `The Email Address field is required.` | Máy chủ |
| REQ-LOGIN-11 | Bỏ trống email | `The Email Address field is required.` | Máy chủ |
| REQ-LOGIN-12 | Bỏ trống mật khẩu | `The Password field is required.` | Máy chủ |
| REQ-LOGIN-14 | Email sai định dạng | *(Chrome)* `Please include an '@' in the email address. 'abc' is missing an '@'.` — **CẤM dùng làm assertion**, chỉ assert `checkValidity() === false` | Trình duyệt |
| REQ-LOGIN-15 | Email có thật + mật khẩu sai | `Invalid email or password` | Máy chủ |
| REQ-LOGIN-16 | Email không tồn tại | `Invalid email or password` *(giống hệt REQ-LOGIN-15)* | Máy chủ |
| REQ-LOGIN-30 | Quên mật khẩu — email không tồn tại | `Email not found` | Máy chủ |
| REQ-LOGIN-31 | Quên mật khẩu — bỏ trống email | `Email not found` | Máy chủ |
| REQ-LOGIN-40 | Truy cập chức năng ngoài quyền | Thông báo nổi `Access denied` + thân trang `Something went wrong. Try again` | Máy chủ |

### ⚠️ Ba điểm không nhất quán giữa hai biểu mẫu — đáng đưa vào TC

| # | Trang đăng nhập | Trang Quên mật khẩu |
|---|---|---|
| 1 | Bỏ trống → báo **thiếu trường** (`... field is required.`) | Bỏ trống → báo **không tìm thấy** (`Email not found`) |
| 2 | **Không** lộ email có tồn tại hay không | **Lộ thẳng** email không tồn tại |
| 3 | **Xoá** giá trị email sau khi lỗi | **Giữ lại** giá trị email sau khi lỗi |

---

## 6. Danh mục Evidence

Tất cả chụp ngày 19-09-2026 bằng **Google Chrome + Playwright**, viewport `1600×750`, **đã mở lại xác minh từng ảnh** trước khi ghi bảng này.

| Tệp ảnh | Màn hình / trạng thái | REQ làm bằng chứng |
|---|---|---|
| [`login_form_default_viewport.png`](evidence/login_form_default_viewport.png) | Trang đăng nhập, trạng thái mặc định, 2 ô rỗng, checkbox chưa tick | REQ-LOGIN-01 → 05 |
| [`login_form_empty_submit_viewport.png`](evidence/login_form_empty_submit_viewport.png) | Submit rỗng → **2 hộp thông báo đỏ** xếp trên biểu mẫu | REQ-LOGIN-10, 13 |
| [`login_form_empty_password_viewport.png`](evidence/login_form_empty_password_viewport.png) | Email hợp lệ, mật khẩu rỗng | REQ-LOGIN-12 |
| [`login_form_invalid_email_format_viewport.png`](evidence/login_form_invalid_email_format_viewport.png) | Email `abc` → bong bóng cảnh báo của Chrome | REQ-LOGIN-14 |
| [`login_form_wrong_password_viewport.png`](evidence/login_form_wrong_password_viewport.png) | Email thật + mật khẩu sai → `Invalid email or password`, ô email đã bị xoá | REQ-LOGIN-15, 17 |
| [`login_form_unknown_email_viewport.png`](evidence/login_form_unknown_email_viewport.png) | Email không tồn tại → **cùng** thông báo, **cùng** bố cục | REQ-LOGIN-16 |
| [`login_form_repeated_failures_viewport.png`](evidence/login_form_repeated_failures_viewport.png) | Sau **6 lần** sai liên tiếp — vẫn thông báo cũ, không khoá, không CAPTCHA | REQ-LOGIN-18 |
| [`login_success_dashboard_viewport.png`](evidence/login_success_dashboard_viewport.png) | Đăng nhập thành công → Dashboard | REQ-LOGIN-07 |
| [`login_when_already_authenticated_viewport.png`](evidence/login_when_already_authenticated_viewport.png) | Đang đăng nhập mà mở lại trang đăng nhập → Dashboard | REQ-LOGIN-28 |
| [`protected_route_redirect_viewport.png`](evidence/protected_route_redirect_viewport.png) | Chưa đăng nhập vào `/admin/clients` → trang đăng nhập | REQ-LOGIN-26, 27 |
| [`logout_result_viewport.png`](evidence/logout_result_viewport.png) | Sau khi bấm Logout → trang đăng nhập | REQ-LOGIN-22 |
| [`autologin_cookie_after_logout_viewport.png`](evidence/autologin_cookie_after_logout_viewport.png) | Còn cookie `autologin` sau đăng xuất nhưng vào route bảo vệ **vẫn bị chặn** | REQ-LOGIN-24 |
| [`remember_me_autologin_effect_viewport.png`](evidence/remember_me_autologin_effect_viewport.png) | Xoá `sp_session`, chỉ giữ `autologin` → **vào thẳng** `/admin/clients` | REQ-LOGIN-20 |
| [`forgot_password_default_viewport.png`](evidence/forgot_password_default_viewport.png) | Quên mật khẩu, trạng thái mặc định, nút `Confirm` | REQ-LOGIN-29, 33 |
| [`forgot_password_empty_submit_viewport.png`](evidence/forgot_password_empty_submit_viewport.png) | Submit rỗng → `Email not found` | REQ-LOGIN-31 |
| [`forgot_password_unknown_email_viewport.png`](evidence/forgot_password_unknown_email_viewport.png) | Email không tồn tại → `Email not found`, **email được giữ lại** trong ô | REQ-LOGIN-30, 32 |
| [`login_over_http_viewport.png`](evidence/login_over_http_viewport.png) | Trang đăng nhập mở qua **HTTP**, hiển thị đầy đủ | REQ-LOGIN-35, 36, 37 |
| [`access_denied_viewport.png`](evidence/access_denied_viewport.png) | `/admin/staff` → `/admin/access_denied` kèm 2 thông báo | REQ-LOGIN-40 |

**18 ảnh · phủ 39/40 REQ.** `REQ-LOGIN-34` không có ảnh vì **chưa kiểm chứng** (không gửi email thật) — đã đánh trạng thái ⚪.

Bốn REQ không có ảnh riêng mà dựa vào **số liệu đọc từ DOM/cookie** đã chép vào Acceptance Criteria: `REQ-LOGIN-06` (hình thái token) · `REQ-LOGIN-08`, `REQ-LOGIN-19`, `REQ-LOGIN-21`, `REQ-LOGIN-38`, `REQ-LOGIN-39` (hình thái cookie) · `REQ-LOGIN-09`, `REQ-LOGIN-25` (mã HTTP). Ảnh không thể hiện được những dữ kiện này.

---

## 7. Yêu cầu phi chức năng quan sát được

| Mục | Ghi nhận |
|---|---|
| Giao thức | HTTPS hoạt động, nhưng **không ép** và **không có HSTS** (REQ-LOGIN-35, 36) |
| Viewport khảo sát | `1600×750`. Mọi kết luận về kích thước phần tử chỉ đúng với viewport này — chưa recon ở viewport khác |
| Đa ngôn ngữ | Hệ thống hỗ trợ 28 ngôn ngữ (module `PROFILE`). Mọi thông báo ở mục 5 ghi theo **ngôn ngữ English** đang chọn → TC assert theo chuỗi sẽ gãy khi đổi ngôn ngữ |
| Khả dụng | Màn hình Quên mật khẩu không có lối quay lại (REQ-LOGIN-33); trang đăng nhập không nhớ trang người dùng định vào (REQ-LOGIN-27) |

← [Về tài liệu index của module](../requirements_login.md)
