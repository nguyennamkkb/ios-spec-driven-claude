---
name: figma-mcp
description: Tích hợp Figma để lấy design specs. Dùng khi cần lấy màu sắc, typography, spacing từ Figma, export component specs, sync design tokens, implement UI theo Figma design.
allowed-tools: Read, Write, MCP
---

# Figma MCP Integration

## Mục đích
Lấy design specs từ Figma để implement UI chính xác theo design.

## MCP Tools Available

### 1. figma_get_file
Lấy thông tin file Figma.

**Input:**
- fileKey: ID của file Figma (từ URL)

**Output:**
- File metadata
- Document structure
- All pages và frames

**Khi dùng:**
- Khám phá cấu trúc file design
- Lấy danh sách screens/components

### 2. figma_get_node
Lấy chi tiết 1 node cụ thể (frame, component, screen).

**Input:**
- fileKey: ID của file Figma
- nodeId: ID của node (từ URL hoặc figma_get_file)

**Output:**
- Layout properties (width, height, padding, spacing)
- Colors (fill, stroke)
- Typography (font, size, weight, line height)
- Effects (shadows, blur)
- Children nodes

**Khi dùng:**
- Implement 1 screen cụ thể
- Lấy specs của 1 component

### 3. figma_get_styles
Lấy design tokens (colors, typography, effects).

**Input:**
- fileKey: ID của file Figma

**Output:**
- Color styles
- Text styles
- Effect styles

**Khi dùng:**
- Setup design system
- Tạo `Shared/Styles/` files
- Sync design tokens

### 4. figma_export_image
Export hình ảnh từ node.

**Input:**
- fileKey: ID của file Figma
- nodeId: ID của node
- format: png | jpg | svg | pdf
- scale: 1x, 2x, 3x

**Output:**
- Image URL

**Khi dùng:**
- Export icons
- Export illustrations
- Export assets cho app

---

## Workflow

### Setup Design System (1 lần đầu)

1. **Lấy styles từ Figma:**
```
Tool: figma_get_styles
Input: fileKey
```

2. **Tạo AppColors.swift:**
```swift
import SwiftUI

extension Color {
    // Primary Colors
    static let primary = Color(hex: "#007AFF")
    static let secondary = Color(hex: "#5856D6")
    
    // Semantic Colors
    static let success = Color(hex: "#34C759")
    static let warning = Color(hex: "#FF9500")
    static let error = Color(hex: "#FF3B30")
    
    // Neutral Colors
    static let textPrimary = Color(hex: "#000000")
    static let textSecondary = Color(hex: "#8E8E93")
    static let background = Color(hex: "#FFFFFF")
}

extension Color {
    init(hex: String) {
        let scanner = Scanner(string: hex)
        scanner.currentIndex = hex.hasPrefix("#") ? hex.index(after: hex.startIndex) : hex.startIndex
        var rgbValue: UInt64 = 0
        scanner.scanHexInt64(&rgbValue)
        
        let r = Double((rgbValue & 0xFF0000) >> 16) / 255.0
        let g = Double((rgbValue & 0x00FF00) >> 8) / 255.0
        let b = Double(rgbValue & 0x0000FF) / 255.0
        
        self.init(red: r, green: g, blue: b)
    }
}
```

3. **Tạo AppFonts.swift:**
```swift
import SwiftUI

extension Font {
    // Headings
    static let h1 = Font.system(size: 34, weight: .bold)
    static let h2 = Font.system(size: 28, weight: .bold)
    static let h3 = Font.system(size: 22, weight: .semibold)
    
    // Body
    static let body = Font.system(size: 17, weight: .regular)
    static let bodyBold = Font.system(size: 17, weight: .semibold)
    
    // Small
    static let caption = Font.system(size: 13, weight: .regular)
    static let footnote = Font.system(size: 11, weight: .regular)
}
```

4. **Tạo AppSpacing.swift:**
```swift
import SwiftUI

enum Spacing {
    static let xs: CGFloat = 4
    static let sm: CGFloat = 8
    static let md: CGFloat = 16
    static let lg: CGFloat = 24
    static let xl: CGFloat = 32
    static let xxl: CGFloat = 48
}
```

