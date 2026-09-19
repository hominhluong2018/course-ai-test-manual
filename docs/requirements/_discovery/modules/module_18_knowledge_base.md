# Module 18 — Knowledge Base (Cơ sở tri thức)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `KB` |
| **Tên trên website** | Knowledge Base |
| **Bí danh** | Articles |
| **Nền tảng** | Web |
| **Risk** | 🟢 Thấp |
| **Ước REQ** | 15–20 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/knowledge_base` | Danh sách bài viết + quản lý Nhóm |
| `/admin/knowledge_base/article` | Tạo bài viết mới |
| `/admin/reports/knowledge_base_articles` | Báo cáo bài viết (thuộc module `RPT`) |

## Quan sát được

**Cột bảng:** `Article Name` · `Group` · `Date Published`

**Nút thanh công cụ:** `New Article` · `Groups` · `Export`

Bảng chỉ có **3 cột** — module nhỏ nhất trong nhóm nội dung.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ đầy đủ |
| Status flow | Không có cột trạng thái trên bảng — ❔ nghi có cờ Công khai/Nháp trong biểu mẫu, chưa xác minh |
| Entity con | **Nhóm bài viết** — quản lý ngay trong màn hình này |

## Lý do risk 🟢

- Nội dung tĩnh, không chạm tiền, không chạm dữ liệu cá nhân
- Không module nào phụ thuộc vào nó (trừ 1 báo cáo đọc số liệu)
- Ít trường, ít đường đi

⚠️ Một điểm cần lưu ý dù risk thấp: bài viết **có thể hiển thị công khai cho khách hàng** — nếu đúng thì việc đăng nhầm bài nháp là lỗi lộ thông tin. Cần xác minh ở tầng recon.

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Có cờ Công khai / Nháp không | Chưa mở biểu mẫu tạo bài |
| Bài viết hiển thị ở đâu cho khách hàng | Liên quan cổng Khách hàng — `AMB-SYS-03` |
| Trình soạn thảo có những gì (ảnh, tệp đính kèm, HTML) | Chưa mở |
| Xếp thứ tự bài viết và nhóm | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/knowledge_base` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/knowledge_base/article` — biểu mẫu tạo bài
- `/admin/reports/knowledge_base_articles` — báo cáo (thuộc `RPT`)
- Màn hình sau nút `Groups`

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`kb_list_viewport.png`](../evidence/kb_list_viewport.png) | Danh sách **rỗng**; đủ 3 cột; nút `New Article` · `Groups` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
