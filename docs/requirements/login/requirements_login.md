# Requirements — Login (Đăng nhập & Phiên làm việc)

← [Danh mục toàn hệ thống](../README.md) · [Bản đồ hệ thống](../_discovery/system_map.md) · [Tệp khám phá module](../_discovery/modules/module_01_login.md)

| Mục | Giá trị |
|---|---|
| **Module** | Login (Đăng nhập & Phiên làm việc) |
| **Prefix** | `LOGIN` |
| **Hệ thống** | Perfex CRM `3.1.6` — khu vực quản trị `/admin` |
| **Nền tảng** | **Web ✅ đã khảo sát** · Mobile ⬜ chưa có · API ⬜ chưa có |
| **Dải mã đã dùng** | `REQ-LOGIN-01` → `REQ-LOGIN-43` *(đợt 1: UI recon 01→40 · đợt 2: chốt AMB 41→43)* · `AMB-LOGIN-01` → `AMB-LOGIN-12` · `RISK-LOGIN-01` → `RISK-LOGIN-07` |
| **Mã kế tiếp** | Đợt phân tích sau bắt đầu từ `REQ-LOGIN-44` · `AMB-LOGIN-13` · `RISK-LOGIN-08` — **KHÔNG đánh lại từ 01** |
| **Tiền tố TC ID** | `CRM_LOGIN_TC_<3 số>` |
| **Trình duyệt khảo sát** | **Google Chrome** (điều khiển bằng Playwright), viewport `1600×750`. Mọi AC dựa trên **thông báo mặc định của trình duyệt chỉ đúng với trình duyệt này** và **không được dùng làm assertion** |
| **Nguồn** | UI thực tế — không có tài liệu nào được cung cấp |
| **Ngày khảo sát** | 19-09-2026 |
| **Ngày phát hành** | 21-09-2026 |
| **Ambiguity** | **12/12 đã chốt ✅** — không còn AMB treo. Xem mục 8 |

## Tổng quan

Module `LOGIN` là **cổng vào duy nhất** của khu vực quản trị Perfex CRM. Nó quản lý: xác thực bằng email + mật khẩu, duy trì phiên làm việc, tuỳ chọn ghi nhớ đăng nhập dài ngày, đăng xuất, khôi phục mật khẩu, và việc chặn mọi route quản trị khi chưa đăng nhập.

Hỏng ở module này thì **27 module còn lại không tiếp cận được** — đó là lý do toàn bộ module xếp risk 🔴.

> ⚠️ **Ba REQ mô tả hành vi ĐÚNG, không phải hành vi đang chạy.** Sau khi chốt `AMB-LOGIN-02` và `AMB-LOGIN-03`, các yêu cầu **REQ-LOGIN-30 · REQ-LOGIN-31 · REQ-LOGIN-38** trở thành chuẩn mà build hiện tại **vi phạm**. TC viết theo chúng sẽ FAIL thật — đó là kết quả đúng. Xem `RISK-LOGIN-07`.

> 📄 **Tài liệu một tệp.** Module hiện chỉ có **một nền tảng (Web)** nên toàn bộ REQ nằm ngay trong tệp này. Khi khảo sát thêm app mobile hoặc API, tách theo nền tảng bằng cách tạo `mobile/requirements_login_mobile.md` / `api/requirements_login_api.md` và **giữ nguyên mã REQ** — phần cắt ngang (phân quyền, trạng thái phiên, AMB/RISK, Story, nhật ký) ở lại tệp index này.

---

## 1. Phạm vi

| Trong phạm vi | Ngoài phạm vi |
|---|---|
| Trang đăng nhập `/admin/authentication` | Cổng đăng nhập của **khách hàng** (người liên hệ) — đã chốt là **hệ thống xác thực tách biệt**, không dùng `/admin/authentication`; thuộc module `CTC` (`AMB-LOGIN-11` ✅) |
| Màn hình Quên mật khẩu `/admin/authentication/forgot_password` | Màn hình **đặt lại mật khẩu** sau khi bấm liên kết trong email — chưa kiểm chứng |
| Đăng xuất, bảo vệ route, điều hướng theo trạng thái phiên | Cấu hình thời hạn phiên (nằm ở vùng Setup bị chặn) |
| Cookie phiên và cookie ghi nhớ đăng nhập | Quản lý tài khoản nhân viên (vùng Setup bị chặn) |
| Trang từ chối truy cập `/admin/access_denied` | Ma trận quyền chi tiết của từng chức năng — thuộc module tương ứng |

## 2. Bản đồ phủ tài liệu

**Không có tài liệu nào được cung cấp cho module này.** 40 REQ đầu (`REQ-LOGIN-01` → `40`) sinh từ khảo sát UI thực tế; 3 REQ sau (`REQ-LOGIN-41` → `43`) sinh từ **quyết định chốt ambiguity** ngày 21-09-2026, chưa kiểm chứng được trên hệ thống.

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
| REQ-LOGIN-43 | Mọi vai trò dùng chung một luồng đăng nhập | Chốt `AMB-LOGIN-09`: hệ thống **không** phân nhánh luồng xác thực theo vai trò | Mọi vai trò của khu vực quản trị dùng chung route `/admin/authentication`, chung quy tắc xác thực và chung cơ chế phiên. Khác biệt giữa các vai trò nằm ở **quyền sau khi đăng nhập** (REQ-LOGIN-40), **không** ở bản thân luồng đăng nhập.<br>⚠️ **Chưa kiểm chứng được** — dự án mới có một tài khoản. Cần tài khoản vai trò thứ hai để xác nhận (`RISK-LOGIN-05`) | ⚪ | 21-09-2026 · AMB-LOGIN-09 | Chốt AMB-LOGIN-09 |

### 3.3. Kiểm tra dữ liệu nhập

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-10 | Bỏ trống cả hai ô thì báo thiếu cả hai | Người dùng biết còn thiếu gì | Để trống cả 2 ô → bấm `Login` → trang hiện **đồng thời 2 thông báo**, nguyên văn: `The Password field is required.` và `The Email Address field is required.`<br>⚠️ **KHÔNG assert thứ tự** hai thông báo — thứ tự không thuộc yêu cầu (chốt `AMB-LOGIN-12`), chỉ kiểm **có mặt cả hai** | 🟡 | 21-09-2026 · AMB-LOGIN-12 | Kiểm chứng thực tế + chốt AMB-LOGIN-12 |
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
| REQ-LOGIN-18 | Không giới hạn số lần đăng nhập sai liên tiếp | **Chính sách đã chốt** (`AMB-LOGIN-01`): hệ thống **không** có cơ chế khoá tài khoản và **không** có giới hạn tần suất — áp cho mọi tài khoản | Gửi **6 lần** đăng nhập sai liên tiếp cùng một email → cả 6 lần đều trả `Invalid email or password`, **không** thông báo khoá tài khoản, **không** CAPTCHA, **không** độ trễ tăng dần.<br>⚠️ Đã kiểm trên email **không tồn tại**. Kiểm trên tài khoản **có thật** chỉ được dùng **tài khoản phụ** — xem `RISK-LOGIN-06` | 🟡 | 21-09-2026 · AMB-LOGIN-01 | Kiểm chứng thực tế + chốt AMB-LOGIN-01 |

