### Bazel Build System Support - AQuery Integration

**🚀 New Implementation**: The latest InjectionIII releases include a completely rewritten Bazel integration using Action Query (aquery) for faster and more reliable hot reload.

#### Current AQuery-Based Approach

The modern implementation uses Bazel's Action Query system to directly extract Swift compilation commands without requiring full builds. This provides:

- **⚡ Faster Iteration**: No build overhead for command extraction
- **🔧 More Reliable**: Direct access to exact compilation commands
- **🎯 Better Performance**: Multi-layer caching with smart invalidation
- **🛠️ Zero Configuration**: Works with existing Bazel projects out-of-the-box

#### Quick Start

1. **Add Bundle Load Code** to your app initialization:
```swift
#if DEBUG
Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/iOSInjection.bundle")!.load()
#endif
```

2. **Use Regular Bazel Targets** (no special configuration needed):
```python
swift_library(
    name = "MyApp",
    srcs = ["Sources/MyApp.swift"],
)
```

3. **Start Developing**: Save any Swift file and watch it hot reload instantly!

#### How It Works

The aquery approach:
1. **Detects** Bazel workspace (MODULE/MODULE.bazel files)
2. **Converts** file paths to Bazel labels (//package:target)
3. **Queries** action graph: `bazel aquery mnemonic("SwiftCompile", //package:target)`
4. **Extracts** exact Swift compilation commands
5. **Modifies** commands (adds `-emit-library` and hot reload flags)
6. **Executes** `swiftc` directly (bypasses Bazel for speed)
7. **Injects** generated dylib into running app

#### Legacy Implementations (Deprecated)

Previous versions used two different approaches:

1. **BEP Parser**: Monitored build logs and parsed Build Event Protocol streams
2. **Patched Bazel**: Modified Bazel installation to preserve parameter files

Both approaches had limitations (build dependencies, reliability issues, complexity) and have been replaced by the superior aquery implementation.

#### Migration

- **No Changes Required**: Existing projects automatically use the new aquery approach
- **Better Performance**: Significantly faster hot reload with intelligent caching
- **Same User Experience**: Save file → see changes, but much faster

For detailed information, see:
- `BAZEL_AQUERY_INTEGRATION.md` - Complete aquery documentation
- `BAZEL_INTEGRATION.md` - Updated integration guide

For technical details and troubleshooting, consult the [GitHub issue #388](https://github.com/johnno1962/InjectionIII/issues/388) for the complete evolution of this feature.

$Date: 2025/03/26 $
