---
name: xcode-mcp
description: Build và test iOS app với Xcode. Dùng khi cần build project, run tests, check errors, get build logs, list schemes, clean build, archive app.
allowed-tools: Read, MCP, Bash
---

# Xcode MCP Integration

## Mục đích
Build, test, và debug iOS app thông qua Xcode command line tools.

## MCP Tools Available

### 1. xcode_list_schemes
List tất cả schemes trong project.

**Input:** None (tự động detect project)

**Output:**
- Danh sách schemes
- Default scheme

**Khi dùng:**
- Lần đầu setup project
- Trước khi build để biết scheme name

**Example:**
```
Tool: xcode_list_schemes
→ Output: ["MyApp", "MyAppTests", "MyAppUITests"]
```

### 2. xcode_build
Build project hoặc workspace.

**Input:**
- scheme: Tên scheme (required)
- configuration: Debug | Release (default: Debug)
- destination: Simulator hoặc device (optional)
- clean: true | false (default: false)

**Output:**
- Build status (success/failed)
- Errors và warnings
- Build time

**Khi dùng:**
- Sau khi implement tasks
- Trước khi commit
- Kiểm tra syntax errors

**Example:**
```
Tool: xcode_build
Input:
  scheme: "MyApp"
  configuration: "Debug"
  clean: false
```

### 3. xcode_test
Run unit tests và UI tests.

**Input:**
- scheme: Tên scheme (required)
- testPlan: Test plan name (optional)
- only: Specific test class/method (optional)

**Output:**
- Test results (passed/failed)
- Failed test details
- Code coverage (nếu enabled)

**Khi dùng:**
- Sau khi viết tests
- Trước khi commit
- CI/CD pipeline

**Example:**
```
Tool: xcode_test
Input:
  scheme: "MyApp"
  only: "LoginViewModelTests"
```

### 4. xcode_clean
Clean build folder.

**Input:**
- scheme: Tên scheme (required)

**Output:**
- Clean status

**Khi dùng:**
- Build errors không rõ nguyên nhân
- Sau khi thay đổi dependencies
- Trước khi archive

### 5. xcode_get_build_settings
Lấy build settings của project.

**Input:**
- scheme: Tên scheme (required)
- configuration: Debug | Release (optional)

**Output:**
- All build settings
- Paths, flags, versions

**Khi dùng:**
- Debug build issues
- Check configuration

---

## Workflow

### Setup Project (lần đầu)

1. **List schemes:**
```
Tool: xcode_list_schemes
→ Lưu scheme name để dùng sau
```

2. **Test build:**
```
Tool: xcode_build
Input:
  scheme: [scheme name]
  configuration: Debug
```

3. **Nếu có errors:**
- Đọc error messages
- Fix theo skill `xcode-debug`
- Build lại

### Development Workflow

#### Sau mỗi task implementation:

1. **Build:**
```
Tool: xcode_build
Input:
  scheme: [scheme name]
  configuration: Debug
```

2. **Check results:**
- ✅ Success → Continue
- ❌ Failed → Fix errors → Build lại

#### Sau mỗi phase completion:

1. **Clean build:**
```
Tool: xcode_clean
Input:
  scheme: [scheme name]
```

2. **Full build:**
```
Tool: xcode_build
Input:
  scheme: [scheme name]
  configuration: Debug
  clean: true
```

3. **Run tests:**
```
Tool: xcode_test
Input:
  scheme: [scheme name]
```

### Testing Workflow

#### Run all tests:
```
Tool: xcode_test
Input:
  scheme: [scheme name]
```

#### Run specific test class:
```
Tool: xcode_test
Input:
  scheme: [scheme name]
  only: "LoginViewModelTests"
```

#### Run specific test method:
```
Tool: xcode_test
Input:
  scheme: [scheme name]
  only: "LoginViewModelTests/testLoginSuccess"
```

### Pre-Commit Checklist

```
1. xcode_clean
2. xcode_build (clean: true)
3. xcode_test
4. All pass → git commit
```

---

## Error Handling

### Common Build Errors