### 3.5. Ghi nhớ đăng nhập (Remember me)

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-19 | Tick Remember me thì cấp thêm cookie ghi nhớ | *Tác tạo được tạo ra đúng hình thái* | Phép thử **trạng thái sạch**: (1) xoá toàn bộ cookie → (2) xác nhận danh sách cookie **rỗng** → (3) đăng nhập **có tick** `Remember me` → (4) xuất hiện cookie `autologin`. Hình thái: **độ dài 110**, `Secure = true`, `SameSite = Lax`, thời hạn **~62 ngày**.<br>Thời hạn 62 ngày là **giá trị mặc định của sản phẩm, đã chốt** (`AMB-LOGIN-05`) — AC chấp nhận sai số ±1 ngày, **không** assert khớp tới giây.<br>Về `HttpOnly` xem REQ-LOGIN-38 | 🟡 | 21-09-2026 · AMB-LOGIN-05 | Kiểm chứng thực tế + chốt AMB-LOGIN-05 |
| REQ-LOGIN-20 | Cookie ghi nhớ tự đăng nhập lại khi mất cookie phiên | *Tác tạo dùng được đúng mục đích* — vế thứ hai của REQ-LOGIN-19 | (1) Đăng nhập có tick `Remember me` → (2) xoá **riêng** cookie `sp_session`, giữ `autologin` → (3) mở `/admin/clients` → **vào thẳng được**, điểm dừng là `/admin/clients`, tiêu đề `Customers`, **không** bị đẩy về trang đăng nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-21 | Không tick thì không cấp cookie ghi nhớ | | Phép thử **trạng thái sạch**: (1) xoá toàn bộ cookie → (2) xác nhận rỗng → (3) đăng nhập **không tick** → (4) danh sách cookie chỉ có `csrf_cookie_name` và `sp_session`, **không** có `autologin` | 🟢 | — | Kiểm chứng thực tế |

### 3.6. Đăng xuất

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-22 | Đăng xuất đưa người dùng về trang đăng nhập | | Bấm `Logout` trong menu hồ sơ → điểm dừng là `/admin/authentication`, tiêu đề tab **chứa** `Login` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-23 | Đăng xuất vô hiệu hoá phiên ở máy chủ | Đây mới là điều kiện an toàn thật sự | Sau khi đăng xuất, mở `/admin/clients` → bị đẩy về `/admin/authentication`. Kiểm ở **cùng ngữ cảnh trình duyệt**, không xoá cookie thủ công | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-24 | Cookie ghi nhớ còn lại trong trình duyệt sau đăng xuất nhưng hết tác dụng | Mô tả hành vi hiện tại — xem `RISK-LOGIN-02` | (1) Đăng nhập có tick `Remember me` → (2) đăng xuất → (3) cookie `autologin` **vẫn còn** trong danh sách cookie → (4) chỉ giữ `autologin`, xoá phần còn lại, mở `/admin/clients` → **vẫn bị đẩy về** trang đăng nhập → token đã bị vô hiệu phía máy chủ | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-25 | Đăng xuất chuyển hướng về trang đăng nhập — KHÔNG assert mã trạng thái | Chốt `AMB-LOGIN-08`: mã **307** quan sát được **không** phải hành vi cố ý và chưa loại trừ được sai số của công cụ đo | Bấm `Logout` → điểm dừng là `/admin/authentication` và trang hiển thị biểu mẫu đăng nhập.<br>⚠️ **CẤM assert mã trạng thái HTTP** của yêu cầu đăng xuất. Ghi nhận để tham khảo: quan sát ngày 19-09-2026 thấy `GET /admin/authentication/logout` trả **307**, sau đó `GET /admin/authentication` trả **200** | 🟡 | 21-09-2026 · AMB-LOGIN-08 | Kiểm chứng thực tế + chốt AMB-LOGIN-08 |

### 3.7. Bảo vệ route & điều hướng theo trạng thái phiên

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-26 | Chưa đăng nhập thì mọi route quản trị bị chặn | | Ngữ cảnh trình duyệt **sạch cookie** → mở `/admin/clients` → điểm dừng là `/admin/authentication` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-27 | Hệ thống không ghi nhớ trang người dùng định vào | Mô tả hành vi hiện tại | Chưa đăng nhập, mở `/admin/clients/client/9091` → bị đẩy về `/admin/authentication` **không kèm tham số truy vấn nào**. Sau khi đăng nhập, điểm dừng là `/admin/`, **không** phải trang đã yêu cầu ban đầu | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-28 | Đã đăng nhập mà mở lại trang đăng nhập thì về Bảng điều khiển | | Đang đăng nhập → mở `/admin/authentication` → điểm dừng là `/admin/`, tiêu đề **chứa** `Dashboard` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-40 | Truy cập chức năng ngoài quyền thì hiện trang từ chối | | Đang đăng nhập bằng tài khoản không có quyền Setup → mở `/admin/staff` → điểm dừng là `/admin/access_denied`; trang hiện thông báo nổi nguyên văn `Access denied` và nội dung thân trang `Something went wrong. Try again` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-41 | Phiên hết hạn theo cấu hình mặc định của nền tảng | Chốt `AMB-LOGIN-06`: ứng dụng **không** cài đặt thời hạn phiên riêng ở tầng nghiệp vụ | Phiên nhàn rỗi hết hạn theo cấu hình mặc định của PHP trên máy chủ; hết hạn rồi thì mở route quản trị bị đẩy về `/admin/authentication` (như REQ-LOGIN-26).<br>⚠️ **Chưa đo được giá trị cụ thể.** Trước khi viết TC hết hạn phiên phải đo thực nghiệm (để phiên nhàn rỗi rồi mở route bảo vệ) hoặc lấy số từ dev — **không** suy từ mặc định của PHP | ⚪ | 21-09-2026 · AMB-LOGIN-06 | Chốt AMB-LOGIN-06 |

