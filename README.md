# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.6` (extension-paced continuous glide + per-bubble OCR stops)
- Minimum Android: 7.0 (API 24)
- Size: 24,780,562 bytes
- SHA-256: `03B6A75FA5EAA65043BD979F916A7C5481DB4CA3E239D2A7621B7AAD3E6E187D`

## What changed in 1.6

- **No more long wait when tapping Open.** The Drawing access and Accessibility buttons now go straight to Android settings; they no longer wait for a web bridge call that could stall for ~20 seconds.
- **Much faster, smoother scroll.** The five speeds are now roughly 620/900/1300/1750/2300 px/s. Motion is one velocity-ramped glide with continued drag segments instead of short separate flicks, and gaps are crossed at nearly 1.9x while artwork is revealed more slowly.
- **Bubble stops fixed.** Bubble reading is on by default. Local OCR now produces a separate stop for each recognised line, so the reader pauses on individual speech bubbles instead of treating the whole screen as one blob. Repeated bubbles no longer collapse into one because stop keys combine text, position, and app.
- **Reading time is proportional.** Dwell follows the extension's ~210 wpm model plus a short bonus, clamped from 0.8 s up to 90 s, so long bubbles get proportionally long rests.
- **Foreground app is detected at start**, so "Use this app" works without waiting for a window-change event.

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. **Uninstall the old prototype first**, then install this one. The version code changed, but a clean install avoids stale permission state.
4. Open **Calm Reader Mobile** and grant the accesses below.

This is a debug APK for testing, not a Play Store release.

### If a main-app button looks dead

Build `1.6` shows a small diagnostic line under the status message. It should include `v1.6`, a tap counter, `native yes`, `overlay yes`, and `bridge ready`. Tap any button: the counter must increase and the message should briefly say `Tapped: …`. For the two **Open** buttons the message then changes to `Opening … settings…` immediately, and Android should switch screens right away. Build `1.6` also shows a native setup dialog at launch when a permission is missing. If the tap counter does not move and no setup dialog appears, the phone is still running an older APK — uninstall and reinstall.

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
- **Bubbles: On/Off** — enable local OCR for image speech bubbles (on by default).
- **Slower / Faster** — five speeds, now close to the desktop extension's pace.
- **Close controls** — hide the panel; the circle remains.

## Text and bubble pauses

- The reader uses the desktop extension's motion model: a reading line at 42% of the screen, one continuous velocity-ramped glide toward the next unread stop, a dwell whose length comes from the text, longer rests on artwork, and a faster cruise through empty gaps.
- Dwell timing is independent of the voice, so a slow or unavailable local voice can never freeze scrolling.
- For image-only chapters, **Bubbles** is on by default. Allow screen capture once when asked. OCR runs on the device using a model bundled inside the APK. The floating controls are hidden during each crop, frames are throttled, stale frames are discarded, and repeated frames are ignored.

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

- APK content audit: all checks passed, including new v1.6 checks for build string, composite stop keys, per-line OCR stops, and continued glide gestures.
- Java compile, Gradle lint, and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Bundled OCR model assets and native libraries present.
- Target scoping, text/bubble dwell, gesture scrolling, voice comfort, and no skipped/repeated speech still need a real-device check by the user.

## Source

Durable source copy: `calm-reader-mobile-src`.
