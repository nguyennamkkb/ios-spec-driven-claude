---
name: xcode-debug
description: Debug và fix lỗi iOS/Swift. Dùng khi có compile error, runtime error, crash, memory leak, performance issue, SwiftUI preview không chạy, build failed.
allowed-tools: Read, Edit, Grep, Glob
---

# Xcode Debug Guide

## Mục đích
Hướng dẫn debug và fix các lỗi thường gặp khi develop iOS app.

---

## Common Compile Errors

### 1. Syntax Errors

#### Missing Brace/Bracket
```
Error: Expected '}' at end of closure
```
**Fix:**
- Đếm số lượng `{` và `}`
- Dùng Xcode auto-indent để detect
- Check nested closures

#### Missing Comma
```
Error: Expected ',' separator
```
**Fix:**
- Check array/dictionary literals
- Check function parameters

#### Semicolon (từ thói quen ngôn ngữ khác)
```
Error: Consecutive statements on a line must be separated by ';'
```
**Fix:**
- Swift KHÔNG cần semicolon
- Xóa semicolon

### 2. Type Errors

#### Type Mismatch
```
Error: Cannot convert value of type 'String' to expected argument type 'Int'
```
**Fix:**
```swift
// Bad
let age: Int = "25"

// Good
let age: Int = Int("25") ?? 0
```

#### Optional Unwrapping
```
Error: Value of optional type 'String?' must be unwrapped
```
**Fix:**
```swift
// Bad
let name: String? = getName()
print(name.uppercased())

// Good - Optional binding
if let name = getName() {
    print(name.uppercased())
}

// Good - Nil coalescing
let name = getName() ?? "Unknown"
print(name.uppercased())

// Good - Optional chaining
print(getName()?.uppercased() ?? "")
```

#### Cannot Infer Type
```
Error: Type of expression is ambiguous without more context
```
**Fix:**
```swift
// Bad
let items = []

// Good
let items: [String] = []
let items = [String]()
```

### 3. Access Control

#### Private/Internal Access
```
Error: 'init' is inaccessible due to 'private' protection level
```
**Fix:**
```swift
// Bad
private init() { }

// Good
public init() { }
internal init() { }
```

### 4. Protocol Conformance

#### Missing Protocol Method
```
Error: Type 'MyClass' does not conform to protocol 'Codable'
```
**Fix:**
```swift
// Add required methods/properties
struct User: Codable {
    let id: String
    let name: String
    
    // Codable auto-synthesized nếu tất cả properties là Codable
}
```

#### Protocol Witness
```
Error: Candidate has non-matching type
```
**Fix:**
- Check method signature khớp với protocol
- Check parameter types và return type

---

## SwiftUI Specific Errors

### 1. View Body Type

#### Type Mismatch in Body
```
Error: Function declares an opaque return type, but has no return statements
```
**Fix:**
```swift
// Bad
var body: some View {
    Text("Hello")
    Text("World")
}

// Good
var body: some View {
    VStack {
        Text("Hello")
        Text("World")
    }
}
```

#### Too Many Views
```
Error: Extra argument in call
```
**Fix:**
```swift
// Bad - SwiftUI ViewBuilder limit 10 views
VStack {
    Text("1")
    Text("2")
    // ... 11 views
}

// Good - Group hoặc extract
VStack {
    Group {
        Text("1")
        Text("2")
        // ...
    }
    Group {
        Text("6")
        // ...
    }
}
```

### 2. State Management

#### @State Outside View
```
Error: @State can only be applied to properties of structs
```
**Fix:**
```swift
// Bad
class MyClass {
    @State var count = 0  // ❌
}

// Good
struct MyView: View {
    @State private var count = 0  // ✅
}
```

#### @Published Outside ObservableObject
```
Error: @Published can only be applied to classes
```
**Fix:**
```swift
// Bad
struct MyStruct {
    @Published var name = ""  // ❌
}

// Good
class MyViewModel: ObservableObject {
    @Published var name = ""  // ✅
}
```

### 3. Preview Errors

#### Preview Crash
```
Error: Cannot preview in this file
```
**Fix:**
```swift
// Ensure Preview provider
struct MyView_Previews: PreviewProvider {
    static var previews: some View {
        MyView()
    }
}

// Provide mock data nếu cần
struct MyView_Previews: PreviewProvider {
    static var previews: some View {
        MyView(viewModel: MockViewModel())
    }
}
```

---

## Runtime Errors

### 1. Force Unwrap Crash

```
Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

**Fix:**
```swift
// Bad
let name = user.name!  // Crash nếu nil

// Good
guard let name = user.name else {
    return
}

// Good
let name = user.name ?? "Unknown"
```

### 2. Array Index Out of Bounds

```
Fatal error: Index out of range
```

**Fix:**
```swift
// Bad
let item = items[5]  // Crash nếu items.count < 6

// Good
guard items.indices.contains(5) else {
    return
}
let item = items[5]

