# Requirement Analysis – Module Login (Đăng nhập)

**Hệ thống:** Perfex CRM – Anh Tester Demo
**Tài liệu nguồn:** [docs/SRS_Login_Module.md](../SRS_Login_Module.md) (phiên bản 1.0, khảo sát 2026-08-26)
**URL:** https://crm.anhtester.com/admin/authentication
**Vai trò phân tích:** Senior QA Engineer
**Ngày phân tích:** 2026-09-07
**Phiên bản:** 1.0

---

## 1. Phạm vi phân tích

| Nội dung | Trong phạm vi | Ghi chú |
|---|---|---|
| Màn hình Login (Email / Password / Remember me / Login) | ✅ | FR-01 → FR-06 |
| Forgot Password (điều hướng + gửi mail khôi phục) | ✅ | FR-07 |
| Điều hướng Logo về trang chủ | ✅ | FR-08 |
| Đổi mật khẩu sau đăng nhập, phân quyền, 2FA | ❌ | SRS mục 1.2 loại trừ |

**Tác nhân (Actor):** Admin / Staff của hệ thống CRM.

---

## 2. HAPPY PATH – Luồng chính

### HP-01: Đăng nhập thành công với tài khoản hợp lệ (không chọn Remember me)

**Điều kiện tiên quyết:** Tài khoản Admin/Staff tồn tại, trạng thái `active`; người dùng chưa có session đăng nhập.

| Bước | Hành động của người dùng | Phản hồi mong đợi của hệ thống | Truy vết |
|---|---|---|---|
| 1 | Truy cập `https://crm.anhtester.com/admin/authentication` | Hiển thị form Login đầy đủ: Logo, tiêu đề "Login", Email Address, Password, checkbox Remember me, nút Login, link "Forgot Password?" | FR-01 |
| 2 | Nhập Email đúng định dạng và đã đăng ký | Giá trị hiển thị bình thường trong ô Email, không có lỗi validate | FR-03 |
| 3 | Nhập Password đúng | Ký tự bị che (masked), không hiển thị plain text | NFR-01 |
| 4 | Bấm nút **Login** | Request được gửi lên server qua HTTPS | FR-04, NFR-01 |
| 5 | – | Server xác thực thành công → tạo session cho người dùng | FR-05 |
| 6 | – | Điều hướng đến `https://crm.anhtester.com/admin/` (Dashboard) | FR-05 |
| 7 | – | Dashboard hiển thị đủ: Sidebar (Bảng tin, Khách hàng, Các dự án, Công việc, Hợp đồng, Doanh số, Thuê bao, Chi phí, Hỗ trợ, Khách tiềm năng, Yêu cầu báo giá, Kiến thức, Tiện ích, Báo cáo); Thanh trên cùng (Tìm kiếm, nút +, chia sẻ, việc cần làm, avatar + dropdown, lịch sử, thông báo); 4 widget tổng quan; 5 bảng tổng quan | FR-05 |
| 8 | – | Thời gian phản hồi < 3 giây | NFR-04 |

**Kết quả cuối cùng:** Người dùng ở trạng thái đã đăng nhập, có quyền truy cập khu vực quản trị.

---

## 3. ALTERNATE PATH – Luồng thay thế

> Các luồng vẫn dẫn tới kết quả hợp lệ nhưng đi theo nhánh khác với HP-01.

### AP-01: Đăng nhập thành công **có chọn "Remember me"**

1. Thực hiện bước 1–3 của HP-01.
2. Tích chọn checkbox **Remember me** trước khi submit.
3. Bấm **Login** → đăng nhập thành công, điều hướng Dashboard (giống HP-01).
4. Hệ thống ghi thêm cookie/token ghi nhớ đăng nhập với thời hạn dài hơn session mặc định.
5. Đóng hoàn toàn trình duyệt → mở lại `https://crm.anhtester.com/admin/` → người dùng **vẫn ở trạng thái đăng nhập**, không bị đẩy về màn hình Login.

*Truy vết: FR-06, BR-05.*

