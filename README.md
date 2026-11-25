# Calm Reader Mobile

A floating, hands-free reader for Android. It stays with you across apps, but scrolls and reads **only the app you choose**.

This is a local-only prototype. It uses the phone's own on-device Android `TextToSpeech` voice and bundled ML Kit OCR. It has no `INTERNET` permission and makes no external requests.

## APK

Download [`calm-reader-mobile-prototype-debug.apk`](https://raw.githubusercontent.com/ahmedwahchy-maker/calm-reader-mobile/main/calm-reader-mobile-prototype-debug.apk).

- Package: `com.calmreader.mobile`
- Version: debug prototype `1.13` (length-based text/bubble dwells)
- Minimum Android: 7.0 (API 24)
- Size: 16,740,880 bytes
- SHA-256: `3AA2742E909F8E1E0B2DAC671545F9FEC7F547634032C2F13D8217A4FDB1BC26`

## What changed in 1.13

- **The reader now really stops to read.** Stop checks run while a scroll gesture result is still pending, and the gesture callback immediately checks the reading line again. Text and OCR bubbles are no longer skipped just because a gesture is in flight.
- **Dwell length follows the text.** Short bubbles rest at the 800 ms floor; longer bubbles and exposed text rest longer, using the same 210-wpm-based `dwellFor()` calculation as the desktop extension. Voice speed never affects the pause.
- **OCR bubbles use the same reading rest.** A recognised bubble begins the same text-length dwell as native text and is announced locally as **Reading bubbles**.
- **Fixed the capture stall.** The `ImageReader` was returning before it acquired/closed throttled frames. With `maxImages = 2`, that filled the queue and stopped all later frames, so OCR never saw a bubble. Every frame is now acquired first and closed whether or not it is used, keeping OCR fed.
- **Gesture safety from 1.12 is kept.** Only one gesture result can be pending at a time; stops never lift a finger over a pending result, and missing callbacks fail safe instead of overlapping strokes.

## What changed in 1.12

- **Gesture callbacks are strictly serialized.** Only one scroll gesture can be in flight at a time. A pending result must arrive before another segment is dispatched, so Android 10 can no longer deliver `onPerformGestureResult` to a client that has already been cleared.
- **No lifting gesture on top of a pending result.** Stopping, pausing for a dwell, or losing scope now drops the continuation locally and lets the existing callback finish it. The old extra "lift" stroke was the direct trigger for the `system_server` crash.
- **Stalls fail safe.** If a callback does not arrive, the reader stops with **Scroll gesture stalled. Tap Start again.** instead of freeing the slot and overlapping gestures. Accessibility interruption aborts the continuation without dispatching anything new.
- The result is a steady real-site glide with local text/bubble dwells and no gesture-callback crash.

## Earlier fixes in 1.11

- **No stale gesture continuations.** Text/bubble stop checks no longer lift the virtual finger while a scroll segment is still being injected. A late lift or reconnect invalidated the old callback and started a fresh stroke.
- The result was a steadier real-site glide: text and image bubbles still paused locally, but the finger was never lifted from an in-flight stroke.

## Earlier fixes in 1.10

- **One capture-consent prompt.** A pending prompt is now tracked so duplicate requests from the main UI, the floating panel, and the reading loop cannot queue multiple system dialogs.
- **Capture restart no longer self-stops.** The old `MediaProjection` callback is unregistered before a planned restart, so its `onStop()` cannot shut down the replacement session.
- **Accessibility reconnects stay safe.** A brief service interruption pauses the glide instead of issuing a gesture through a stale callback; reconnecting the service resumes the user's existing scroll request.
- **Temporary logs removed.** The diagnostic `CalmDiag` logging used while testing is gone from both the source and the APK. State shown in the app remains numeric/boolean only.

### Earlier fixes in 1.9

- **No target, no scrolling.** An empty target used to mean "Any app", so the reader could glide over the launcher, Settings, the phone app, or Calm Reader's own screen. Scrolling is now refused until you tap **Use this app** in the app you actually want to read.
- **The active window is checked live.** The old code trusted an accessibility event and ignored launcher/system windows, leaving a stale target package while gestures were delivered to whatever was actually on screen. Every tick and every gesture callback now re-reads the window that receives gestures and stops immediately if it is not the chosen app.
- **A continued gesture cannot escape the target.** The gesture completion path used to call the next segment directly, bypassing the target check. It is now gated too, and any in-flight stroke is invalidated without sending another gesture when scope is lost.
- **"Use this app" reads the live window.** It no longer uses the stale package from an event, and the floating panel shows the detected active window.
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

Build `1.13` shows a small diagnostic line under the status message. It should include `v1.13`, a tap counter, `native yes`, `overlay yes`, and `bridge ready`. Tap any button: the counter must increase and the message should briefly say `Tapped: …`. For the two **Open** buttons the message then changes to `Opening … settings…` immediately, and Android should switch screens right away. Build `1.13` also shows a native setup dialog at launch when a permission is missing. If the tap counter does not move and no setup dialog appears, the phone is still running an older APK — uninstall and reinstall.

## Set up once

1. Tap **Drawing access** and allow Calm Reader to display over other apps.
2. Tap **Accessibility**, find **Calm Reader scrolling**, and switch it on.
3. Tap **Start floating reader**. A small green circle now stays on top of whatever you open. Allow screen capture when asked so image bubbles can be read locally.

You can drag the circle anywhere. It avoids browser controls, scrollbars, and safe areas.

## Read only one app

1. Open the manga/manhwa or website app you want to read.
2. Tap the floating circle to open the controls.
3. Tap **Use this app**. The panel now shows `Reading only <app>` and the detected active window.
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

- APK content audit: all checks passed, including v1.13 dwell checks (stop checks before the pending-result gate, direct line check in gesture callbacks, text-length `dwellFor()`, bubbles using the same dwell, capture queue always drained, no temporary probe logs) plus the v1.12 serialized gesture checks, the earlier capture prompt guard, capture callback unregister, no diagnostic logging, strict target scoping, live active-window verification, target-only stops, no Any-app mode, CJK recognizers and bundled models, OCR in-flight guard, repeat-dwell suppression, current-screen reading-line stops, visible-frame stop tracking, first-run bubble-reading migration, composite stop keys, per-line OCR stops, and continued glide gestures.
- Java compile, Gradle lint, and web JavaScript syntax checks passed.
- No external URLs and no network fetch/XHR in the bundled web app.
- No `INTERNET` or `ACCESS_NETWORK_STATE` permission in the merged manifest.
- Only ARM phone ABIs are packaged; the APK is under GitHub's 25 MB upload limit.
- Bundled OCR model assets and native libraries present.
- Device validation: strict single-app scoping, continued glide, and service-reconnect resilience were verified during testing. The final `1.13` APK showed the correct build label (`versionName=1.13`, `versionCode=15`), started the overlay, produced one capture-consent prompt, and kept `system_server` on PID 13990.
- A temporary probe build on Asura Scans Chapter 7 in Brave proved the capture fix and the dwell behaviour: OCR frames arrived continuously (`ocr items > 0`), bubbles and native text both started dwells (`Reading bubbles` / `Reading text`), short text rested at 800 ms, 10–15 character text rested 921–1207 ms, and a 36-character stop rested 2636 ms, with matching `dwell_end` gaps. All temporary probe logging was removed from the final source and APK.
- A clean final real-site pass on the exact published APK ran a 45-second glide on the same chapter, held the scroll wake lock, advanced from Page 1 to Page 4, emitted zero probe-log lines, and produced no new `system_server_crash` entry. The three historical `MotionEventInjector`/`onPerformGestureResult` dropbox entries all predate this validation (latest at 2026-11-25 17:54).

## Source

Durable source copy: `calm-reader-mobile-src`.
