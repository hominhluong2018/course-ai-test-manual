# Đặc tả Yêu cầu — Module Đăng nhập / Xác thực (`LOGIN`) · Nền tảng **Web**

> File nền tảng của module `LOGIN`. **Điểm vào là index** [../REQUIREMENTS_LOGIN_SUMMARY.md](../REQUIREMENTS_LOGIN_SUMMARY.md) — metadata, phạm vi, ma trận phân quyền/trạng thái, Story, AMB/RISK và Nhật ký thay đổi nằm ở đó.
>
> Đánh số mục **giữ nguyên** như tài liệu một file trước 19-09-2026 (mục 2 · 3 · 4 · 5 · 8 · 9 · 12) để các tham chiếu cũ kiểu "mục 3.2" không gãy. Mục 1, 6, 7, 10, 11, 13 ở index.

| Mục | Giá trị |
|---|---|
| **Nền tảng** | Web — khu quản trị `/admin` |
| **Trình duyệt khảo sát** | Google Chrome (Playwright MCP), headed, viewport `1600×750`. Mọi AC dựa trên thông báo mặc định của trình duyệt **chỉ đúng với Chrome** — xem `REQ-LOGIN-13` |
| **Tầng network** | Quan sát thụ động request do UI tự phát sinh — không gọi API trực tiếp |
| **REQ trong file** | `REQ-LOGIN-01` → `REQ-LOGIN-55` (55 — toàn bộ REQ của module, chưa có nền tảng thứ hai) |

---

## 2. Bản đồ phủ tài liệu

Đợt khởi tạo **không có tài liệu nào** — 35 REQ đầu sinh từ khảo sát UI thực tế và tầng network ngày 14-08-2026. Các REQ bổ sung sau đó đến từ quyết định PO và ticket:

| Vùng chức năng | Tài liệu phủ | Mức phủ | REQ liên quan |
|---|---|---|---|
| Toàn bộ module (trừ khoá tài khoản) | — | ⬜ Trắng — khảo sát UI | REQ-LOGIN-01 → 40 (trừ 15) · 42 → 44 |
| Khoá tài khoản khi sai mật khẩu nhiều lần | Ticket `CRM-LOGIN-101` (PO chốt 28-09-2026) — 6 dòng + PO trả lời `AMB-LOGIN-21`→`25` cùng ngày | 🟨 Một phần — tính năng **chưa deploy**, chưa đối chiếu được UI thật | REQ-LOGIN-15 (thu hẹp) · 41 · 45 → 55 |

---

## 3. Yêu cầu Chức năng

> Quy ước cột `Nguồn`: `Kiểm chứng thực tế` = đã tương tác và xác nhận trên UI · `UI thực tế` = chỉ quan sát, chưa tương tác · `DOM` = đọc bằng `browser_evaluate` · `Network` = quan sát thụ động request do UI phát sinh.

### 3.1. Giao diện trang đăng nhập (STORY-LOGIN-01)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-01 | Truy cập trang đăng nhập | Người dùng chưa đăng nhập mở `/admin/authentication` thì thấy biểu mẫu đăng nhập | Trang trả HTTP 200 · tiêu đề tab `Perfex CRM \| Anh Tester Demo - Login` · tiêu đề trang `Login` · `body` mang class `login_admin` | 🟢 | — | Kiểm chứng thực tế + DOM |
| REQ-LOGIN-02 | Thành phần biểu mẫu đăng nhập | Biểu mẫu gồm đúng 3 control nhập liệu, 1 nút gửi và 1 liên kết | Có `Email Address` (`input#email[type=email]`), `Password` (`input#password[type=password]`, hiển thị dạng chấm che), checkbox `Remember me` (`input#remember`), nút `Login` (`button[type=submit]`), liên kết `Forgot Password?`. Biểu mẫu `POST` về chính `/admin/authentication` | 🟢 | — | DOM |
| REQ-LOGIN-03 | Tự động đặt con trỏ vào ô Email | Mở trang thì con trỏ nằm sẵn ở ô Email Address | `input#email` mang thuộc tính `autofocus="1"` | 🟢 | — | DOM |
| REQ-LOGIN-04 | Logo dẫn về trang chủ | Bấm logo trên trang đăng nhập thì về trang chủ công khai | Liên kết bọc logo trỏ tới `https://crm.anhtester.com/` | 🟢 | — | DOM |
| REQ-LOGIN-05 | Không có CAPTCHA | Trang đăng nhập ở trạng thái mặc định không hiển thị CAPTCHA | Không tồn tại phần tử `.g-recaptcha`, `[class*=captcha]`, `iframe[src*=recaptcha]` | 🟡 | 18-08-2026 · rà soát | DOM |
| REQ-LOGIN-36 | Không có đăng nhập mạng xã hội | Trang đăng nhập không có nút đăng nhập bên thứ ba | Không tồn tại nút/liên kết đăng nhập qua Google, Facebook, Microsoft hay nhà cung cấp OAuth nào. Toàn trang chỉ có **1** `<form>` và **1** `<button[type=submit]>` | 🟢 | — | DOM |
| REQ-LOGIN-37 | Trang đăng nhập không nạp tệp JavaScript nào | Toàn bộ kiểm tra dữ liệu chạy ở máy chủ, trang không phụ thuộc script phía trình duyệt | Đếm được **0** `<script>` inline và **0** `<script src>` trên `/admin/authentication` | 🟢 | — | DOM |

### 3.2. Đăng nhập thành công (STORY-LOGIN-02)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-06 | Đăng nhập bằng thông tin hợp lệ | Nhập đúng email và mật khẩu thì vào được khu quản trị | `POST /admin/authentication` trả **303** với header `location: https://crm.anhtester.com/admin/` · trình duyệt dừng ở `/admin/` · tiêu đề tab **chứa** chuỗi `Dashboard` · `body.className` **chứa đủ** 4 token `app` `admin` `dashboard` `user-id-2` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế + Network |
| REQ-LOGIN-07 | Email không phân biệt hoa thường | Nhập email khác kiểu chữ vẫn đăng nhập được | Nhập `ADMIN@Example.COM` kèm mật khẩu đúng → đăng nhập thành công, chuyển tới `/admin/` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế |
| REQ-LOGIN-38 | Email bỏ qua khoảng trắng thừa đầu/cuối | Nhập email kèm khoảng trắng đầu/cuối vẫn đăng nhập được | Nhập `  admin@example.com  ` (2 khoảng trắng mỗi đầu) kèm mật khẩu đúng → đăng nhập thành công, chuyển tới `/admin/` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-08 | Ghi nhớ đăng nhập sinh cookie `autologin` | Tích **Remember me** trước khi đăng nhập thì hệ thống phát hành cookie ghi nhớ | Từ trạng thái **không có** cookie `autologin`: tích Remember me rồi đăng nhập → xuất hiện cookie `autologin` chứa chuỗi PHP-serialize đúng hình thái `a:2:{s:7:"user_id";s:1:"<id>";s:3:"key";s:16:"<16 ký tự hex>";}` — kiểm **hình thái và độ dài key**, KHÔNG kiểm giá trị cụ thể | 🟢 | 18-08-2026 · kiểm chứng lại | Kiểm chứng thực tế + DOM |
| REQ-LOGIN-09 | Không tích Ghi nhớ thì không phát hành cookie `autologin` | Đăng nhập khi **Remember me** bỏ trống thì hoàn toàn không có cookie ghi nhớ | **Phép thử trạng thái sạch (bắt buộc theo đúng thứ tự):** (1) xoá cookie `autologin` khỏi trình duyệt, (2) đăng xuất, (3) xác nhận `document.cookie` **rỗng** và `#remember.checked = false`, (4) đăng nhập bằng thông tin đúng → sau khi vào `/admin/`, cookie `autologin` **không tồn tại** (`document.cookie` rỗng) | 🟡 | 18-08-2026 · sửa phép thử | Kiểm chứng thực tế + DOM |