5. **Update COMPONENT_FORMAT.md:**
```markdown
# Component Format

## Colors
Use `Color.primary`, `Color.secondary`, etc. from AppColors.swift

## Typography
Use `Font.h1`, `Font.body`, etc. from AppFonts.swift

## Spacing
Use `Spacing.md`, `Spacing.lg`, etc. from AppSpacing.swift

## Shadows
```swift
.shadow(color: .black.opacity(0.1), radius: 8, x: 0, y: 2)
```

## Corner Radius
- Small: 8pt
- Medium: 12pt
- Large: 16pt
```

### Implement Screen từ Figma

1. **Lấy file structure:**
```
Tool: figma_get_file
Input: fileKey
→ Tìm nodeId của screen cần implement
```

2. **Lấy chi tiết screen:**
```
Tool: figma_get_node
Input: fileKey, nodeId
→ Phân tích layout, colors, typography
```

3. **Map Figma → SwiftUI:**

| Figma | SwiftUI |
|-------|---------|
| Frame | VStack/HStack/ZStack |
| Auto Layout (vertical) | VStack(spacing: X) |
| Auto Layout (horizontal) | HStack(spacing: X) |
| Padding | .padding(.all, X) |
| Fill | .background(Color) |
| Stroke | .border(Color, width: X) |
| Corner Radius | .cornerRadius(X) |
| Shadow | .shadow(...) |
| Text | Text().font().foregroundColor() |

4. **Implement View:**
```swift
struct LoginView: View {
    var body: some View {
        VStack(spacing: Spacing.lg) {
            // Header
            Text("Welcome Back")
                .font(.h1)
                .foregroundColor(.textPrimary)
            
            // Input fields
            VStack(spacing: Spacing.md) {
                TextField("Email", text: $email)
                    .textFieldStyle(PrimaryTextFieldStyle())
                
                SecureField("Password", text: $password)
                    .textFieldStyle(PrimaryTextFieldStyle())
            }
            
            // CTA
            Button("Login") {
                // Action
            }
            .buttonStyle(PrimaryButtonStyle())
        }
        .padding(Spacing.xl)
    }
}
```

### Export Assets

1. **Export icons:**
```
Tool: figma_export_image
Input: 
  fileKey: [key]
  nodeId: [icon node id]
  format: pdf
  scale: 1
→ Lưu vào Assets.xcassets
```

2. **Export illustrations:**
```
Tool: figma_export_image
Input:
  fileKey: [key]
  nodeId: [illustration node id]
  format: png
  scale: 3
→ Lưu vào Assets.xcassets
```

---

## Lấy Figma File Key

Từ URL Figma:
```
https://www.figma.com/file/ABC123DEF456/MyDesign
                              ↑
                         File Key
```

Từ URL với node:
```
https://www.figma.com/file/ABC123DEF456/MyDesign?node-id=123:456
                              ↑                              ↑
                         File Key                        Node ID
```

---

## Checklist

### Khi setup project mới:
- [ ] Chạy `figma_get_styles` để lấy design tokens
- [ ] Tạo `Shared/Styles/AppColors.swift`
- [ ] Tạo `Shared/Styles/AppFonts.swift`
- [ ] Tạo `Shared/Styles/AppSpacing.swift`
- [ ] Tạo/Update `Shared/COMPONENT_FORMAT.md`

### Khi implement screen mới:
- [ ] Lấy nodeId từ Figma URL
- [ ] Chạy `figma_get_node` để lấy specs
- [ ] Map layout Figma → SwiftUI structure
- [ ] Dùng design tokens từ `Shared/Styles/`
- [ ] Export assets nếu cần

### Khi tạo component:
- [ ] Check `COMPONENT_FORMAT.md` trước
- [ ] Dùng design tokens
- [ ] Follow naming conventions
- [ ] Add Preview

---

## Tips

1. **Figma Auto Layout → SwiftUI:**
   - Vertical = VStack
   - Horizontal = HStack
   - Absolute = ZStack

2. **Spacing:**
   - Figma padding → SwiftUI .padding()
   - Figma gap → SwiftUI spacing parameter

3. **Colors:**
   - Luôn dùng từ AppColors.swift
   - Không hardcode hex trong View

4. **Typography:**
   - Luôn dùng từ AppFonts.swift
   - Không hardcode font size

5. **Responsive:**
   - Figma fixed width → SwiftUI .frame(width:)
   - Figma fill container → SwiftUI .frame(maxWidth: .infinity)