### AP-02: Đăng nhập **không** chọn "Remember me" – phiên kết thúc theo mặc định

1. Đăng nhập thành công theo HP-01 (checkbox để trống).
2. Đóng trình duyệt / chờ hết thời gian timeout mặc định.
3. Truy cập lại `/admin/` → hệ thống điều hướng về màn hình Login, yêu cầu đăng nhập lại.

*Truy vết: FR-06, BR-05.*

### AP-03: Submit form bằng bàn phím (không dùng chuột)

1. Ở màn hình Login, dùng phím **Tab** để di chuyển focus theo thứ tự: Email → Password → Remember me → Login.
2. Nhập thông tin hợp lệ, nhấn **Enter** tại ô Password.
3. Form được submit, đăng nhập thành công như HP-01.

*Truy vết: NFR-02.*

### AP-04: Khôi phục mật khẩu qua "Forgot Password" (email đã đăng ký)

1. Tại màn hình Login, bấm link **Forgot Password?**.
2. Hệ thống điều hướng tới `/admin/authentication/forgot_password`, hiển thị ô **Email Address** (bắt buộc) và nút **Confirm**.
3. Nhập email đã đăng ký trong hệ thống, bấm **Confirm**.
4. Hệ thống hiển thị thông báo xác nhận và gửi email chứa liên kết đặt lại mật khẩu tới hộp thư người dùng.
5. Người dùng đặt lại mật khẩu, quay về màn hình Login và đăng nhập bằng mật khẩu mới → thành công.

*Truy vết: FR-07.*

### AP-05: Điều hướng về trang chủ qua Logo

1. Tại màn hình Login, bấm vào **Logo**.
2. Hệ thống điều hướng tới `https://crm.anhtester.com/` (trang chủ), không thực hiện đăng nhập.
3. Người dùng có thể quay lại `/admin/authentication` để tiếp tục đăng nhập.

*Truy vết: FR-08.*

### AP-06: Truy cập màn hình Login khi đã có session hợp lệ

1. Người dùng đang đăng nhập, mở tab mới và truy cập trực tiếp `/admin/authentication`.
2. Hệ thống nhận diện session còn hiệu lực → điều hướng thẳng tới Dashboard (hoặc hiển thị lại form Login — **hành vi chưa được đặc tả**, xem Q-08).

*Truy vết: FR-05 (suy diễn).*

### AP-07: Hiển thị responsive trên nhiều kích thước màn hình

1. Truy cập màn hình Login ở độ phân giải 1280x720 → layout hiển thị đầy đủ, không vỡ.
2. Thu nhỏ viewport về 529x678 (hoặc mở trên mobile) → layout co giãn, các thành phần vẫn thao tác được, không tràn ngang.
3. Đăng nhập thành công ở cả hai kích thước.

*Truy vết: NFR-03.*

---

## 4. EXCEPTION PATH – Luồng lỗi / bị từ chối

### EP-01: Bỏ trống cả hai trường Email và Password

1. Tại màn hình Login, không nhập gì, bấm **Login**.
2. Hệ thống hiển thị **đồng thời hai** thông báo lỗi ở phía trên các trường nhập liệu:
   - `The Email Address field is required.`
   - `The Password field is required.`
3. Không tạo session, người dùng ở lại màn hình Login.

*Truy vết: FR-02, BR-01.*

### EP-02: Bỏ trống riêng trường Email (có nhập Password)

1. Nhập Password hợp lệ, để trống Email, bấm **Login**.
2. Hệ thống hiển thị lỗi bắt buộc cho trường Email (`The Email Address field is required.`), không hiển thị lỗi cho Password.
3. Không submit thành công.

*Truy vết: FR-02, BR-01. **Lưu ý:** SRS chỉ mô tả tường minh trường hợp trống cả hai — xem Q-01.*

### EP-03: Bỏ trống riêng trường Password (có nhập Email)