### 3.8. Quên mật khẩu

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-29 | Màn hình Quên mật khẩu có route và biểu mẫu riêng | | Mở `/admin/authentication/forgot_password` → tiêu đề màn hình **`Forgot Password`**, một ô nhập nhãn `Email Address` (`#email`, `type="email"`), nút nhãn **`Confirm`**. Biểu mẫu `POST` về chính route đó kèm `csrf_token_name` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-30 | Quên mật khẩu với email không tồn tại phải báo thông báo trung tính | **Yêu cầu đã chốt** (`AMB-LOGIN-02`): màn hình Quên mật khẩu theo **cùng** chính sách không lộ danh tính như trang đăng nhập (REQ-LOGIN-16) | Nhập email đúng định dạng nhưng **không tồn tại** → bấm `Confirm` → thông báo hiển thị **không cho biết** email có đăng ký hay không: không chứa `Email not found` hay bất kỳ chuỗi nào mang nghĩa "không tìm thấy". Thông báo phải **giống nhau** giữa email có thật và email không tồn tại.<br>❌ **Build hiện tại trả `Email not found` — VI PHẠM yêu cầu này**, cần raise bug (xem `RISK-LOGIN-07`) | 🟡 | 21-09-2026 · AMB-LOGIN-02 | Chốt AMB-LOGIN-02 — khác hành vi hiện tại |
| REQ-LOGIN-31 | Quên mật khẩu bỏ trống email phải báo thiếu trường | **Yêu cầu đã chốt** (`AMB-LOGIN-02`): thống nhất với trang đăng nhập — bỏ trống là lỗi **thiếu trường**, không phải lỗi tra cứu | Để trống ô email → bấm `Confirm` → hiện thông báo **bắt buộc nhập** (`The Email Address field is required.` hoặc chuỗi tương đương thống nhất với trang đăng nhập), **không** hiện thông báo mang nghĩa "không tìm thấy email".<br>❌ **Build hiện tại trả `Email not found` — VI PHẠM yêu cầu này** (xem `RISK-LOGIN-07`) | 🟡 | 21-09-2026 · AMB-LOGIN-02 | Chốt AMB-LOGIN-02 — khác hành vi hiện tại |
| REQ-LOGIN-32 | Màn hình Quên mật khẩu giữ lại email đã nhập sau khi lỗi | **Khác** hành vi của trang đăng nhập (REQ-LOGIN-17) | Sau khi submit lỗi, ô `#email` **vẫn chứa** giá trị vừa nhập | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-33 | Màn hình Quên mật khẩu không có lối quay lại đăng nhập | Mô tả hành vi hiện tại | Quét toàn bộ thẻ `<a>` trên trang → danh sách **rỗng**. Người dùng chỉ quay lại được bằng nút Back của trình duyệt hoặc gõ URL | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-34 | Gửi yêu cầu đặt lại mật khẩu cho email có thật | Chốt `AMB-LOGIN-10`: hệ thống **có** gửi email kèm liên kết đặt lại. Hạn dùng của liên kết **chưa chốt được** — phải đo hoặc hỏi dev | **Chưa xác minh.** Không submit email có thật vì thao tác này **gửi email đặt lại mật khẩu ra ngoài**. Cần hộp thư kiểm soát được để đo: thông báo hiển thị sau khi gửi · nội dung email · hình thái liên kết đặt lại · **hạn dùng của liên kết** · màn hình đặt mật khẩu mới.<br>🔒 Khi kiểm chứng, ghi **hình thái** chuỗi ký trong liên kết, **không** ghi giá trị thật | ⚪ | 21-09-2026 · AMB-LOGIN-10 | Chốt AMB-LOGIN-10 — vẫn chưa kiểm chứng được |

### 3.9. Giao thức & thuộc tính bảo mật quan sát được

| REQ ID | Tên yêu cầu | Mô tả | Acceptance Criteria | Trạng thái | Cập nhật lần cuối | Nguồn |
|---|---|---|---|---|---|---|
| REQ-LOGIN-35 | Trang đăng nhập mở được qua HTTP trên môi trường demo | Chốt `AMB-LOGIN-04`: **chỉ chấp nhận trên môi trường demo** vì cấu hình đơn giản. Môi trường production bắt buộc ép HTTPS — xem REQ-LOGIN-42 | Trên **môi trường demo**: mở `http://crm.anhtester.com/admin/authentication` → trả **200**, giao thức của trang vẫn là `http:`, biểu mẫu hiển thị đầy đủ. **Không** có chuyển hướng sang `https:` | 🟡 | 21-09-2026 · AMB-LOGIN-04 | Kiểm chứng thực tế + chốt AMB-LOGIN-04 |
| REQ-LOGIN-36 | Phản hồi trên môi trường demo không kèm header ép dùng HTTPS | Chốt `AMB-LOGIN-04`: chấp nhận ở demo, production bắt buộc có — xem REQ-LOGIN-42 | Trên **môi trường demo**: phản hồi của trang đăng nhập qua HTTP **không** có header `Strict-Transport-Security` | 🟡 | 21-09-2026 · AMB-LOGIN-04 | Kiểm chứng thực tế + chốt AMB-LOGIN-04 |
| REQ-LOGIN-37 | Thông tin đăng nhập vẫn gửi qua HTTPS kể cả khi trang mở bằng HTTP | Yếu tố giảm nhẹ của REQ-LOGIN-35 | Trên trang mở bằng `http:`, đọc `form.action` → là **đường dẫn tuyệt đối bắt đầu bằng `https://`** → dữ liệu biểu mẫu không đi qua kênh không mã hoá | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-38 | Cookie ghi nhớ đăng nhập phải chặn JavaScript đọc | **Yêu cầu đã chốt** (`AMB-LOGIN-03`): `HttpOnly = false` là **sơ suất cấu hình**, phải sửa. Cookie này một mình đủ để vào hệ thống (REQ-LOGIN-20) và sống ~62 ngày | Cookie `autologin` phải có `HttpOnly = true` — không đọc được bằng `document.cookie`.<br>❌ **Build hiện tại `HttpOnly = false` — VI PHẠM yêu cầu này**, cần raise bug (xem `RISK-LOGIN-02`, `RISK-LOGIN-07`) | 🟡 | 21-09-2026 · AMB-LOGIN-03 | Chốt AMB-LOGIN-03 — khác hành vi hiện tại |
| REQ-LOGIN-39 | Cookie phiên và cookie chống giả mạo đều đặt cờ bảo vệ | | Cookie `sp_session` và `csrf_cookie_name` đều có `Secure = true` và `SameSite = Lax`; riêng `sp_session` có `HttpOnly = true` | 🟢 | — | Kiểm chứng thực tế |
| REQ-LOGIN-42 | Môi trường production phải ép HTTPS và đặt HSTS | Chốt `AMB-LOGIN-04`: hành vi ở REQ-LOGIN-35, 36 **chỉ** chấp nhận trên demo | Trên môi trường **production**: mở `http://<host>/admin/authentication` → chuyển hướng sang `https://`; phản hồi có header `Strict-Transport-Security`.<br>⚠️ **Không kiểm được trên môi trường demo hiện tại** — TC đánh `skip` cho tới khi có môi trường production | ⚪ | 21-09-2026 · AMB-LOGIN-04 | Chốt AMB-LOGIN-04 |