### 3.3. Kiểm tra dữ liệu đầu vào & đăng nhập thất bại (STORY-LOGIN-03)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-10 | Bỏ trống cả hai trường | Gửi biểu mẫu rỗng thì hiện đủ hai thông báo bắt buộc | Trang nạp lại và hiển thị **2** banner `.alert.alert-danger.text-center`, thứ tự trên xuống: `The Password field is required.` rồi `The Email Address field is required.` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-11 | Bỏ trống riêng Email | Nhập mật khẩu, để trống email thì báo thiếu email | Hiển thị **duy nhất 1** banner: `The Email Address field is required.` — không kiểm tra tiếp thông tin đăng nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-12 | Bỏ trống riêng Mật khẩu | Nhập email, để trống mật khẩu thì báo thiếu mật khẩu | Hiển thị **duy nhất 1** banner: `The Password field is required.` — không kiểm tra tiếp thông tin đăng nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-13 | Chặn email sai định dạng ngay tại trình duyệt | Email thiếu ký tự `@` thì trình duyệt chặn gửi biểu mẫu, không gọi tới máy chủ | Nhập `abc` → `input#email.checkValidity()` trả `false` · **không** phát sinh request POST · trang không nạp lại.<br>⚠️ **Nội dung `validationMessage` do TRÌNH DUYỆT sinh, không phải của ứng dụng — không dùng làm assertion cross-browser.** Trên **Google Chrome** (trình duyệt khảo sát): `Please include an '@' in the email address. 'abc' is missing an '@'.` · Firefox/Safari trả chuỗi khác. TC chỉ được assert `checkValidity() === false` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế + DOM + Network |
| REQ-LOGIN-14 | Thông báo khi sai thông tin đăng nhập | Sai email hoặc sai mật khẩu đều trả về một thông báo chung | Hiển thị đúng 1 banner `.alert.alert-danger`: `Invalid email or password` · người dùng vẫn ở `/admin/authentication` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-15 | Thông báo lỗi không tiết lộ email nào có thật — khi bộ đếm lần sai **chưa** tới ngưỡng khoá | Với **dưới 5** lần sai liên tiếp, email không tồn tại và email có thật nhưng sai mật khẩu phải cho cùng một thông báo | Gửi `notexist_20260814@auto.test` + mật khẩu bất kỳ → `Invalid email or password`. Gửi email có thật + mật khẩu sai → **cùng chuỗi** `Invalid email or password`. Không có khác biệt về nội dung, số lượng banner hay URL.<br>⚠️ **Phạm vi thu hẹp 28-09-2026:** chỉ đảm bảo khi email có thật **chưa** bị khoá. Khi tính năng khoá được deploy, email có thật sai từ lần 6 trở đi hiện thông báo khoá còn email không tồn tại thì không (`REQ-LOGIN-50`) — khác biệt **PO đã chấp nhận** (`AMB-LOGIN-24` ✅, `RISK-LOGIN-10`).<br>🔒 Email có thật dùng tài khoản `Project Manager`, không dùng `Admin` (ticket CRM-LOGIN-101 dòng 6) | 🟡 | 28-09-2026 · CRM-LOGIN-101 (`AMB-LOGIN-24`) | Kiểm chứng thực tế · phạm vi sửa theo PO 28-09-2026 |
| REQ-LOGIN-41 | Khoá tài khoản khi đăng nhập sai mật khẩu 5 lần liên tiếp | Cùng một email nhập sai mật khẩu **5 lần liên tiếp** (không có lần đăng nhập thành công xen giữa) thì tài khoản bị khoá. Lần sai thứ 5 vẫn hiện thông báo sai thông tin; khoá có hiệu lực **từ lần gửi kế tiếp** | **Tiền điều kiện trạng thái sạch:** tài khoản `Project Manager` chưa bị khoá và bộ đếm = 0 (vừa đăng nhập thành công rồi đăng xuất).<br>`AC1` Sai mật khẩu 4 lần liên tiếp → cả 4 lần hiện banner `Invalid email or password` · lần gửi thứ 5 bằng **mật khẩu đúng** → đăng nhập **thành công** (chưa chạm ngưỡng).<br>`AC2` Sai mật khẩu 5 lần liên tiếp → **cả 5 lần**, kể cả lần thứ 5, hiện banner `Invalid email or password` (`AMB-LOGIN-22` ✅) · lần gửi thứ 6 bằng mật khẩu đúng bị từ chối, hiện thông báo khoá (`REQ-LOGIN-45`).<br>Lần gửi bỏ trống mật khẩu hoặc sai CSRF **cũng tính** là một lần sai (`REQ-LOGIN-52`, `53` · `AMB-LOGIN-26` ✅) — TC của REQ này chỉ dùng lần gửi có đủ email + mật khẩu sai để dễ đếm.<br>🔒 Môi trường dùng chung: **chỉ** dùng tài khoản `Project Manager` cho lần sai, **cấm** `Admin` (ticket dòng 6).<br>📌 **Đảo ngược** quyết định PO 18-08-2026 (`AMB-LOGIN-02`). ⚪ **Chưa deploy** lên môi trường test (`AMB-LOGIN-21` ✅) — TC viết trước, để `skip` | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 1 + PO trả lời `AMB-LOGIN-22` 28-09-2026 — thay quyết định PO 18-08-2026 |
| REQ-LOGIN-45 | Thông báo khi tài khoản đang bị khoá | Trong thời gian bị khoá, trang đăng nhập hiển thị thông báo khoá riêng, khác thông báo sai thông tin | Tài khoản đang bị khoá (sau `REQ-LOGIN-41` AC2) → gửi biểu mẫu `/admin/authentication` bằng email đó → hiện banner **nguyên văn** `Your account is locked. Please try again in 15 minutes.` · người dùng vẫn ở `/admin/authentication`.<br>Chuỗi **cố định** `15 minutes`, **không** đếm ngược theo thời gian còn lại (`AMB-LOGIN-23` ✅) → assert khớp nguyên văn ở mọi thời điểm trong lúc khoá | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 2 + PO trả lời AMB 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-46 | Mật khẩu đúng vẫn bị từ chối trong thời gian khoá | Khoá chặn mọi lần đăng nhập của email đó, không phân biệt mật khẩu đúng hay sai | Tài khoản đang bị khoá → nhập **mật khẩu đúng** (🔒 `PM_PASSWORD` từ `.env`) → **không** tạo phiên: vẫn ở `/admin/authentication`, hiện thông báo khoá của `REQ-LOGIN-45`, mở `/admin/` bị đưa về trang đăng nhập | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 3 + PO trả lời AMB 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-47 | Tài khoản tự mở khoá sau 15 phút | Thời gian khoá là 15 phút; hết thời gian thì đăng nhập đúng được trở lại | Tài khoản bị khoá lúc `T` (lần sai thứ 5) → trước `T + 15 phút` đăng nhập đúng **bị từ chối** · sau `T + 15 phút` đăng nhập đúng **thành công**, vào `/admin/`.<br>Thử lại trong lúc khoá **không** gia hạn mốc `T` (`AMB-LOGIN-26` ✅) — TC được phép thử lại giữa chừng (VD phút thứ 10) mà mốc mở khoá vẫn là `T + 15 phút` | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 1 (thời lượng 15 phút) + PO trả lời AMB 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-48 | Đăng nhập thành công đặt lại bộ đếm lần sai về 0 | Lần đăng nhập thành công trước khi đủ 5 lần sai xoá toàn bộ số lần sai đã tích luỹ | **Trạng thái sạch:** bộ đếm = 0.<br>Sai 4 lần → đăng nhập đúng (thành công) → đăng xuất → sai tiếp 4 lần (tổng tích luỹ 8 nếu **không** đặt lại) → đăng nhập đúng **thành công**, **không** hiện thông báo khoá. Có khoá ở bước này = bộ đếm không được đặt lại → FAIL | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 4 + PO trả lời AMB 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-49 | Khoá áp theo email, không áp theo địa chỉ IP | Tài khoản bị khoá không làm ảnh hưởng tới email khác đăng nhập từ cùng máy / cùng IP | Email A (`Project Manager`) đang bị khoá → **cùng trình duyệt, cùng IP**, đăng nhập bằng email B = `admin@example.com` với **mật khẩu đúng** (🔒 `ADMIN_PASSWORD` từ `.env`) → **thành công**. Chỉ email A bị chặn.<br>🔒 Tài khoản `Admin` chỉ dùng cho **một lần đăng nhập đúng**, **không** bao giờ nhập sai mật khẩu → không tiêu hao bộ đếm, không trái dòng 6 của ticket (`AMB-LOGIN-25` ✅) | ⚪ | 28-09-2026 · CRM-LOGIN-101 | Ticket CRM-LOGIN-101 · dòng 5 + PO trả lời AMB 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) · email B theo `AMB-LOGIN-25` |
| REQ-LOGIN-50 | Email không tồn tại không bị khoá | Nhập sai nhiều lần với email không có trong hệ thống thì không bao giờ hiện thông báo khoá, vì không có tài khoản nào để khoá | Email `notexist_<timestamp>@auto.test` + mật khẩu bất kỳ, gửi **6 lần** liên tiếp → **cả 6 lần** đều hiện đúng 1 banner `Invalid email or password` · **không** lần nào hiện `Your account is locked…`.<br>⚠️ Hệ quả PO đã chấp nhận: từ lần gửi thứ 6, email có thật (bị khoá) và email không tồn tại cho **thông báo khác nhau** → phân biệt được email nào có tài khoản (`RISK-LOGIN-10`) | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-24` 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-51 | Hết thời gian khoá thì bộ đếm lần sai về 0 | Sau khi tự mở khoá, email được hưởng lại đủ ngưỡng 5 lần sai, không bị khoá lại ngay ở lần sai đầu tiên | Tài khoản vừa hết khoá (`REQ-LOGIN-47`) → sai mật khẩu **4 lần** → đăng nhập đúng → **thành công**, không hiện thông báo khoá. Bị khoá lại ở bước này = bộ đếm không được đặt lại sau khi mở khoá → FAIL | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-23` 28-09-2026 ("Reset về 0") — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-52 | Bỏ trống mật khẩu cũng tính là một lần đăng nhập sai | Lần gửi có email nhưng để trống mật khẩu được cộng vào bộ đếm lần sai của email đó, dù hệ thống dừng ở bước kiểm trường bắt buộc | **Trạng thái sạch:** tài khoản `Project Manager`, bộ đếm = 0.<br>Gửi email PM + **mật khẩu trống** 1 lần (banner `The Password field is required.` — `REQ-LOGIN-12`) → sai mật khẩu 4 lần → lần gửi thứ 6 bằng mật khẩu đúng **bị từ chối**, hiện thông báo khoá (`REQ-LOGIN-45`). Vào được = lần bỏ trống không được đếm → FAIL.<br>Chỉ tính khi **bấm `Login`** — để trống ô mà chưa bấm thì không tính (PO làm rõ 28-09-2026).<br>Bỏ trống **email** (hoặc bỏ trống cả hai ô) rồi bấm `Login` → **không** tính cho tài khoản nào, vì không có email để gắn bộ đếm (`AMB-LOGIN-28` ✅). Kiểm: sai mật khẩu PM 4 lần → bấm `Login` với email trống 1 lần → đăng nhập PM bằng mật khẩu đúng **thành công** | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-26` 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-53 | Gửi biểu mẫu với mã CSRF sai cũng tính là một lần đăng nhập sai | Lần gửi bị chặn vì mã CSRF không hợp lệ được cộng vào bộ đếm lần sai của email trong biểu mẫu | **Trạng thái sạch:** tài khoản `Project Manager`, bộ đếm = 0.<br>Gửi email PM + mật khẩu đúng với **mã CSRF giả** 1 lần (trang `419 Page Expired!` — `REQ-LOGIN-22`) → sai mật khẩu 4 lần → lần gửi kế tiếp bằng mật khẩu đúng + mã CSRF hợp lệ **bị từ chối**, hiện thông báo khoá. Vào được → FAIL | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-26` 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-54 | Cổng khách hàng cũng khoá tài khoản khi sai mật khẩu 5 lần liên tiếp | Cơ chế khoá áp cả cho tài khoản `Customer` ở cổng khách hàng, cùng ngưỡng, cùng thời lượng, cùng thông báo như khu `/admin` | **Điểm đăng nhập:** `/login` hoặc `/authentication/login` — cả hai trả `200` (đo `curl` 28-09-2026, không đăng nhập).<br>**Trạng thái sạch:** tài khoản `Customer` (🔒 `CUSTOMER_EMAIL` từ `.env`) chưa bị khoá, bộ đếm = 0.<br>Sai mật khẩu 5 lần liên tiếp ở cổng khách hàng → cả 5 lần hiện thông báo sai thông tin · lần gửi thứ 6 bằng mật khẩu đúng (🔒 `CUSTOMER_PASSWORD`) **bị từ chối**, hiện **nguyên văn** `Your account is locked. Please try again in 15 minutes.` · sau 15 phút đăng nhập đúng **thành công**, vào `/` (`AMB-LOGIN-27` ✅ — giống `/admin`).<br>⚠️ Nội dung banner *sai thông tin* ở cổng khách hàng **chưa khảo sát** — TC chỉ assert thông báo khoá.<br>🔒 Tài khoản `Customer` **dùng chung** cả đội — TC chạy **cuối lượt, một luồng, báo đội trước**, chờ đủ 15 phút (`RISK-LOGIN-03`) | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-26` (d) + `AMB-LOGIN-27` (a) 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-55 | Bộ đếm lần sai dùng chung giữa khu `/admin` và cổng khách hàng | Một email có một bộ đếm duy nhất. Tài khoản `Customer` bị từ chối ở `/admin` (luôn xảy ra — `REQ-LOGIN-43`) cũng tính là một lần sai, cộng dồn với lần sai ở cổng khách hàng | **Trạng thái sạch:** tài khoản `Customer`, bộ đếm = 0 (vừa đăng nhập thành công ở cổng khách hàng rồi đăng xuất).<br>Gửi biểu mẫu `/admin/authentication` bằng email + **mật khẩu đúng** của Customer **3 lần** (mỗi lần `Invalid email or password` — `REQ-LOGIN-43`) → sai mật khẩu ở cổng khách hàng **2 lần** → lần gửi kế tiếp ở cổng khách hàng bằng mật khẩu đúng **bị từ chối**, hiện thông báo khoá. Vào được = hai nơi đếm riêng → FAIL.<br>⚠️ Hệ quả: TC nào gửi tài khoản Customer vào `/admin` (`CRM_LOGIN_TC_027`, `TC_044` bước 4) đều **tiêu hao** bộ đếm của tài khoản Customer dùng chung.<br>**Chiều ngược lại cũng tính** (`AMB-LOGIN-28` ✅): tài khoản `Project Manager` gửi ở cổng khách hàng (luôn bị từ chối) được cộng vào bộ đếm của PM — PM bị từ chối ở cổng khách hàng 3 lần + sai mật khẩu ở `/admin` 2 lần → lần đăng nhập đúng kế tiếp ở `/admin` **bị từ chối**, hiện thông báo khoá | ⚪ | 28-09-2026 · CRM-LOGIN-101 | PO trả lời `AMB-LOGIN-27` (b) + câu hỏi phát sinh 28-09-2026 — chưa implement (`AMB-LOGIN-21` ✅) |
| REQ-LOGIN-16 | Máy chủ **phải** giữ lại email đã nhập sau khi đăng nhập thất bại | Sau lỗi đăng nhập, HTML do máy chủ trả về phải điền sẵn email người dùng vừa nhập | `input#email` trong HTML máy chủ trả về **phải** mang thuộc tính `value` bằng đúng email vừa gửi lên.<br>🐞 **Hiện trạng KHÔNG đạt:** `getAttribute('value')` = `null` — máy chủ không trả lại giá trị. PO xác nhận đây là **lỗi hệ thống** (`AMB-LOGIN-10` ✅). TC viết theo REQ này **sẽ FAIL** trên bản hiện tại — đó là kết quả đúng, phải mở bug chứ không sửa TC.<br>⚠️ Kiểm ở **tầng HTML máy chủ trả về**, không kiểm trên màn hình: trình duyệt tự điền lại giá trị cũ (nền xanh autofill ở `login_form_wrong_credentials_fullpage.png`) nên nhìn màn hình sẽ tưởng đã đạt | 🟡 | 18-08-2026 · PO xác nhận là lỗi (`AMB-LOGIN-10`) | Quyết định PO 18-08-2026 · hiện trạng đọc từ DOM |

