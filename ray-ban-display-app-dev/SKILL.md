---
name: "ray-ban-display-app-dev"
description: "Use when building or debugging an Android app for Meta Ray-Ban Display with the Wearables DAT SDK 1.0 (session, display, inputs, Neural Band, camera, speech, motion, on-phone vision/AI): device-verified facts, build environment, verification. Web Apps: Meta's webapp plugin leads."
---

# Ray-Ban Display app development (Meta Wearables DAT SDK 1.0.0)

Facts verified on device (Meta Ray-Ban Display, firmware 128.0.0.172.321, Galaxy S25 / Android 16) or read from the 1.0.0 jars and the official samples DisplayAccess and BirdSpotter (Fridge Assistant 2026-09/10; HackIX2026 / Bee There 2026-10-02 — HackIX device notes did not record firmware, last recorded 128.0.0.172.321). Anything marked *unverified* needs a device test before you rely on it.

Second hardware source: hackix26 / OCD Companion (2026-10-03): Samsung S25 / Android 16, firmware 128.0.0.172.321, Meta AI app 293.0.0.17.161, Neural Band, Developer Mode. Where two projects saw different things, both results are kept with their date and app, and the safe rule is stated.

## Setup

- Maven Central `com.meta.wearable:mwdat-core / -camera / -display / -inputs / -speech / -motion / -mockdevice:1.0.0`. No GitHub Packages token is needed any more (older docs, e.g. a team doc written for v0.7, say GitHub Maven + `read:packages` token: outdated).
- Minimums for DAT 1.0.0 (Meta "Version Dependencies" page): glasses firmware **V128**, Meta AI app **V290**. Older docs that say firmware v21 / app v254 are for older DAT versions.
- Developer Mode: Meta AI app → Settings → App Info → tap the version 5×. Leaving `mwdat_application_id` / `mwdat_client_token` empty in `local.properties` = Developer Mode, which works [DEVICE, hackix26]; Meta's docs use `"0"` for both — either is fine, never real values in git. Never ship real credentials inside a public APK. If registration or sessions suddenly fail after a Meta AI app or firmware update, re-check Developer Mode first (it can reset, and it is per linked device — `mwdat-android:debugging`).
- Display, Inputs, Motion and Speech are **beta** capabilities; capabilities can also be gated server-side per user (`CAPABILITY_DENIED`). Per Meta's plugin skills: Inputs, Motion, Speech, standalone Camera Capture, Audio Streaming and Voice Invocations must be enabled for the app in Wearables Developer Center and **cannot go to production release channels yet**; an Inputs denial is `InputsError.PERMISSION_DENIED` (not a runtime permission, `requestPermission` doesn't fix it). Developer Mode builds worked without this [DEVICE, hackix26]. Check this before promising a public APK.
- Meta's own Claude Code tooling: `claude plugin marketplace add facebook/meta-wearables-dat-android`, then `claude plugin install mwdat-android@mwdat-android-marketplace`; docs MCP at `https://mcp.developer.meta.com/wearables` (`search_dat_docs`). Installing the plugin already registers that MCP server — don't add it again in `.claude/settings.json`. Web Apps have their own Meta plugin (`meta-wearables-webapp`, games: `meta-wearables-webapp-game`).
- Build environment (school PC, checked 2026-10-06): each project pins Gradle in its own wrapper — FridgeAssistance / LiftLensMini: Gradle 8.14.1 + AGP 8.13.2 / 8.11.1; HackIX2026 / VisualMemory: Gradle 9.6.0 + AGP 9.4.1; hackix26: Gradle 8.13. DAT 1.0.0 works on both AGP lines; LiftLensMini is still on DAT 0.9.0. Android Studio's bundled JDK 25 cannot run Gradle 8.x → Gradle JDK = JBR 21 (`~/.jdks/jbr-21.0.11`, also `JAVA_HOME`). Modules target JVM 17 bytecode without `jvmToolchain(17)` (no JDK 17 installed). Run the wrapper: `.\gradlew.bat <task>` in PowerShell, `./gradlew <task>` in Git Bash.
- Meta plugin skills on Windows: their examples assume bash/macOS. Gradle → `.\gradlew.bat`; port check → `Get-NetTCPConnection -LocalPort 5173` instead of `lsof`; Chrome for CDP → the PowerShell block in `ai-glasses-webapp-optimize-performance`, not `npm run chrome`; MockDevice video → `ffmpeg -i in.mp4 -c:v libx265 -tag:v hvc1 -vf scale=540:960 out.mp4` (not `videotoolbox`). Node/npm are not installed on the school PC (2026-10-06).
- Manifest: `BLUETOOTH`, `BLUETOOTH_CONNECT`, `INTERNET`; meta-data `com.meta.wearable.mwdat.APPLICATION_ID` and `CLIENT_TOKEN` (values from `local.properties` via manifest placeholders, never committed); opt out with `ANALYTICS_OPT_OUT` and `CRASH_REPORTING_OPT_OUT` = true. Registration and permissions go through the Meta AI app and come back on a custom deep-link scheme (VIEW intent filter).
- The DAT libraries merge no CAMERA permission, so the phone camera intent (`TakePicture`) works without asking.
- Capabilities belong to one `DeviceSession`; when the session ends (fold, Bluetooth drop) they are gone and must be added again on the new session.
- Attach with patience: a capability requested while the device is still arriving fails once (DEVICE_DISCONNECTED) and never retries by itself. Retry every 750 ms (about 40 tries) while the session is STARTED.
- After the glasses reset the Bluetooth socket, the SDK link can stay DISCONNECTED for 100+ s; a fresh app process reconnects in under a second. `Wearables.reset()` does not close channels. Restarting the process is the proven recovery.
- Battery: `sessionState.device.batteryLevel`. Battery drain while streaming the camera is **unmeasured** (no Meta figure either) — never quote a number.
- [DEVICE, hackix26 2026-10-03] Display, inputs and camera attached on try 1; auto-reconnect after the glasses ended the session reopened it in ~0.5–1 s; a session start with the glasses folded fails with `NO_ELIGIBLE_DEVICE`; folding ends the session. Track wear state (worn / not worn / folded) from the session and end sensor modes on doff.
- DAT stops inputs on doff and does not restart them on a plain resume (found in SDK bytecode + review, not seen on hardware): drop dead inputs during a pause and add them again on session start.
- Exactly **one class opens the DAT session** in an app (e.g. `GlassesSessionManager`); features ask it for display / camera / inputs. Two owners = fights over capabilities.

