# Module 21 — Dashboard (Bảng điều khiển) · Search (Tìm kiếm toàn cục)

← [Về bản đồ hệ thống](../system_map.md)

> Tệp này chứa **2 module**. Gộp vì cả hai là **thành phần khung giao diện** xuất hiện trên mọi trang, không thuộc phân hệ nghiệp vụ nào.
>
> ⚠️ **Gộp tệp KHÔNG gộp prefix.** `DASH` và `SEARCH` vẫn là 2 prefix riêng.

---

## 21a. Dashboard (Bảng điều khiển) — `DASH`

| Mục | Giá trị |
|---|---|
| **Prefix** | `DASH` |
| **Tên trên website** | Dashboard |
| **Nền tảng** | Web |
| **Risk** | 🟢 Thấp |
| **Ước REQ** | 8–12 |

### Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/` | Bảng điều khiển |
| `/admin/staff/reset_dashboard` | Đặt lại bố cục bảng điều khiển |

### Quan sát được

**Nút:** `Dashboard Options` — cho phép tuỳ biến widget hiển thị.

**Các khối widget:**

| Widget | Nội dung quan sát được |
|---|---|
| Invoice overview | 6 trạng thái hoá đơn kèm số lượng và phần trăm |
| Estimate overview | 6 trạng thái báo giá kèm số lượng và phần trăm |
| Proposal overview | 6 trạng thái đề xuất kèm số lượng và phần trăm |
| Bộ chọn năm | `2026` · `2025` · `2024` |
| Chỉ số tài chính | Outstanding Invoices · Past Due Invoices · Paid Invoices |
| Chỉ số tỷ lệ | Invoices Awaiting Payment · Converted Leads · Projects In Progress · Tasks Not Finished |
| Bảng cá nhân | My Tasks · My Projects · My Reminders · Tickets · Announcements |

⭐ **Giá trị lớn nhất của widget này cho việc recon:** ba khối overview là nơi **duy nhất** đọc được **tập trạng thái đầy đủ** của `INV`, `EST`, `PROP` mà không cần mở từng bộ lọc.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ❌ — chỉ đọc + tuỳ biến bố cục |
| Status flow | Không |
| Số tab | 5 tab trong bảng cá nhân |

### Lý do risk 🟢

- Chỉ đọc, không ghi dữ liệu nghiệp vụ
- Không module nào phụ thuộc vào nó
- Nhưng là **màn hình đầu tiên mọi người nhìn thấy** → xứng đáng có TC smoke

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| `Dashboard Options` cho bật/tắt những widget nào | Chưa mở |
| Số liệu widget có khớp dữ liệu gốc không | Cần recon module nguồn trước |
| Bộ chọn năm ảnh hưởng widget nào | Chưa thử |
| Bảng cá nhân lọc theo người đăng nhập thế nào | Chỉ có 1 tài khoản — `AMB-SYS-01` |
| ❔ **Bảng tin ("Share documents, ideas..")** | Phần tử `.open_newsfeed` **có thật** trong DOM. Route đoán `/admin/newsfeed` trả **404** → là panel mở tại chỗ, **chưa mở thử**. Chưa đủ căn cứ để cấp prefix riêng |

---

## 21b. Search (Tìm kiếm toàn cục) — `SEARCH`

| Mục | Giá trị |
|---|---|
| **Prefix** | `SEARCH` |
| **Tên trên website** | *(ô tìm kiếm, không có nhãn)* |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 8–12 |

### Vị trí

**Không có route riêng** — là widget trên thanh công cụ, xuất hiện ở **mọi** trang.

| Phần tử | Định danh đọc từ DOM |
|---|---|
| Ô nhập | `#search_input`, `type="search"`, `placeholder="Search..."`, `autocomplete="off"` |
| Nút tìm | `#top_search_button` |
| Khung kết quả | `#search_results` |
| Vùng bọc | `#top_search` |

**Gợi ý sử dụng đọc được nguyên văn từ thuộc tính `data-title`:**

> Use # + tagname to search by tags

→ Có **cú pháp tìm theo thẻ** bằng tiền tố `#`. Đây là một hành vi riêng, phải có TC.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ❌ |
| Status flow | Không |
| Số tab | 0 |

### Lý do risk 🟡

- **Cắt ngang toàn hệ thống** — tìm được ra thứ lẽ ra không được thấy là lỗi phân quyền, mà phân quyền đang không kiểm chứng được (`AMB-SYS-01`)
- Có cú pháp đặc biệt (`#tag`) → có nhánh xử lý riêng, dễ sót
- Ô nhập tự do → cần kiểm ký tự đặc biệt

### Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Tìm được trong những module nào | Chưa gõ thử |
| Kết quả hiển thị dạng gì, giới hạn bao nhiêu dòng | Chưa gõ thử |
| Cú pháp `#tag` hoạt động thế nào | Chưa thử |
| Kết quả có tôn trọng phân quyền không | Chỉ có 1 tài khoản — `AMB-SYS-01` |
| Hành vi với chuỗi rỗng, ký tự đặc biệt, chuỗi rất dài | Chưa thử |

---

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |
| ❔ **Chưa xác minh được** | Có dấu hiệu tồn tại nhưng chưa mở được, hoặc cố ý không thử |

**✅ Đã mở thật:**

- `DASH` — `/admin/` — có ảnh
- `SEARCH` — widget `#top_search` — có ảnh (chụp phần tử, trạng thái rỗng)

**🔗 Mới thấy liên kết, CHƯA mở:**

- `DASH` — `/admin/staff/reset_dashboard`
- `DASH` — hộp thoại sau nút `Dashboard Options`
- `SEARCH` — **trạng thái có kết quả**: chưa gõ chữ nào vào ô tìm kiếm

**❔ Chưa xác minh được:**

- `DASH` — **Bảng tin** ("Share documents, ideas.."): phần tử `.open_newsfeed` có thật trong DOM, `/admin/newsfeed` trả **404** → là panel mở tại chỗ, **chưa mở thử**

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`dash_overview_viewport.png`](../evidence/dash_overview_viewport.png) | `DASH` — 3 khối overview đủ 6 trạng thái mỗi khối; bộ chọn năm `2026`; 3 ô chỉ số tiền; nút `Dashboard Options` |
| [`search_widget_default_element.png`](../evidence/search_widget_default_element.png) | `SEARCH` — chụp **phần tử** `#top_search`: ô `Search...` + nút kính lúp, trạng thái rỗng |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