---

## 4. Đặc tả Trường Dữ liệu

### 4.1. Biểu mẫu đăng nhập — `/admin/authentication`

| Field (Label) | Loại UI | Required | Ràng buộc đọc được từ DOM | REQ liên quan | Ghi chú |
|---|---|---|---|---|---|
| Email Address | `input[type=email]` `#email` `name=email` | **Có — chỉ ở máy chủ** | **Không** có `required` · **không** có `maxlength` · **không** có `minlength` · **không** có `pattern` · **không** có `placeholder` · có `autofocus="1"` | REQ-LOGIN-02, 11, 13, 14 | Định dạng email do `type=email` của trình duyệt kiểm |
| Password | `input[type=password]` `#password` `name=password` | **Có — chỉ ở máy chủ** | **Không** có `required` · **không** có `maxlength` · **không** có `minlength` · **không** có `autocomplete` | REQ-LOGIN-03, 12, 13 | Không có nút hiện/ẩn mật khẩu |
| Remember me | `input[type=checkbox]` `#remember` `name=remember` | Không | Mặc định **không tick** · `value="estimate"` | REQ-LOGIN-04, 19, 21 | ⚠️ `value="estimate"` đã chốt là **lỗi sao chép**, không mang nghĩa nghiệp vụ (`AMB-LOGIN-07`). **CẤM** dùng `value` làm locator hay điều kiện assert — chức năng chỉ phụ thuộc việc trường có được gửi lên hay không |
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
| REQ-LOGIN-30 | Quên mật khẩu — email không tồn tại | **Yêu cầu:** thông báo trung tính, không tiết lộ email có đăng ký hay không.<br>❌ Build hiện tại: `Email not found` — vi phạm | Máy chủ |
| REQ-LOGIN-31 | Quên mật khẩu — bỏ trống email | **Yêu cầu:** thông báo bắt buộc nhập, thống nhất với trang đăng nhập.<br>❌ Build hiện tại: `Email not found` — vi phạm | Máy chủ |
| REQ-LOGIN-40 | Truy cập chức năng ngoài quyền | Thông báo nổi `Access denied` + thân trang `Something went wrong. Try again` | Máy chủ |

### ⚠️ Ba điểm không nhất quán giữa hai biểu mẫu — trạng thái sau khi chốt `AMB-LOGIN-02`

| # | Trang đăng nhập | Trang Quên mật khẩu *(build hiện tại)* | Phán quyết |
|---|---|---|---|
| 1 | Bỏ trống → báo **thiếu trường** (`... field is required.`) | Bỏ trống → báo **không tìm thấy** (`Email not found`) | ❌ **Lỗi** — phải thống nhất theo trang đăng nhập (REQ-LOGIN-31) |
| 2 | **Không** lộ email có tồn tại hay không | **Lộ thẳng** email không tồn tại | ❌ **Lỗi bảo mật** — phải thống nhất không lộ (REQ-LOGIN-30 · `RISK-LOGIN-04`) |
| 3 | **Xoá** giá trị email sau khi lỗi | **Giữ lại** giá trị email sau khi lỗi | ✅ **Chấp nhận** — khác biệt về tiện dụng, không thuộc `AMB-LOGIN-02`, không phải lỗi |

---

## 6. Ma trận Phân quyền

Module này áp cho **mọi** người dùng của khu vực quản trị. Hàng = hành động, cột = vai trò.

| Hành động | Tài khoản hiện có *(nhân viên, không có quyền Setup)* | Super Admin | Vai trò khác | Người liên hệ khách hàng |
|---|---|---|---|---|
| Mở trang đăng nhập | ✅ | ⚠️✅ | ⚠️✅ | — |
| Đăng nhập thành công vào `/admin` | ✅ | ⚠️✅ | ⚠️✅ | — |
| Dùng Remember me | ✅ | ⚠️✅ | ⚠️✅ | — |
| Dùng Quên mật khẩu | ✅ | ⚠️✅ | ⚠️✅ | — |
| Đăng xuất | ✅ | ⚠️✅ | ⚠️✅ | — |

```
Tổng 20 ô = Đã kiểm chứng 5 · Suy diễn 10 · Chưa rõ 0 · Không áp dụng 5
```

- Ô **"không áp dụng"** là toàn bộ cột **Người liên hệ khách hàng** — đã chốt (`AMB-LOGIN-11` ✅) họ dùng **hệ thống xác thực tách biệt**, không đi qua `/admin/authentication`. Thuộc module `CTC`, xem thêm `AMB-SYS-03`
- Cột **Super Admin** và **Vai trò khác** chuyển từ `❔` sang `⚠️✅` sau khi chốt `AMB-LOGIN-09`: mọi vai trò dùng **chung một luồng đăng nhập** (REQ-LOGIN-43), khác biệt chỉ nằm ở quyền **sau khi** đăng nhập
- ⚠️ `⚠️✅` là **suy từ quyết định đã chốt, chưa đăng nhập thử** — dự án vẫn chỉ có một tài khoản (`RISK-LOGIN-05`). Có tài khoản vai trò thứ hai thì nâng lên `✅`

---

## 7. Ma trận Trạng thái phiên làm việc

Module không quản lý entity nghiệp vụ, nhưng **phiên làm việc có trạng thái rõ ràng** nên vẫn lập ma trận.