1. Nhập Email hợp lệ, để trống Password, bấm **Login**.
2. Hệ thống hiển thị lỗi bắt buộc cho trường Password (`The Password field is required.`).
3. Không submit thành công.

*Truy vết: FR-02, BR-01. Xem Q-01.*

### EP-04: Email sai định dạng – thiếu ký tự `@`

1. Nhập Email dạng `adminanhtester.com` (không có `@`), nhập Password bất kỳ, bấm **Login**.
2. Trình duyệt chặn submit (validate native HTML5), hiển thị tooltip:
   `Please include an '@' in the email address. 'adminanhtester.com' is missing an '@'.`
3. **Không** có request nào được gửi lên server.

*Truy vết: FR-03, BR-02.*

### EP-05: Email sai định dạng – các biến thể khác

1. Nhập Email dạng `admin@`, `@domain.com`, `admin@@test.com`, hoặc chuỗi có khoảng trắng.
2. Trình duyệt chặn submit và hiển thị tooltip lỗi định dạng tương ứng.
3. Form không được gửi lên server.

*Truy vết: FR-03, BR-02. **Lưu ý:** SRS chỉ liệt kê thông báo cho trường hợp thiếu `@` — xem Q-02.*

### EP-06: Email đúng định dạng nhưng **không tồn tại** trong hệ thống

1. Nhập Email hợp lệ chưa từng đăng ký (ví dụ `notexist@anhtester.com`) + Password bất kỳ, bấm **Login**.
2. Server trả về thông báo lỗi chung: `Invalid email or password`.
3. Thông báo **không** tiết lộ việc email không tồn tại (chống username enumeration).
4. Người dùng ở lại màn hình Login, không có session được tạo.

*Truy vết: FR-04, BR-03, NFR-01.*

### EP-07: Email tồn tại nhưng **sai Password**

1. Nhập Email đã đăng ký + Password sai, bấm **Login**.
2. Server trả về đúng thông báo `Invalid email or password` — **giống hệt** EP-06 về nội dung, vị trí hiển thị và thời gian phản hồi.
3. Không tạo session.

*Truy vết: FR-04, BR-03, NFR-01.*

### EP-08: Tài khoản bị vô hiệu hóa (inactive/banned)

1. Nhập Email + Password đúng của một tài khoản đang ở trạng thái inactive/banned, bấm **Login**.
2. Hệ thống từ chối đăng nhập, không tạo session, không điều hướng tới Dashboard.
3. Hiển thị thông báo phù hợp (nội dung chính xác **chưa được xác định** trong SRS — xem Q-03).

*Truy vết: BR-04 (đang ở trạng thái "cần xác minh thêm").*

### EP-09: Đăng nhập sai nhiều lần liên tiếp (Brute-force)

1. Thực hiện đăng nhập sai password liên tục N lần (ví dụ 5, 10, 20 lần) với cùng một email.
2. Kỳ vọng: hệ thống kích hoạt cơ chế bảo vệ — khóa tạm thời tài khoản / giới hạn tần suất (rate limiting) / hiển thị captcha.
3. **Rủi ro:** nếu không có cơ chế nào, hệ thống vẫn cho phép thử vô hạn → lỗ hổng bảo mật cần báo cáo.

*Truy vết: NFR-01 (chưa xác minh) — xem Q-04.*

### EP-10: Truy cập trực tiếp trang nội bộ khi chưa đăng nhập

1. Chưa đăng nhập, gõ trực tiếp URL `https://crm.anhtester.com/admin/` (hoặc một trang con như `/admin/clients`).
2. Kỳ vọng: hệ thống chặn truy cập và điều hướng về màn hình Login.

*Truy vết: FR-05 (suy diễn) — hành vi chưa được đặc tả, xem Q-09.*

### EP-11: Sử dụng lại session sau khi đã đăng xuất / hết hạn

1. Đăng nhập thành công, ghi lại URL Dashboard.
2. Đăng xuất (hoặc chờ hết timeout), sau đó bấm nút Back của trình duyệt hoặc truy cập lại URL Dashboard.
3. Kỳ vọng: hệ thống không cho xem lại nội dung đã cache, điều hướng về màn hình Login.

