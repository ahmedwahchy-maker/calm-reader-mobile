# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.9` (strict single-app scoping)
- Minimum Android: 7.0 (API 24)
- Size: 16,656,817 bytes
- SHA-256: `CC7FD25B2BEB20420898C7BCA473BB8CD97847FF3F549C71A2770780BC68E7EB`

## What changed in 1.9

- **No target, no scrolling.** An empty target used to mean "Any app", so the reader could glide over the launcher, Settings, the phone app, or Calm Reader's own screen. Scrolling is now refused until you tap **Use this app** in the app you actually want to read.
- **The active window is checked live.** The old code trusted an accessibility event and ignored launcher/system windows, leaving a stale target package while gestures were delivered to whatever was actually on screen. Every tick and every gesture callback now re-reads the window that receives gestures and stops immediately if it is not the chosen app.
- **A continued gesture cannot escape the target.** The gesture completion path used to call the next segment directly, bypassing the target check. It is now gated too, and any in-flight stroke is invalidated without sending another gesture when scope is lost.
- **"Use this app" reads the live window.** It no longer uses the stale package from an event. The floating panel shows a small `Active window:` line so you can see exactly what the reader detected.
- **Accessibility text is target-only.** Text nodes from overlays, system dialogs, and other apps can no longer become stops. OCR likewise discards its result if the screen changed while recognition was running.
- **Clear now stops first.** **Clear** removes the target and stops scrolling instead of switching to an unscoped "Any app" mode.
- **Voice remains diagnostic only.** It never controls pacing or stops, and everything still runs locally.

## Install

1. Copy the APK to your Android phone.
2. Open it and allow installation from that file manager/browser when asked.
3. **Uninstall the old prototype first**, then install this one. A clean install avoids stale permission state.
4. Open **Calm Reader Mobile** and grant the accesses below.

This is a debug APK for testing, not a Play Store release.

### If a main-app button looks dead

Build `1.9` shows a small diagnostic line under the status message. It should include `v1.9`, a tap counter, `native yes`, `overlay yes`, and `bridge ready`. Tap any button: the counter must increase and the message should briefly say `Tapped: …`. For the two **Open** buttons the message then changes to `Opening … settings…` immediately, and Android should switch screens right away. Build `1.9` also shows a native setup dialog at launch when a permission is missing. If the tap counter does not move and no setup dialog appears, the phone is still running an older APK — uninstall and reinstall.

## Set up once

1. Tap **Drawing access** and allow Calm Reader to display over other apps.
2. Tap **Accessibility**, find **Calm Reader scrolling**, and switch it on.
3. Tap **Start floating reader**. A small green circle now stays on top of whatever you open. Allow screen capture when asked so image bubbles can be read locally.

You can drag the circle anywhere. It avoids browser controls, scrollbars, and safe areas.

## Read only one app

1. Open the manga/manhwa or website app you want to read.
2. Tap the floating circle to open the controls.
3. Tap **Use this app**. The panel now shows `Reading only <app>` and the detected `Active window:`.
4. Tap **Start scrolling**.

Calm Reader will not scroll anything else. If you switch to the launcher, Settings, the phone app, or back into Calm Reader itself, it pauses with **Waiting for the chosen app** until you return to the chosen app. With no target chosen it stays stopped and says **Choose an app first**.

## Controls in the floating panel

- **Start scrolling / Pause** — start or stop the gentle glide (needs a chosen app).
- **Use this app** — scope reading to the live app currently in front.
- **Clear** — stop and remove the target; choose an app again before scrolling.
- **Read now** — speak exposed text or run bubble OCR immediately, only in the chosen app.
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

- APK content audit: all checks passed, including v1.9 checks for strict target scoping, live active-window verification, target-only stops, no Any-app mode, CJK recognizers and bundled models, OCR in-flight guard, repeat-dwell suppression, current-screen reading-line stops, visible-frame stop tracking, first-run bubble-reading migration, composite stop keys, per-line OCR stops, and continued glide gestures.
- Java compile, Gradle lint, and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Only ARM phone ABIs are packaged; the APK is under GitHub's 25 MB upload limit.
- Bundled OCR model assets and native libraries present.
- Target scoping across app switches, text/bubble dwell (including Japanese/Korean), gesture scrolling, voice comfort, and no skipped/repeated speech still need a real-device check by the user.

## Source

Durable source copy: `calm-reader-mobile-src`.
