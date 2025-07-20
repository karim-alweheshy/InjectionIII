# Zero-Configuration Bazel Hot Reload

**TL;DR**: Works with any existing Bazel project. No setup required.

## How Simple Is It?

### 1. Your Existing Bazel Project
```python
# Any regular Bazel target works
swift_library(
    name = "MyApp", 
    srcs = ["Sources/MyApp.swift"],
    deps = ["//shared:Utils"],
)

ios_application(
    name = "App",
    bundle_id = "com.example.app",
    deps = [":MyApp"],
)
```

### 2. Add One Line to Your App
```swift
#if DEBUG
Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/iOSInjection.bundle")!.load()
#endif
```

### 3. Start Developing
- Edit any Swift file
- Save it
- Watch it hot reload instantly

**That's literally it.** No configuration files, no special build rules, no setup scripts.

## What Makes This Possible?

The integration uses Bazel's Action Query system to:
1. **Detect** your Bazel workspace automatically
2. **Find** which target contains your Swift file  
3. **Extract** the exact `swiftc` command Bazel would use
4. **Modify** it for hot reload (add `-emit-library` and dynamic replacement flags)
5. **Execute** `swiftc` directly to generate a hot reload dylib
6. **Inject** the dylib into your running app

## Performance Benefits

### No Build Overhead
- **Before**: `bazel build` → parse build logs → extract commands
- **After**: `bazel aquery` → extract commands directly

### Direct Execution
- **Before**: Go through Bazel's build system for hot reload
- **After**: Run `swiftc` directly with modified arguments

### Smart Caching
- Action graph results cached for 5 minutes
- Compilation commands cached for 10 minutes  
- File-to-target mappings cached permanently

## Compatibility

### Works With Any Bazel Target
- ✅ `swift_library`
- ✅ `ios_application` 
- ✅ `macos_application`
- ✅ `swift_test`
- ✅ Complex dependency graphs
- ✅ Nested packages and modules

### Works With Any Bazel Setup
- ✅ MODULE-based projects (Bazel 6.0+)
- ✅ WORKSPACE-based projects (legacy)
- ✅ Mixed iOS/macOS projects
- ✅ Large monorepos
- ✅ Simple single-app projects

## Tradeoffs

### What You Get
- ✅ **Instant hot reload** - no build waiting
- ✅ **Zero configuration** - works out of the box
- ✅ **Any target type** - no special rules needed
- ✅ **Full compatibility** - existing projects work unchanged

### What You Don't Get (And Why That's OK)
- ❌ **No Bazel caching** for hot reload dylibs
  - *But hot reload is single-file compilation anyway*
- ❌ **No remote execution** for hot reload builds  
  - *But hot reload needs to be local and instant*

## Migration

### From Existing Hot Reload
If you already use InjectionIII with Xcode projects, the Bazel integration runs alongside - no conflicts.

### From Bazel Build Logs Parsing
If you used previous experimental Bazel support, this replaces it with something much more reliable and faster.

### Zero Migration Required
Your existing Bazel project works immediately with hot reload. No BUILD file changes, no configuration files, no setup.

---

**Bottom Line**: If you have a Bazel project with Swift code, you now have hot reload. Just add the bundle load line and start developing.**