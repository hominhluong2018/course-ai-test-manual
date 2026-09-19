# Requirements — Login (Đăng nhập & Phiên làm việc)

← [Danh mục toàn hệ thống](../README.md) · [Bản đồ hệ thống](../_discovery/system_map.md) · [Tệp khám phá module](../_discovery/modules/module_01_login.md)

| Mục | Giá trị |
|---|---|
| **Module** | Login (Đăng nhập & Phiên làm việc) |
| **Prefix** | `LOGIN` |
| **Hệ thống** | Perfex CRM `3.1.6` — khu vực quản trị `/admin` |
| **Nền tảng** | **Web ✅ đã khảo sát** · Mobile ⬜ chưa có · API ⬜ chưa có |
| **Dải mã đã dùng** | `REQ-LOGIN-01` → `REQ-LOGIN-40` · `AMB-LOGIN-01` → `AMB-LOGIN-12` · `RISK-LOGIN-01` → `RISK-LOGIN-06` |
| **Mã kế tiếp** | Đợt phân tích sau bắt đầu từ `REQ-LOGIN-41` · `AMB-LOGIN-13` · `RISK-LOGIN-07` — **KHÔNG đánh lại từ 01** |
| **Tiền tố TC ID** | `CRM_LOGIN_TC_<3 số>` |
| **Ngày phát hành** | 19-09-2026 |

## Tổng quan

Module `LOGIN` là **cổng vào duy nhất** của khu vực quản trị Perfex CRM. Nó quản lý: xác thực bằng email + mật khẩu, duy trì phiên làm việc, tuỳ chọn ghi nhớ đăng nhập dài ngày, đăng xuất, khôi phục mật khẩu, và việc chặn mọi route quản trị khi chưa đăng nhập.

Hỏng ở module này thì **27 module còn lại không tiếp cận được** — đó là lý do toàn bộ module xếp risk 🔴.

**Phạm vi chi tiết và toàn bộ REQ** nằm ở tệp nền tảng: [`web/requirements_login_web.md`](web/requirements_login_web.md).

## Bản đồ phủ tài liệu

**Không có tài liệu nào được cung cấp cho module này — toàn bộ 40 REQ sinh từ khảo sát UI thực tế.**

---

## Bản đồ tài liệu

| Tệp | Nội dung | Dải REQ |
|---|---|---|
| `requirements_login.md` *(tệp này)* | Phần cắt ngang: Ma trận phân quyền · Ma trận trạng thái phiên · Ambiguity & Risk · Phân rã Story · Nhật ký thay đổi | — |
| [`web/requirements_login_web.md`](web/requirements_login_web.md) | Toàn bộ REQ của nền tảng Web · Đặc tả trường · Thông báo lỗi · Danh mục Evidence | `REQ-LOGIN-01` → `REQ-LOGIN-40` |
| [`web/evidence/`](web/evidence/) | 18 ảnh bằng chứng khảo sát | — |

> Hiện module chỉ có **một nền tảng**, nên toàn bộ REQ nằm ở tệp `web/`. Khi khảo sát thêm app hoặc API, REQ nào **đúng ở mọi nền tảng** sẽ được chuyển lên tệp index này và **giữ nguyên mã** (skill mục 2.2).

---

## 1. Ma trận Phân quyền

Module này áp cho **mọi** người dùng của khu vực quản trị. Hàng = hành động, cột = vai trò.

| Hành động | Tài khoản hiện có *(nhân viên, không có quyền Setup)* | Super Admin | Vai trò khác | Người liên hệ khách hàng |
|---|---|---|---|---|
| Mở trang đăng nhập | ✅ | ❔ | ❔ | — |
| Đăng nhập thành công vào `/admin` | ✅ | ❔ | ❔ | — |
| Dùng Remember me | ✅ | ❔ | ❔ | — |
| Dùng Quên mật khẩu | ✅ | ❔ | ❔ | — |
| Đăng xuất | ✅ | ❔ | ❔ | — |

```
Tổng 20 ô = Đã kiểm chứng 5 · Suy diễn 0 · Chưa rõ 10 · Không áp dụng 5
```

- Ô **"không áp dụng"** là toàn bộ cột **Người liên hệ khách hàng** — họ đăng nhập ở **cổng khách hàng có URL riêng**, không dùng `/admin/authentication`. Cổng đó chưa được cung cấp (`AMB-LOGIN-11`, `AMB-SYS-03`)
- Cột **Super Admin** và **Vai trò khác** toàn `❔` vì dự án **chỉ có một tài khoản** và màn hình quản lý vai trò bị chặn — xem `AMB-LOGIN-09`
- ⚠️ Ô `❔` **không** đồng nghĩa `❌`. Chưa ai kiểm, không phải "không có quyền"