## Display (`session.addDisplay()`)

- `display.state`: STARTING, STARTED, STOPPING, STOPPED, CLOSED. Send only after STARTED; nothing is resent automatically after a reconnect.
- `display.sendContent { … }` replaces the whole screen and resets the cursor to the first button. `clearDisplay()`, `stop()`, `session.removeDisplay()` to take it down.
- Canvas 600 × 600. Hangul renders. The display adds light: black is see-through, large bright areas hide the world, so use dark backgrounds with bright content and bright marks for status (not dark fills).
- DSL (names checked against the jar):
  - `ContentScope.flexBox(direction, gap, alignment, crossAlignment, wrap, padding, …, background = FlexBoxBackground.NONE | CARD, onClick, block)`; `video(VideoPlayer)`.
  - `FlexBoxScope.flexBox(… , onClick, flexGrow, flexShrink, alignSelf, block)` — the content lambda parameter is called `block`.
  - `text(text, style = TextStyle.HEADING | BODY | META, color = TextColor.PRIMARY | SECONDARY, flexGrow, …)` — only two text colors.
  - `image(uri = … or bitmap = …, sizePreset = ImageSize.ICON | FILL, cornerRadius = CornerRadius.NONE | SMALL | MEDIUM)` — app-drawn bitmaps work (thumbnails, colored dots).
  - `icon(IconName.X, style = IconStyle.FILLED | OUTLINE)`; about 130 IconName values (e.g. CALENDAR, CLOCK, CHECKMARK, FORK_KNIFE, CART, CONTAINER_WITH_LID, EYE, FOUR_CORNER_FRAME, HOUSE, ARROW_LEFT, CARET_RIGHT, META_AI, PAPER_AIRPLANE, PHONE, PLUS, X, TWO_ARROWS_CLOCKWISE). There is no camera icon. Dump the enum with javap before using a new one.
  - `buttonGroup { button(label, style = ButtonStyle.PRIMARY | SECONDARY | OUTLINE, iconName, onClick, actionRole = ActionRole.PRIMARY) }` — ActionRole.PRIMARY marks where the cursor starts [JAR/SAMPLE — not yet measured on device; until measured, also put the next action first].
  - Clickable card rows: `flexBox(padding = 12, background = CARD, onClick = …) { row: column(flexGrow 1){ image FILL }, column(flexGrow 5){ text … } }` (DisplayAccess pattern). *Unverified on our device:* cursor movement over clickable cards, send time with several images, how many rows fit — measure with a display test screen first.