*Truy vết: FR-06 (suy diễn) — xem Q-05, Q-09.*

### EP-12: Forgot Password – bỏ trống Email

1. Vào `/admin/authentication/forgot_password`, để trống ô Email, bấm **Confirm**.
2. Hệ thống hiển thị lỗi bắt buộc và không gửi email.

*Truy vết: FR-07. Nội dung thông báo chưa được đặc tả — xem Q-06.*

### EP-13: Forgot Password – Email sai định dạng

1. Nhập chuỗi không đúng định dạng email (ví dụ `abc`), bấm **Confirm**.
2. Hệ thống chặn submit / hiển thị lỗi định dạng, không gửi email.

*Truy vết: FR-07, BR-02.*

### EP-14: Forgot Password – Email không tồn tại trong hệ thống

1. Nhập email đúng định dạng nhưng chưa đăng ký, bấm **Confirm**.
2. Kỳ vọng theo SRS: hiển thị **thông báo trung lập** (ví dụ: "Nếu email tồn tại, chúng tôi đã gửi liên kết khôi phục"), không tiết lộ email có tồn tại hay không.
3. Không gửi email khôi phục cho địa chỉ không tồn tại.

*Truy vết: FR-07, NFR-01. SRS mới chỉ nói "nên dùng thông báo trung lập" — xem Q-06.*

### EP-15: Nhập dữ liệu bất thường / thử tấn công injection

1. Nhập vào ô Email/Password các chuỗi đặc biệt: `' OR 1=1 --`, `<script>alert(1)</script>`, chuỗi dài > 255 ký tự, ký tự Unicode/emoji, khoảng trắng đầu–cuối.
2. Kỳ vọng: hệ thống xử lý an toàn — không lỗi 500, không thực thi script, không bypass đăng nhập; trả về `Invalid email or password` hoặc lỗi validate phù hợp.

*Truy vết: NFR-01 (suy diễn) — giới hạn độ dài chưa được đặc tả, xem Q-07.*

### EP-16: Mất kết nối / server lỗi trong lúc submit

1. Ngắt mạng (hoặc server trả về 500/timeout) ngay khi bấm **Login**.
2. Kỳ vọng: hệ thống hiển thị thông báo lỗi thân thiện, không trắng trang, không lộ stack trace; người dùng có thể thử lại.

*Truy vết: NFR-04 (suy diễn) — chưa được đặc tả, xem Q-10.*

---

## 5. Ma trận truy vết (Traceability Matrix)

| Requirement | Happy Path | Alternate Path | Exception Path |
|---|---|---|---|
| FR-01 Hiển thị form | HP-01 (b1) | AP-07 | – |
| FR-02 Validate required | – | – | EP-01, EP-02, EP-03 |
| FR-03 Validate định dạng email | HP-01 (b2) | – | EP-04, EP-05 |
| FR-04 Xác thực server-side | HP-01 (b4–5) | – | EP-06, EP-07, EP-15 |
| FR-05 Đăng nhập thành công | HP-01 (b5–7) | AP-06 | EP-10 |
| FR-06 Remember me | – | AP-01, AP-02 | EP-11 |
| FR-07 Forgot Password | – | AP-04 | EP-12, EP-13, EP-14 |
| FR-08 Điều hướng Logo | – | AP-05 | – |
| NFR-01 Bảo mật | HP-01 (b3–4) | – | EP-06, EP-07, EP-09, EP-14, EP-15 |
| NFR-02 Usability (bàn phím) | – | AP-03 | – |
| NFR-03 Responsive | – | AP-07 | – |
| NFR-04 Hiệu năng < 3s | HP-01 (b8) | – | EP-16 |
| BR-04 Tài khoản inactive | – | – | EP-08 |

---

## 6. Các điểm mơ hồ, thiếu sót, mâu thuẫn – Câu hỏi cần làm rõ

### 6.1 Ưu tiên CAO (chặn việc viết/thực thi test case)

