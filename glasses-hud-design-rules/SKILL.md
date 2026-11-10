---
name: "glasses-hud-design-rules"
description: "Use when designing or changing a native Meta Ray-Ban Display screen (DAT display DSL: sendContent, buttonGroup, cards), incl. animated/character screens, nudges, shared display, Figma glasses frames: cursor-reset, snapshot, input and turn-taking rules. Web Apps: Meta UI Toolkit leads."
---

# Glasses HUD design rules (Meta DAT SDK 0.9.0 and 1.0.0, verified on device)

Scope: native DAT display-DSL apps. For **Web Apps**, Meta's UI Toolkit guidance (`meta-wearables-webapp:ai-glasses-webapp-ui`) decides components, text styles and Back (it says: no in-app Back button); only the content and turn-taking rules (13, 18, 20–23) carry over.

## What the hardware actually gives you

- The display is a **snapshot**. Every `sendContent { }` replaces the whole screen; the renderer resets the cursor to the **first button** of the new content (1.0: to the button with `ActionRole.PRIMARY` [JAR/SAMPLE — not yet measured on device; until measured, also put the next action first]). There is no focus API.
- 0.9.0: the DSL exposes seven views (`flexBox`, `text`, `button`, `buttonGroup`, `icon`, `image`, `video`) and **one event: `onClick`**. No scroll, rotate, long-press or gesture events reach the app; the Neural Band pinch-and-twist dial is consumed by the glasses OS.
- 1.0.0 adds clickable cards (`flexBox(onClick = …)`), `flexGrow`, `wrap`, `image(bitmap, ImageSize.ICON | FILL, CornerRadius)`, and the inputs capability (glasses capture button, Back, Nav, Select; `consumeBack = true`). The cursor itself is still moved by the system.
- With the band sources **not** subscribed, the Neural Band presses buttons natively (pinch = press, thumb swipe = move). Subscribing a source takes it away from the cards (`ray-ban-display-app-dev` → Inputs).
- Text has three styles (HEADING, BODY, META) and two colors (PRIMARY, SECONDARY); any other color must come from icons or small bitmaps.
- The display adds light: black is see-through. Use dark backgrounds, bright content and bright status marks; avoid large bright areas.
- `onClick` lambdas run on the phone; the usual pattern is mutate state → re-render.
- Nothing is sent automatically on `DisplayState.STARTED` after a reconnect; a ticking screen reappears only because the next tick redraws it.
- Verify every `IconName`, `TextStyle`, `ButtonStyle` identifier against the SDK enums (from the sample code, javap on the jar, or an APK DEX dump). Never guess identifiers.

## Rules (apply to every screen)

