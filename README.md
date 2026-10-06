# CameraZoomUnlock

**Unlock 100x digital zoom and 1920fps super slow-motion in the RedMagic 11 Pro stock camera app**

An LSPosed module that removes artificial software limits in Nubia's stock camera (`com.android.camera`):
- **10x → 100x** digital zoom in the zoom arc slider
- **480fps → 1920fps** super slow-motion (via 4× frame interpolation)

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

4. **Open the camera**
   - **Zoom:** Arc slider now extends to **100.0X (2300MM)**
   - **Slow-mo:** FPS tab shows `[120, 240, 1920]` — pick 1920

## ✅ What It Does

### Zoom Unlock
- Extends zoom key list from `[0.6, 1, 2, 5, 10]` to `[0.6, 1, 2, 5, 10, 20, 50, 100]`
- Extends main zoom range from `[1.0, 10.0]` to `[1.0, 100.0]`
- Hooked:
  - `cn.nubia.camera.nubiacameraconfig.Config#getFloatList(String)`
  - `cn.nubia.camera.nubiacameraconfig.Config#getRangeMap(String)`

### Super Slow-Motion Unlock
- Enables **4× frame interpolation** on the 480fps native stream → **1920fps effective output**
- Hooked:
  - `cn.nubia.camera.video.VideoUtil#getSlomoFps` → forces `960` when base is `480`
  - `cn.nubia.camera.video.VideoUtil#getMultipleSlomoFps` → forces multiplier `4`
  - `cn.nubia.camera.video.VideoUtil#isSlomoInterpolationOn` → forces `true`
  - `cn.nubia.camera.nubiacameraconfig.Config#hasSlomoInterpolationFps` → forces `true`
  - `android.widget.TextView#setText` → rewrites `"480"` → `"1920"` when called from `TabSelectView.addTab`

## ⚠️ Limitations

- **Digital zoom only** — the RedMagic 11 Pro has no telephoto lens. All zoom above ~1x is software crop. Images at 100x will be extremely blurry and pixelated.
- **1920fps is NOT native** — it is 480fps captured natively, with 3 interpolated frames inserted between each real frame. Fast motion may show ghosting/artifacts.
- **Processing time is long** — 1920fps videos require several minutes of post-processing ("Süper yüksek hızlı video işleniyor" screen). Keep the screen ON and the device plugged in.
- **File sizes are large** — 1920fps output is ~4× the size of the equivalent 480fps clip.
- **Camera config is loaded once** on launch. Force-stop the camera after enabling the module.
- **LSPosed scope must include `com.android.camera`** — otherwise hooks never fire.

## 🧪 Verified Behavior

Logcat after enabling the module (RedMagic 11 Pro, REDMAGICOS 11.0.19_EA):

```
=== CameraZoomUnlock v1.1 loading (target 1920fps) ===
ZOOM hooks OK
SLOMO core hooks OK
TabSelectView 480->1920 label hook OK
getFloatList(ZoomKeyPhoto) -> [0.6, 1.0, 2.0, 5.0, 10.0, 20.0, 50.0, 100.0]
getRangeMap(ZoomRange) main -> [1.0, 100.0]
getSlomoFps 480->1920
getMultipleSlomoFps 480 -> 4
isSlomoInterpolationOn -> true
TabSelectView.addTab: 480 -> 1920
```

**Verified on device:**
- Zoom arc reaches **100.0X (2300MM)**
- Slow-mo tab labels: **120 / 240 / 1920**
- 1920fps video successfully processed and saved to gallery

## 🛠️ Compatibility

- **Target package:** `com.android.camera` (stock Nubia Camera)
- **APK location:** `/system/priv-app/NubiaCamera/NubiaCamera.apk`
- **Confirmed working on:** REDMAGICOS 11.0.19_EA / Android 16 / NX809J-EEA
- **Not tested on:** other RedMagic models or firmware versions. Use at your own risk.

## ⚠️ Disclaimer

This module is provided as-is for educational and personal use. It hooks into a system camera application using LSPosed. If the camera app crashes or behaves unexpectedly, simply disable the module in LSPosed and force-stop the camera. No system partitions are modified, so rollback is trivial.

## 📄 License

MIT