### 3.4. Bảo vệ phiên & điều hướng (STORY-LOGIN-04)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-17 | Chặn URL nội bộ khi chưa đăng nhập | Mở thẳng một URL trong `/admin` khi chưa đăng nhập thì bị đưa về trang đăng nhập | `GET /admin/clients` khi chưa đăng nhập → bị chuyển hướng, trình duyệt dừng ở `/admin/authentication`.<br>⚠️ **Assert điểm dừng, KHÔNG assert mã chuyển hướng.** Đợt khảo sát ghi nhận **307** trên tab Network — giá trị bất thường cho chuyển hướng xác thực (thường là 302/303) và chưa loại trừ được khả năng là cách công cụ ghi nhận. Xem `AMB-LOGIN-17` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế + Network |
| REQ-LOGIN-18 | Không ghi nhớ URL đích sau khi chuyển hướng | Bị đưa về trang đăng nhập thì URL đích ban đầu không được giữ lại | URL sau chuyển hướng là `/admin/authentication` **trần** — không có tham số `?redirect=`, `?return_url=` hay tương đương. Đăng nhập xong vào Dashboard, không quay lại `/admin/clients` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-19 | Đã đăng nhập thì không vào lại trang đăng nhập | Người dùng đang có phiên mở `/admin/authentication` sẽ được đưa về Dashboard | Mở `/admin/authentication` khi đang đăng nhập → dừng ở `/admin/`, tiêu đề tab **chứa** `Dashboard` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế |
| REQ-LOGIN-20 | Đã đăng nhập thì không vào trang Quên mật khẩu | Người dùng đang có phiên mở trang Quên mật khẩu sẽ được đưa về Dashboard | Mở `/admin/authentication/forgot_password` khi đang đăng nhập → dừng ở `/admin/`, tiêu đề tab **chứa** `Dashboard` | 🟡 | 18-08-2026 · rà soát | Kiểm chứng thực tế |
| REQ-LOGIN-43 | Tài khoản khách hàng không đăng nhập được vào khu quản trị | Tài khoản thuộc cổng khách hàng bị từ chối ở `/admin`, kể cả khi thông tin đăng nhập đúng | Gửi biểu mẫu `/admin/authentication` bằng tài khoản khách hàng **hợp lệ** → hiển thị đúng **1** banner `Invalid email or password`, vẫn ở `/admin/authentication`, **không** tạo phiên.<br>✅ Đã loại trừ khả năng sai mật khẩu: cùng tài khoản đó đăng nhập **thành công** ở `/login` (cổng khách hàng).<br>✅ Đang có phiên cổng khách hàng hợp lệ mà mở `/admin/clients` vẫn bị đưa về `/admin/authentication`.<br>🔒 Thông báo **giống hệt** trường hợp email không tồn tại (`REQ-LOGIN-15`) — không lộ ra rằng email này có tài khoản ở hệ thống khác | 🟢 | — | Kiểm chứng thực tế 18-08-2026 (3 tài khoản) |
| REQ-LOGIN-42 | Phiên đăng nhập hết hạn sau 1 giờ | Phiên sống **1 giờ tính theo thời gian không hoạt động** — **mỗi thao tác gia hạn lại** mốc 1 giờ; hết hạn thì mọi URL nội bộ bị đưa về trang đăng nhập | **AC1 — hết hạn khi để yên:** sau khi đăng nhập, **không thao tác gì** quá **1 giờ** rồi mở một URL trong `/admin` → bị chuyển về `/admin/authentication` như trường hợp chưa đăng nhập (`REQ-LOGIN-17`).<br>**AC2 — thao tác thì được gia hạn:** đang dùng liên tục (mỗi lần cách nhau dưới 1 giờ) thì **không** bị đăng xuất, kể cả khi tổng thời gian từ lúc đăng nhập đã quá 1 giờ.<br>⏱️ TC của REQ này chạy **trên 1 giờ**, không xếp vào bộ smoke | 🟡 | 19-09-2026 · `AMB-LOGIN-19` ✅ | Quyết định PO 18-08-2026 — giải quyết `AMB-LOGIN-13` · Quyết định PO 19-09-2026 — giải quyết `AMB-LOGIN-19` |
| REQ-LOGIN-21 | Biểu mẫu đăng nhập mang mã chống CSRF | Mỗi lần nạp trang đăng nhập, biểu mẫu chứa một trường ẩn chống giả mạo yêu cầu | Tồn tại `input[type=hidden][name=csrf_token_name]` với giá trị 32 ký tự hex · body của `POST /admin/authentication` chứa `csrf_token_name=<token>&email=…&password=…` · mã **cố định trong một phiên** (gắn cookie `csrf_cookie_name`): nạp lại trang không đổi mã, phiên mới mới sinh mã khác — đo 19-09-2026 | 🟢 | 19-09-2026 · ✏️ bổ sung hành vi đã đo | DOM + Network |
| REQ-LOGIN-22 | Từ chối yêu cầu có mã CSRF sai | Gửi biểu mẫu với mã CSRF bị sửa thì bị chặn, không xử lý đăng nhập | Sửa `csrf_token_name` thành giá trị tuỳ ý rồi gửi → máy chủ trả **HTTP 403**, tiêu đề tab `Error`, nội dung nguyên văn: `419 Page Expired!` và `Sorry, the page has expired, return to previous page and refresh to continue.` | 🟢 | — | Kiểm chứng thực tế + Network |
| REQ-LOGIN-44 | Ép truy cập qua HTTPS | Mở trang đăng nhập bằng `http://` thì hệ thống phải tự chuyển sang `https://`, và trình duyệt được yêu cầu chỉ dùng HTTPS cho các lần sau | Mở `http://crm.anhtester.com/admin/authentication` → máy chủ trả chuyển hướng vĩnh viễn (`301`/`308`) kèm `Location: https://crm.anhtester.com/admin/authentication`; thanh địa chỉ dừng ở `https://…`, có biểu tượng kết nối an toàn.<br>Phản hồi HTTPS mang header `Strict-Transport-Security`.<br>⚠️ **Chấm theo phản hồi của máy chủ**, không chấm theo thanh địa chỉ của Chrome — Chrome ≥ 115 tự nâng `http://` lên `https://` (HTTPS-Upgrades) nên nhìn thanh địa chỉ có thể tưởng đã đạt.<br>🐞 **Hệ thống chưa đạt:** đo 19-09-2026 bằng `curl -I` — `http://` trả `200`, không `Location`, không HSTS → bug `BUG_login_1785678750_TC039` | 🟢 | 19-09-2026 · `AMB-LOGIN-20` ✅ | Quyết định PO 19-09-2026 — giải quyết `AMB-LOGIN-20` · đo bằng `curl -I` |

