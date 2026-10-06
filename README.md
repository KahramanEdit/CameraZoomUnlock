# CameraZoomUnlock

**Unlock 100x digital zoom in the RedMagic 11 Pro stock camera app**

An LSPosed module that removes the artificial 10x zoom cap in Nubia's stock camera (`com.android.camera`), exposing real zoom values up to **100x** in the zoom arc slider.

---

## 📋 Requirements

- RedMagic 11 Pro (NX809J) — tested on REDMAGICOS 11.0.19_EA
- Android 16
- Root (Magisk or KernelSU)
- **LSPosed** installed and active
- Target app: `com.android.camera` (stock Nubia Camera)

## 📦 Installation

1. **Install the APK**
   Download `CameraZoomUnlock.apk` and install it via your file manager.
   ⚠️ Do **not** install via `su` context — use the normal package installer.

2. **Enable in LSPosed**
   - Open **LSPosed** → **Modules**
   - Enable **CameraZoomUnlock**
   - Tap **Scope** and check only `com.android.camera`
   - Do **not** enable any other app in scope

3. **Restart the camera app**
   ```bash
   su -c "am force-stop com.android.camera"
   ```
   Or: Settings → Apps → Camera → Force stop

4. **Open the camera** from the launcher
   The zoom arc should now extend up to **100.0X (2300MM)**.

## ✅ What It Does

- Extends the zoom key list from `[0.6, 1, 2, 5, 10]` to `[0.6, 1, 2, 5, 10, 20, 50, 100]`
- Extends the main zoom range from `[1.0, 10.0]` to `[1.0, 100.0]`
- Hooked classes:
  - `cn.nubia.camera.nubiacameraconfig.Config#getFloatList(String)`
  - `cn.nubia.camera.nubiacameraconfig.Config#getRangeMap(String)`

## ⚠️ Limitations

- **Digital zoom only** — the RedMagic 11 Pro has no telephoto lens. All zoom above ~1x is software crop. Images at 100x will be extremely blurry and pixelated.
- **Bottom shortcut buttons** still show `0.6X 1X 2X 5X 10X` (they come from a different config key — planned for a future update).
- **Config is loaded once** on camera launch. If zoom values don't change, force-stop the camera and reopen.
- **LSPosed scope must include `com.android.camera`** — otherwise the hook never fires.

## 🧪 Verified Behavior

Logcat output after enabling the module:

```
getFloatList(ZoomKeyPhoto) -> [0.6, 1.0, 2.0, 5.0, 10.0, 20.0, 50.0, 100.0]
getRangeMap(ZoomRange) main -> [1.0, 100.0]
```

Zoom arc confirms: **20X (460MM) · 50X (1150MM) · 100.0X (2300MM)**

## 🛠️ Compatibility

- **Target package:** `com.android.camera` (stock Nubia Camera)
- **APK location:** `/system/priv-app/NubiaCamera/NubiaCamera.apk`
- **Confirmed working on:** REDMAGICOS 11.0.19_EA / Android 16 / NX809J-EEA
- **Not tested on:** other RedMagic models or firmware versions. Use at your own risk.

## ⚠️ Disclaimer

This module is provided as-is for educational and personal use. It hooks into a system camera application using LSPosed. If the camera app crashes or behaves unexpectedly, simply disable the module in LSPosed and force-stop the camera. No system partitions are modified, so rollback is trivial.

## 📄 License

MIT