| ID | Vấn đề phát hiện | Câu hỏi cần làm rõ | Liên quan |
|---|---|---|---|
| **Q-01** | FR-02 chỉ mô tả trường hợp **để trống cả hai trường** và yêu cầu hiển thị đồng thời 2 thông báo. Không mô tả trường hợp chỉ trống **một** trường. | Khi chỉ để trống Email (hoặc chỉ trống Password), hệ thống hiển thị chính xác thông báo nào? Chỉ 1 thông báo tương ứng hay vẫn hiện cả 2? Lỗi hiển thị ở đầu form hay ngay dưới từng trường? | FR-02, EP-02, EP-03 |
| **Q-02** | FR-03 chỉ nêu ví dụ thông báo cho trường hợp **thiếu ký tự `@`**; chưa liệt kê các dạng sai định dạng khác. Đồng thời việc validate hoàn toàn dựa vào trình duyệt là điểm yếu. | Danh sách đầy đủ các định dạng email bị coi là **không hợp lệ** là gì (thiếu domain `admin@`, thiếu local-part `@abc.com`, hai dấu `@`, có khoảng trắng, ký tự Unicode, TLD quá ngắn/dài)? Hệ thống có validate lại phía **server** không, hay chỉ dựa vào validate native của trình duyệt? Nếu người dùng tắt JS / gửi request trực tiếp (Postman, DevTools) thì hành vi ra sao? | FR-03, BR-02, EP-05 |
| **Q-03** | BR-04 nêu "tài khoản bị vô hiệu hóa không được phép đăng nhập" nhưng đánh dấu *(cần xác minh thêm)*, và **không có thông báo lỗi tương ứng** trong mục 6 "Observed Messages". | Hệ thống có thực sự hỗ trợ trạng thái inactive/banned không? Nếu có, thông báo hiển thị chính xác là gì — dùng chung `Invalid email or password` (nhất quán với chính sách chống enumeration ở NFR-01) hay một thông báo riêng như "Tài khoản đã bị vô hiệu hóa"? **Mâu thuẫn tiềm ẩn:** thông báo riêng sẽ tiết lộ email có tồn tại, vi phạm NFR-01. | BR-04, NFR-01, EP-08 |
| **Q-04** | NFR-01 chỉ dùng từ "**nên có**" cơ chế chống brute-force và ghi rõ chưa xác minh được → yêu cầu không kiểm chứng được (not testable). | Cơ chế chống brute-force là **bắt buộc** hay tùy chọn cho bản release này? Nếu bắt buộc: ngưỡng bao nhiêu lần sai? Áp dụng theo email hay theo IP? Hành vi khi vượt ngưỡng là khóa tài khoản, delay, hay captcha? Thời gian khóa bao lâu? Thông báo hiển thị ra sao? Có cơ chế mở khóa (tự hết hạn / admin mở) không? | NFR-01, EP-09 |

### 6.2 Ưu tiên TRUNG BÌNH

