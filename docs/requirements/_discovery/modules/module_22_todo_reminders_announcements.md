# Module 22 — To Do (Việc cần làm) · Reminders (Nhắc nhở) · Announcements (Thông báo nội bộ)

← [Về bản đồ hệ thống](../system_map.md)

> Tệp này chứa **3 module** — mức tối đa cho phép. Gộp vì cả ba là **module nhỏ cùng loại**: mỗi cái một màn hình danh sách, đều là thông tin cá nhân/nội bộ, đều risk 🟢.
>
> ⚠️ **Gộp tệp KHÔNG gộp prefix.** `TODO`, `REM`, `ANN` vẫn là 3 prefix riêng, về sau sinh ra 3 thư mục và 3 tệp `requirements_<module>.md` riêng.

---

## 22a. To Do (Việc cần làm) — `TODO`

| Mục | Giá trị |
|---|---|
| **Prefix** | `TODO` |
| **Tên trên website** | To Do — tiêu đề trang "My To Do Items" |
| **Nền tảng** | Web |
| **Risk** | 🟢 Thấp |
| **Ước REQ** | 10–14 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/todo` | Danh sách việc cần làm của tôi |

Biểu tượng trên thanh công cụ mang `title="Todo items"` và hiển thị **số đếm** (quan sát thấy giá trị `2`).

### Quan sát được

**Nút:** `New To Do` · `Save` (trong hộp thoại)

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | ✅ Hoàn thành / Chưa hoàn thành — xác minh qua 2 khối trên trang |
| Bố cục | ✅ **2 khối cạnh nhau**: "Unfinished to do's" và "Latest finished to do's" (không phải tab). Mỗi dòng có tay cầm **kéo–thả**, nút sửa và xoá |

### Phát hiện tầng network của riêng module

`POST /admin/todo` được gọi **2 lần** khi mở trang. ✅ **Đã xác minh nguyên nhân**: trang có 2 khối riêng — "Unfinished to do's" và "Latest finished to do's" — mỗi khối nạp bằng một request.

### Lý do risk 🟢

Dữ liệu cá nhân của chính người dùng, không chia sẻ, không chạm tiền, không module nào phụ thuộc.

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Biểu mẫu tạo việc — số trường | Chưa mở hộp thoại |
| ~~Có tab "Đã xong" riêng không~~ | ✅ **Có 2 khối riêng**, không phải tab |
| Kéo thả đổi thứ tự | ✅ Đã xác minh **có tay cầm kéo–thả**; chưa thử kéo thật |
| Số đếm trên biểu tượng cập nhật thế nào | Chưa thử |

---

## 22b. Reminders (Nhắc nhở) — `REM`

| Mục | Giá trị |
|---|---|
| **Prefix** | `REM` |
| **Tên trên website** | Reminders |
| **Nền tảng** | Web |
| **Risk** | 🟢 Thấp |
| **Ước REQ** | 10–14 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/misc/reminders` | Danh sách nhắc nhở — **không nằm trong sidebar**, vào được bằng URL trực tiếp (đã xác minh) |

Cũng xuất hiện dưới dạng widget "My Reminders" trên Bảng điều khiển, và dạng tab "Reminders" trong chi tiết Khách hàng.

### Quan sát được

**Cột bảng:** `Related to` · `Description` · `Date` · `Remind` · `Is notified?`

Cột `Related to` cho thấy nhắc nhở **gắn vào entity khác** — đây là module cắt ngang.

⭐ **Quy tắc phạm vi hiển thị do chính hệ thống khai**, chép nguyên văn từ evidence:

> Showing your reminders and reminders created by you.

Tức danh sách gồm **nhắc nhở dành cho tôi** cộng **nhắc nhở do tôi tạo cho người khác**. Đây là rule kiểm được, phải thành REQ.

### Phát hiện tầng network của riêng module

`POST /admin/misc/reminders_table` — **không** theo mẫu `/<module>/table`, là một trong ba ngoại lệ đặt tên của hệ thống.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (tạo từ entity cha, không có nút tạo trên màn hình danh sách) |
| Status flow | Cờ `Is notified?` |
| Số tab | 0 |

### Lý do risk 🟢

Không chạm tiền, không chạm dữ liệu khách hàng ngoài phần tham chiếu. Tuy vậy **cắt ngang 8 module** (xem bản đồ phụ thuộc ở index mục 4) → nên kiểm ở mức tích hợp khi recon các module cha.

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Nhắc nhở gắn được vào **những entity nào** | Quan sát thấy tab Reminders trong Khách hàng; các module khác chưa xác minh |
| Cơ chế gửi nhắc (email / thông báo trong hệ thống) | Chưa xác minh |
| Cờ `Is notified?` chuyển khi nào | Chưa xác minh |
| Vì sao module này **không** có trong sidebar | ❔ Có chủ đích hay thiếu sót — cần hỏi PO |

---

## 22c. Announcements (Thông báo nội bộ) — `ANN`

| Mục | Giá trị |
|---|---|
| **Prefix** | `ANN` |
| **Tên trên website** | Announcements |
| **Nền tảng** | Web |
| **Risk** | 🟢 Thấp |
| **Ước REQ** | 6–10 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/announcements` | Danh sách thông báo — **không nằm trong sidebar**, vào được bằng URL trực tiếp (đã xác minh, HTTP 200, tiêu đề trang "Announcements") |

Cũng xuất hiện dưới dạng tab "Announcements" trên Bảng điều khiển.

### Quan sát được

**Cột bảng:** `Name` · `Date` — chỉ 2 cột.

**Nút:** chỉ `Export`. **Không có nút tạo.**

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ❌ với tài khoản hiện tại — **chỉ xem** |
| Status flow | Không |
| Số tab | 0 |

### ⚠️ Ghi chú phân quyền — đã kiểm chứng

Tài khoản hiện tại **xem được nhưng không tạo được** thông báo. Nghi việc tạo thông báo nằm ở vùng Setup đang bị chặn (`AMB-SYS-01`). Module này vì thế là **ví dụ rõ nhất** trong hệ thống về ranh giới phân quyền đọc/ghi — đáng viết TC ngay khi có tài khoản Super Admin để so sánh hai vai trò.

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Màn hình tạo thông báo nằm ở đâu | `AMB-SYS-01` — chưa có quyền |
| Thông báo hiển thị cho ai (nhân viên / khách hàng / cả hai) | Chưa mở chi tiết; liên quan `AMB-SYS-03` |
| Cơ chế đánh dấu đã đọc | Chưa xác minh |
| Vì sao module này **không** có trong sidebar | ❔ Có thể vì tài khoản không có quyền tạo — cần xác minh lại khi có Super Admin |

---

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `TODO` — `/admin/todo` — có ảnh
- `REM` — `/admin/misc/reminders` — có ảnh
- `ANN` — `/admin/announcements` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `TODO` — hộp thoại sau nút `New To Do`

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`todo_list_viewport.png`](../evidence/todo_list_viewport.png) | `TODO` — **2 khối** "Unfinished to do's" (2 mục) và "Latest finished to do's"; có tay cầm kéo–thả, nút sửa/xoá từng dòng |
| [`rem_list_viewport.png`](../evidence/rem_list_viewport.png) | `REM` — **rỗng**; câu mô tả phạm vi *"Showing your reminders and reminders created by you."*; đủ 5 cột |
| [`ann_list_viewport.png`](../evidence/ann_list_viewport.png) | `ANN` — **rỗng**; 2 cột; chỉ có nút `Export`, **không có nút tạo** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