| Trạng thái hiện tại | Hành động | Trạng thái kế tiếp | REQ |
|---|---|---|---|
| Chưa đăng nhập | Mở route quản trị bất kỳ | Chưa đăng nhập *(bị đẩy về trang đăng nhập, không nhớ trang đích)* | REQ-LOGIN-26, 27 |
| Chưa đăng nhập | Đăng nhập đúng, **không** tick Remember me | Đã đăng nhập *(có `sp_session`)* | REQ-LOGIN-07, 08, 21 |
| Chưa đăng nhập | Đăng nhập đúng, **có** tick Remember me | Đã đăng nhập + ghi nhớ *(có thêm `autologin`)* | REQ-LOGIN-19 |
| Chưa đăng nhập | Đăng nhập sai | Chưa đăng nhập *(thông báo lỗi, email bị xoá)* | REQ-LOGIN-15, 16, 17 |
| Đã đăng nhập | Mở lại trang đăng nhập | Đã đăng nhập *(đẩy về Bảng điều khiển)* | REQ-LOGIN-28 |
| Đã đăng nhập | Đăng xuất | Chưa đăng nhập *(phiên bị vô hiệu ở máy chủ)* | REQ-LOGIN-22, 23 |
| Đã đăng nhập + ghi nhớ | Mất `sp_session`, còn `autologin` | Đã đăng nhập *(tự đăng nhập lại)* | REQ-LOGIN-20 |
| Đã đăng nhập + ghi nhớ | Đăng xuất | Chưa đăng nhập *(cookie `autologin` còn trong trình duyệt nhưng đã vô hiệu)* | REQ-LOGIN-24 |
| Bất kỳ trạng thái đã đăng nhập | Mở chức năng ngoài quyền | Giữ nguyên *(hiện trang từ chối truy cập)* | REQ-LOGIN-40 |

❔ **Chưa xác minh:** trạng thái *hết hạn phiên do không hoạt động* — cấu hình thời hạn nằm ở vùng Setup bị chặn (`AMB-LOGIN-06`).

---

## 8. Điểm Mơ Hồ (Ambiguities)

| Mã | Câu hỏi | Nguy cơ nếu không giải quyết | Mức độ | Assumption tạm | Trạng thái | Kết luận |
|---|---|---|---|---|---|---|
| AMB-LOGIN-01 | Hệ thống có khoá tài khoản sau N lần đăng nhập sai không? Chỉ kiểm được trên email **không tồn tại** (6 lần đều không bị chặn) — không dám thử trên tài khoản thật vì đó là **tài khoản duy nhất** của dự án | Nếu thực tế có khoá mà tài liệu ghi không, TC sẽ đỏ hàng loạt. Nếu thực sự không có khoá thì đây là lỗ hổng | 🔴 | **Không** có cơ chế khoá và **không** có giới hạn tần suất | ✅ Đã trả lời 21-09-2026 | Chốt: **không** có khoá tài khoản, **không** giới hạn tần suất → REQ-LOGIN-18 🟡. Vẫn là lỗ hổng đã biết — `RISK-LOGIN-01` |
| AMB-LOGIN-02 | Màn hình Quên mật khẩu trả `Email not found` — cố ý hay sơ suất? Trang đăng nhập lại **cố tình** không lộ (`Invalid email or password`). Hai màn hình cùng một module mà ngược chính sách nhau | Nếu là sơ suất thì đây là lỗ hổng dò danh tính cần sửa; nếu cố ý thì cần ghi vào chuẩn để test đúng | 🔴 | Là **sơ suất**, cần thống nhất theo hướng không lộ | ✅ Đã trả lời 21-09-2026 | Chốt: là **sơ suất**, thống nhất theo hướng không lộ → REQ-LOGIN-30, 31 🟡. ❌ Build hiện tại vi phạm — `RISK-LOGIN-07` |
| AMB-LOGIN-03 | Vì sao cookie `autologin` đặt `HttpOnly = false` trong khi `sp_session` lại có? Cookie này **một mình đủ để vào hệ thống** và sống 62 ngày | JavaScript đọc được token đăng nhập dài ngày | 🔴 | Là **sơ suất cấu hình** | ✅ Đã trả lời 21-09-2026 | Chốt: là **sơ suất cấu hình**, `autologin` phải `HttpOnly = true` → REQ-LOGIN-38 🟡. ❌ Build hiện tại vi phạm — `RISK-LOGIN-07` |
| AMB-LOGIN-04 | Vì sao không ép HTTPS và không đặt HSTS? | Người dùng có thể mở trang đăng nhập qua kênh không mã hoá | 🔴 | Môi trường demo cấu hình đơn giản; môi trường thật phải ép | ✅ Đã trả lời 21-09-2026 | Chốt: chấp nhận ở **demo**, production bắt buộc ép HTTPS + HSTS → REQ-LOGIN-35, 36 🟡 + REQ-LOGIN-42 ⚪ |
| AMB-LOGIN-09 | Hệ thống có những vai trò nào, và mỗi vai trò đăng nhập có khác biệt gì? Chỉ có **một** tài khoản, màn hình quản lý vai trò bị chặn | Ma trận phân quyền mục 6 có 10/20 ô chưa rõ | 🔴 | Mọi vai trò dùng chung một luồng đăng nhập | ✅ Đã trả lời 21-09-2026 | Chốt: mọi vai trò dùng **chung một luồng** đăng nhập → REQ-LOGIN-43 ⚪. Ma trận mục 6: 10 ô `❔` → `⚠️✅`. Vẫn cần tài khoản thứ hai — `RISK-LOGIN-05` |
| AMB-LOGIN-05 | Thời hạn ~62 ngày của cookie `autologin` là cố ý? Cấu hình ở đâu? | Không biết giá trị đúng thì không viết được TC kiểm hết hạn | 🟡 | 62 ngày là mặc định của sản phẩm | ✅ Đã trả lời 21-09-2026 | Chốt: **~62 ngày** là mặc định của sản phẩm → REQ-LOGIN-19 🟡, AC chấp nhận sai số ±1 ngày |
| AMB-LOGIN-06 | Phiên hết hạn sau bao lâu không hoạt động? | Không viết được TC hết hạn phiên | 🟡 | Theo mặc định của PHP, chưa xác định | ✅ Đã trả lời 21-09-2026 | Chốt: ứng dụng **không** đặt thời hạn riêng, theo mặc định của PHP → REQ-LOGIN-41 ⚪. ⚠️ Giá trị số vẫn **phải đo** trước khi viết TC hết hạn phiên |
| AMB-LOGIN-07 | Checkbox `Remember me` có `value="estimate"` — có ý nghĩa gì không, hay là lỗi sao chép? | Nếu automation chọn theo `value` sẽ viết ra locator vô nghĩa | 🟡 | Là **lỗi sao chép**, chức năng chỉ phụ thuộc việc trường có được gửi hay không | ✅ Đã trả lời 21-09-2026 | Chốt: là **lỗi sao chép** → ghi chú ở Field Spec mục 4.1, **cấm** dùng `value` làm locator |
| AMB-LOGIN-08 | Đăng xuất trả HTTP **307** thay vì 302/303 — cố ý hay do tầng hạ tầng? | Chưa rõ thì không được assert mã này | 🟡 | Không cố ý; **không** assert mã trạng thái, chỉ assert điểm dừng | ✅ Đã trả lời 21-09-2026 | Chốt: **không** cố ý → REQ-LOGIN-25 🟡, AC chỉ assert điểm dừng, cấm assert mã trạng thái |
| AMB-LOGIN-10 | Sau khi gửi Quên mật khẩu **thành công** thì màn hình hiện gì, email nội dung ra sao, liên kết đặt lại sống bao lâu? | `REQ-LOGIN-34` đang ⚪, cả nhánh khôi phục mật khẩu chưa có TC | 🟡 | Có gửi email kèm liên kết đặt lại, hạn dùng chưa rõ | ✅ Đã trả lời 21-09-2026 | Chốt: **có** gửi email kèm liên kết đặt lại. ⚠️ Hạn dùng **chưa chốt được** — REQ-LOGIN-34 giữ ⚪, phải đo khi có hộp thư kiểm soát được |
| AMB-LOGIN-11 | Người liên hệ khách hàng đăng nhập ở cổng nào, luồng có khác không? | Cột thứ 4 của ma trận phân quyền để `—`; nhánh xác thực thứ hai của hệ thống chưa ai khảo sát | 🟡 | Là hệ thống đăng nhập **tách biệt**, không dùng `/admin/authentication` | ✅ Đã trả lời 21-09-2026 | Chốt: là hệ thống đăng nhập **tách biệt** → chính thức ngoài phạm vi module này, thuộc module `CTC`. Cột 4 ma trận mục 6 = không áp dụng |
| AMB-LOGIN-12 | Khi bỏ trống cả hai ô, thông báo **Password hiện trước Email** — thứ tự này cố ý? | TC assert theo thứ tự sẽ gãy nếu sau này đổi | 🟢 | Thứ tự không thuộc yêu cầu; TC chỉ kiểm **có mặt cả hai**, không kiểm thứ tự | ✅ Đã trả lời 21-09-2026 | Chốt: thứ tự **không** thuộc yêu cầu → REQ-LOGIN-10 🟡, TC chỉ kiểm **có mặt cả hai** |