1. **First button = the natural next action** (`Log set`, `Start set`, `Roll`, `Cut`, `Next take`, `Next cue`, `Capture`, `Review`, `Back` on a detail card). Design common loops to need zero cursor moves.
2. **Sticky last-pressed-first for ± controls.** Store the last delta's sign in state (`lastWeightDelta`, `lastRepsDelta`, `lastRestTargetDelta`, paging direction) and draw that button first. Redraw even when the value is clamped so the cursor never jumps at a limit.
3. **A screen that ticks (1 s redraw) holds at most one button; an animated screen (frames every ~500 ms) holds none.** Anything else on it is unreachable, and clicks on a re-sent card do not register [DEVICE, HackIX]. Drive animated screens with the capture button (SHORT / HOLD) and Neural Band pinch (drag source) and open a static menu card for choices. Do not "fix" this with a slower refresh; move the other controls to a non-ticking screen.
4. **≤ 3 buttons per row, ≤ 2 rows on main screens.** More → a dedicated screen (Reps, Weight, Rest goal, History, Session).
5. **Setup on the phone, action on the glasses.** Editors, pickers with many options, role/setting changes and history clearing live on the phone. Glasses carry mid-activity actions and quick browsing.
6. **Rare or destructive actions one screen away, `Cancel` first.** A rapid double press must not be able to finish, end or delete anything — make the second press a no-op by state, not by a flag. Also drop glasses presses within ~400 ms of a new card.
7. **Track which screen the glasses show** (`GlassesScreen` enum in the renderer). Any action triggered from the phone redraws the screen that is actually showing, and tick-driven redraws only run while the display is attached, the activity is active, and the glasses are on that screen.
8. **One render function per state**: every path (button, phone, reconnect, activity switch) goes through a dispatcher that reads the latest state (e.g. resting → REST, pending resume → RESUME, no session → no-session card, else main). Send only when the card changed: an identical resend still resets the cursor.
9. Text cards: one HEADING, one BODY, one META line; ~5 text lines plus two button rows is the verified maximum. Rich cards (1.0 tiles, rows with images) need a device test screen to find what fits before you commit to a layout. Enforce copy limits with a unit test over every card factory (hackix26: ASCII only, titles ≤ 28 characters, ≤ 3 buttons, button labels ≤ 16 characters and ≤ 3 words, plus the project's forbidden-word list).
10. Time-based UI computes elapsed from `SystemClock.elapsedRealtime()`, not tick counts; keep the tick interval a named constant.
11. **Back is a stack** (only when the Back check in rule 15 passes): detail → the list it came from (same page) → home → close. When the phone removes what a detail card shows, send the glasses back to where the card came from.
12. **Keep a plain fallback**: rich layouts behind a setting, the proven text cards one switch away.
13. **Never color alone**: a colored dot or icon always sits next to the words ("2 days", "Expired 3d").
14. **On re-sent screens, text lives inside the image.** SDK `text` flickers on every resend; captions drawn into the bitmap do not. Max two caption lines, the second can carry the input hint ("Pinch: menu · Hold: stuck").
15. **Check Back before designing around it.** At the start of a project and after any SDK, firmware or inputs-configuration change, run a short Back test (see `ray-ban-display-app-dev` → "Back check") and record the result with SDK, firmware, phone and date. Then choose:
   - **Back reaches the app on every screen** → use Back as a stack (rule 11); still give each screen a visible way out.
   - **Back closes the app or never arrives** (HackIX2026, DAT 1.0.0, 2026-10-02: middle-finger pinch closed the app on every screen) → never require Back: every screen gets its own exit (button, pinch, capture button), hints never say "Back:", and state survives a close so Start resumes where the user was.
   - **Mixed or unclear** → treat it as the second case until a re-test says otherwise.
   (Hyun, 2026-10-05: check first, apply the no-Back design only when it fits — the same goes for any hardware-dependent rule.)
16. **Input mode follows the screen type.** Image screens: band drag on (pinch = the one action). Decision cards: band drag off so the cursor + pinch select buttons; put the safe/neutral choice ("Not now", "Keep going") where a stray press is harmless.
17. **One clear exit on every choice card** ("Not now", "Stop for now", "Head out") and no failure states for stopping: stopping early still records what was done.
18. **Companion characters do, they don't watch.** A character on the HUD works on its own task beside the user (body doubling); it never comments on how the user looks or performs, shows attention rather than verdicts (never proud, sad or disappointed), and "Quiet" / "less movement" settings reduce lines and frame rate (≥1 s per frame).
19. **One display owner when several features draw.** Route every card through an arbiter with explicit priorities (e.g. safety > conversation > nudge > idle). A lower-priority card that arrives while a higher one owns the screen is **dropped, not queued** — nothing stale pops up later; only the base state (e.g. the Watching card) may sit underneath. A transition is one send: post the new top card, then release the old one (no flash).
20. **Speak at turning points only.** Cards change when something meaningful happens (start, the k-th event, the user's choice, the end). No running tallies or reminders during a moment; a ticking value is fine only when the time itself is the content (rest timer, take countdown).
21. **Proactive nudges fire once per loop, then stay quiet.** Later events in the same loop update the record, not the screen; after any answer (including "Not now") that target stays quiet for the rest of the session. Unanswered nudges never re-post with a bigger number.
22. **Sensors are user-started, named on the card, and end quietly.** A card states when a sensor is on and until when ("Camera on for the door lock." / "until 8:52 AM"). Ending shows a short confirmation ("Camera off." for 3 s) and then nothing — no "ended" card. The glasses never re-offer a sensor-starting button on their own (a stray pinch must not turn a camera on); restart comes from the phone.
23. **Blank when nothing is happening.** Between sessions the glasses show nothing (no idle line, no card background) — the companion is visibly absent when absent. (How to draw "nothing" — one invisible space, no card background — is unit-tested in hackix26 but *unverified* on the glasses.)

### Recognition output (captions from a model — ASL Love, 2026-10/11)

24. **Show a result only above a threshold fitted for precision; otherwise show nothing or "?".** Fit the threshold before the test on held-out data (e.g. shown results ≥ 90% right and inputs outside the vocabulary shown ≤ 2%), and accept low coverage: a missed word costs the reader less than a confident wrong one. "?" may carry a reason ("not sure", "not a list word"). Never present the output as a translation.
25. **Label what is unconfirmed on the card itself** (a second caption line such as "fluent model; unconfirmed" or "50 provisional words"), and nothing the model read is voiced or logged as "said" until the user confirms it.
26. **Don't show a guess before the source is decided.** When the input could come from more than one source (which hand signs, which speaker), show nothing from either until one clearly leads (hysteresis: an EMA of each source's activity and a margin; ASL Love needs ~0.9 s), then show the open result whole. Early guesses from the wrong source (a hand holding a phone or a card) read as nonsense letters. Pair it with an operating rule in the session plan (only the signing hand raised).
27. **Two directions, two fixed zones in one bitmap.** When one card carries both what the partner did and the wearer's own reply, give each a fixed zone (partner on top, reply below) in a single composed bitmap (rule 14), each with its own hold time; a stale result is dropped, never shown late.

## Phone screens that mirror the glasses

To show the wearer's view on a phone, projector or screen recording (the glasses' own recording takes the camera), composite the exact card bitmap sent to the glasses over the camera picture as light: alpha = the brightest channel, black = see-through (ASL Love "demo view", 2026-11-05; checked on the phone with a test caption).

Give Compose columns `verticalScroll` + top alignment (centered fixed-height columns clip and truncate labels), keep button rows to three, and let the phone follow the engine's active activity with a guarded `LaunchedEffect` so glasses-side switches move the phone but never hijack non-activity routes (Connect, Samples, History).

## Changelog

- 2026-11-09: rules 24–27 for recognition output (precision-fitted threshold, unconfirmed labels, no guess before the source is decided, two zones) and the additive phone mirror, from ASL Love's glasses app (DAT 1.0.0, 2026-10/11).

- 2026-10-06: restored the 2026-10-05 rules that were never installed (3 revised, 14–18, conditional Back in 15, rule 11 tied to it); added 19–23 from hackix26 / OCD Companion (display arbiter, turning points, once per loop, user-started sensors with quiet ends, blank idle) and the copy-limit test in rule 9.
- 2026-10-05: rules 3 (animated screens), 14–18 from HackIX2026 / Bee There device tests (DAT 1.0.0, 2026-10-02); rule 15 made conditional on a Back check.