### 3.5. Quên mật khẩu (STORY-LOGIN-05)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-23 | Truy cập trang Quên mật khẩu | Bấm `Forgot Password?` trên trang đăng nhập thì mở biểu mẫu khôi phục | Liên kết trỏ tới `/admin/authentication/forgot_password` · trang có tiêu đề `Forgot Password` · gồm đúng 1 ô `Email Address` (`input#email[type=email]`) và nút `Confirm` · `POST` về chính URL đó · có trường ẩn `csrf_token_name` | 🟢 | — | Kiểm chứng thực tế + DOM |
| REQ-LOGIN-24 | Trang Quên mật khẩu không có lối quay lại đăng nhập | Trên trang Quên mật khẩu, liên kết duy nhất là logo về trang chủ | Toàn trang chỉ có **1** thẻ `<a>`, trỏ tới `https://crm.anhtester.com/`. Không có liên kết nào về `/admin/authentication` | 🟢 | — | DOM |
| REQ-LOGIN-25 | Gửi biểu mẫu Quên mật khẩu khi bỏ trống email | Bỏ trống ô email rồi bấm Confirm thì hệ thống **phải** báo thiếu trường bắt buộc | Hiển thị banner báo **trường bắt buộc** cho ô Email — nhất quán với trang đăng nhập (`The Email Address field is required.` ở `REQ-LOGIN-11`).<br>🐞 **Hiện trạng KHÔNG đạt:** hệ thống trả `Email not found`, tức báo sai bản chất lỗi và làm người dùng tưởng email của mình không tồn tại. PO xác nhận là **thiếu validate trường bắt buộc** (`AMB-LOGIN-05` ✅). TC viết theo REQ này **sẽ FAIL** trên bản hiện tại — phải mở bug chứ không sửa TC | 🟡 | 18-08-2026 · PO xác nhận là lỗi (`AMB-LOGIN-05`) | Quyết định PO 18-08-2026 · hiện trạng kiểm chứng thực tế |
| REQ-LOGIN-26 | Email không tồn tại trong hệ thống | Nhập email không có trong hệ thống thì báo không tìm thấy | Nhập `notexist_20260814@auto.test` → banner `Email not found` · vẫn ở `/admin/authentication/forgot_password` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-27 | Email tồn tại thì gửi liên kết đặt lại mật khẩu | Nhập email có thật thì hệ thống gửi email chứa liên kết đặt lại | ❔ **Chưa kiểm chứng và sẽ KHÔNG kiểm chứng** — `AMB-LOGIN-04` ⏭️ bỏ qua theo quyết định 18-08-2026. Yêu cầu này ra **ngoài phạm vi kiểm thử**, không viết TC. Giữ dòng để không mất dấu vết | ⚪ | 18-08-2026 · ra ngoài phạm vi | Suy diễn từ sự tồn tại của route `reset_password` |
| REQ-LOGIN-28 | Liên kết đặt lại mật khẩu sai/hết hạn | Mở liên kết đặt lại với mã khoá không hợp lệ | `GET /admin/authentication/reset_password/<id>/<mã khoá sai>` → **HTTP 500**, thân phản hồi rỗng, người dùng thấy trang lỗi mặc định của trình duyệt. **Không** có trang thông báo thân thiện.<br>✅ **Đây là hành vi được chấp nhận**, không phải bug cần mở — `AMB-LOGIN-03` ⏭️ bỏ qua 18-08-2026. TC ghi nhận đúng hiện trạng (HTTP 500 + thân rỗng), **không** kỳ vọng trang thông báo thân thiện | 🟡 | 18-08-2026 · chốt chấp nhận hiện trạng (`AMB-LOGIN-03`) | Kiểm chứng thực tế + Network |