## 9. Rủi ro (Risks)

| Mã | Rủi ro | Mô tả | Mitigation khi test |
|---|---|---|---|
| RISK-LOGIN-01 | Không có hàng rào chống thử mật khẩu hàng loạt | 6 lần sai liên tiếp không sinh khoá, không CAPTCHA, không độ trễ (REQ-LOGIN-18) Đã chốt `AMB-LOGIN-01`: hệ thống **không** có hàng rào — đây là lỗ hổng **đã xác nhận**, không còn là nghi vấn. Đưa vào TC bảo mật, báo dev. **Không** tự chạy kịch bản hàng nghìn lần trên môi trường này |
| RISK-LOGIN-02 | Token ghi nhớ đăng nhập bị JavaScript đọc được | `autologin` có `HttpOnly = false`, sống 62 ngày, và **một mình đủ để vào hệ thống** (REQ-LOGIN-20, 38). Một lỗ XSS ở bất kỳ module nào cũng lấy được token này Đã chốt `AMB-LOGIN-03` là sơ suất phải sửa → REQ-LOGIN-38 yêu cầu `HttpOnly = true`. Raise bug; ưu tiên cao khi test XSS ở các module có ô nhập tự do |
| RISK-LOGIN-03 | Trang đăng nhập phục vụ qua HTTP, không có HSTS | Kẻ tấn công ở giữa có thể sửa trang HTTP, đổi `form.action` sang máy chủ của họ (REQ-LOGIN-35, 36) | Ghi nhận; giảm nhẹ nhờ `form.action` tuyệt đối HTTPS (REQ-LOGIN-37). Đề nghị bật ép HTTPS + HSTS |
| RISK-LOGIN-04 | Màn hình Quên mật khẩu để lộ email nào chưa đăng ký | `Email not found` cho phép dò ngược danh sách email hợp lệ (REQ-LOGIN-30) Đã chốt `AMB-LOGIN-02` là lỗi → REQ-LOGIN-30 yêu cầu thông báo trung tính. Raise bug; TC viết theo REQ mới và sẽ FAIL tới khi dev sửa |
| RISK-LOGIN-05 | Chỉ có một tài khoản để kiểm thử | Sau khi chốt `AMB-LOGIN-09`, 10/20 ô ma trận ở mức `⚠️✅` **suy diễn** — chưa ô nào được đăng nhập thử. REQ-LOGIN-43 vẫn ⚪ | Xin thêm tài khoản vai trò thứ hai để nâng 10 ô lên `✅` và kiểm chứng REQ-LOGIN-43. Không làm tròn `⚠️✅` thành `✅` khi báo độ phủ |
| RISK-LOGIN-06 | Thử nghiệm khoá tài khoản có thể khoá chính tài khoản duy nhất | Nếu hệ thống có khoá mà ta thử trên tài khoản thật thì **mất quyền truy cập toàn dự án** | ⛔ **Chỉ** thử trên tài khoản phụ. `AMB-LOGIN-01` đã chốt là không có khoá, nhưng **chưa kiểm trên tài khoản thật** nên rủi ro vẫn còn |
| RISK-LOGIN-07 | Ba yêu cầu vừa chốt **trái với build hiện tại** | REQ-LOGIN-30, 31 (thông báo Quên mật khẩu) và REQ-LOGIN-38 (`autologin` phải `HttpOnly`) mô tả hành vi **đúng**, không phải hành vi đang chạy. TC viết theo chúng sẽ **FAIL thật** | Raise 3 bug trước khi thực thi TC, gắn đúng REQ ID. ⛔ **CẤM hạ AC xuống theo hành vi hiện tại** để test xanh — làm vậy là che bug. TC đánh dấu `known-bug` cho tới khi dev sửa |

---

## 10. Phân rã Epic / Story

43 REQ ≥ ngưỡng 25 → bắt buộc có mục này.