#### 1. Syntax Error
```
Error: Expected '}' at end of closure
File: LoginView.swift:45
```
**Fix:** Đọc file, sửa syntax

#### 2. Type Mismatch
```
Error: Cannot convert value of type 'String' to expected argument type 'Int'
File: ViewModel.swift:23
```
**Fix:** Check type, convert nếu cần

#### 3. Missing Import
```
Error: Cannot find type 'Combine' in scope
File: ViewModel.swift:1
```
**Fix:** Add `import Combine`

#### 4. Unresolved Identifier
```
Error: Cannot find 'authService' in scope
File: LoginViewModel.swift:15
```
**Fix:** Check property declaration, dependency injection

### Common Test Errors

#### 1. Test Timeout
```
Error: Asynchronous wait failed: Exceeded timeout of 1 seconds
Test: testLoginSuccess
```
**Fix:** Increase timeout hoặc check async logic

#### 2. Assertion Failed
```
Error: XCTAssertEqual failed: ("actual") is not equal to ("expected")
Test: testUserRoundTrip
```
**Fix:** Check logic, update test hoặc code

#### 3. Missing Test Target
```
Error: Test target 'MyAppTests' not found
```
**Fix:** Check scheme includes test target

---

## Build Configurations

### Debug vs Release

| Setting | Debug | Release |
|---------|-------|---------|
| Optimization | None (-Onone) | Aggressive (-O) |
| Debug Symbols | Yes | No |
| Assertions | Enabled | Disabled |
| Build Time | Fast | Slow |
| App Size | Large | Small |
| Performance | Slow | Fast |

**Khi dùng:**
- Debug: Development, testing
- Release: Production, App Store

### Destinations

```swift
// iOS Simulator
destination: "platform=iOS Simulator,name=iPhone 15 Pro"

// iOS Device
destination: "platform=iOS,id=[UDID]"

// Generic iOS
destination: "generic/platform=iOS"
```

---

## Integration với Task Executor

### Phase Completion Flow

```
Complete all tasks in Phase X
    ↓
xcode_clean
    ↓
xcode_build (clean: true)
    ↓
Success? → xcode_test → git commit
    ↓ No
Fix errors → Repeat
```

### Example trong task-executor:

```markdown
## Phase 2 Complete

1. Clean build:
   Tool: xcode_clean
   Input: scheme: "MyApp"
   Status: ✅

2. Build:
   Tool: xcode_build
   Input: 
     scheme: "MyApp"
     configuration: Debug
     clean: true
   Status: ✅ Success (12.3s)
   Warnings: 0
   Errors: 0

3. Tests:
   Tool: xcode_test
   Input: scheme: "MyApp"
   Status: ✅ 15/15 passed
   Coverage: 87%

4. Commit:
   git commit -m "feat(auth): Complete Phase 2 - Core Logic"
```

---

## Tips

### 1. Build Performance
- Dùng `clean: false` khi develop
- Chỉ `clean: true` khi:
  - Phase completion
  - Pre-commit
  - Build errors không rõ

### 2. Test Performance
- Run specific tests khi develop
- Run all tests khi:
  - Phase completion
  - Pre-commit
  - CI/CD

### 3. Error Messages
- Đọc kỹ file path và line number
- Fix từ trên xuống (first error first)
- Build lại sau mỗi fix

### 4. Parallel Builds
- Xcode tự động parallel
- Không cần config thêm

### 5. Derived Data
- Xcode MCP tự quản lý
- Không cần manual clean thường xuyên

---

## Checklist

### Mỗi lần build:
- [ ] Scheme name đúng
- [ ] Configuration phù hợp (Debug/Release)
- [ ] Đọc errors nếu có
- [ ] Fix errors từ trên xuống

### Mỗi lần test:
- [ ] Build pass trước
- [ ] Scheme includes test targets
- [ ] Đọc failed test details
- [ ] Check coverage nếu cần

### Pre-commit:
- [ ] Clean build
- [ ] All tests pass
- [ ] No warnings (hoặc acceptable)
- [ ] Code coverage acceptable
