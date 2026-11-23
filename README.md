# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.0` (target-scoped reading build)
- Minimum Android: 7.0 (API 24)
- SHA-256: `3072867051E65FBAE32AB1D374064E98F496B5CC9B083E06D36EE2CE15DE864B`

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. Install it over the earlier prototype. If your phone refuses the update, uninstall the old app first and install this one.
4. Open **Calm Reader Mobile** and grant the accesses below.

This is a debug APK for testing, not a Play Store release.

## Set up once

1. Tap **Drawing access** and allow Calm Reader to display over other apps.
2. Tap **Accessibility**, find **Calm Reader scrolling**, and switch it on.
3. Tap **Start floating reader**. A small green circle now stays on top of whatever you open.

You can drag the circle anywhere. It avoids browser controls, scrollbars, and safe areas.

## Read only your manga app

1. Open the manga/manhwa app you want to read.
2. Tap the floating circle to open the controls.
3. Tap **Use this app**. The panel now shows the app name.
4. Tap **Start scrolling**.

Calm Reader will not scroll other apps. When a different app is in front, it waits and says **Waiting for the chosen app**.

## Controls in the floating panel

- **Start scrolling / Pause** — start or stop the gentle glide.
- **Use this app** — scope reading to the app currently in front.
- **Clear** — remove the target; use this only if you deliberately want any app.
- **Read now** — speak exposed text or run bubble OCR immediately.
- **Bubbles: On/Off** — enable local OCR for image speech bubbles.
- **Slower / Faster** — five gentle speeds.
- **Close controls** — hide the panel; the circle remains.

## Text and bubble pauses

- When Android exposes readable text, the reader pauses while the local voice speaks it. A longer bubble gets a proportionally longer pause.
- For image-only chapters, turn on **Bubbles** and allow screen capture once. OCR runs on the device using a model bundled inside the APK. The floating controls are hidden during each crop, frames are throttled, and repeated frames are ignored.
- The reader moves faster through empty gaps after it has checked for text.

## Privacy

- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the APK.
- Web assets use `connect-src 'none'`.
- No speech text, audio, screenshots, or page content are uploaded.
- OCR model and native library are bundled in the APK; no runtime model download.
- Screen capture is used only while bubble reading is on, only for the visible screen, and only locally.
- Accessibility is used only for the chosen app's scroll gestures and exposed text. No pointer paths, image URLs, or hidden-tab content are recorded.
- Debug state is numeric/boolean only: permission flags, running state, target matched, speed level, voice readiness, hidden state, error count, and external request count.
- It does not bypass logins, paywalls, region locks, or access controls.

## Validation

- APK content audit: 77/77 passed.
- Java compile and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Bundled OCR model assets and native libraries present.
- Target scoping, text/bubble dwell, gesture scrolling, voice comfort, and no skipped/repeated speech still need a real-device check by the user.

## Source

Durable source copy: `calm-reader-mobile-src`.