| Story ID | Tên Story | REQ bao phủ | Số REQ | AMB / RISK liên quan | Ghi chú phạm vi |
|---|---|---|---|---|---|
| STORY-LOGIN-01 | Giao diện & cấu trúc trang đăng nhập | REQ-LOGIN-01 → 06 | 6 | AMB-LOGIN-07 | Chỉ kiểm hiển thị và cấu trúc, chưa chạm nghiệp vụ |
| STORY-LOGIN-02 | Đăng nhập thành công & cấp phiên | REQ-LOGIN-07 → 09, REQ-LOGIN-43 | 4 | AMB-LOGIN-09 ✅ · RISK-LOGIN-05 | Luồng chính, mức độ blocker. REQ-LOGIN-43 ⚪ — chờ tài khoản vai trò thứ hai |
| STORY-LOGIN-03 | Kiểm tra dữ liệu nhập | REQ-LOGIN-10 → 14 | 5 | AMB-LOGIN-12 | Phân biệt rõ validation máy chủ và trình duyệt |
| STORY-LOGIN-04 | Đăng nhập thất bại & chống dò danh tính | REQ-LOGIN-15 → 18 | 4 | AMB-LOGIN-01 · RISK-LOGIN-01 | Có phần bảo mật, cần thận trọng khi chạy |
| STORY-LOGIN-05 | Ghi nhớ đăng nhập | REQ-LOGIN-19 → 21 | 3 | AMB-LOGIN-03, AMB-LOGIN-05 · RISK-LOGIN-02 | Mỗi tác tạo có REQ *tạo ra* và REQ *dùng được* |
| STORY-LOGIN-06 | Đăng xuất | REQ-LOGIN-22 → 25 | 4 | AMB-LOGIN-08 | |
| STORY-LOGIN-07 | Bảo vệ route & điều hướng phiên | REQ-LOGIN-26 → 28, REQ-LOGIN-40, REQ-LOGIN-41 | 5 | AMB-LOGIN-06 ✅ | REQ-LOGIN-40 xếp ở đây vì cùng là hành vi chặn truy cập. REQ-LOGIN-41 ⚪ — chưa đo được thời hạn phiên |
| STORY-LOGIN-08 | Quên mật khẩu | REQ-LOGIN-29 → 34 | 6 | AMB-LOGIN-02, AMB-LOGIN-10 · RISK-LOGIN-04 | `REQ-LOGIN-34` đang ⚪ → TC viết trước, đánh `skip` |
| STORY-LOGIN-09 | Giao thức & thuộc tính bảo mật | REQ-LOGIN-35 → 39, REQ-LOGIN-42 | 6 | AMB-LOGIN-04 ✅ · RISK-LOGIN-03, RISK-LOGIN-07 | Kiểm ở tầng giao thức và cookie. REQ-LOGIN-42 ⚪ — chỉ chạy được trên production |

**Dòng tổng kiểm chứng:** `Tổng: 9 Story / 43 REQ — mọi REQ thuộc đúng một Story, không mồ côi, không trùng`
`6 + 4 + 5 + 4 + 3 + 4 + 5 + 6 + 6 = 43 ✔`

### Bảng đối chiếu AMB / RISK

| Nhóm | Mã | Nằm ở đâu |
|---|---|---|
| AMB thuộc Story | AMB-LOGIN-01, 02, 03, 04, 05, 07, 08, 10, 12 | Phân bổ ở bảng Story |
| AMB cấp Epic | AMB-LOGIN-06 | Cấu hình hết hạn phiên — bị chặn bởi vùng Setup, cắt ngang mọi Story |
| AMB cấp Epic | AMB-LOGIN-09 | Ma trận Phân quyền (mục 6) |
| AMB cấp Epic | AMB-LOGIN-11 | Cổng đăng nhập của khách hàng — liên module `CTC` |
| RISK thuộc Story | RISK-LOGIN-01, 02, 03, 04 | Phân bổ ở bảng Story |
| RISK cấp Epic | RISK-LOGIN-05 | Thiếu tài khoản — ảnh hưởng toàn module |
| RISK cấp Epic | RISK-LOGIN-06 | Rủi ro của chính việc kiểm thử — ảnh hưởng toàn module |
| RISK thuộc Story | RISK-LOGIN-07 | STORY-LOGIN-08 (REQ-LOGIN-30, 31) và STORY-LOGIN-09 (REQ-LOGIN-38) — ba REQ trái build hiện tại |

`12/12 AMB` (toàn bộ ✅ Đã trả lời) và `7/7 RISK` đều có chỗ thuộc về. ✔

### Hạng mục cấp Epic — cố ý không gán vào Story nào

| Hạng mục | Lý do |
|---|---|
| Ma trận Phân quyền (mục 6) | Cắt ngang mọi Story; đang bị chặn bởi `RISK-LOGIN-05` |
| Ma trận Trạng thái phiên (mục 7) | Mô tả quan hệ **giữa** các Story, không thuộc riêng Story nào |
| Yêu cầu phi chức năng (mục 11) | Áp cho toàn module |

### Thứ tự triển khai đề xuất

| Thứ tự | Story | Lý do |
|---|---|---|
| 1 | STORY-LOGIN-02 | Luồng chính; không chạy được thì 8 Story còn lại vô nghĩa |
| 2 | STORY-LOGIN-01 | Cấu trúc trang — nền cho mọi locator về sau |
| 3 | STORY-LOGIN-03 | Validation, không phụ thuộc gì thêm |
| 4 | STORY-LOGIN-07 | Bảo vệ route — cần luồng đăng nhập/đăng xuất đã chạy |
| 5 | STORY-LOGIN-06 | Đăng xuất |
| 6 | STORY-LOGIN-05 | Ghi nhớ đăng nhập — phức tạp nhất về cookie |
| 7 | STORY-LOGIN-09 | Kiểm tầng giao thức, độc lập |
| 8 | STORY-LOGIN-04 | `AMB-LOGIN-01` đã chốt → **hết BLOCKED**. Riêng phép thử trên tài khoản **có thật** vẫn cần tài khoản phụ (`RISK-LOGIN-06`) |
| 9 ⚠️ `BLOCKED` một phần | STORY-LOGIN-08 | `REQ-LOGIN-34` ⚪ — cần hộp thư nhận được email thật. `REQ-LOGIN-30, 31` chạy được ngay nhưng **sẽ FAIL** cho tới khi dev sửa (`RISK-LOGIN-07`) |

---

## 11. Yêu cầu phi chức năng quan sát được

| Mục | Ghi nhận |
|---|---|
| Giao thức | HTTPS hoạt động, nhưng **không ép** và **không có HSTS** (REQ-LOGIN-35, 36) |
| Viewport khảo sát | `1600×750`. Mọi kết luận về kích thước phần tử chỉ đúng với viewport này — chưa recon ở viewport khác |
| Đa ngôn ngữ | Hệ thống hỗ trợ 28 ngôn ngữ (module `PROFILE`). Mọi thông báo ở mục 5 ghi theo **ngôn ngữ English** đang chọn → TC assert theo chuỗi sẽ gãy khi đổi ngôn ngữ |
| Khả dụng | Màn hình Quên mật khẩu không có lối quay lại (REQ-LOGIN-33); trang đăng nhập không nhớ trang người dùng định vào (REQ-LOGIN-27) |