- One render path: snapshot → card model (pure, unit-tested) → send only when the card changed (an identical resend still resets the cursor). Load bitmaps before sending; keep them small (≈128 px rows, 256 px hero).
- When several features can draw, put one **display arbiter** in front of `sendContent` (owner + priority, e.g. safety > conversation > nudge > idle; it re-sends only when the top card changes). A transition is one send: post the new top card first, then release the old one, so the old card never flashes.
- Retry a failed send (twice worked in hackix26) and redraw the current card when the display returns to STARTED. A card with no title, body or buttons can render as a single invisible space = blank glasses (*unverified* on device).
- Meta's docs say a send can take up to 5 s; measured full-screen sends were ~170–195 ms (below).
- [DEVICE, 2026-10-04, hackix26 `team/one-app`] The display failed with `SERVICE_NOT_FOUND`, and Meta's own sample app showed nothing either → a platform/device-state problem, not app code. Resolution not recorded: if it happens, test the official sample first, then restart glasses, Meta AI app and phone. If the DAT Inspector MCP is available, run `mwdat-android:live-debugging-mcp` first.
- [DEVICE, HackIX 2026-10-02] Full-screen send time: 192 px bitmap + SDK text averaged 187–195 ms at 250/500/1000 ms intervals; one 384 × 304 bitmap with text drawn into it averaged 169 ms (853 frames at 500 ms).
- [DEVICE] **SDK `text` flickers on every full-screen resend; an `image` does not.** On any screen that is resent repeatedly, draw captions into the bitmap (Android Canvas) instead of using `text`.
- [DEVICE] `flexBox(onClick)` on a card that is being re-sent for animation does **not** register clicks. Animated screens get their input from the inputs capability, not from DSL clicks.

## Inputs (`session.addInputs`)

