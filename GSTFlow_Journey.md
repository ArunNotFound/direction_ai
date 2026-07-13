# 🚀 The GSTFlow Journey: From Zero to SOTA

## 1. Where We Started
GSTFlow started as an ambitious effort to create a **Unified, State-of-the-Art (SOTA) GST Verification Engine**. The core logic was built strictly in **F#**, utilizing mathematical domain modeling, Property-Based Testing (FsCheck), and strict adherence to the GST Council Laws (e.g. strict RCM validation, Intrastate vs Interstate rules, and Checksum validation algorithms).
Our goal was to have a single "Golden Engine" that could be compiled into Native AOT (for CLI/Desktop) and WebAssembly (for Web and Mobile).

## 2. Experiments & The Pivot
Initially, we wanted to build a web and mobile wrapper around this F# engine using web-based technologies (Capacitor/Ionic, Blazor, WebAssembly).

### The Web / React / WASM Experiment
- **What We Did:** We compiled the F# engine into WebAssembly (WASM) and tried hooking it up to a React UI (via Vite) and bundling it with Capacitor for mobile.
- **Issues Encountered:**
  - **WASM Friction:** Hooking up .NET WASM to a JavaScript frontend requires heavy JS-Interop. Memory sharing between JS and WASM was slow and clumsy.
  - **Node.js/NPM Dependency Hell:** We ran into severe pipeline problems on GitHub Actions with Node 20 deprecation warnings, Gradle mismatches, and `npm` build failures.
  - **Capacitor Limitations:** Capacitor is just a web-view wrapper. It didn't provide the native, SOTA feel we wanted for Android and Windows. The "Goofiness" of the web view was apparent.

### The Fable + Flutter Leap
- **What We Did:** Realizing that wrapping a website inside a mobile app wasn't cutting it, we made a radical shift. We threw away WASM and Capacitor. Instead, we used **Fable** to transpile our F# engine directly into **Dart**, the native language of Flutter.
- **Why It's Better:** 
  - Fable produced highly optimized, idiomatic Dart code from our F# models.
  - Flutter compiles straight to native ARM code for Android, bypassing web views entirely!
  - No more JS-Interop, no more WASM memory limits, no more Node/NPM/Capacitor build pipelines!

## 3. Where We Are Now
We now have a **Pure Native Flutter Application** that directly calls our compiled F# rules engine.
- The UI is entirely Flutter (Dart) and runs seamlessly on Android.
- The Business Logic is entirely F# (compiled to Dart).
- **GitHub Actions pipeline** has been scrubbed clean of Capacitor and JS dependencies. It simply restores Fable, translates F# to Dart, and runs `flutter build apk`.

## 4. What Went Well
- **F# Domain Modeling:** The mathematical strictness of the F# engine saved us. When we updated the rules (e.g., adding strict RCM validations and B2C POS checks), FsCheck immediately flagged invalid fixtures.
- **Fable Transpilation:** The fact that F# types (`Union`, `Record`, `Result`) translated perfectly into Dart classes (`FSharpResult$2`) was incredible. It proved that we don't need WASM to share F# code with mobile.
- **CI/CD Resiliency:** We successfully debugged Node deprecations, Java SDK missing errors, and Dart SDK version conflicts in GitHub Actions.

## 5. Lessons Learned
- **Don't wrap websites if you want SOTA:** Capacitor is great for quick ports, but nothing beats native execution (Flutter).
- **Toolchain Alignment is Critical:** We hit friction when `pubspec.yaml` was generated with Dart 3.12, but the CI had Dart 3.4. Aligning minimum SDK requirements saves hours of CI headaches.
- **Test Fixtures Must Evolve with the Rules:** We made our GST rules extremely strict, but forgot to update the old JSON fixtures. Always keep test fixtures aligned with the strictest domain rules.

## 6. The Way Forward
We are locking in **Flutter + Fable** as our SOTA Mobile and Desktop strategy. 
Next steps:
1. **Windows App Delivery:** Since Flutter supports Windows Desktop natively, we will compile this exact same Dart codebase into a Windows `.exe` to bypass Tauri/Pake complexity.
2. **On-Device OCR Integration:** We will integrate Google ML Kit into the Flutter app to perform on-device invoice scanning and OCR. This will extract GSTINs offline (no server uploads) and feed them directly into the F# engine!
3. **Microsoft Store Publication:** Ensure the Windows application is ready for the Microsoft App Store.
