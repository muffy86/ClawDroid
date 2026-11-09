# Building ClawDroid on-device (Android/Termux) — verified recipe
_Added 2026-11-09 by the muffs agent (RikkaHub). Verified on Pixel 11 Pro Fold, Android 17, arm64. Build time ~2m49s._

## Toolchain (Termux)
- `pkg install openjdk-21 gradle aapt2` (aapt2 = Android Asset Packaging Tool 2.20-android-16.0.0_r4)
- Android SDK at `~/android-sdk`; `local.properties`: `sdk.dir=$HOME/android-sdk`
- `ANDROID_HOME=$HOME/android-sdk`

## Critical fixes
1. **aapt2**: the Maven-fetched aapt2 is glibc and cannot run under Android/bionic. Override with Termux's native aapt2 via `-Pandroid.aapt2FromMavenOverride=$PREFIX/bin/aapt2`.
2. **Kotlin daemon instability under Termux**: compile in-process via `-Dkotlin.compiler.execution.strategy=in-process`.
3. Recommended flags: `--no-daemon --console=plain --no-watch-fs -Dorg.gradle.jvmargs="-Xmx3g -XX:MaxMetaspaceSize=768m"`.

## Command (verified)
```bash
./gradlew :app:assembleDebug --no-daemon --console=plain --no-watch-fs \
  -Dorg.gradle.jvmargs="-Xmx3g -XX:MaxMetaspaceSize=768m" \
  -Dkotlin.compiler.execution.strategy=in-process \
  -Pandroid.aapt2FromMavenOverride=$PREFIX/bin/aapt2
```
Output: `app/build/outputs/apk/debug/app-debug.apk` (~174 MB debug with all native libs).
First build downloads gradle 8.14.3 + deps (~10 min); rebuilt in ~3 min after.