| ID | Vấn đề phát hiện | Câu hỏi cần làm rõ | Liên quan |
|---|---|---|---|
| **Q-05** | FR-06 mô tả "Remember me" duy trì phiên "**lâu hơn**" và "timeout **mặc định** hoặc khi đóng trình duyệt (**tùy cấu hình**)" — không có con số cụ thể, không thể viết Expected Result kiểm chứng được. | Thời hạn cookie/token khi **có** chọn Remember me là bao lâu (7 ngày / 30 ngày)? Thời gian timeout session khi **không** chọn là bao nhiêu phút? Session có được gia hạn khi người dùng hoạt động (sliding expiration) không? Tên cookie và các thuộc tính `HttpOnly` / `Secure` / `SameSite` là gì? | FR-06, BR-05, AP-01, AP-02 |
| **Q-06** | FR-07 dùng từ "**nên** dùng thông báo trung lập" và không cung cấp nội dung thông báo cụ thể cho bất kỳ trường hợp nào của Forgot Password (thành công / email trống / email không tồn tại). | Nội dung chính xác của các thông báo ở màn hình Forgot Password là gì cho từng trường hợp? Liên kết reset password có thời hạn bao lâu, dùng được mấy lần? Có giới hạn số lần gửi yêu cầu khôi phục (chống spam mail) không? Sau khi bấm Confirm thành công, hệ thống ở lại trang hay điều hướng về Login? | FR-07, EP-12, EP-14 |
| **Q-07** | Không có bất kỳ đặc tả nào về **giới hạn độ dài** và ký tự cho phép của Email/Password; cũng không có quy tắc về khoảng trắng đầu–cuối. | Độ dài tối đa/tối thiểu của Email và Password là bao nhiêu? Hệ thống có tự trim khoảng trắng đầu–cuối của Email không? Email có phân biệt chữ hoa/thường không (`Admin@x.com` vs `admin@x.com`)? Password có phân biệt hoa/thường (mặc định là có, cần xác nhận)? Ký tự đặc biệt/Unicode/emoji trong Password có được chấp nhận không? | NFR-01, EP-15 |
| **Q-08** | Không đặc tả hành vi khi người dùng **đã đăng nhập** mà truy cập lại URL `/admin/authentication`. | Hệ thống điều hướng thẳng tới Dashboard, hay vẫn hiển thị form Login? Nếu đăng nhập bằng tài khoản khác trong cùng trình duyệt thì session cũ bị thay thế hay báo lỗi? Có cho phép đăng nhập đồng thời trên nhiều thiết bị/nhiều tab không? | FR-05, AP-06 |
| **Q-09** | Không đặc tả cơ chế **bảo vệ trang nội bộ** khi chưa đăng nhập, cũng như hành vi nút Back sau khi đăng xuất. | Truy cập trực tiếp `/admin/...` khi chưa đăng nhập thì hệ thống làm gì — redirect về Login kèm tham số `redirect_url` để quay lại đúng trang sau khi đăng nhập, hay luôn về Dashboard? Sau khi logout, bấm Back có xem lại được nội dung cache không? | FR-05, EP-10, EP-11 |

### 6.3 Ưu tiên THẤP (bổ sung cho đầy đủ)

| ID | Vấn đề phát hiện | Câu hỏi cần làm rõ | Liên quan |
|---|---|---|---|
| **Q-10** | NFR-04 chỉ nêu ngưỡng "< 3 giây trong **điều kiện mạng bình thường**" — định nghĩa "bình thường" không rõ; không có yêu cầu xử lý khi server lỗi/timeout. | "Điều kiện mạng bình thường" được định nghĩa thế nào (băng thông, độ trễ) và đo ở đâu (client hay server)? Khi server trả 500 hoặc request timeout, màn hình Login hiển thị gì cho người dùng? | NFR-04, EP-16 |
| **Q-11** | NFR-03 nói giao diện "cần hiển thị đúng trên nhiều kích thước màn hình" nhưng chỉ liệt kê 2 độ phân giải đã kiểm tra, không định nghĩa danh sách trình duyệt/thiết bị hỗ trợ. | Ma trận trình duyệt và thiết bị cần hỗ trợ chính thức là gì (Chrome/Firefox/Edge/Safari, phiên bản tối thiểu; iOS/Android)? Có breakpoint responsive cụ thể nào cần kiểm tra không? | NFR-03, AP-07 |
| **Q-12** | Mục 1.2 loại trừ 2FA với lý do "không phát hiện trên giao diện khảo sát" — đây là kết luận từ khảo sát black-box, chưa được xác nhận từ phía sản phẩm. | Hệ thống có hỗ trợ 2FA (bật theo cấu hình từng tài khoản) không? Nếu có, luồng đăng nhập sẽ phát sinh thêm bước nhập mã và cần bổ sung vào SRS + test case. | Mục 1.2, mục 7 |
| **Q-13** | FR-08 ghi logo ở "**góc trên phải**", trong khi bảng UI Elements (mục 2.1) xếp Logo ở vị trí số 1 (trên cùng) mà không nêu vị trí — mô tả chưa nhất quán. | Logo nằm chính xác ở vị trí nào trên màn hình Login? Bấm logo mở trang chủ ở tab hiện tại hay tab mới? | FR-08, mục 2.1, AP-05 |
| **Q-14** | Không có yêu cầu về **đa ngôn ngữ**, dù Dashboard sau đăng nhập hiển thị tiếng Việt còn thông báo lỗi ở màn hình Login lại là tiếng Anh (`Invalid email or password`) — không nhất quán về ngôn ngữ. | Màn hình Login có hỗ trợ đa ngôn ngữ không? Thông báo lỗi cuối cùng sẽ là tiếng Anh hay tiếng Việt? Nếu có chuyển ngôn ngữ, việc chọn ngôn ngữ diễn ra ở đâu (trước hay sau đăng nhập)? | Mục 6, FR-05 |

