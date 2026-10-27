# Lite TV Launcher

[English](README.md) | **繁體中文**

輕巧、快速、無廣告的 Android TV 桌面。專為記憶體較小的電視（例如小米電視）設計，這類電視執行 PatchWall 或 Google TV 桌面時容易卡頓。

本專案 fork 自 [anujmv/lite-tv-launcher](https://github.com/anujmv/lite-tv-launcher)，加入繁體中文、網路狀態圖示和一些介面調整，詳見[本 fork 的修改](#本-fork-的修改)。

![截圖](docs/screenshot.png)

|              | Google TV 桌面 | FLauncher | **Lite TV Launcher** |
|--------------|---------------:|----------:|---------------------:|
| APK 大小     | —              | 26 MB     | **約 44 KB**         |
| 記憶體（PSS）| 約 164 MB      | 約 43 MB  | **約 22 MB**         |

上表數據來自原專案（小米電視）。本 fork 的實測結果：

| | 結果 |
|---|---|
| APK 大小 | 45,630 bytes（約 45 KB） |
| 記憶體（TOTAL PSS），安裝 2 個電視 App | 約 22.9 MB |
| 記憶體（TOTAL PSS），安裝 22 個電視 App | 約 26.1 MB |

記憶體是在 Android 16（API 36）模擬器上，用 `adb shell dumpsys meminfo dev.lite.launcher` 量測多次冷啟動後取平均。每次量測的誤差約 200–500 KB。

只用原生 Android View 加 Kotlin。不使用 AndroidX、不依賴任何函式庫、沒有網路存取權限、不追蹤使用者。

## 功能

- App 橫幅格狀排列；選中的 App 會放大 1.15 倍並加上陰影
- 時鐘和日期
- 網路狀態圖示：每次回到桌面時檢查，離線時顯示紅色，點擊開啟 Wi-Fi 設定
- 長按 App：**移動**、**隱藏**、**應用程式資訊**、**解除安裝**（選單標題會顯示 App 圖示）
- 移動模式：以細白框標示，用方向鍵移動位置，按 OK 儲存
- 隱藏的應用程式清單（眼睛圖示），附 App 圖示，可重新顯示
- 系統設定捷徑（齒輪圖示）
- 安裝或移除 App 時自動更新
- 支援英文和繁體中文
- Android 5.0 以上（API 21）

測試環境：

- 小米電視，Android 9（原專案）
- Toshiba SmartTV 4K，Android 12（API 31）
- Android 16（API 36）模擬器

## 本 fork 的修改

- 繁體中文介面與在地化的日期格式
- 隱藏 App 按鈕旁的網路狀態圖示（使用 `ACCESS_NETWORK_STATE` 權限，仍然沒有網路存取權限）
- 選中的 App 以放大 1.15 倍加陰影表示，不再使用白框
- 移動模式改用 2dp 白框
- 長按選單標題和隱藏 App 清單會顯示 App 圖示
- 標題和時鐘文字加上陰影

## 安裝

1. 在電視上開啟**開發人員選項**（設定 → 裝置偏好設定 → 關於 → 連按「版本號碼」7 次），再開啟 **USB 偵錯**或網路偵錯。
2. 從 [Releases](../../releases) 下載 `lite-tv-launcher-v*.apk` 並安裝：

   ```bash
   adb connect <電視IP>:5555
   adb install lite-tv-launcher-v1.0-fork3.apk
   adb shell cmd package set-home-activity dev.lite.launcher/.MainActivity
   ```

3. 按遙控器的 Home 鍵。

本 fork 的 APK 和原專案使用不同的簽章金鑰，安裝前請先解除安裝原專案的版本。簽章憑證 SHA-256：

```
78c0217051855aa2e866d4c80fb6065a97a7243e5bc9afbc0549035a4698c512
```

### 選用：停用原本的桌面

請**先**安裝並確認 Lite TV Launcher 可以正常使用。如果停用所有桌面 App，電視會變成黑畫面。

```bash
# 列出所有桌面 App
adb shell cmd package query-activities --brief -a android.intent.action.MAIN -c android.intent.category.HOME

# Android TV 桌面 / Google TV 桌面 / 小米 PatchWall
adb shell pm disable-user --user 0 com.google.android.tvlauncher
adb shell pm disable-user --user 0 com.google.android.apps.tv.launcherx
adb shell pm disable-user --user 0 com.mitv.tvhome.atv
```

要恢復時執行 `adb shell pm enable <套件名稱>`。

## 編譯

需要 JDK 17 以上和 Android SDK。已用 Android Studio 內建的 JDK 17 測試過。

```bash
./gradlew assembleRelease
adb install -r app/build/outputs/apk/release/app-release.apk
```

如果沒有在 `~/.gradle/gradle.properties` 設定以下內容，release 版本會用你的 debug 金鑰簽署：

```properties
liteStoreFile=/path/to/release.jks
liteStorePassword=...
liteKeyAlias=...
liteKeyPassword=...
```

## 授權

[GPL-3.0](LICENSE)