---

## 12. Danh mục Evidence

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
| [`forgot_password_empty_submit_viewport.png`](evidence/forgot_password_empty_submit_viewport.png) | Submit rỗng → `Email not found` | ❌ **Bằng chứng VI PHẠM** REQ-LOGIN-31 |
| [`forgot_password_unknown_email_viewport.png`](evidence/forgot_password_unknown_email_viewport.png) | Email không tồn tại → `Email not found`, **email được giữ lại** trong ô | REQ-LOGIN-32 · ❌ **Bằng chứng VI PHẠM** REQ-LOGIN-30 |
| [`login_over_http_viewport.png`](evidence/login_over_http_viewport.png) | Trang đăng nhập mở qua **HTTP**, hiển thị đầy đủ | REQ-LOGIN-35, 36, 37 |
| [`access_denied_viewport.png`](evidence/access_denied_viewport.png) | `/admin/staff` → `/admin/access_denied` kèm 2 thông báo | REQ-LOGIN-40 |

**18 ảnh · phủ 39/43 REQ.**

| REQ không có ảnh | Lý do |
|---|---|
| `REQ-LOGIN-34` ⚪ | Chưa kiểm chứng — không gửi email thật ra ngoài |
| `REQ-LOGIN-41` ⚪ | Chưa đo được thời hạn phiên |
| `REQ-LOGIN-42` ⚪ | Chỉ kiểm được trên môi trường production |
| `REQ-LOGIN-43` ⚪ | Chỉ có một tài khoản, chưa thử vai trò thứ hai |

⚠️ **Ba ảnh làm bằng chứng NGƯỢC:** `forgot_password_empty_submit` · `forgot_password_unknown_email` · và dữ liệu cookie của `REQ-LOGIN-38` chứng minh build hiện tại **vi phạm** REQ-LOGIN-31, 30, 38 — dùng làm evidence khi raise bug (`RISK-LOGIN-07`), **không** phải bằng chứng REQ đã đạt.

Bốn REQ không có ảnh riêng mà dựa vào **số liệu đọc từ DOM/cookie** đã chép vào Acceptance Criteria: `REQ-LOGIN-06` (hình thái token) · `REQ-LOGIN-08`, `REQ-LOGIN-19`, `REQ-LOGIN-21`, `REQ-LOGIN-38`, `REQ-LOGIN-39` (hình thái cookie) · `REQ-LOGIN-09`, `REQ-LOGIN-25` (mã HTTP). Ảnh không thể hiện được những dữ kiện này.

---

## 13. Nhật ký thay đổi

| Ngày | Nguồn | REQ ảnh hưởng | Loại | Tóm tắt thay đổi | TC cần xử lý |
|---|---|---|---|---|---|
| 21-09-2026 | Chốt AMB · quyết định người dùng | REQ-LOGIN-41 → 43 | 🟢 Thêm | Sinh 3 REQ từ kết luận ambiguity: `REQ-LOGIN-41` (hết hạn phiên theo mặc định nền tảng — `AMB-LOGIN-06`) · `REQ-LOGIN-42` (production phải ép HTTPS + HSTS — `AMB-LOGIN-04`) · `REQ-LOGIN-43` (mọi vai trò chung một luồng đăng nhập — `AMB-LOGIN-09`). Cả 3 ở trạng thái ⚪ vì chưa kiểm chứng được | — (viết TC mới, đánh `skip`) |
| 21-09-2026 | Chốt AMB · quyết định người dùng | REQ-LOGIN-30, 31, 38 | 🟡 Sửa | **Đổi từ mô tả hiện trạng sang yêu cầu đúng.** `AMB-LOGIN-02` chốt là sơ suất → Quên mật khẩu phải báo trung tính (30) và báo thiếu trường (31). `AMB-LOGIN-03` chốt là sơ suất cấu hình → `autologin` phải `HttpOnly = true` (38). ❌ Build hiện tại **vi phạm cả ba** → mở `RISK-LOGIN-07`, cần raise 3 bug | ⚠️ Viết TC theo REQ mới, đánh `known-bug` — **cấm** hạ AC theo hành vi hiện tại |
| 21-09-2026 | Chốt AMB · quyết định người dùng | REQ-LOGIN-10, 18, 19, 25, 34, 35, 36 | 🟡 Sửa | Chốt theo Assumption tạm, không đổi hành vi hệ thống: `10` cấm assert thứ tự thông báo (`AMB-12`) · `18` chốt không có khoá tài khoản (`AMB-01`) · `19` chốt ~62 ngày ±1 (`AMB-05`) · `25` cấm assert mã 307 (`AMB-08`) · `34` chốt có gửi email, hạn dùng vẫn phải đo (`AMB-10`) · `35, 36` giới hạn phạm vi vào môi trường demo (`AMB-04`) | ⚠️ Review khi viết TC — AC đã siết lại cách assert |
| 21-09-2026 | Chốt AMB · quyết định người dùng | — | ✏️ Biên tập | `AMB-LOGIN-07` chốt `value="estimate"` là lỗi sao chép → ghi chú cấm dùng làm locator ở Field Spec 4.1. `AMB-LOGIN-11` chốt cổng khách hàng là hệ thống tách biệt → cập nhật mục Phạm vi, cột 4 ma trận phân quyền. `AMB-LOGIN-09` → 10 ô ma trận từ `❔` sang `⚠️✅`. Toàn bộ **12/12 AMB chuyển ✅**, không mở AMB mới | — |
| 21-09-2026 | Dọn tài liệu | — | 🔵 Sửa | **Gộp hai tệp thành một.** `web/requirements_login_web.md` được nhập vào tệp index này; thư mục `web/` bị gỡ, ảnh chuyển từ `web/evidence/` sang `evidence/`. **Không** REQ/AMB/RISK/Story nào bị thêm, sửa nội dung hay đánh lại mã — chỉ đổi vị trí và số thứ tự **mục** trong tài liệu | Không — TC không tham chiếu số mục |
| 19-09-2026 | UI recon | REQ-LOGIN-01 → 40 | 🟢 Thêm | Khởi tạo tài liệu từ khảo sát UI thực tế trên Chrome viewport `1600×750`. Sinh 40 REQ, 12 AMB, 6 RISK, 9 Story, 18 ảnh evidence. `REQ-LOGIN-34` ở trạng thái ⚪ vì không gửi email thật ra ngoài | — (viết TC mới) |

---

← [Danh mục toàn hệ thống](../README.md)