### 3.6. Đăng xuất (STORY-LOGIN-06)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-29 | Mỗi viewport có **một** lối đăng xuất dùng được | Desktop đăng xuất qua menu ảnh đại diện; mobile đăng xuất qua menu điều hướng thu gọn | DOM có **2** phần tử `li.header-logout`, mỗi cái phục vụ **một** viewport — không bao giờ dùng được cả hai cùng lúc:<br>• **Desktop** (`1600×750`, ✅ đã kiểm chứng) — dùng được cái trong `.dropdown-menu` của `li.header-user-profile`: hộp `160×64`, `offsetParent ≠ null`. Cái còn lại hộp `0×0`, `offsetParent === null` vì tổ tiên `div.mobile-navbar.collapse` + `div.mobile-menu` đều `display: none`<br>• **Mobile** (⚠️ **chưa recon**, nguồn là quyết định PO `AMB-LOGIN-16` ✅) — dùng được cái trong `ul.nav.navbar-nav` thuộc `div.mobile-navbar`<br>⚠️ Locator desktop phải là `.dropdown-menu > li.header-logout` (**con trực tiếp**): menu có **32** `<li>` lồng trong submenu Language, nên `.dropdown-menu li:last-child` trỏ nhầm sang một mục ngôn ngữ<br>🚧 **Muốn viết TC cho nhánh mobile thì phải recon ở viewport mobile trước** — hiện chưa có số liệu DOM nào cho nhánh đó | 🟡 | 18-08-2026 · bổ sung nhánh mobile (`AMB-LOGIN-16`) | Desktop: kiểm chứng thực tế + DOM · Mobile: quyết định PO 18-08-2026 |
| REQ-LOGIN-30 | Cảnh báo khi còn bộ đếm giờ đang chạy | Đăng xuất trong lúc còn timer công việc đang chạy thì phải xác nhận | Hàm `logout()` kiểm tra `$(".started-timers-top").find("li.timer").length > 0`; nếu có thì mở popup với nội dung nguyên văn `Started tasks timers found!` / `Are you sure you want to logout without stopping the timers?` kèm nút `Logout` trỏ tới `/admin/authentication/logout`, và **không** đăng xuất ngay.<br>🔗 **Kiểm chứng đầu-cuối thuộc module `TASK`** (`AMB-LOGIN-14` ⏭️ 18-08-2026) — module `TASK` sở hữu tính năng timer. Bằng chứng hiện tại dừng ở mức đọc mã nguồn; khi recon `TASK` phải chạy thật rồi **cập nhật ngược** REQ này | 🟡 | 18-08-2026 · chuyển việc kiểm chứng sang `TASK` | DOM (mã nguồn hàm `logout()` + template `#timers-logout-template-warning`) |
| REQ-LOGIN-31 | Đăng xuất ngay khi không có bộ đếm giờ | Không có timer nào chạy thì bấm Logout là đi thẳng | Hàm `logout()` gán `window.location.href = admin_url + "authentication/logout"` mà không hỏi lại | 🟢 | — | DOM (mã nguồn hàm `logout()`) |
| REQ-LOGIN-32 | Kết thúc phiên và về trang đăng nhập | Gọi điểm cuối đăng xuất thì phiên chấm dứt | `GET /admin/authentication/logout` → chuyển hướng, trình duyệt dừng ở `/admin/authentication` · không hiển thị banner thông báo nào | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-33 | URL nội bộ bị chặn lại sau khi đăng xuất | Sau khi đăng xuất, phiên cũ không dùng lại được | Đăng xuất rồi mở `/admin/clients` → bị đưa về `/admin/authentication`, mặc dù cookie `autologin` vẫn còn trong trình duyệt | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-34 | Cookie `autologin` không bị xoá khỏi trình duyệt khi đăng xuất | Sau khi đăng xuất, cookie ghi nhớ vẫn nằm lại phía trình duyệt | `document.cookie` sau khi đăng xuất **vẫn** chứa `autologin` với nguyên giá trị trước đó. Cookie đã mất hiệu lực ở phía máy chủ (chứng minh bởi REQ-LOGIN-33) nhưng không được xoá phía trình duyệt.<br>🚫 **Ra ngoài phạm vi viết TC** — `AMB-LOGIN-06` ⏭️ bỏ qua 18-08-2026. Giữ dòng làm ghi nhận hiện trạng | 🟡 | 18-08-2026 · ngoài phạm vi TC (`AMB-LOGIN-06`) | Kiểm chứng thực tế + DOM |
| REQ-LOGIN-35 | Cookie `autologin` đọc được bằng JavaScript | Cookie ghi nhớ không được gắn cờ chống truy cập từ script | `document.cookie` trả về đầy đủ giá trị `autologin` → cookie **không** có cờ `HttpOnly`. (Cookie phiên của ứng dụng không xuất hiện trong `document.cookie` → có `HttpOnly`.)<br>🚫 **Ra ngoài phạm vi viết TC** — `AMB-LOGIN-07` ⏭️ bỏ qua 18-08-2026. Giữ dòng làm ghi nhận hiện trạng cho `RISK-LOGIN-02` (đã chấp nhận) | 🟡 | 18-08-2026 · ngoài phạm vi TC (`AMB-LOGIN-07`) | DOM |
| REQ-LOGIN-39 | Cookie `autologin` không tự đăng nhập lại sau khi đăng xuất chủ động | Đăng xuất rồi thì cookie ghi nhớ còn lại cũng không đưa người dùng vào khu quản trị | Đăng nhập có tích Remember me → xác nhận có cookie `autologin` → gọi `/admin/authentication/logout` → xác nhận cookie `autologin` **vẫn còn** → mở `/admin/` → bị đưa về `/admin/authentication`, **không** tự đăng nhập | 🟢 | — | Kiểm chứng thực tế + DOM |
| REQ-LOGIN-40 | Cookie `autologin` khi phiên hết hạn tự nhiên | Phiên tự hết hạn (không do đăng xuất) thì cookie ghi nhớ có đưa người dùng vào thẳng khu quản trị không | ❌ **PO xác nhận tính năng Ghi nhớ đăng nhập KHÔNG hoạt động** (`AMB-LOGIN-15` ⏭️ 18-08-2026). Ra **ngoài phạm vi kiểm thử** — không viết TC. Giữ dòng để không mất dấu vết, và vì checkbox vẫn hiển thị trên UI (`RISK-LOGIN-08`).<br>⚠️ Ba REQ đã kiểm chứng thật vẫn giữ nguyên hiệu lực: `REQ-08` (cookie **được cấp** khi tích) · `REQ-09` (**không** cấp khi bỏ trống) · `REQ-39` (**không** tự đăng nhập sau đăng xuất chủ động) | ⚪ | 18-08-2026 · ra ngoài phạm vi | Quyết định PO 18-08-2026 |

