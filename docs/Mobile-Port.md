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

## Current work

GitHub is connected. Changes have been published to the user's fork on separate
branches; nx2 has not been modified.

- Android: `mobile/android-build`, latest source fix d57df7bfac550f48166a94870450b25cd1004bd3.
  The Gradle variant compiler tasks now depend on their native build so SDL Java
  sources exist before Kotlin compilation. A local Gradle dry run passed and
  showed the expected order without a dependency cycle. Latest cloud run:
  https://github.com/Erzoy38/Lichii/actions/runs/43843598686
- iOS: `mobile/ios-core`, latest commit bef33694a162a08692957334f893d835f41947f7.
  The first core configure reached FFmpeg discovery and failed because no iOS
  FFmpeg libraries were available. The second attempt enables a source cross-build
  with the iPhone SDK and Darwin target, and limits FFmpeg build parallelism.
  Latest cloud run:
  https://github.com/Erzoy38/Lichii/actions/runs/43843657629

No APK or IPA has been produced yet. The iOS branch is a core compilation probe,
not a functioning iPhone app. Real-device guest-code execution, frontend and
rendering/input/audio integration are still outstanding.

