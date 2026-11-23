# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.8` (Japanese/Korean OCR + fresh current-screen stops + reliable capture status)
- Minimum Android: 7.0 (API 24)
- Size: 16,613,011 bytes
- SHA-256: `BFCF80C04128D7DB5BA3D47D0E300FD4FAC919F59274FCB80E6AE9FCD615ED6F`

## What changed in 1.8

- **Japanese and Korean bubbles are now read.** The bundled Latin model was the reason manga/manhwa text often produced no stops at all. Three local recognizers (Latin, Japanese, Korean) now run in parallel on each cropped frame, and the first one that finds text wins. All models ship inside the APK; nothing is downloaded.
- **Fresh frames are sampled twice as fast.** On-device OCR runs about every 250 ms instead of 450 ms, and the capture loop produces frames every 200 ms instead of 350 ms, so the text crossing the reading line is much closer to what is actually on screen at speed.
- **A bubble is never read twice in a row.** Each OCR frame gives the same bubble a new stop key, which could re-trigger a dwell. A text-and-position guard now suppresses an immediate repeat at the same place.
- **Capture status no longer flickers.** Frames are consumed after each OCR pass, so using "a frame exists" as the ready signal made bubble reading look permanently ungranted. The app now tracks the actual capture session, so the status line, prompts, and the bubble toggle agree.
- **The floating controls no longer get OCR'd.** `FLAG_SECURE` keeps the circle and panel visible to you but invisible to screen capture, so their labels cannot be mistaken for speech bubbles. The controls no longer blink out during each crop.
- **Gap cruise is less likely to outrun OCR.** The empty-space multiplier dropped from 2.1× to 1.3×. Text stops still brake hard and dwell proportionally; only blind gaps slowed slightly to give OCR time to keep up.
- **Smaller download.** The APK now ships only ARM phone libraries (arm64-v8a and armeabi-v7a), dropping unused x86 emulator binaries. The CJK models still fit comfortably under GitHub's 25 MB upload limit.

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. **Uninstall the old prototype first**, then install this one. A clean install avoids stale permission state.
4. Open **Calm Reader Mobile** and grant the accesses below.

This is a debug APK for testing, not a Play Store release.

### If a main-app button looks dead

Build `1.8` shows a small diagnostic line under the status message. It should include `v1.8`, a tap counter, `native yes`, `overlay yes`, and `bridge ready`. Tap any button: the counter must increase and the message should briefly say `Tapped: …`. For the two **Open** buttons the message then changes to `Opening … settings…` immediately, and Android should switch screens right away. Build `1.8` also shows a native setup dialog at launch when a permission is missing. If the tap counter does not move and no setup dialog appears, the phone is still running an older APK — uninstall and reinstall.

## Set up once

1. Tap **Drawing access** and allow Calm Reader to display over other apps.
2. Tap **Accessibility**, find **Calm Reader scrolling**, and switch it on.
3. Tap **Start floating reader**. A small green circle now stays on top of whatever you open. Allow screen capture when asked so image bubbles can be read locally.

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
- **Bubbles: On/Off** — enable local OCR for image speech bubbles (on by default).
- **Slower / Faster** — five speeds, close to the desktop extension's pace.
- **Close controls** — hide the panel; the circle remains.

## Text and bubble pauses

- The reader uses the desktop extension's motion model: a reading line at 42% of the screen, one continuous velocity-ramped glide, a dwell whose length comes from the text, longer rests on artwork, and a faster cruise through empty gaps.
- Stops are chosen from text currently on screen, not predicted from old offscreen positions. This is what makes it work "blind" with image-only chapters.
- Dwell timing is independent of the voice, so a slow or unavailable local voice can never freeze scrolling. The built-in voice is diagnostic only.
- For image-only chapters, **Bubbles** is on by default. Allow screen capture once when asked. Latin, Japanese, and Korean OCR all run on the device using models bundled inside the APK. The floating controls are excluded from capture with `FLAG_SECURE`, frames are throttled, stale frames are discarded, overlapping OCR runs are skipped, and repeated text at the same place is ignored.

## Privacy

- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the APK.
- Web assets use `connect-src 'none'`.
- No speech text, audio, screenshots, or page content are uploaded.
- All OCR models and native libraries are bundled in the APK; no runtime model download.
- Screen capture is used only while bubble reading is on, only for the visible screen, and only locally.
- Accessibility is used only for the chosen app's scroll gestures and exposed text. No pointer paths, image URLs, or hidden-tab content are recorded.
- Debug state is numeric/boolean only: permission flags, running state, target matched, speed level, voice readiness, hidden state, error count, and external request count.
- It does not bypass logins, paywalls, region locks, or access controls.

## Validation

- APK content audit: all checks passed, including v1.8 checks for the build string, Latin/Japanese/Korean recognizers, bundled CJK OCR models, OCR in-flight guard, repeat-dwell suppression, current-screen reading-line stops, visible-frame stop tracking, first-run bubble-reading migration, composite stop keys, per-line OCR stops, and continued glide gestures.
- Java compile, Gradle lint, and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Only ARM phone ABIs are packaged; the APK is under GitHub's 25 MB upload limit.
- Bundled OCR model assets and native libraries present.
- Target scoping, text/bubble dwell (including Japanese/Korean), gesture scrolling, voice comfort, and no skipped/repeated speech still need a real-device check by the user.

## Source

Durable source copy: `calm-reader-mobile-src`.
