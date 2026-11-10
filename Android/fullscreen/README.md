# MSDisplay 2.0.1.3 Fullscreen (modified, unofficial)

🇬🇧 English | 🇧🇷 [Português](README.pt-BR.md)

A modified build of **MSDisplay Android 2.0.1.3** (USB → RCA/CVBS dongle, MacroSilicon MS2160/MS9120 chips) that **fills the rear screen** on Android head units with a 16:9 display, instead of showing a small picture surrounded by black bars.

> **Disclaimer:** community project, not affiliated with MacroSilicon, UltraSemi or MindShow. Use at your own risk. The APK is provided as is, without warranty.

## The problem

The original app captures the head unit screen into a fixed 720x480 (or 720x576) frame. Android fits the head unit screen (for example 1024x600) inside that frame **keeping its aspect ratio**, which leaves black bars. On the rear headrest screen, the picture looks smaller than the available area.

## What changes in this build

1. **Capture at the head unit's real aspect ratio.** The `VirtualDisplay` and `ImageReader` now use the output width (720) and a height computed from the real screen resolution (1024x600 → 720x422), so there are no bars.
2. **Stretch to 720x480.** Each frame is stretched vertically (row copy, no resampling) to 720x480 before it is sent over USB. CPU cost is low.
3. **480/60 Hz default for CVBS output.** With no saved setting, the app uses 720x480 at 60 Hz instead of 720x576 at 50 Hz. You can still choose either option in the app's settings screen.

Code changes (smali, from the 2.0.1.3 APK):
- `CaptureService`: new fields `capHeight` and `sb`; `getSize()` computes `capHeight`; `displayInit()` uses `capHeight`; `onImageAvailable()` calls the stretch routine and sends the compact frame (stride 0); CVBS timing default.
- New class `com.ms.ms2160.service.Stretch`, with `capH(...)` and `stretch(...)`.

The package name (`com.ms.ms2160`) was **not changed**.

## Limitations

- The output is still **RCA/CVBS (analog), at most 720x480**. The rear screen upscales that picture to its own resolution. It fills the screen, but it is softer than a native image, and small text has low definition.
- Set the rear screen to **Wide / Full** (aspect button on the remote or OSD menu). In 4:3 mode the picture is distorted.
- If the picture looks too stretched, open an issue: an alternative is to crop the height instead of stretching.

## Installation

1. **Uninstall the original MSDisplay.** This build is signed with a different key, and Android refuses to install it over the original ("could not install the update"). If you see "Uninstall updates", use it first, then uninstall again.
2. Install `MSDisplay_2.0.1.3_fullscreen.apk` (download it from the **Releases** tab).
3. Grant the permissions again: draw over other apps and screen capture.
4. Plug in the dongle and set the rear screen aspect to Wide/Full.

To go back to the original, uninstall this build and reinstall `MSDisplay_Android_V2.0.1.3.apk` from the MindShow/USBDisplay repository.

## Tested on

- Android head unit with a 1024x600 screen and a rear screen with an RCA input. The build installs and works.
- Other devices: reports are welcome in the issues (head unit model, Android version, resolution, and whether the black bars are gone).

## Verification

```
sha256sum MSDisplay_2.0.1.3_fullscreen.apk
```

SHA-256: ED1017B6563B786F025737297E65F45756D3E047CCF3B5FF35C7DC96FD6D7CBE

## Credits and license

- Base: [MindShow/USBDisplay](https://github.com/MindShow/USBDisplay) (MacroSilicon / UltraSemi), file `Android/MSDisplay_Android_V2.0.1.3.apk`.
- The original repository does not state a license. This build is a modification made by reverse engineering the APK, without access to the app's source code, published for personal use and experimentation. If the rights holders ask for it to be removed, it will be removed.
