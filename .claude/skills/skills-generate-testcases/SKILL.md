---
name: skills-generate-testcases
description: SSinh bộ Test Case Manual đầy đủ (Happy path, Negative case, Edge case) từ mô tả một chức năng. Dùng khi cần viết Test Case nhanh cho một chức năng cụ thể, có thể dùng ngay sau Skill skills-requirements-analyzer.
---

Đóng vai Senior Manual QA Engineer. Sinh bộ Test Case Manual cho chức năng sau: $ARGUMENTS

Nếu trong hội thoại đã có kết quả phân tích luồng (Happy/Alternate/Exception Path) từ Skill skills-requirements-analyzer, hãy dùng chính kết quả đó làm nền — không phân tích lại từ đầu.

# Kỹ thuật thiết kế test case
Equivalence Partitioning – chia input thành các nhóm tương đương, chỉ test đại diện mỗi nhóm
Boundary Value Analysis – test tại biên (min, max, min-1, max+1)
Decision Table – khi logic có nhiều điều kiện kết hợp
State Transition – khi hệ thống có nhiều trạng thái (VD: đơn hàng: Pending → Confirmed → Shipped)
Error Guessing – dựa kinh nghiệm đoán các trường hợp dễ lỗi

Nhưng mà viết không được cảm tính, áp dụng kỹ thuật phù hợp nhất để sinh test cases

## Phân loại cần cover
Positive case (luồng đúng)
Negative case (input sai, dữ liệu rỗng, ký tự đặc biệt...)
Edge case (biên dữ liệu)
UI/UX case (giao diện, responsive)
Performance/Security cơ bản (nếu trong phạm vi manual)

### Yêu cầu:
- Hãy đọc file rule .claude\rules\rule-generate-testcases.md để nắm được những yêu cầu của tôi để gernerate test case đúng cách