- **Always pass `sources` explicitly.** Meta's `mwdat-android:inputs` sample uses `InputsConfiguration(consumeBack = true)`, whose default subscribes every source (incl. `NEURAL_BAND`, `NEURAL_BAND_DRAG`, `CAPTOUCH`) — that takes the band and temple away from display buttons [DEVICE, see below]. Don't copy the sample's default into a button-card app.
- `InputsConfiguration(sources = …, consumeBack = true)`. Subscribe only the sources the app truly handles itself: Fridge used `setOf(CAPTOUCH, CAPTURE_BUTTON, ACTION_BUTTON)`, but subscribing `CAPTOUCH` takes temple taps away from the cards (hackix26), so card-based apps may leave it out. CAPTURE_BUTTON alone once delivered nothing (BirdSpotter); without `consumeBack` Back ends the whole session.
- Events: `InputEvent.Capture(pressType = SHORT_PRESS | HOLD | DOUBLE_PRESS, source)`, `Back`, `Nav`, `Select`. The Neural Band moves the cursor and is handled by the system.
- Full enums [JAR, `mwdat-inputs-1.0.0`]: `InputEvent` = `Nav(UP/DOWN/LEFT/RIGHT)`, `Select`, `Back`, `Button(ACTION)`, `Capture(SHORT_PRESS/HOLD/DOUBLE_PRESS)`, `Drag(DOWN/MOVE/UP, x, y, dx, dy)`; `InputSource` = `CAPTOUCH`, `NEURAL_BAND`, `CAPTURE_BUTTON`, `ACTION_BUTTON`, `NEURAL_BAND_DRAG`, `UNKNOWN`. Inputs report decoded interactions, never raw sensor signal.
- **A source the app subscribes is taken away from the glasses UI.** This is the one rule both projects agree on.
- Neural Band — default (recommended for any app whose screens are button cards): **do not subscribe the band sources.** The band then presses display buttons natively: index pinch = press the focused button, thumb swipe = move between buttons [DEVICE, hackix26 2026-10-03: Start, Register, Cancel, End, Pause, Not now, Done; no accidental presses during real door-lock checks with the band hand].
- Neural Band — when subscribed, results differed by project (keep both; reproduce in your app before relying on either):
  - [DEVICE, hackix26 2026-10-03, `NEURAL_BAND` + `NEURAL_BAND_DRAG`] attach worked. Index pinch → a short `Drag` DOWN/MOVE/UP with near-zero x/y, then `Select`; thumb swipes → `Nav` RIGHT/LEFT/UP; pinch-and-drag → a `Drag` stream (an event every ~55 ms, gaps 16–101 ms) with x/y growing to ~0.35. While subscribed, the band did **not** press display buttons (its `Select` went to the app); after removing the sources, the same button worked at once.
  - [DEVICE, HackIX2026 / Bee There 2026-10-02] `NEURAL_BAND` in `sources` → attaching inputs failed and no input worked. With `NEURAL_BAND_DRAG` only, an index pinch arrived as DOWN … UP with x, y, dx, dy = 0.00 → treat any DOWN…UP as one "pinch". With drag off, a pinch on an image-only screen did nothing; with drag on, pinch stopped selecting buttons → switch drag **off while a decision card is up** and on for image screens (`wantsBandDrag = !isChoiceScreen`).