---

## 7. Đánh giá rủi ro & khuyến nghị

| # | Rủi ro | Mức độ | Khuyến nghị |
|---|---|---|---|
| 1 | Chưa xác minh cơ chế chống brute-force (Q-04) | **Cao** | Ưu tiên kiểm thử bảo mật trên môi trường được cấp phép; nếu không có cơ chế, raise bug bảo mật trước khi release. |
| 2 | Mâu thuẫn tiềm ẩn giữa BR-04 (thông báo tài khoản bị khóa) và NFR-01 (chống enumeration) (Q-03) | **Cao** | Thống nhất chính sách thông báo với BA/PO trước khi viết Expected Result cho EP-08. |
| 3 | FR-06 không có số liệu thời hạn session → test case không kiểm chứng được (Q-05) | Trung bình | Yêu cầu bổ sung giá trị cụ thể vào SRS; tạm thời viết test case ở mức "kiểm tra cookie còn tồn tại sau khi đóng trình duyệt". |
| 4 | FR-03 phụ thuộc validate native của trình duyệt, có thể bị bypass (Q-02) | Trung bình | Bổ sung test case gửi request trực tiếp (Postman/DevTools) để xác minh validate phía server. |
| 5 | Thông báo Forgot Password chưa có nội dung cụ thể (Q-06) | Trung bình | Chốt nội dung message với BA trước khi viết test case cho FR-07. |
| 6 | Nhiều mục trong SRS dùng từ "nên", "tùy cấu hình", "cần xác minh thêm" | Trung bình | Đề nghị BA/PO cập nhật SRS lên phiên bản 1.1 với các yêu cầu **testable** (có giá trị đo được) trước khi baseline. |

---

## 8. Tổng kết

| Chỉ số | Số lượng |
|---|---|
| Happy Path | 1 luồng (HP-01) |
| Alternate Path | 7 luồng (AP-01 → AP-07) |
| Exception Path | 16 luồng (EP-01 → EP-16) |
| **Tổng số luồng** | **24** |
| Câu hỏi cần làm rõ | 14 (4 CAO / 5 TRUNG BÌNH / 5 THẤP) |
| Requirement được truy vết | 8 FR + 4 NFR + 5 BR |

**Kết luận:** SRS đã mô tả tốt luồng chính và các lỗi validate cơ bản (có thông báo thực tế xác nhận). Tuy nhiên các khu vực **session/Remember me, tài khoản bị vô hiệu hóa, chống brute-force và Forgot Password** vẫn còn mơ hồ, chủ yếu do khảo sát black-box không truy cập được mã nguồn. Khuyến nghị làm rõ nhóm câu hỏi **ưu tiên CAO (Q-01 → Q-04)** trước khi bước sang giai đoạn sinh Test Case chi tiết; các luồng còn lại vẫn có thể viết test case ngay kèm giả định được ghi chú rõ ràng.

---

*Tài liệu này là đầu vào cho bước tiếp theo: sinh bộ Test Case Manual (Happy path / Negative case / Edge case) theo quy tắc `rule-generate-testcases`.*
