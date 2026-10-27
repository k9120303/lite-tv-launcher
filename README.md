# Lite TV Launcher

**English** | [繁體中文](README.zh-TW.md)

A tiny, fast, ad-free home screen for Android TV. Built for low-RAM TVs (like Xiaomi Mi TV) where PatchWall and the Google TV launcher lag.

This is a fork of [anujmv/lite-tv-launcher](https://github.com/anujmv/lite-tv-launcher) with Traditional Chinese, a network status icon and a few UI tweaks. See [What this fork changes](#what-this-fork-changes).

![Screenshot](docs/screenshot.png)

|            | Google TV launcher | FLauncher | **Lite TV Launcher** |
|------------|-------------------:|----------:|---------------------:|
| APK size   | —                  | 26 MB     | **~44 KB**           |
| RAM (PSS)  | ~164 MB            | ~43 MB    | **~22 MB**           |

Figures above are from the upstream project (Xiaomi Mi TV). Measured for this fork:

| | Result |
|---|---|
| APK size | 45,630 bytes (~45 KB) |
| RAM (TOTAL PSS), 2 TV apps installed | ~22.9 MB |
| RAM (TOTAL PSS), 22 TV apps installed | ~26.1 MB |

RAM was measured with `adb shell dumpsys meminfo dev.lite.launcher` on an Android 16 (API 36) emulator, averaging several cold starts. Each run varies by about 200–500 KB.

Plain Android Views + Kotlin. No AndroidX, no libraries, no internet permission, no tracking.

## Features

- Grid of app banners; the focused app zooms in (1.15×) with a shadow
- Clock and date
- Network status icon: checked each time you return home, red when offline, click to open Wi-Fi settings
- Long-press an app: **Move**, **Hide**, **App info**, **Uninstall** (menu title shows the app icon)
- Move mode: a thin white ring marks the tile, arrows shift it, OK saves
- Hidden apps list (eye icon), with app icons, to bring apps back
- Settings shortcut (gear icon)
- Updates automatically when apps are installed or removed
- English and Traditional Chinese
- Android 5.0+ (API 21)

Tested on:

- Xiaomi Mi TV, Android 9 (upstream)
- Toshiba SmartTV 4K, Android 12 (API 31)
- Android 16 (API 36) emulator

## What this fork changes

- Traditional Chinese strings and a localized date format
- Network status icon next to the hidden apps button (`ACCESS_NETWORK_STATE`; still no internet permission)
- Focus is shown by a 1.15× zoom and shadow instead of a white ring
- Move mode uses a 2 dp white ring
- App icons in the long-press menu title and the hidden apps list
- Text shadow on the title and clock

## Install

1. On the TV, enable **Developer options** (Settings → Device Preferences → About → click *Build* 7 times), then turn on **USB debugging** / network debugging.
2. Download `lite-tv-launcher-v*.apk` from [Releases](../../releases) and install it:

   ```bash
   adb connect <TV_IP>:5555
   adb install lite-tv-launcher-v1.0-fork3.apk
   adb shell cmd package set-home-activity dev.lite.launcher/.MainActivity
   ```

3. Press Home on the remote.

This fork's APKs are signed with a different key from upstream's, so uninstall the upstream launcher first. Signing certificate SHA-256:

```
78c0217051855aa2e866d4c80fb6065a97a7243e5bc9afbc0549035a4698c512
```

### Optional: disable the stock launcher

Install and test Lite TV Launcher **first**. Disabling every home app leaves the TV with a black screen.

```bash
# find home apps
adb shell cmd package query-activities --brief -a android.intent.action.MAIN -c android.intent.category.HOME

# Android TV home / Google TV home / Xiaomi PatchWall
adb shell pm disable-user --user 0 com.google.android.tvlauncher
adb shell pm disable-user --user 0 com.google.android.apps.tv.launcherx
adb shell pm disable-user --user 0 com.mitv.tvhome.atv
```

Undo with `adb shell pm enable <package>`.

## Build

Requires JDK 17+ and the Android SDK. Tested with the JDK 17 bundled with Android Studio.

```bash
./gradlew assembleRelease
adb install -r app/build/outputs/apk/release/app-release.apk
```

Release builds are signed with your debug key unless these are set in `~/.gradle/gradle.properties`:

```properties
liteStoreFile=/path/to/release.jks
liteStorePassword=...
liteKeyAlias=...
liteKeyPassword=...
```

## License

[GPL-3.0](LICENSE)