---

## 2. Ma trận Trạng thái phiên làm việc

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

## 3. Điểm Mơ Hồ (Ambiguities)

| Mã | Câu hỏi | Nguy cơ nếu không giải quyết | Mức độ | Assumption tạm | Trạng thái | Kết luận |
|---|---|---|---|---|---|---|
| AMB-LOGIN-01 | Hệ thống có khoá tài khoản sau N lần đăng nhập sai không? Chỉ kiểm được trên email **không tồn tại** (6 lần đều không bị chặn) — không dám thử trên tài khoản thật vì đó là **tài khoản duy nhất** của dự án | Nếu thực tế có khoá mà tài liệu ghi không, TC sẽ đỏ hàng loạt. Nếu thực sự không có khoá thì đây là lỗ hổng | 🔴 | **Không** có cơ chế khoá và **không** có giới hạn tần suất | ❓ Chờ trả lời | — |
| AMB-LOGIN-02 | Màn hình Quên mật khẩu trả `Email not found` — cố ý hay sơ suất? Trang đăng nhập lại **cố tình** không lộ (`Invalid email or password`). Hai màn hình cùng một module mà ngược chính sách nhau | Nếu là sơ suất thì đây là lỗ hổng dò danh tính cần sửa; nếu cố ý thì cần ghi vào chuẩn để test đúng | 🔴 | Là **sơ suất**, cần thống nhất theo hướng không lộ | ❓ Chờ trả lời | — |
| AMB-LOGIN-03 | Vì sao cookie `autologin` đặt `HttpOnly = false` trong khi `sp_session` lại có? Cookie này **một mình đủ để vào hệ thống** và sống 62 ngày | JavaScript đọc được token đăng nhập dài ngày | 🔴 | Là **sơ suất cấu hình** | ❓ Chờ trả lời | — |
| AMB-LOGIN-04 | Vì sao không ép HTTPS và không đặt HSTS? | Người dùng có thể mở trang đăng nhập qua kênh không mã hoá | 🔴 | Môi trường demo cấu hình đơn giản; môi trường thật phải ép | ❓ Chờ trả lời | — |
| AMB-LOGIN-09 | Hệ thống có những vai trò nào, và mỗi vai trò đăng nhập có khác biệt gì? Chỉ có **một** tài khoản, màn hình quản lý vai trò bị chặn | Ma trận phân quyền mục 1 có 10/20 ô chưa rõ | 🔴 | Mọi vai trò dùng chung một luồng đăng nhập | ❓ Chờ trả lời | Liên quan `AMB-SYS-01`, `AMB-SYS-02` |
| AMB-LOGIN-05 | Thời hạn ~62 ngày của cookie `autologin` là cố ý? Cấu hình ở đâu? | Không biết giá trị đúng thì không viết được TC kiểm hết hạn | 🟡 | 62 ngày là mặc định của sản phẩm | ❓ Chờ trả lời | — |
| AMB-LOGIN-06 | Phiên hết hạn sau bao lâu không hoạt động? | Không viết được TC hết hạn phiên | 🟡 | Theo mặc định của PHP, chưa xác định | ❓ Chờ trả lời | Cấu hình nằm ở vùng Setup bị chặn |
| AMB-LOGIN-07 | Checkbox `Remember me` có `value="estimate"` — có ý nghĩa gì không, hay là lỗi sao chép? | Nếu automation chọn theo `value` sẽ viết ra locator vô nghĩa | 🟡 | Là **lỗi sao chép**, chức năng chỉ phụ thuộc việc trường có được gửi hay không | ❓ Chờ trả lời | — |
| AMB-LOGIN-08 | Đăng xuất trả HTTP **307** thay vì 302/303 — cố ý hay do tầng hạ tầng? | Chưa rõ thì không được assert mã này | 🟡 | Không cố ý; **không** assert mã trạng thái, chỉ assert điểm dừng | ❓ Chờ trả lời | — |
| AMB-LOGIN-10 | Sau khi gửi Quên mật khẩu **thành công** thì màn hình hiện gì, email nội dung ra sao, liên kết đặt lại sống bao lâu? | `REQ-LOGIN-34` đang ⚪, cả nhánh khôi phục mật khẩu chưa có TC | 🟡 | Có gửi email kèm liên kết đặt lại, hạn dùng chưa rõ | ❓ Chờ trả lời | Chưa thử vì **gửi email thật ra ngoài** |
| AMB-LOGIN-11 | Người liên hệ khách hàng đăng nhập ở cổng nào, luồng có khác không? | Cột thứ 4 của ma trận phân quyền để `—`; nhánh xác thực thứ hai của hệ thống chưa ai khảo sát | 🟡 | Là hệ thống đăng nhập **tách biệt**, không dùng `/admin/authentication` | ❓ Chờ trả lời | Liên quan module `CTC` và `AMB-SYS-03` |
| AMB-LOGIN-12 | Khi bỏ trống cả hai ô, thông báo **Password hiện trước Email** — thứ tự này cố ý? | TC assert theo thứ tự sẽ gãy nếu sau này đổi | 🟢 | Thứ tự không thuộc yêu cầu; TC chỉ kiểm **có mặt cả hai**, không kiểm thứ tự | ❓ Chờ trả lời | — |

