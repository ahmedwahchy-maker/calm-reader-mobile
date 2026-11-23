# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.7` (current-screen reading-line stops + continuous fast glide)
- Minimum Android: 7.0 (API 24)
- Size: 24,741,180 bytes
- SHA-256: `6FE83D7331E8595F0606E9FED6EC49BA3F8DDD7294361E8919E87A98D9D49416`

## What changed in 1.7

- **Stops are chosen from the current screen.** Fresh on-device OCR of the visible frame runs every ~450 ms and a stop is triggered when text crosses the reading area nearest 42% of the screen. Accessibility-exposed text is used as a fallback for apps that expose it.
- **Velocity is no longer thrown away between gesture runs.** Short Android drag segments now keep their speed, so the glide behaves like one continuous extension-style motion instead of repeatedly starting from zero.
- **Faster through gaps, still slow at text.** Five speeds are now roughly 900/1300/1800/2400/3200 px/s, with acceleration 4200 px/s², braking 5200 px/s², a 4800 px/s cruise ceiling, and a 2.1× gap multiplier.
- **Bubble reading is forced on for existing installs.** v1.5/v1.6 could leave the saved setting off, which meant image comics never got OCR stops.
- **Capture consent is asked while you are still in the app.** Tapping **Start floating reader** now opens the screen-capture prompt when bubble reading is on, so local OCR is not left blind.
- **Proportional reading rests remain.** Dwell still follows the extension's ~210 wpm model plus a short bonus, clamped from 0.8 s up to 90 s, so long bubbles get proportionally long rests.

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. **Uninstall the old prototype first**, then install this one. The version code changed, but a clean install avoids stale permission state.
4. Open **Calm Reader Mobile** and grant the accesses below.

This is a debug APK for testing, not a Play Store release.

### If a main-app button looks dead

Build `1.7` shows a small diagnostic line under the status message. It should include `v1.7`, a tap counter, `native yes`, `overlay yes`, and `bridge ready`. Tap any button: the counter must increase and the message should briefly say `Tapped: …`. For the two **Open** buttons the message then changes to `Opening … settings…` immediately, and Android should switch screens right away. Build `1.7` also shows a native setup dialog at launch when a permission is missing. If the tap counter does not move and no setup dialog appears, the phone is still running an older APK — uninstall and reinstall.

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
- **Slower / Faster** — five speeds, now close to the desktop extension's pace.
- **Close controls** — hide the panel; the circle remains.

## Text and bubble pauses

- The reader uses the desktop extension's motion model: a reading line at 42% of the screen, one continuous velocity-ramped glide, a dwell whose length comes from the text, longer rests on artwork, and a faster cruise through empty gaps.
- Stops are chosen from text currently on screen, not predicted from old offscreen positions. This is what makes it work "blind" with image-only chapters.
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

- APK content audit: all checks passed, including v1.7 checks for the build string, current-screen reading-line stops, visible-frame stop tracking, first-run bubble-reading migration, composite stop keys, per-line OCR stops, and continued glide gestures.
- Java compile, Gradle lint, and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Bundled OCR model assets and native libraries present.
- Target scoping, text/bubble dwell, gesture scrolling, voice comfort, and no skipped/repeated speech still need a real-device check by the user.

## Source

Durable source copy: `calm-reader-mobile-src`.