---

## 4. Đặc tả Trường Dữ liệu

### 4.1. Biểu mẫu Đăng nhập — `/admin/authentication`

| Field (Label) | Loại UI | Tên field gửi lên | Required | Ràng buộc (min/max/format/default) | REQ liên quan | Ghi chú |
|---|---|---|---|---|---|---|
| Email Address | `input[type=email]` `#email` | `email` | **Có** — kiểm ở máy chủ | Không có `required`, `maxlength`, `minlength`, `pattern` trong HTML. Định dạng do `type=email` của trình duyệt kiểm. Mặc định rỗng. Không phân biệt hoa/thường, bỏ qua khoảng trắng đầu/cuối | REQ-LOGIN-02, 07, 11, 13, 16, 38 | Có `autofocus="1"`. **Không** có `autocomplete` → trình duyệt vẫn tự điền theo suy đoán riêng, đây là lý do ô Email trông như được giữ lại sau lỗi (REQ-LOGIN-16) |
| Password | `input[type=password]` `#password` | `password` | **Có** — kiểm ở máy chủ | Không có `required`, `maxlength`, `minlength`, `pattern`. Không ràng buộc độ mạnh ở màn hình đăng nhập. Mặc định rỗng | REQ-LOGIN-02, 12 | Hiển thị che ký tự. **Không** có nút hiện/ẩn mật khẩu. **Không** có `autocomplete` |
| Remember me | `input[type=checkbox]` `#remember` | `remember` | Không | Mặc định **không tích** (`checked = false`), `disabled = false`. Thuộc tính `value="estimate"` — giá trị gửi lên khi tích là chuỗi `estimate` | REQ-LOGIN-02, 08, 09 | Giá trị `estimate` không mang nghĩa nghiệp vụ nào ở màn hình này — xem `AMB-LOGIN-11` |
| (ẩn) csrf_token_name | `input[type=hidden]` | `csrf_token_name` | Có — hệ thống tự điền | Chuỗi 32 ký tự hex, đổi theo phiên | REQ-LOGIN-21, 22 | Bắt buộc với **mọi** POST |
| Login | `button[type=submit]` | — | — | Luôn ở trạng thái bật (`disabled = false`) kể cả khi biểu mẫu rỗng | REQ-LOGIN-02, 10 | Class `btn btn-primary btn-block` |

### 4.2. Biểu mẫu Quên mật khẩu — `/admin/authentication/forgot_password`

| Field (Label) | Loại UI | Tên field gửi lên | Required | Ràng buộc | REQ liên quan | Ghi chú |
|---|---|---|---|---|---|---|
| Email Address | `input[type=email]` `#email` | `email` | Không kiểm "bắt buộc" — bỏ trống vẫn gửi được và rơi vào nhánh `Email not found` | Không có `required`, `maxlength`, `pattern`. Có `value=""` sẵn trong HTML. Không có `autofocus` | REQ-LOGIN-23, 25, 26 | Khác biểu mẫu đăng nhập ở chỗ **không** có thông báo trường bắt buộc |
| (ẩn) csrf_token_name | `input[type=hidden]` | `csrf_token_name` | Có — hệ thống tự điền | Chuỗi 32 ký tự hex | REQ-LOGIN-23 | — |
| Confirm | `button[type=submit]` | — | — | Luôn bật | REQ-LOGIN-23 | Class `btn btn-primary btn-block` |

