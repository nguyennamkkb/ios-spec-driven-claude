---
name: swiftui-architecture
description: Cấu trúc dự án cho SwiftUI. Dùng khi tạo project mới, tạo feature mới, tạo màn hình mới, đặt file ở đâu, đặt tên file thế nào, tổ chức folder, refactor code, tách ViewModel, cấu trúc thư mục chuẩn, MVVM, Clean Architecture.
allowed-tools: Read, Grep, Glob, Bash
---

# SwiftUI MVVM Structure

## Cấu trúc thư mục

```
ProjectName/
├── App/
│   └── ProjectNameApp.swift
├── Core/
│   ├── Extensions/
│   ├── Utilities/
│   └── Network/
├── Features/
│   └── FeatureName/
│       ├── Views/
│       ├── ViewModels/
│       └── Models/
├── Shared/
│   ├── Components/
│   └── Modifiers/
├── Resources/
└── Services/
```

## Quy tắc vàng

### Đặt tên thư mục
- `Features/` - Chứa các module theo tính năng (Authentication, Home, Profile...)
- `Core/` - Code dùng chung toàn app (Extensions, Network, Utilities)
- `Shared/` - UI components tái sử dụng (Buttons, Cards, Modifiers)
- `Services/` - Business logic, API services
- `Resources/` - Assets, Localizable, Info.plist

### Đặt tên file
| Loại | Quy tắc | Ví dụ |
|------|---------|-------|
| View | `[Feature]View.swift` | `LoginView.swift` |
| ViewModel | `[Feature]ViewModel.swift` | `LoginViewModel.swift` |
| Model | `[Tên đối tượng].swift` | `User.swift` |
| Service | `[Tên]Service.swift` | `AuthService.swift` |
| Extension | `[Type]+Extensions.swift` | `String+Extensions.swift` |
| Component | `[Tên mô tả].swift` | `PrimaryButton.swift` |

### Quy tắc tổ chức
1. Mỗi feature là 1 folder riêng trong `Features/`
2. Mỗi feature có 3 subfolder: `Views/`, `ViewModels/`, `Models/`
3. View chỉ chứa UI, không có logic
4. ViewModel chứa state và business logic
5. Model chỉ là data structure
6. Service xử lý API và data persistence

### Ví dụ feature Authentication

```
Features/
└── Authentication/
    ├── Views/
    │   ├── LoginView.swift
    │   └── RegisterView.swift
    ├── ViewModels/
    │   ├── LoginViewModel.swift
    │   └── RegisterViewModel.swift
    └── Models/
        └── User.swift
```
