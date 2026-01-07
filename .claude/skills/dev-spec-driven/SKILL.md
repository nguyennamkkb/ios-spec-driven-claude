---
name: spec-driven-dev
description: Spec-driven development workflow. Dùng khi tạo feature mới, viết requirements, design document, implementation plan, EARS notation, user stories, acceptance criteria, property-based testing.
allowed-tools: Read, Write, Grep, Glob
---

# Spec-Driven Development

## Concept

Mỗi feature cần 3 files trong `.claude/specs/[feature-name]/`:
1. `requirements.md` - User stories + acceptance criteria (EARS)
2. `design.md` - Technical design + **Correctness Properties**
3. `tasks.md` - Implementation plan + **PBT tasks**

## Workflow với User Confirmation

```
Ý tưởng
    ↓
Requirements ──→ [User Review] ──→ Approve?
    ↓                                 ↓ No: Sửa đổi
    ↓ Yes                             ↓
Design ────────→ [User Review] ──→ Approve?
    ↓                                 ↓ No: Sửa đổi
    ↓ Yes                             ↓
Tasks ─────────→ [User Review] ──→ Approve?
    ↓                                 ↓ No: Sửa đổi
    ↓ Yes                             ↓
Implementation ──→ Code ──→ Validate
```

### Confirmation Checkpoints

| Sau khi tạo | Hỏi user |
|-------------|----------|
| `requirements.md` | Tiếp tục design? / Sửa đổi? / Dừng? |
| `design.md` | Tiếp tục tasks? / Sửa đổi? / Dừng? |
| `tasks.md` | Bắt đầu implement? / Sửa đổi? / Dừng? |

## Cấu trúc thư mục
```
.claude/specs/
├── user-authentication/
│   ├── requirements.md
│   ├── design.md
│   └── tasks.md
├── product-catalog/
│   ├── requirements.md
│   ├── design.md
│   └── tasks.md
└── shopping-cart/
    └── ...
```

---

## 1. Requirements Format (EARS Notation)

```markdown
# [Feature Name] - Requirements

## Overview
[Mô tả ngắn feature]

## User Stories

### US-001: [Tên story]
**As a** [role]
**I want** [action]
**So that** [benefit]

#### Acceptance Criteria
- AC-001.1: WHEN [condition] THE SYSTEM SHALL [behavior]
- AC-001.2: WHEN [condition] THE SYSTEM SHALL [behavior]
- AC-001.3: IF [error condition] THEN THE SYSTEM SHALL [error handling]
```

### EARS Patterns
| Pattern | Format | Ví dụ |
|---------|--------|-------|
| Event-driven | WHEN [event] THE SYSTEM SHALL [response] | WHEN user taps login THE SYSTEM SHALL validate credentials |
| State-driven | WHILE [state] THE SYSTEM SHALL [behavior] | WHILE offline THE SYSTEM SHALL show cached data |
| Unwanted | IF [condition] THEN THE SYSTEM SHALL [response] | IF password invalid THEN THE SYSTEM SHALL show error |
| Optional | WHERE [feature enabled] THE SYSTEM SHALL [behavior] | WHERE premium user THE SYSTEM SHALL show advanced features |

---

## 2. Design Format (với Correctness Properties)

```markdown
# [Feature Name] - Technical Design

## Overview
[Giải pháp kỹ thuật]

## Architecture
[Components, data flow]

## Data Models
[Struct definitions]

## Correctness Properties

### Property 1: [Tên property]
**Validates:** AC-001.1, AC-001.2
**Statement:** For any [input type], when [action], then [expected outcome]
**Testable:** Yes - Property-based test

### Property 2: [Tên property]
**Validates:** AC-001.3
**Statement:** For any [invalid input], the system shall [error behavior]
**Testable:** Yes - Property-based test

## Dependencies
[List dependencies]
```

### Property Types
| Type | Mô tả | Ví dụ |
|------|-------|-------|
| Invariant | Luôn đúng sau mọi operation | "Balance >= 0 after any transaction" |
| Round-trip | Encode/decode trả về giá trị gốc | "JSON encode then decode = original" |
| Idempotent | Làm nhiều lần = làm 1 lần | "Delete twice = delete once" |
| Commutative | Thứ tự không quan trọng | "Add A then B = Add B then A" |

---

## 3. Tasks Format (với PBT)

```markdown
# [Feature Name] - Implementation Plan

## Tasks

### Phase 1: Setup
- [ ] 1.1 Create folder structure
  - Refs: Setup

### Phase 2: Core Implementation  
- [ ] 2.1 Implement [Component]
  - Files: `path/to/file.swift`
  - Refs: US-001, AC-001.1

- [ ] 2.2 [PBT] Property test for [Component]
  - Property: Property 1
  - Validates: AC-001.1, AC-001.2
  - Optional: Yes

### Phase 3: Testing
- [ ] 3.1 Unit tests
- [ ] 3.2 [PBT] Property-based tests
  - Optional: Yes

## Progress
| Phase | Total | Done | Status |
|-------|-------|------|--------|
| Setup | 1 | 0 | ⬜ |
| Core | 2 | 0 | ⬜ |
| Testing | 2 | 0 | ⬜ |
```

---

## Traceability

Mọi thứ phải link với nhau:
```
US-001 (User Story)
  └── AC-001.1 (Acceptance Criteria)
        └── Property 1 (Correctness Property)
              └── Task 2.2 [PBT] (Property-based test)
```

## Quy tắc
1. Mỗi feature = 1 folder trong `.claude/specs/`
2. PHẢI có đủ 3 files
3. Design PHẢI có Correctness Properties section
4. Tasks PHẢI có PBT tasks (optional nhưng recommended)
5. Mọi task PHẢI reference AC-xxx
6. Mọi property PHẢI reference AC-xxx

## Quy tắc User Confirmation (QUAN TRỌNG)

### Nguyên tắc chính
- **KHÔNG BAO GIỜ** tự động tạo file tiếp theo mà không hỏi user
- **LUÔN LUÔN** đợi user xác nhận trước khi tiếp tục
- **LUÔN LUÔN** cho user option sửa đổi

### Sau mỗi file, PHẢI hỏi:
```
❓ Bạn muốn:
1. ✅ Tiếp tục bước tiếp theo
2. ✏️ Có yêu cầu sửa đổi
3. ⏸️ Dừng lại, sẽ tiếp tục sau
```

### Flow xử lý response:
- User chọn **Tiếp tục** → Gọi agent tiếp theo
- User chọn **Sửa đổi** → Apply changes → Hỏi lại
- User chọn **Dừng** → Kết thúc, lưu state hiện tại

### Agent Chain:
```
write-spec → [confirm] → write-design → [confirm] → plan-tasks → [confirm] → task-executor
```
