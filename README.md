# VexEdit Studio — Android Offline-First Modular Creative Editor

VexEdit Studio is an offline-first modular video and audio creative studio designed for Android with hybrid editing controls, real-time preview, contextual editing tools, parametric effects lab, audio multi-tracking, and full on-device processing.

---

## Features

- **Offline-First Creative Engine**: 100% on-device timeline evaluation, video/audio processing, effect rendering, and project persistence. No cloud login, online verification, or remote server required.
- **Hybrid Studio UI**: Intuitive mobile-first video editor with high-precision multi-layer timeline, zoomable time ruler, contextual inspector panels (Edit, Speed, Volume, Effects, Animation, Crop), live waveform display, and aspect ratio support (9:16, 16:9, 1:1).
- **Real Video Preview & Export**: Live multi-track Canvas compositor supporting video/photo uploads, text overlays, color grading presets (Teal & Orange, Cyberpunk, Noir, Vintage, Golden Hour, Vivid), clip transitions (Cross Fade, Wipe), and real-time audio-synchronized video export with progress tracking and cancellation.
- **Audio Studio**: Multi-track mixing, parametric EQ, compressor, limiter, reverb, delay, distortion, and noise gate.
- **Effects Lab & Presets**: Parametric video filters (vignette, blur, contrast, chromatic aberration, saturation, brightness) with keyframe and preset management.
- **Module Center**: Extensible on-device module management with dependency checking and real progress monitoring.
- **Safe State & Undo/Redo**: Auto-saving, history snapshots, and crash recovery.

---

## Build Instructions

### 1. Web Application (React + Vite + Tailwind CSS)

```bash
# Install dependencies
npm install

# Build production bundle
npm run build
```

The production output is generated in `dist/index.html`.

### 2. Android Native Application (Kotlin + Jetpack Compose + AGP)

Prerequisites:
- **JDK 17** (Temurin / OpenJDK 17)
- **Android SDK** (Compile SDK: 34, Min SDK: 24, Target SDK: 34)
- **Gradle 8.7** with Android Gradle Plugin (AGP) 8.5.2

```bash
# Run unit tests
./gradlew :app:testDebugUnitTest

# Build Debug APK
./gradlew :app:assembleDebug

# Build Release APK (unsigned or signed via Gradle/apksigner)
./gradlew :app:assembleRelease
```

Generated APK paths:
- **Debug APK**: `app/build/outputs/apk/debug/app-debug.apk`
- **Release APK**: `app/build/outputs/apk/release/app-release.apk` (or `vexedit-studio-release-signed.apk`)

---

## GitHub Actions CI/CD Pipeline

The `.github/workflows/android-build.yml` workflow automatically tests, builds, and publishes APK artifacts:

- **Triggers**:
  - Push to `main` and `develop`
  - Pull requests targeting `main` and `develop`
  - Manual triggers via `workflow_dispatch`
  - Tagged releases matching `v*`
- **Job Structure**:
  1. **JVM Unit Tests (`test`)**:
     - Executes `./gradlew :app:testDebugUnitTest --no-daemon --stacktrace`.
     - Always uploads test reports HTML & XML (`app/build/reports/tests/`) as artifact `jvm-test-reports`.
  2. **Build & Package Android APK (`build`)**:
     - Runs with `if: always()` to ensure a clean, independent Debug APK is built and uploaded even during test analysis.
     - Accepts Android SDK licenses.
     - Compiles Debug APK and uploads as `vexedit-studio-debug-<commit-sha>`.
     - Compiles Release APK.
     - If signing secrets are configured, signs and verifies the APK using Android `apksigner`.
     - Uploads Release APK artifact.
     - Automatically attaches APKs to GitHub Releases for `v*` tags.

### Release Signing with GitHub Secrets

To produce signed release APKs in GitHub Actions, configure the following secrets in your repository (**Settings → Secrets and variables → Actions**):

- `KEYSTORE_BASE64`: Base64-encoded `.jks` or `.keystore` file (`base64 -w 0 release.jks`)
- `KEYSTORE_PASSWORD`: Keystore store password
- `KEY_ALIAS`: Keystore key alias name
- `KEY_PASSWORD`: Keystore key password

*If secrets are not configured, the debug APK is still built and uploaded without errors.*

---

## License

- VexEdit Studio Code: [MIT License](LICENSE)
- Third-party components: See [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md)
