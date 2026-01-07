---
name: ui-ux-principles
description: Nguyên tắc thiết kế UI/UX cho iOS. Dùng khi thiết kế màn hình, làm sao cho đẹp, user flow thế nào, xử lý loading state, empty state, error state, animation, navigation pattern, accessibility, dark mode, thiết kế form, UX tốt hơn.
allowed-tools: Read, Grep, Glob
---

# UI/UX Design Principles

## Triết lý thiết kế

### Core Values
- **Simple first** - Ưu tiên đơn giản, tránh phức tạp hóa
- **Consistency** - Nhất quán trong toàn bộ app
- **Feedback** - Luôn phản hồi hành động của user
- **Accessibility** - Thiết kế cho mọi người dùng

### Design Style
- Minimalist / Clean
- iOS native feel (theo Human Interface Guidelines)
- Light mode first, hỗ trợ dark mode

## Navigation Patterns

### Cấu trúc chính
- Tab Bar cho navigation chính (tối đa 5 tabs)
- Navigation Stack cho drill-down
- Modal/Sheet cho actions tạm thời

### Quy tắc
- Không quá 3 levels deep trong navigation stack
- Back button luôn visible
- Swipe back gesture enabled

## States & Feedback

### Mọi màn hình phải handle:
| State | Hiển thị |
|-------|----------|
| Loading | Skeleton hoặc ProgressView |
| Empty | Illustration + message + CTA |
| Error | Message + retry button |
| Success | Confirmation + next action |

### User Feedback
- Haptic feedback cho actions quan trọng
- Animation cho state transitions (0.3s default)
- Toast/Snackbar cho confirmations nhẹ

## Interaction Patterns

### Touch Targets
- Minimum 44x44pt cho tappable elements
- Spacing giữa buttons: 8pt minimum

### Gestures
- Tap: Primary action
- Long press: Secondary options
- Swipe: Delete, archive, quick actions
- Pull to refresh: Reload data

### Forms
- Inline validation
- Clear error messages
- Auto-focus next field
- Keyboard type phù hợp

## Visual Hierarchy

### Typography Scale
- Title: Bold, large
- Subtitle: Medium weight
- Body: Regular
- Caption: Small, secondary color

### Spacing System
- Base unit: 8pt
- Sử dụng: 4, 8, 16, 24, 32, 48

### Colors
- Primary: Brand color cho CTAs
- Secondary: Supporting actions
- Destructive: Red cho delete/danger
- Semantic: Success (green), Warning (yellow), Error (red)

## Accessibility

### Bắt buộc
- VoiceOver labels cho mọi interactive elements
- Dynamic Type support
- Contrast ratio tối thiểu 4.5:1
- Không dùng màu làm indicator duy nhất

## Performance UX

### Loading
- Skeleton screens thay vì spinner khi có thể
- Optimistic UI cho actions nhanh
- Lazy loading cho lists dài

### Offline
- Cache data quan trọng
- Hiển thị offline indicator
- Queue actions để sync sau

## Checklist thiết kế màn hình mới

- [ ] Xác định user goal của màn hình
- [ ] Handle đủ 4 states (loading, empty, error, success)
- [ ] Touch targets >= 44pt
- [ ] VoiceOver labels
- [ ] Dark mode compatible
- [ ] Keyboard handling (nếu có input)
- [ ] Loading/error states
- [ ] Animation cho transitions
