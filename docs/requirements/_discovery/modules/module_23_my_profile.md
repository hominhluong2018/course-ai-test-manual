# Module 23 — My Profile (Hồ sơ cá nhân & Ngôn ngữ)

← [Về bản đồ hệ thống](../system_map.md)

| Mục | Giá trị |
|---|---|
| **Prefix** | `PROFILE` |
| **Tên trên website** | My Profile · Edit Profile · Language |
| **Nền tảng** | Web |
| **Risk** | 🟡 Trung bình |
| **Ước REQ** | 15–20 |

## Route

| Đường dẫn | Màn hình |
|---|---|
| `/admin/profile` | Hồ sơ của tôi — tiêu đề trang `Profile - <tên hiển thị>` |
| `/admin/profile/{id}` | Hồ sơ của người khác |
| `/admin/profile?notifications=true` | Xem toàn bộ thông báo |
| `/admin/staff/edit_profile` | Sửa hồ sơ |
| `/admin/staff/change_language` | Đổi về ngôn ngữ mặc định hệ thống |
| `/admin/staff/change_language/<ngôn_ngữ>` | Đổi sang ngôn ngữ cụ thể |

## Language (Ngôn ngữ) — **28 lựa chọn**, đã đếm đủ từ DOM

```
System Default · english · chinese · vietnamese · indonesia · bulgarian
portuguese_br · swedish · dutch · persian · turkish · catalan · spanish
portuguese · french · czech · japanese · italian · slovak · russian
polish · greek · finnish · norwegian · german · ukrainian · romanian
```

Ngôn ngữ đang chọn trong phiên khảo sát: `english` (thẻ `<li class="active">`).

⚠️ **Hệ quả cho mọi module khác:** hệ thống đa ngôn ngữ. Mọi TC assert theo **chuỗi hiển thị** đều gãy khi đổi ngôn ngữ. Đây là ràng buộc cấp hệ thống, phải nêu khi viết TC và khi chọn locator (ưu tiên `id`, `name`, `data-*` thay vì text).

## Notifications (Thông báo)

Khay thông báo (chuông) có: nút `Mark all as read`, danh sách thông báo mang `data-notification-id`, liên kết `View all notifications`.

Trong phiên khảo sát quan sát thấy **15 thông báo** đều thuộc loại *"New comment from customer on contract ..."*.

| Tiêu chí | Giá trị |
|---|---|
| CRUD | ✅ (sửa hồ sơ của chính mình) |
| Status flow | Đã đọc / Chưa đọc (thông báo) |
| Số tab | ❔ Trang `/admin/profile` không có `.nav-tabs` — bố cục khác các module khác |

## Lý do risk 🟡

- **Đổi mật khẩu** nằm ở đây → chạm tới xác thực
- **Đổi ngôn ngữ ảnh hưởng toàn hệ thống** — đổi nhầm là mọi TC khác đỏ theo
- Route `/admin/profile/{id}` cho phép xem hồ sơ **người khác** → ranh giới phân quyền, mà phân quyền đang không kiểm chứng được
- Nhưng không chạm tiền, không module nghiệp vụ nào phụ thuộc

## Vùng chưa xác minh

| Hạng mục | Lý do |
|---|---|
| Biểu mẫu `edit_profile` — số trường, có đổi mật khẩu không | Chưa mở |
| `/admin/profile/{id}` xem được hồ sơ của ai | ⚠️ Chưa thử với `id` của người khác — **chỉ đọc nên thử được**, để dành cho tầng recon |
| Đổi ngôn ngữ có dịch đủ 26 module không | Chưa thử. ⚠️ Thử xong **phải đổi lại `english`** |
| Cấu hình nhận thông báo | Nghi nằm ở vùng Setup đang bị chặn — `AMB-SYS-01` |
| Ảnh đại diện — ràng buộc tệp | Chưa mở |

## Mức xác minh của từng route

> Bảng Route ở trên liệt kê **mọi** đường dẫn phát hiện được. Nhưng *đọc được đường dẫn từ một liên kết* **không** đồng nghĩa *đã mở màn hình đó*. Mục này tách bạch hai mức để bước sinh TC sau không tin nhầm.

| Mức | Nghĩa |
|---|---|
| ✅ **Đã mở thật** | Đã điều hướng tới, quan sát DOM, và có ảnh evidence |
| 🔗 **Mới thấy liên kết** | Đường dẫn/nút đọc được trên DOM, **chưa mở màn hình phía sau** — không được coi là đã khảo sát |

**✅ Đã mở thật:**

- `/admin/profile` — có ảnh

**🔗 Mới thấy liên kết, CHƯA mở:**

- `/admin/profile/{id}` — hồ sơ **người khác** (chưa thử với id của người khác)
- `/admin/profile?notifications=true`
- `/admin/staff/edit_profile` — biểu mẫu sửa hồ sơ
- `/admin/staff/change_language/<28 ngôn ngữ>` — mới đếm được từ DOM, **chưa đổi ngôn ngữ**

## Evidence

| Ảnh | Trạng thái đã xác minh |
|---|---|
| [`profile_view_viewport.png`](../evidence/profile_view_viewport.png) | 5 ô chỉ số giờ; thẻ nhân viên (**khối liên hệ đã làm mờ có chủ đích** — email và điện thoại của tài khoản test không được để lọt vào `docs/`); bảng Projects; khay Notifications với `Mark all as read` |

Danh mục đầy đủ: [bản đồ hệ thống mục 6b](../system_map.md#6b-danh-mục-evidence).
