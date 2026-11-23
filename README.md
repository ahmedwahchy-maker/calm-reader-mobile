# Calm Reader Mobile

Floating, hands-free reader for Android. It follows you across browsers and apps.

This is a local-only prototype. It uses the phone's own on-device text-to-speech voices, not the desktop neural voice, and it makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.0` (floating overlay build)
- Minimum Android: 7.0 (API 24)
- SHA-256: `37A0C8AA86BA751564471861A952D63D3E25FCA8F36CEE10AA9676A19CEC045C`

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. Install it over the earlier prototype; if your phone refuses the update, uninstall the old app first and then install this one.
4. Open **Calm Reader Mobile** and grant the two accesses below.

This is a debug APK for testing, not a Play Store release.

## Set up once

1. Tap **1. Drawing access** and allow Calm Reader to display over other apps.
2. Tap **2. Accessibility**, find **Calm Reader scrolling**, and switch it on.
3. Tap **Start floating reader**. A small circle now stays on top of whatever you open.

You can drag the circle anywhere. It does not sit in the browser controls or the page content.

## Use in any app

1. Open your browser or manhwa app, for example Asura Scans.
2. Tap the floating circle to open the controls.
3. Choose what you want:
   - **Start/Pause scrolling** — continuous gentle glide down the page.
   - **Scroll up / Scroll down** — one movement at a time.
   - **Read screen text** — speaks text that the current app exposes through Android accessibility, using the local voice.
   - **Slower / Faster** — five scroll speeds.
4. Tap **Close controls** to hide the panel; the circle remains.

Stop the floating reader from the app with **Stop**, or swipe the notification away.

## What it can and cannot read

- **Text-based pages:** exposed headings, captions, and dialogue can be read aloud with the device voice.
- **Image-only chapters:** speech bubbles inside images cannot be read yet. That needs local OCR, which is not in this prototype. The app says so instead of guessing.
- It does not bypass logins, paywalls, region locks, or site access controls.

## Privacy

- No `INTERNET` permission in the APK.
- Web assets use `connect-src 'none'`.
- Uses native Android `TextToSpeech`, preferring voices marked as on-device.
- No speech text or audio is uploaded. No screenshots are taken or uploaded.
- Accessibility is used only for scroll gestures and exposed text. No pointer paths, images, or URLs are recorded.
- Debug telemetry is numeric/boolean only: permission flags, running state, speed level, voice readiness, hidden state, error count, external request count.

## Validation

- APK content audit: 36/36 passed.
- Overlay service, accessibility service, and JS bridge compiled into the APK.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` permission in the manifest.
- Web UI JavaScript syntax check passed.

Real-device testing is still needed to confirm the overlay follows between apps, gestures scroll the target app, voice comfort, and that no text is skipped or repeated.