---

## 5. Business Rules & Validation Messages

Toàn bộ thông báo dưới đây **ghi nguyên văn từ UI thực tế**, hiển thị bằng tiếng Anh trong khung `.alert.alert-danger.text-center` đặt phía trên các trường nhập.

| REQ ID | Rule / Trigger | Thông báo lỗi mong đợi (nguyên văn) |
|---|---|---|
| REQ-LOGIN-10 | Đăng nhập: bỏ trống cả Email và Password | `The Password field is required.` (banner 1) và `The Email Address field is required.` (banner 2) |
| REQ-LOGIN-11 | Đăng nhập: bỏ trống Email, có Password | `The Email Address field is required.` |
| REQ-LOGIN-12 | Đăng nhập: có Email, bỏ trống Password | `The Password field is required.` — lần gửi này **tính** vào bộ đếm khoá của email (`REQ-LOGIN-52`) |
| REQ-LOGIN-13 | Đăng nhập: email thiếu ký tự `@` | ⚠️ **Không phải thông báo của ứng dụng** — tooltip do trình duyệt sinh, đổi theo trình duyệt và ngôn ngữ hệ điều hành. Trên **Chrome**: `Please include an '@' in the email address. 'abc' is missing an '@'.` **Cấm dùng làm assertion** — chỉ assert `checkValidity() === false` |
| REQ-LOGIN-14 | Đăng nhập: email không tồn tại, hoặc mật khẩu sai | `Invalid email or password` — kể cả **lần sai thứ 5** (`REQ-LOGIN-41`) và mọi lần sai với email không tồn tại (`REQ-LOGIN-50`) |
| REQ-LOGIN-45 | Đăng nhập: email đang bị khoá do sai mật khẩu 5 lần liên tiếp (`REQ-LOGIN-41`) — mật khẩu đúng hay sai đều vậy (`REQ-LOGIN-46`) | `Your account is locked. Please try again in 15 minutes.` — chuỗi **cố định** (`AMB-LOGIN-23` ✅). ⚠️ Nguồn **ticket CRM-LOGIN-101**, tính năng **chưa deploy** (`AMB-LOGIN-21` ✅) — chưa quan sát trên UI thật. Email **không tồn tại** không bao giờ nhận thông báo này (`REQ-LOGIN-50`) |
| REQ-LOGIN-22 | Đăng nhập: mã CSRF không hợp lệ | Trang lỗi HTTP 403 — `419 Page Expired!` / `Sorry, the page has expired, return to previous page and refresh to continue.` |
| REQ-LOGIN-25 | Quên mật khẩu: bỏ trống email | `Email not found` |
| REQ-LOGIN-26 | Quên mật khẩu: email không tồn tại | `Email not found` |
| REQ-LOGIN-28 | Đặt lại mật khẩu: mã khoá sai hoặc hết hạn | Không có thông báo — máy chủ trả HTTP 500 với thân phản hồi rỗng |
| REQ-LOGIN-30 | Đăng xuất khi còn timer đang chạy | `Started tasks timers found!` / `Are you sure you want to logout without stopping the timers?` |

**Quan sát chéo:** thông báo của trang Đăng nhập (`The … field is required.`) và trang Quên mật khẩu (`Email not found` cho cùng tình huống bỏ trống) **không nhất quán** — xem `AMB-LOGIN-05`.

---

## 8. Luồng xử lý chính

### 8.1. Đăng nhập thành công

```
1. Mở /admin/authentication          → biểu mẫu hiện ra, con trỏ ở ô Email
2. Nhập Email Address hợp lệ
3. Nhập Password đúng
4. (tuỳ chọn) Tích Remember me       → sẽ nhận cookie autologin
5. Bấm Login                          → POST /admin/authentication (kèm csrf_token_name)
6. Máy chủ trả 303 + location /admin/ → dừng ở Dashboard
```

### 8.2. Đăng nhập thất bại

```
1–3. Như trên nhưng sai email hoặc sai mật khẩu
4. Bấm Login                          → POST /admin/authentication
5. Trang nạp lại, banner "Invalid email or password"
6. Ô Email trả về rỗng                → người dùng phải gõ lại từ đầu
```

### 8.2a. Khoá tài khoản khi sai mật khẩu nhiều lần (CRM-LOGIN-101 — ⚪ chưa deploy)

```
1. Bộ đếm lần sai của email = 0 (sau lần đăng nhập thành công gần nhất)
2. Sai mật khẩu lần 1 → 4               → banner "Invalid email or password", bộ đếm tăng dần
   └─ Đăng nhập đúng trước lần 5        → thành công, bộ đếm về 0            (REQ-LOGIN-48)
   (bỏ trống mật khẩu, sai CSRF cũng tính 1 lần — REQ-LOGIN-52, 53)
3. Sai mật khẩu lần 5                   → vẫn banner "Invalid email or password",
                                          tài khoản BỊ KHOÁ 15 phút từ lần gửi kế tiếp (REQ-LOGIN-41)
   └─ Email không tồn tại               → không bao giờ bị khoá             (REQ-LOGIN-50)
4. Đăng nhập trong lúc khoá (đúng/sai)  → "Your account is locked. Please try again in 15 minutes."
                                                                               (REQ-LOGIN-45, 46)
   └─ Email khác, cùng máy/IP           → đăng nhập bình thường              (REQ-LOGIN-49)
   └─ Thử lại trong lúc khoá            → KHÔNG gia hạn 15 phút              (REQ-LOGIN-47)
5. Hết 15 phút                          → đăng nhập đúng thành công, bộ đếm về 0 (REQ-LOGIN-47, 51)

Cổng khách hàng (/login · /authentication/login) áp CÙNG cơ chế: 5 lần · 15 phút · cùng thông báo (REQ-LOGIN-54)
Bộ đếm DÙNG CHUNG theo email: Customer bị từ chối ở /admin cũng +1                  (REQ-LOGIN-55)
```

### 8.3. Quên mật khẩu

```
1. Ở trang đăng nhập, bấm "Forgot Password?"
2. Mở /admin/authentication/forgot_password
3. Nhập email → bấm Confirm
4a. Email không có trong hệ thống      → banner "Email not found"
4b. Email có thật                      → ⚪ chưa kiểm chứng (AMB-LOGIN-04)
5.  Không có lối quay lại trang đăng nhập — phải sửa URL thủ công (REQ-LOGIN-24)
```

### 8.4. Đăng xuất

```
1. Bấm Logout (thanh điều hướng hoặc menu ảnh đại diện) → gọi hàm logout()
2a. Có timer công việc đang chạy       → popup "Started tasks timers found!" → cần bấm Logout lần nữa
2b. Không có timer                     → đi thẳng bước 3
3. GET /admin/authentication/logout    → phiên kết thúc
4. Dừng ở /admin/authentication        → URL nội bộ không truy cập được nữa
```

---

## 9. Yêu cầu Phi chức năng (quan sát được)