// Good
if let item = items[safe: 5] {
    // Use item
}

// Extension helper
extension Array {
    subscript(safe index: Int) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}
```

### 3. Main Thread Violation

```
Warning: Publishing changes from background threads is not allowed
```

**Fix:**
```swift
// Bad
DispatchQueue.global().async {
    self.items = newItems  // ❌ UI update on background
}

// Good
DispatchQueue.global().async {
    let newItems = fetchItems()
    DispatchQueue.main.async {
        self.items = newItems  // ✅ UI update on main
    }
}

// Good - Swift Concurrency
Task {
    let newItems = await fetchItems()
    await MainActor.run {
        self.items = newItems
    }
}
```

### 4. Memory Leaks (Retain Cycles)

```
Warning: Instance will be immediately deallocated
```

**Fix:**
```swift
// Bad - Strong reference cycle
class ViewModel {
    var onComplete: (() -> Void)?
    
    func setup() {
        onComplete = {
            self.doSomething()  // ❌ Strong self
        }
    }
}

// Good - Weak self
class ViewModel {
    var onComplete: (() -> Void)?
    
    func setup() {
        onComplete = { [weak self] in
            self?.doSomething()  // ✅ Weak self
        }
    }
}
```

---

## Build Errors

### 1. Missing File

```
Error: No such file or directory
```

**Fix:**
- Check file exists trong project
- Check file added to target
- Clean build folder

### 2. Duplicate Symbols

```
Error: Duplicate symbol '_OBJC_CLASS_$_MyClass'
```

**Fix:**
- Check file added to target multiple times
- Remove duplicate

### 3. Framework Not Found

```
Error: Framework not found 'SomeFramework'
```

**Fix:**
- Check SPM dependencies resolved
- Check CocoaPods installed
- Clean + rebuild

### 4. Code Signing

```
Error: Code signing failed
```

**Fix:**
- Check provisioning profile
- Check bundle identifier
- Check team selection

---

## Performance Issues

### 1. Slow View Rendering

**Symptoms:**
- UI lag
- Slow scrolling

**Debug:**
```swift
// Add to View
.onAppear {
    print("⏱️ View appeared: \(Date())")
}

// Measure time
let start = Date()
// ... code
print("⏱️ Took: \(Date().timeIntervalSince(start))s")
```

**Fix:**
- Lazy loading cho lists
- Reduce view complexity
- Cache computed values

### 2. Memory Warning

**Symptoms:**
- App crash on low memory
- Slow performance

**Debug:**
```swift
// Monitor memory
NotificationCenter.default.addObserver(
    forName: UIApplication.didReceiveMemoryWarningNotification,
    object: nil,
    queue: .main
) { _ in
    print("⚠️ Memory warning!")
}
```

**Fix:**
- Release unused objects
- Reduce image sizes
- Implement pagination

### 3. Network Timeout

**Symptoms:**
- Requests fail
- Long loading

**Debug:**
```swift
// Add timeout logging
URLSession.shared.dataTask(with: request) { data, response, error in
    if let error = error as NSError?, error.code == NSURLErrorTimedOut {
        print("⚠️ Request timeout")
    }
}
```

**Fix:**
- Increase timeout
- Optimize API
- Add retry logic

---

## Debug Workflow

### Step 1: Read Error Message
- File path
- Line number
- Error description

### Step 2: Locate Issue
```bash
# Search for error pattern
grep -r "problematic code" .
```

### Step 3: Understand Context
- Read surrounding code
- Check related files
- Review recent changes

### Step 4: Apply Fix
- Fix theo patterns trên
- Test fix
- Build lại

### Step 5: Verify
- Build success
- Tests pass
- No new warnings

---

## Debug Tools

### 1. Print Debugging
```swift
print("🔍 Debug: \(variable)")
print("🔍 Type: \(type(of: variable))")
dump(complexObject)
```

### 2. Breakpoints
- Set breakpoint tại line có issue
- Inspect variables
- Step through code

### 3. LLDB Commands
```
po variable          // Print object
p variable           // Print value
bt                   // Backtrace
frame variable       // All variables in frame
```

### 4. View Hierarchy
- Xcode → Debug → View Debugging
- Inspect view layout
- Check constraints

---

## Checklist

### Khi gặp compile error:
- [ ] Đọc error message đầy đủ
- [ ] Locate file và line number
- [ ] Check syntax (braces, commas)
- [ ] Check types
- [ ] Check imports

### Khi gặp runtime error:
- [ ] Check crash log
- [ ] Identify crash line
- [ ] Check force unwraps
- [ ] Check array access
- [ ] Check thread safety

### Khi gặp performance issue:
- [ ] Profile với Instruments
- [ ] Check memory usage
- [ ] Check CPU usage
- [ ] Optimize hot paths
- [ ] Add caching

### Trước khi commit:
- [ ] No compile errors
- [ ] No runtime crashes
- [ ] No memory leaks
- [ ] Tests pass
- [ ] Performance acceptable
