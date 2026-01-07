---
name: swiftui-components
description: Tạo UI components cho SwiftUI. Dùng khi tạo button, card, input field, modal, bottom sheet, loading view, empty state, tạo component từ ảnh design, code UI theo Figma, làm custom view, tạo reusable view.
allowed-tools: Read, Grep, Glob, Bash
---

# SwiftUI Reusable Components

## Quy trình tạo/sửa component

### Bước 1: Đọc file format chuẩn
LUÔN đọc file này trước khi tạo component:
```
Shared/COMPONENT_FORMAT.md
```
File này chứa format code, style guide, và quy tắc của project.

### Bước 2: Tham khảo components hiện có
```
Shared/Components/     # Xem các component đã có
Shared/Styles/         # Xem design tokens
Shared/Modifiers/      # Xem custom modifiers
```

### Bước 3: Tạo file đúng folder

| Loại | Đặt ở đâu |
|------|-----------|
| Button | `Shared/Components/Buttons/` |
| Input | `Shared/Components/Inputs/` |
| Card | `Shared/Components/Cards/` |
| Modal | `Shared/Components/Modals/` |
| Layout | `Shared/Components/Layouts/` |
| Feedback | `Shared/Components/Feedback/` |

## Khi người dùng gửi ảnh UI design

1. Phân tích ảnh để hiểu:
   - Phong cách UI (rounded, sharp, gradient, flat...)
   - Color scheme
   - Typography style
   - Spacing pattern
   - Shadow/elevation style

2. Cập nhật hoặc tạo file `Shared/COMPONENT_FORMAT.md` với:
   - Code template cho component
   - Quy tắc style rút ra từ design
   - Ví dụ cụ thể

3. Cập nhật `Shared/Styles/` nếu cần:
   - AppColors.swift
   - AppFonts.swift
   - AppSpacing.swift

## File COMPONENT_FORMAT.md

Đây là file quan trọng nhất - chứa format chuẩn của project.
Nếu chưa có, tạo mới khi người dùng gửi ảnh design đầu tiên.
Nếu đã có, đọc và follow theo.

Nội dung file bao gồm:
- Code template cho struct View
- Quy tắc đặt tên
- Style conventions (corner radius, shadows, colors...)
- Ví dụ component mẫu

## Checklist

- [ ] Đọc `Shared/COMPONENT_FORMAT.md` trước
- [ ] Kiểm tra components hiện có trong `Shared/Components/`
- [ ] Dùng design tokens từ `Shared/Styles/`
- [ ] Tạo file đúng folder theo loại
- [ ] Follow đúng format trong COMPONENT_FORMAT.md
- [ ] Thêm Preview cho component