| Mục | Ghi nhận | REQ / AMB liên quan |
|---|---|---|
| Giao thức | Toàn bộ qua HTTPS (`https://crm.anhtester.com`). **Bắt buộc** ép chuyển từ HTTP và có HSTS — hiện chưa đạt | REQ-LOGIN-44, AMB-LOGIN-20 |
| Tải trang đăng nhập | Trang **không nạp bất kỳ tệp JavaScript nào** — 0 script inline, 0 script ngoài. Toàn bộ kiểm tra dữ liệu chạy ở máy chủ | REQ-LOGIN-37, 13 |
| Máy chủ | `server: LiteSpeed`, ứng dụng server-render (CodeIgniter) | — |
| Không lưu đệm trang sau đăng nhập | Phản hồi mang `cache-control: no-store, no-cache, must-revalidate` và `pragma: no-cache` — bấm Back sau khi đăng xuất không xem lại được nội dung đã tải. ⚠️ Đây là chống **lưu đệm**, **không** phải chống tấn công phát lại (replay) — hệ thống không có nonce/timestamp nào được quan sát | — |
| Ngôn ngữ | Trang đăng nhập chỉ có tiếng Anh; bộ chọn ngôn ngữ (26 ngôn ngữ) chỉ xuất hiện **sau** khi đăng nhập, thuộc module `PROF` | — |
| Khả năng truy cập | Ba trường đều có `<label for>` khớp `id` — đọc được bằng trình đọc màn hình | REQ-LOGIN-02 |
| Chống dò tài khoản | Trang đăng nhập đạt (một thông báo chung); trang Quên mật khẩu **chưa rõ** | REQ-LOGIN-15, AMB-LOGIN-04 |
| Chống thử vét cạn | Không quan sát thấy CAPTCHA, không thấy dấu hiệu giới hạn tần suất | RISK-LOGIN-01, AMB-LOGIN-02 |
| Cookie | Cookie phiên có `HttpOnly`; cookie `autologin` **không có** | REQ-LOGIN-35, RISK-LOGIN-02 |

---

## 12. Danh mục Evidence

| Tệp | Màn hình | Trạng thái | REQ làm bằng chứng |
|---|---|---|---|
| [evidence/login_form_default_fullpage.png](evidence/login_form_default_fullpage.png) | Đăng nhập | Mặc định, các trường rỗng | REQ-LOGIN-01, 02, 04, 05 |
| [evidence/login_form_filled_remember_checked_fullpage.png](evidence/login_form_filled_remember_checked_fullpage.png) | Đăng nhập | Đã nhập đủ, **Remember me đang tích** | REQ-LOGIN-02, 08 |
| [evidence/login_form_empty_submit_error_fullpage.png](evidence/login_form_empty_submit_error_fullpage.png) | Đăng nhập | Gửi biểu mẫu rỗng — 2 banner lỗi | REQ-LOGIN-10 |
| [evidence/login_form_wrong_credentials_fullpage.png](evidence/login_form_wrong_credentials_fullpage.png) | Đăng nhập | Sai thông tin — banner `Invalid email or password` | REQ-LOGIN-14, 15 |
| [evidence/login_csrf_invalid_403_fullpage.png](evidence/login_csrf_invalid_403_fullpage.png) | Đăng nhập | Mã CSRF bị sửa — trang lỗi 403 | REQ-LOGIN-22 |
| [evidence/login_success_dashboard_viewport.png](evidence/login_success_dashboard_viewport.png) | Dashboard | Ngay sau khi đăng nhập thành công, dropdown đóng | REQ-LOGIN-06 |
| [evidence/logout_menu_open_viewport.png](evidence/logout_menu_open_viewport.png) | Dashboard | Menu ảnh đại diện **đang mở** — thấy Logout là mục cuối, và thanh đầu trang **không** có lối Logout thứ hai | REQ-LOGIN-29 |
| [evidence/forgot_password_form_default_fullpage.png](evidence/forgot_password_form_default_fullpage.png) | Quên mật khẩu | Mặc định | REQ-LOGIN-23, 24 |
| [evidence/forgot_password_empty_submit_error_fullpage.png](evidence/forgot_password_empty_submit_error_fullpage.png) | Quên mật khẩu | Gửi khi bỏ trống — banner `Email not found` | REQ-LOGIN-25 |

> ⚠️ **Đọc ảnh cho đúng:** ở các ảnh chụp trang đăng nhập, ô Email/Password đôi khi có sẵn nội dung với nền xanh nhạt — đó là **trình duyệt tự điền**, không phải máy chủ trả lại giá trị. Bằng chứng: `input#email` trong HTML trả về không hề có thuộc tính `value` (REQ-LOGIN-16).
>
> 📸 **Vì sao 2 ảnh Dashboard chụp viewport chứ không full-page:** ảnh full-page của Dashboard kéo theo bảng công việc, nhật ký hoạt động và tên khách hàng thật — dữ liệu không liên quan gì tới REQ mà chúng làm bằng chứng. Đối tượng cần chứng minh (thanh đầu trang, dropdown, tiêu đề) nằm trọn trong viewport, nên chụp viewport là **đủ và đúng phạm vi**. Xem quy tắc chung ở `skills-requirements-analyzer` mục 7.2.1.

**Dữ kiện đọc từ DOM, không thể hiện được trên ảnh** (ghi thẳng vào Acceptance Criteria ở mục 3):

| Dữ kiện | Giá trị đọc được | REQ |
|---|---|---|
| Số tệp script trên trang đăng nhập | **0** inline · **0** ngoài | REQ-LOGIN-37 |
| Thuộc tính ràng buộc của `#email` / `#password` | `required=false`, `maxLength=-1`, `minLength=-1`, `pattern=null` trên **cả hai** | REQ-LOGIN-02, mục 4.1 |
| `value` của checkbox Remember me | `"estimate"`, `checked=false`, `disabled=false` | REQ-LOGIN-02, AMB-LOGIN-11 |
| `input#email` sau khi đăng nhập lỗi | `getAttribute('value')` = `null` | REQ-LOGIN-16 |
| **Hình thái** cookie sau khi tích Remember me | `a:2:{s:7:"user_id";s:1:"<id>";s:3:"key";s:16:"<16 ký tự hex>";}` · độ dài key = **16**.<br>🔒 **Giá trị thật KHÔNG được ghi vào tài liệu** — đây là token tự đăng nhập, ghi ra là phát tán credential (xem `RISK-LOGIN-02`, `RISK-LOGIN-07`) | REQ-LOGIN-08, 35 |
| Cookie sau khi đăng nhập **không** tích Remember me (trạng thái sạch) | `document.cookie` **rỗng** — không có `autologin` | REQ-LOGIN-09 |
| Thân request `POST /admin/authentication` | `csrf_token_name=…&email=…&password=…&remember=estimate` | REQ-LOGIN-21 |
| Mã hàm `logout()` | Kiểm `$(".started-timers-top").find("li.timer").length > 0` → mở popup, ngược lại chuyển tới `authentication/logout` | REQ-LOGIN-30, 31 |
| Số thẻ `<a>` trên trang Quên mật khẩu | **1** — chỉ logo trỏ về trang chủ | REQ-LOGIN-24 |
| Hai phần tử `li.header-logout` ở desktop `1600×750` | Cái trong `ul.nav.navbar-nav`: hộp `0×0`, `offsetParent === null`, tổ tiên `div.mobile-navbar.collapse` + `div.mobile-menu` đều `display:none` · Cái trong `.dropdown-menu`: hộp `160×64`, `offsetParent ≠ null` | REQ-LOGIN-29, AMB-LOGIN-16 |
| Số `<li>` lồng trong `.dropdown-menu` hồ sơ | **32** (submenu Language) → `.dropdown-menu li:last-child` KHÔNG phải Logout; phải dùng `> li:last-child` | REQ-LOGIN-29 |
| `document.title` và `body.className` ở Dashboard | Title `(16) Dashboard` — có tiền tố **đếm thông báo động** · class `app admin dashboard invoices-total-manual user-id-2 chrome` — token `chrome` đổi theo trình duyệt | REQ-LOGIN-06, 19, 20 |