## 4. Rủi ro (Risks)

| Mã | Rủi ro | Mô tả | Mitigation khi test |
|---|---|---|---|
| RISK-LOGIN-01 | Không có hàng rào chống thử mật khẩu hàng loạt | 6 lần sai liên tiếp không sinh khoá, không CAPTCHA, không độ trễ (REQ-LOGIN-18) | Đưa vào TC bảo mật; đề nghị dev xác nhận có cơ chế ở tầng hạ tầng không. **Không** tự chạy kịch bản hàng nghìn lần trên môi trường này |
| RISK-LOGIN-02 | Token ghi nhớ đăng nhập bị JavaScript đọc được | `autologin` có `HttpOnly = false`, sống 62 ngày, và **một mình đủ để vào hệ thống** (REQ-LOGIN-20, 38). Một lỗ XSS ở bất kỳ module nào cũng lấy được token này | Ưu tiên cao khi test XSS ở các module có ô nhập tự do. Báo dev ngay |
| RISK-LOGIN-03 | Trang đăng nhập phục vụ qua HTTP, không có HSTS | Kẻ tấn công ở giữa có thể sửa trang HTTP, đổi `form.action` sang máy chủ của họ (REQ-LOGIN-35, 36) | Ghi nhận; giảm nhẹ nhờ `form.action` tuyệt đối HTTPS (REQ-LOGIN-37). Đề nghị bật ép HTTPS + HSTS |
| RISK-LOGIN-04 | Màn hình Quên mật khẩu để lộ email nào chưa đăng ký | `Email not found` cho phép dò ngược danh sách email hợp lệ (REQ-LOGIN-30) | Đưa vào TC bảo mật, gắn `AMB-LOGIN-02` |
| RISK-LOGIN-05 | Chỉ có một tài khoản để kiểm thử | Không kiểm được khác biệt giữa các vai trò; 10/20 ô ma trận phân quyền là `❔` | Xin thêm tài khoản trước khi viết TC phân quyền. Không làm tròn `❔` thành `❌` |
| RISK-LOGIN-06 | Thử nghiệm khoá tài khoản có thể khoá chính tài khoản duy nhất | Nếu hệ thống có khoá mà ta thử trên tài khoản thật thì **mất quyền truy cập toàn dự án** | ⛔ **Chỉ** thử trên tài khoản phụ. Có tài khoản thứ hai rồi mới kiểm `AMB-LOGIN-01` |

---

## 5. Phân rã Epic / Story

40 REQ ≥ ngưỡng 25 → bắt buộc có mục này.

