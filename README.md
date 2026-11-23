# Calm Reader Mobile

Local-only Android prototype for hands-free manhwa reading.

This is not a port of the browser extension. It is a small Capacitor app that uses the phone's own on-device text-to-speech voices instead of the bundled desktop neural voice.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.0`
- Minimum Android: 7.0 (API 24)
- SHA-256: `048AE6DEFCBC369D10D297685D097F813B2B109D72C0912DF9616CE59B7FDA9C`

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. Install and open **Calm Reader Mobile**.

This is a debug APK for testing, not a Play Store release.

## Use

1. Tap **Choose chapter images** and select images from a chapter you can already open.
2. Tap **Start**.
3. Use **Pause**, **Previous**, **Next**, **− / +**, and **Test voice** as needed.

The prototype speaks a short local page position, for example “Page 3 of 12”. It does not bypass logins, paywalls, region locks, or site access controls.

## Behavior

- Continuous glide through chapter images.
- Longer dwell on artwork, scaled by image height.
- Faster travel through the gaps between images.
- Screen stays awake while reading, and releases on pause, finish, or when the app is hidden.
- Large touch controls with visible focus and Android safe-area padding.

## Privacy

- Uses native Android `TextToSpeech`, preferring voices marked as on-device.
- No Kokoro model, no ONNX, no WASM, and no speech upload.
- The APK has **no `INTERNET` permission**.
- Web assets use `connect-src 'none'`.
- Images are loaded from local `blob:` URLs.
- Debug telemetry is numeric/boolean only: page, pages, scroll, speed, dwell time, voice readiness, wake lock, errors, and external request count. No screenshots, page text, image URLs, speech text, or pointer paths are stored.

## Current limits

- No OCR yet, so speech bubbles are not read in this first APK.
- No automatic reading from manhwa sites; the user supplies images.
- iPhone/IPA would need a separate macOS/Xcode build.
- A signed release build is needed for distribution.

## Validation

- Web reading-loop test: 9/9 passed.
- APK content audit: 7/7 passed.
- Native speech plugin compiled into the APK.
- No external URLs in the bundled web app.
- No `INTERNET` permission in the APK manifest.

Real-device listening is still needed to confirm voice comfort and that no page is skipped or repeated.
