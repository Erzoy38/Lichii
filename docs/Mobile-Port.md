# Mobile port status

Source baseline: zortos293/Lichii, nx2, commit
960f2157307caa806035ced4804fe4606b25b49b.

## Android

Branch: https://github.com/Erzoy38/Lichii/tree/mobile/android-build

The existing project already includes the Kotlin app, JNI bridge, touch input,
ARM64 execution backends and Vulkan frontend. The new workflow builds that app;
it does not introduce a new emulator core or prove Switch 2 compatibility.

The workflow installs Java 17, SDK 36, NDK 28.2.13676358 and CMake 3.31.5 and
uploads an ARM64 APK after a successful build. Pushes to the mobile branch trigger
it automatically. The first CI attempt failed because setup-android requested the
obsolete `tools` package; that dependency was corrected to `platform-tools`.

The local Windows build configured the app and processed resources/manifests,
then stopped because CMake 3.31.5 was missing. No local APK has been produced.

The APK inherits upstream development signing and the Eden application ID.
Avoid replacing an existing installation containing valuable data.

## iOS: iPhone 14 Pro Max

Branch: https://github.com/Erzoy38/Lichii/tree/mobile/ios-core

The experimental macOS workflow cross-compiles the real C++ `core` target for
iphoneos/arm64 with an iOS 16 deployment target. It saves compiler/configuration
logs. It does not produce an iOS app or IPA, or establish runtime compatibility.

Initial portability changes:

- Separate iOS deployment settings and frameworks from macOS defaults.
- Prevent the macOS-only MoltenVK binary from being selected for an iOS build.
- Use Khronos MoltenVK 1.4.2 for iOS, verified against its release SHA-256.
- Restrict IOKit power-source APIs to macOS; iOS power reporting remains unknown.

Remaining work includes resolving cross-compilation failures, adding the iOS app
and core bridge, wiring Metal presentation/audio/input/files/lifecycle, establishing
a working guest-code execution strategy on an actual iPhone, and signing plus
real-device testing. Android/Linux NCE does not automatically work on iOS.
A successful static-core build would not prove that JIT execution is available.

No firmware, keys or games are included.

