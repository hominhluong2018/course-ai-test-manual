# Kỹ thuật sinh test cases
- Khi sinh test cases, phải dựa vào các tài liệu: Requirement, Design, User story, Use case, Flow chart, Wireframe, Mockup.

## Nguyên tắc viết test case tốt
Rõ ràng, cụ thể: Không viết mơ hồ như "kiểm tra chức năng login" → nên viết "Kiểm tra đăng nhập thành công với username/password hợp lệ"
Độc lập: Mỗi test case nên tự chạy được, không phụ thuộc test case khác (trừ khi cố ý theo luồng)
Một mục tiêu/test case: Tránh gộp nhiều kịch bản vào 1 case
Có thể lặp lại (repeatable): Bất kỳ ai làm theo bước cũng ra kết quả giống nhau
Traceable: Liên kết với requirement/user story tương ứng

### Yêu cầu:
- Bao gồm Happy path, Negative case, Edge case (tối thiểu 30% là Negative/Edge case).
- Mỗi Test Case có: Test cases ID, Test Case Name, Preconditions, Steps, Data Input, Expected Result, Level, Is Automation.
- Mã Test Case theo định dạng: TC_<MODULE>_<số thứ tự 3 chữ số>.
- Trình bày dạng bảng Markdown.
- Viết toàn bộ bằng tiếng Việt.