# Module 16 — Tasks (Công việc) · Timesheets (Chấm công)

← [Về bản đồ hệ thống](../system_map.md)

> Tệp này chứa **2 module**. Gộp vì quan hệ **cha–con chặt**: bản ghi chấm công luôn thuộc một công việc, và hai module luôn được khảo sát cùng nhau.
>
> ⚠️ **Gộp tệp KHÔNG gộp prefix.** `TASK` và `TIME` vẫn là 2 prefix, về sau vẫn sinh ra 2 thư mục và 2 tệp `requirements_<module>.md` riêng.

---

## 16a. Tasks (Công việc) — `TASK`

| Mục | Giá trị |
|---|---|
| **Prefix** | `TASK` |
| **Tên trên website** | Tasks |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 40–55 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/tasks` | Danh sách công việc |
| `/admin/tasks/list_tasks` | Danh sách (đường dẫn thay thế) |
| `/admin/tasks/view/{id}` | Chi tiết công việc |
| `/admin/tasks/delete_task/{id}` | Xoá công việc — ⚠️ route xoá, **không thử** |

### Quan sát được

**Cột bảng:** `#` · `Name` · `Status` · `Start Date` · `Due Date` · `Assigned to` · `Tags` · `Priority`

**Nút thanh công cụ:** `New Task` · `Tasks Overview` · `Export` · `Bulk Actions` · `Status` (bộ lọc)

**Trạng thái — đủ 5** (đọc từ khối Tasks Summary trên evidence):

`Not Started 123` · `In Progress 123` · `Testing 0` · `Awaiting Feedback 2` · `Complete 1`

⚠️ `Testing` **không bao giờ xuất hiện** trên dữ liệu mẫu có sẵn — nếu chỉ đọc từ bảng danh sách thì đã bỏ sót. Đây là lý do phải đọc khối tổng hợp chứ không đọc giá trị trên dòng.

**Mức ưu tiên quan sát được:** `Medium` · `High` — tập đầy đủ chưa xác minh (chưa mở dropdown).

Cột `Status` và `Priority` là **dropdown sửa tại chỗ ngay trên danh sách** — cần TC riêng cho luồng đổi trạng thái nhanh này.

Mỗi dòng có hành động tại chỗ: `Start Timer` · `Edit` · `Delete`. Có loại **`Recurring Task`** (công việc lặp lại) — quan sát thấy nhãn này trên nhiều dòng.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + Hành động hàng loạt |
| Status flow | ✅ **Có** — trạng thái + mức ưu tiên |
| Số tab | ❔ chưa mở chi tiết |

### Lý do risk 🔴

- Là **entity nhiều bản ghi nhất hệ thống** — Bảng điều khiển hiển thị `248 / 249` công việc chưa hoàn thành
- Có **công việc lặp lại** chạy theo lịch nền
- Gắn với bộ đếm giờ → dữ liệu chấm công → có thể dẫn tới tính tiền
- Có phân công (`Assigned to`) → chạm tới phân quyền đang bị chặn
- Mức phủ tài liệu ⬜ Trắng

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| ~~Tập trạng thái đầy đủ~~ | ✅ **Đã có đủ 5** từ khối Tasks Summary |
| Tập **mức ưu tiên** đầy đủ | Chưa mở dropdown — chỉ thấy `Medium` và `High` trên dữ liệu có sẵn, **không** được suy ra tập đầy đủ từ đó |
| Cơ chế Công việc lặp lại | Chưa mở |
| Công việc gắn được vào những entity nào | Quan sát thấy gắn vào Dự án; còn lại chưa xác minh |
| Màn hình `Tasks Overview` | Chưa mở |
| Chi tiết công việc — tab, checklist, bình luận, tệp đính kèm | Chưa mở |

---

## 16b. Timesheets (Chấm công) — `TIME`

| Mục | Giá trị |
|---|---|
| **Prefix** | `TIME` |
| **Tên trên website** | Timesheets |
| **Bí danh** | Timer (bộ đếm giờ trên thanh công cụ) |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 18–24 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/staff/timesheets` | Chấm công của tôi — tiêu đề trang hiển thị **"Today"** |
| `/admin/staff/timesheets?view=all` | Tổng quan chấm công toàn hệ thống (nằm trong menu Reports) |

### Quan sát được

**Cột bảng:** `Task` · `Timesheet Tags` · `Start Time` · `End Time` · `Note` · `Related` · `Time (h)` · `Time (decimal)`

**Nút / bộ lọc:** `View all timesheets` · `Today` · `Customer` · `Project` · `Apply` · `Export`

Bộ đếm giờ nằm ngay trên thanh công cụ (`Start Timer`) và lặp lại ở từng dòng công việc.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (ghi nhận, sửa, xoá bản ghi giờ) |
| Status flow | Không |
| Số tab | 0 — dùng bộ lọc khoảng thời gian thay tab |

### Lý do risk 🟡

- Dữ liệu giờ **có thể chuyển thành tiền** qua hoá đơn theo giờ — nhưng đường liên kết đó chưa xác minh, nên chưa xếp 🔴
- Bộ đếm giờ là **trạng thái chạy nền trong phiên** — dễ sinh lỗi khi mở nhiều tab, khi hết phiên, khi bấm Start hai lần
- Có hai đơn vị hiển thị (`Time (h)` và `Time (decimal)`) → nguy cơ làm tròn lệch nhau

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Hành vi khi bấm Start Timer hai lần, hoặc trên hai công việc cùng lúc | Chưa thử |
| Cách `Time (h)` và `Time (decimal)` làm tròn | Chưa có dữ liệu đối chiếu |
| Chấm công có chuyển thành hoá đơn theo giờ không | Chưa mở |
| Màn hình `?view=all` khác gì màn hình của tôi | Chưa mở |
| Quyền xem chấm công của người khác | Chỉ có 1 tài khoản — `AMB-SYS-01` |

---

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng chưa mở được, hoặc cố ý không thử |

**✅ Đã mở thật:**

- `TASK` — `/admin/tasks` — có ảnh
- `TIME` — `/admin/staff/timesheets` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `TASK` — `/admin/tasks/view/{id}` — chi tiết công việc
- `TASK` — `/admin/tasks/list_tasks`
- `TASK` — màn hình sau nút `Tasks Overview`
- `TIME` — `/admin/staff/timesheets?view=all`

**❔ Chưa xác minh được:**

- `TASK` — `/admin/tasks/delete_task/{id}` là **route xoá**, cố ý **không thử**

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`task_list_viewport.png`](../evidence/task_list_viewport.png) | `TASK` — Tasks Summary đủ **5 trạng thái**; `Status` và `Priority` là dropdown **sửa tại chỗ**; nhãn `Recurring Task` trên dòng |
| [`time_timesheets_viewport.png`](../evidence/time_timesheets_viewport.png) | `TIME` — 5 ô chỉ số giờ; nút `View all timesheets`; biểu đồ; bộ lọc `Today` · `Customer` · `Project` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