- Temple touchpad (`CAPTOUCH`): when the app subscribed it, temple taps stopped pressing cards (hackix26); leaving it to the display should restore them (E1, *unverified*).
- The band gives apps **no** wrist motion, heart rate or EMG (see Motion).
- Back: [DEVICE] Middle-finger pinch (system Back) closed the glasses app on every screen, with any inputs configuration and `consumeBack = true`; no Back event reached the app (HackIX2026, DAT 1.0.0, 2026-10-02). Fridge Assistant (2026-09-30) once logged Back reaching the app, gesture source not recorded. So Back behaviour is **not** a fixed SDK fact: run the Back check below before deciding.
- A still photo crossing the link can drop inputs ACTIVE → INACTIVE with no restart of its own: remove and add the capability again once the photo is through (only if it had been ACTIVE).
- Ignore presses within ~400 ms of a new card (double press on Save would hit the next card's first button).

### Back check (before designing any flow that could use Back)

1. Inputs with the project's real configuration and `consumeBack = true`; log every `InputEvent` with its source.
2. On an image screen, a decision card and an animated screen, trigger each Back gesture the device offers (at least the Neural Band middle-finger pinch).
3. Record per gesture: event reached the app / app closed / nothing, plus SDK, firmware, phone, date.
4. Back arrives everywhere → Back can be a stack. App closes or no event → design without Back (`glasses-hud-design-rules` rule 15). Re-run after SDK or firmware updates.

## Motion (`session.addMotion(MotionConfiguration(samplingRate))`, beta)

- [JAR, `mwdat-motion-1.0.0`] `Motion.start()/stop()`, `samples: Flow<MotionSample>`, `state`, `errors`. `MotionSample(timestampNs, accelerometer?, gyroscope?, magnetometer?, orientation?, source)`; `MotionSource` = `GLASSES`, `NEURAL_BAND`, `UNKNOWN`. **No source selector** — filter by `source`. Rates `HZ_5 … HZ_60` (a target, not a guarantee), 256-sample buffer, `DROP_OLDEST`. Units m/s² (with gravity), rad/s, µT, quaternion; no coordinate frame documented. No runtime permission. Collect before `start()`. A still photo stops Motion. Raw EMG: no API in any of the 7 DAT 1.0.0 jars.
- [DEVICE, hackix26 2026-10-03, "Wrist lab" probe] `addMotion(HZ_60)` attached on try 1 in Developer Mode, STARTED in ~0.3–0.4 s, 57–63 Hz observed, median dt 16.7 ms, all fields filled after the first second. **8,783 samples, all `GLASSES`, 0 `NEURAL_BAND`** — the samples followed the head (mean |gyro| 2.04 rad/s head-only vs 0.08 wrist-only vs 0.02 still). Motion and a display card coexisted. Gravity at rest mostly on +Y.
- Consequence: head IMU is usable (head nod, "is the wearer facing X" gating — both *unverified* as features); wrist detection is not possible natively today. Re-run the probe after SDK/firmware updates (a `first NEURAL_BAND sample` log line would reopen it). Mock Device Kit replays motion as `GLASSES` only, so band provenance can only be tested on hardware.
- Wrist alternative if a project needs it: a same-wrist Wear OS watch IMU streaming to the phone.

## Speech (`session.addSpeech(SpeechConfiguration())`)

- Needs the Meta `Permission.MICROPHONE`. `start()` / `stop()`; flows `state`, `transcriptions` (TranscriptionResult: text, isFinal, confidence), `locale`, `errors`, `errorDetails`.
- No language setting: `SpeechConfiguration` takes only a dispatcher, `locale` is read-only and came back empty. English is transcribed exactly ("Milk Two bottles."); Korean comes out as English sounds. Plan voice features in English; other languages would need Android `SpeechRecognizer` on the phone mic. Confirmed independently three times (Fridge Assistant F6, Bee There, Riya's obsessive agent).
- Bluetooth HFP audio is 8 kHz mono (raw glasses audio at 16/44.1/48 kHz PCM via the camera stream: `mwdat-android:audio-streaming`, experimental, needs CAMERA + MICROPHONE). The glasses mic does not capture the other person's voice well (Bee There) — bundle a transcript for demos.
- "Hey Meta, start <app>" launch exists in the docs but needs a Developer Center permission; *untried* — see `mwdat-android:voice-invocations`.

## Camera

- `Stream.capturePhoto()` while streaming: HEIC, decodes to 1440 × 1080, 1–4 s; no resolution option. It can restart the video at 360 × 640.
- `Camera.photo.capturePhoto(PhotoResolution SMALL | MEDIUM | LARGE | FULL, PhotoQuality LOW | MEDIUM | HIGH)` only in photo-only mode with `Photo.state == STARTED`.
- Camera stream, display and inputs work together in one session [DEVICE, Fridge + hackix26]. Stop the camera after ~30 s idle; keep the phone screen on while a HUD is shown (the camera stops in the background — a foreground service would be needed for locked-screen use).
- [DEVICE, hackix26 2026-10-03] LOW stream at 15 fps = **360 × 640 I420** (345,600 bytes), rotate 0°; first frame ~3.5 s after attach; ~10 frames/s processed with on-phone MediaPipe. Docs also list 504 × 896 and 720 × 1280 at 2/7/15/24/30 fps.
- [DEVICE] **Read `camera.stream` before `camera.stop()`, and always `close()` the camera.** Reading it after `stop()` threw, `close()` was skipped, and the glasses ended the whole session ~4 s later.
- Release order: read `camera.stream` → `stop()` → `close()` → `session.removeCamera()` [JAR; Meta `camera-streaming`], then `addCamera` again.
- Re-adding a camera right after releasing it failed once (`CRITICAL_STREAM_ERROR`, no frame): wait ~2 s before re-adding and retry once if the stream fails before its first frame (fix unit-tested only).
- Whether the capture LED lights for third-party streaming is undocumented — don't claim it either way.

## On-phone vision (MediaPipe Tasks Vision 1.0.0, hackix26)

- Hand Landmarker (21 keypoints) + Image Embedder (MobileNet V3 Small; "does the centre of the view match the registered object?"), models in `assets/` (Apache-2.0). [DEVICE] hands 20–77 ms, embedding 1–11 ms, model load 43–121 ms on the S25.
- MediaPipe sends its own image-free usage stats via Google datatransport → say "frames never leave the phone", never "the app sends nothing". APK grows to ~127 MB with DAT + MediaPipe natives for 4 ABIs (arm64-only would shrink it).
- Replay recorded clips through the same pipeline with sequential `MediaCodec` decoding (~45× faster than per-frame seeking; an 8 s clip in ~3 s).

## Sprite animation on the glasses (HackIX2026)

- Crop every frame of a sheet with **one shared crop rectangle** (else the cat jitters), scale nearest-neighbour (288 px cat in a 384 × 360 image worked), draw on black (= see-through), draw up to two caption lines into the same bitmap (24 px text readable). Cache bitmaps; never decode per frame.
- Frame loop = **single-flight sender**: skip a frame while the previous send is pending; interval a named constant (500 ms default, ≥1000 ms for "less movement"); log send timestamps.
- Cancel the loop on display STOPPED, session end, leaving the screen, and process death. Keep the engine state outside the loop so reopening the app resumes (e.g. a Focus timer from `elapsedRealtime`).
- Decision screens stay static (buttons need a stable cursor); animated screens hold zero SDK buttons.

## AI on the phone (Muse via the Meta Model API — Fridge Assistant + HackIX pattern)

- Key and model id in `local.properties` → `BuildConfig` fields (`buildConfig = true`); never in git, logs or chat. Each project gets its own key.
- Low reasoning effort, ~6000 output tokens, structured JSON output; on HTTP 400/422 retry once without the schema and parse leniently. `org.json` + `HttpURLConnection`, no new dependency.
- One persona prompt, several narrow structured calls (e.g. `sort(transcript)`, `draft(item)`, `line(event)`). Rule in every prompt: **never invent missing facts** — unknowns stay "not mentioned", with source quotes for what is known. [DEVICE] Muse kept "Due date (not mentioned)" instead of inventing one.
- Explicit opt-in consent switch before anything leaves the phone; a "Muse is off / not set up / failed" card with Try again · Cancel; offline fallback text for demos. Log that a call happened, never its content.

## Web Apps (Ray-Ban Display Web App — CONDUCTOR, personal project, 2026-09)

To build a Web App, use Meta's plugin skills first: `meta-wearables-webapp:ai-glasses-webapp-build` + `-ui` (UI Toolkit, React/Vite/TS, npm with a committed `package-lock.json`), `-device` (inputs, sensors), `-test` (the quality gate), `-optimize-performance`, `-publish` only on request; games: `meta-wearables-webapp-game:*`; docs: `search_webapps_docs`. This section only adds CONDUCTOR's notes (a custom canvas UI without the Toolkit).

- Per Meta's Web App docs (*not device-verified by us*): pinch → `Enter`, thumb swipes → arrow keys, Back → `Escape` or `history.back()`, pinch-and-drag → pointer events (opt-in). That is a richer input surface than a native DAT 0.9 app (onClick only; DAT 1.0 adds Nav/Select/Drag through the inputs capability) — useful as a comparison point.
- Patterns from CONDUCTOR (custom key handlers, no Toolkit): ignore key auto-repeat and duplicates within 80 ms — never on Toolkit `Button`s (one `onClick`, no time-based debounce, per Meta `-device`); when a popup opens, push exactly one history entry so a system Back closes the popup instead of the app; pure black page background (additive display; Toolkit apps use `--uit-color-background-window`), cards dark gray, never `#000`; cap canvas drawing at ~30 fps to leave CPU for audio; create the AudioContext only inside a gesture handler.
- Meta's FAQ mentions "wrist motion from the Meta Neural Band" under Web Apps only; untested.

## Off-device verification

In Claude Code on the school PC, build with the project wrapper (`.\gradlew.bat :app:compileDebugKotlin`, `:app:testDebugUnitTest`). The stub-compiler route below is for Claude.ai/Cowork sandboxes without Gradle. SDK API detail beyond these device facts: `mwdat-android:display-access`, `inputs`, `camera-streaming`, `session-lifecycle`, `mockdevice-testing`.

- Kotlin CLI compiler (same version as the project) with the Compose and serialization compiler plugins, classpath = `android.jar` + the real AAR `classes.jar` files from the Gradle cache (DAT, Compose, ML Kit …) + small stubs (`BuildConfig`, `R`). Compile the whole app, not single files.
- Check SDK identifiers with `javap` on the jar (enums, parameter types); a compile against the real jar also checks named parameters.
- Compose UI can also be compile-checked offline: Kotlin CLI (project version) + Compose compiler plugin against the Compose/material3/activity-compose jars from Hyun's Gradle cache (HackIX UI v1). Files whose jars are missing (e.g. androidx.core/lifecycle for MainActivity) must be reported as not compiled.
- Pure logic (card models, parsers, state machines) in plain Kotlin and unit-tested with JUnit 4 via `org.junit.runner.JUnitCore`; `android.util.Log` stubbed.
- Before writing back to the user's folder: compare device file sizes / mtimes with the last delivered copy, write with `expectedMtimeMs`, scan for secrets, never open files that hold keys. Take commit hashes from `.git/logs/HEAD`, never from memory.

## Design track (Figma)

- Figma write access through MCP needs a paid seat; the free path is a Figma **development plugin** (`manifest.json` + `code.js`, `documentAccess: "dynamic-page"`, async APIs) that generates variables (color Light/Dark — the Starter plan allows one mode), text styles, components and 600 × 600 glasses frames. Add menu commands that add one page without touching the rest.
- Test the plugin against a strict JS mock of the Figma API (all fonts, one font, Starter plan, re-run) before handing it over.
- Keep app tokens and Figma variables in one place each and say which file mirrors which.
- **When Claude cannot open Figma** (school account cannot connect the Figma connector; frame links are unreadable): give the plugin an **"Export selected frames for Claude"** command that writes `<frame>.json` (sizes, auto layout, padding/gaps, colors with variable names, text styles, components/variants; collapse decorative textures) + `<frame>@2x.png`. The designer drops them into `design/specs/`; Claude implements from those files, not from screenshots or memory.
- Art with licence limits (sample sprite sheets) is loaded into the plugin at run time from the app's assets, never embedded in plugin code or committed as PNG exports.
- Implement "no invented content": where the design shows placeholder data the app does not have, show real data or nothing, and list deliberate differences from the frames.


## Evidence and verification

Never confuse API availability, successful compilation, and successful hardware behavior.

Label significant technical claims:

- [DEVICE] Verified on physical hardware.
- [JAR] Verified by inspecting actual SDK binaries.
- [SAMPLE] Observed in an official example.
- [SIMULATED] Verified through tests or simulations.
- [UNVERIFIED] Requires a real-device test.

Hardware findings must include the firmware version, SDK version,
Android version, device model, and test date.

Do not generalize a workaround observed on one firmware and phone
combination into a universal SDK guarantee.

When official documentation is inaccessible, report the limitation
and inspect the installed SDK. Never invent API signatures.

## Multi-project architecture

Projects include:
- LiftLens: workout state, sessions, timers and history.
- Production HUD: production workflows, cues and takes.
- Fridge Assistant: camera, recognition and inventory.
- Visual Memory + Note On: object sightings and contextual notes.
- HackIX2026 (Bee There, OCD Companion / hackix26, Ailo): animated companion, speech → AI sort, camera loop detection, two-agent display arbitration (see `hackix2026-project`).

Before implementing a new capability, inspect the current project
and identify reusable patterns from earlier projects.

Prefer these architectural boundaries:

- DeviceManager: registration and connection.
- SessionManager: DeviceSession lifecycle.
- Capability adapters: camera, display, inputs and speech.
- Domain controllers: application rules and state.
- Repositories: persistent application data.
- Renderers: translate state into glasses UI.

These are suggested boundaries, not mandatory class names.
Reuse proven patterns rather than blindly copying source files.
Avoid introducing shared abstractions before two projects
demonstrate a real need for them.

Each project must remain independently buildable.

## State and lifecycle safety

Every long-running operation must have explicit ownership,
cancellation, timeout and failure behavior.

Prevent:
- Concurrent photo requests.
- Double-submit and duplicate saves.
- Stale callbacks from previous sessions.
- Old camera responses updating a newer draft.
- Inactive activities overwriting the active HUD.
- Unlimited recovery and reconnect loops.

Define whether state survives:
- Display reconnection.
- Bluetooth interruption.
- Glasses removal or folding.
- Android backgrounding.
- Process termination.

Never assume an interrupted operation completed successfully.

## Required hardware regression

Every capability milestone needs a written test checklist.

Test applicable scenarios:
1. Cold launch and registration.
2. Normal connection and capability attachment.
3. Repeated user input.
4. Simultaneous camera, display and inputs.
5. Capture while receiving frames.
6. Bluetooth interruption and recovery.
7. Background / foreground transitions.
8. Session termination and re-creation.
9. Process restart and persistent-state recovery.
10. Permission denial and unavailable capabilities.

Report compilation, unit tests and physical-device tests
as separate outcomes.

Never label a feature complete based only on BUILD SUCCESSFUL.

## Smart-glasses interaction design

Always prioritize glanceability and minimal interaction.

- Present the current state and next logical action.
- Avoid unnecessary display updates.
- Preserve the user's interaction context where possible.
- Never assume arbitrary gesture or cursor control.
- Prefer short labels and shallow navigation.
- Move complex editing to the phone when appropriate.
- Require confirmation before destructive operations.

For frequently updated screens, test whether redraws
interrupt input or reset cursor position.

Measure the real behavior of ActionRole.PRIMARY instead
of assuming identical behavior across firmware versions.

Test actual readability on the additive display.
Phone screenshots alone are insufficient.

## AI and privacy

Prefer local processing when feasible.

Separate:
1. Raw observations.
2. OCR or model-generated interpretations.
3. User-confirmed facts.

Never silently replace user-edited information with AI output.
Do not fabricate missing recognition results.

Cloud transmission must require the appropriate user consent.
Never write API keys, authentication tokens, private images
or sensitive transcription content into diagnostic logs.

Avoid silently uploading photographs or audio.

For Visual Memory specifically:
- Distinguish last recorded location from current location.
- Treat object recognition as uncertain.
- Preserve observation timestamps and original evidence.
- Do not claim spatial tracking without verified support.
- Never claim a physical object is still where it was last seen.

## Performance and battery

Record meaningful performance measurements where applicable:

- Session startup time.
- First camera frame latency.
- Actual frame rate and dropped frames.
- Still-photo capture latency.
- HUD update latency.
- Reconnection time.
- Camera active and idle duration.

Avoid repeatedly sending unchanged display content.
Keep image sizes appropriate for the glasses display.
Release capabilities when they are no longer needed.

Do not invent performance targets. Establish baselines
through real-device measurements first.

## Development documentation

After every verified milestone, update the project's history/devlog per `plan-approve-build-loop` §6 (Fridge: `docs/PROJECT_HISTORY.md`; LiftLens: `DEVLOG_<date>.md`).

Record:
- Features implemented.
- Important architecture decisions.
- Problems encountered and their root causes.
- SDK or hardware discoveries.
- Tests performed and their results.
- Commit references.
- Known limitations.
- Next development milestone.

Distinguish implemented functionality from planned features.

For capstone reporting, explain not only what was built,
but what technical or interaction-design question the
experiment helped answer.

## Changelog

- 2026-10-06 (b): cross-check with Meta plugins — explicit input sources, release-channel gating, Developer Mode `"0"`, `removeCamera()`, Web App routing to Meta's plugin, per-project Gradle/AGP, Windows notes for plugin scripts, plugin cross-references.

- 2026-10-06: hackix26 hardware results (session, camera stream + release order, Motion probe, band and temple inputs, MediaPipe numbers, SERVICE_NOT_FOUND incident); Neural Band subscription results reconciled (10/2 vs 10/3) under "a subscribed source is taken from the UI"; setup minimums, Developer Mode, JDK 21 for Gradle 8.13, Meta's Claude plugin; Web App inputs from CONDUCTOR.
- 2026-10-05: Back behaviour turned into a Back check instead of a fixed rule.
- 2026-10-05: HackIX2026 findings — Neural Band sources and middle-finger Back, text flicker vs bitmap captions, animated-card clicks, send times, sprite animation, Muse pattern, offline Compose compile, Figma export bridge.
