# Module 15 — Projects (Dự án)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `PRJ` |
| **Tên trên website** | Projects |
| **Nền tảng** | Web |
| **Risk** | 🔴 Cao |
| **Ước REQ** | 60–80 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/projects` | Danh sách dự án |
| `/admin/projects/project` | Tạo dự án mới |
| `/admin/projects/view/{id}` | Chi tiết dự án — **18 tab** |

## Projects — màn hình danh sách

**Cột bảng:** `#` · `Project Name` · `Customer` · `Tags` · `Start Date` · `Deadline` · `Members` · `Status`

**Nút thanh công cụ:** `New Project` · `Export` · `Copy Project`

## Project detail (chi tiết dự án) — 18 tab, đã đọc trên DOM

```
Overview · Tasks · Timesheets · Milestones · Files · Discussions · Gantt
Tickets · Contracts · Sales (nhóm) · Proposals · Estimates · Invoices
Subscriptions · Expenses · Credit Notes · Notes · Activity
```

`Sales` là **tab nhóm** gom Proposals · Estimates · Invoices · Subscriptions · Expenses · Credit Notes.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ + **Sao chép dự án** |
| Status flow | ✅ **Có** — cột `Status` |
| Số tab | 18 |

## Lý do risk 🔴

- **Là nút tổng hợp thứ hai của hệ thống** (sau `CUST`) — 6 module tài chính đều hiển thị ngược vào đây
- Có **Sao chép dự án** — thao tác nhân bản hàng loạt, lỗi ở đây sinh dữ liệu rác quy mô lớn
- Có `Members` → chạm tới phân quyền theo thành viên dự án, mà phân quyền lại đang bị chặn (`AMB-SYS-01`)
- Có Mốc (Milestones) và biểu đồ Gantt → phụ thuộc ngày tháng
- Có Thảo luận (Discussions) → nghi có tương tác với cổng Khách hàng
- Mức phủ tài liệu ⬜ Trắng

## Phụ thuộc

Cần `CUST` đã recon xong. **Phải recon TRƯỚC** `TASK` và `TIME`.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Nội dung từng tab trong 18 tab | Tầng khám phá chỉ đếm tab |
| ~~Tập trạng thái đầy đủ của dự án~~ | ✅ **Đã có đủ 5** từ khối Projects Summary: `Not Started 73` · `In Progress 70` · `On Hold 17` · `Cancelled 2` · `Finished 1` (tổng 163) |
| 🟠 **Số ngày còn lại hiển thị ÂM** | Evidence `prj_detail_tabs_viewport.png` cho thấy ô `0 / -699 Days Left` với `Deadline 14-06-2024` (quá khứ). Nghi lỗi hiển thị khi quá hạn — **phải kiểm ở tầng module**, đây là ứng viên bug |
| Biểu mẫu tạo dự án — số trường, ràng buộc | Chưa mở |
| Hành vi Sao chép dự án — chép những gì, bỏ những gì | ⚠️ Chưa mở. Cần kiểm kỹ, đây là nguồn lỗi hay gặp |
| Phân quyền theo `Members` | Chỉ có 1 tài khoản — `AMB-SYS-01` |
| Thảo luận có hiển thị cho khách hàng không | Liên quan `AMB-SYS-03` |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/projects` — có ảnh
- `/admin/projects/view/{id}` — có ảnh (mở 1 bản ghi ở chế độ xem)

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/projects/project` — biểu mẫu tạo mới
- Màn hình sau nút `Copy Project`
- 17 tab còn lại trong chi tiết dự án — chỉ thấy nhãn tab, chưa mở từng tab

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`prj_list_viewport.png`](../evidence/prj_list_viewport.png) | Projects Summary đủ **5 trạng thái** (73/70/17/2/1 = 163); đủ 8 cột; cột `Members` hiển thị avatar |
| [`prj_detail_tabs_viewport.png`](../evidence/prj_detail_tabs_viewport.png) | 12 tab ngang với `Sales ▾` là **tab nhóm**; badge `On Hold`; nút `New Task` · `Invoice Project` · `More`; ô **`0 / -699 Days Left`** |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