| Story ID | Tên Story | REQ bao phủ | Số REQ | AMB / RISK liên quan | Ghi chú phạm vi |
|---|---|---|---|---|---|
| STORY-LOGIN-01 | Giao diện & cấu trúc trang đăng nhập | REQ-LOGIN-01 → 06 | 6 | AMB-LOGIN-07 | Chỉ kiểm hiển thị và cấu trúc, chưa chạm nghiệp vụ |
| STORY-LOGIN-02 | Đăng nhập thành công & cấp phiên | REQ-LOGIN-07 → 09 | 3 | — | Luồng chính, mức độ blocker |
| STORY-LOGIN-03 | Kiểm tra dữ liệu nhập | REQ-LOGIN-10 → 14 | 5 | AMB-LOGIN-12 | Phân biệt rõ validation máy chủ và trình duyệt |
| STORY-LOGIN-04 | Đăng nhập thất bại & chống dò danh tính | REQ-LOGIN-15 → 18 | 4 | AMB-LOGIN-01 · RISK-LOGIN-01 | Có phần bảo mật, cần thận trọng khi chạy |
| STORY-LOGIN-05 | Ghi nhớ đăng nhập | REQ-LOGIN-19 → 21 | 3 | AMB-LOGIN-03, AMB-LOGIN-05 · RISK-LOGIN-02 | Mỗi tác tạo có REQ *tạo ra* và REQ *dùng được* |
| STORY-LOGIN-06 | Đăng xuất | REQ-LOGIN-22 → 25 | 4 | AMB-LOGIN-08 | |
| STORY-LOGIN-07 | Bảo vệ route & điều hướng phiên | REQ-LOGIN-26 → 28, REQ-LOGIN-40 | 4 | — | REQ-LOGIN-40 xếp ở đây vì cùng là hành vi chặn truy cập |
| STORY-LOGIN-08 | Quên mật khẩu | REQ-LOGIN-29 → 34 | 6 | AMB-LOGIN-02, AMB-LOGIN-10 · RISK-LOGIN-04 | `REQ-LOGIN-34` đang ⚪ → TC viết trước, đánh `skip` |
| STORY-LOGIN-09 | Giao thức & thuộc tính bảo mật | REQ-LOGIN-35 → 39 | 5 | AMB-LOGIN-04 · RISK-LOGIN-03 | Kiểm ở tầng giao thức và cookie |

**Dòng tổng kiểm chứng:** `Tổng: 9 Story / 40 REQ — mọi REQ thuộc đúng một Story, không mồ côi, không trùng`
`6 + 3 + 5 + 4 + 3 + 4 + 4 + 6 + 5 = 40 ✔`

### Bảng đối chiếu AMB / RISK

| Nhóm | Mã | Nằm ở đâu |
|---|---|---|
| AMB thuộc Story | AMB-LOGIN-01, 02, 03, 04, 05, 07, 08, 10, 12 | Phân bổ ở bảng Story |
| AMB cấp Epic | AMB-LOGIN-06 | Cấu hình hết hạn phiên — bị chặn bởi vùng Setup, cắt ngang mọi Story |
| AMB cấp Epic | AMB-LOGIN-09 | Ma trận Phân quyền (mục 1) |
| AMB cấp Epic | AMB-LOGIN-11 | Cổng đăng nhập của khách hàng — liên module `CTC` |
| RISK thuộc Story | RISK-LOGIN-01, 02, 03, 04 | Phân bổ ở bảng Story |
| RISK cấp Epic | RISK-LOGIN-05 | Thiếu tài khoản — ảnh hưởng toàn module |
| RISK cấp Epic | RISK-LOGIN-06 | Rủi ro của chính việc kiểm thử — ảnh hưởng toàn module |

`12/12 AMB` và `6/6 RISK` đều có chỗ thuộc về. ✔

### Hạng mục cấp Epic — cố ý không gán vào Story nào

| Hạng mục | Lý do |
|---|---|
| Ma trận Phân quyền (mục 1) | Cắt ngang mọi Story; đang bị chặn bởi `RISK-LOGIN-05` |
| Ma trận Trạng thái phiên (mục 2) | Mô tả quan hệ **giữa** các Story, không thuộc riêng Story nào |
| Yêu cầu phi chức năng (tệp web, mục 7) | Áp cho toàn module |

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
| 8 ⚠️ `BLOCKED` | STORY-LOGIN-04 | **Chặn bởi `AMB-LOGIN-01` + `RISK-LOGIN-06`** — chưa có tài khoản phụ thì không kiểm được khoá tài khoản |
| 9 ⚠️ `BLOCKED` một phần | STORY-LOGIN-08 | `REQ-LOGIN-34` **chặn bởi `AMB-LOGIN-10`** — cần hộp thư nhận được email thật. Năm REQ còn lại chạy được ngay |

---

## 6. Nhật ký thay đổi

| Ngày | Nguồn | REQ ảnh hưởng | Loại | Tóm tắt thay đổi | TC cần xử lý |
|---|---|---|---|---|---|
| 19-09-2026 | UI recon | REQ-LOGIN-01 → 40 | 🟢 Thêm | Khởi tạo tài liệu từ khảo sát UI thực tế trên Chrome viewport `1600×750`. Sinh 40 REQ, 12 AMB, 6 RISK, 9 Story, 18 ảnh evidence. `REQ-LOGIN-34` ở trạng thái ⚪ vì không gửi email thật ra ngoài | — (viết TC mới) |

---

← [Danh mục toàn hệ thống](../README.md)
