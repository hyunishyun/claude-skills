# OCD Companion (repo hackix26) — detail

From the team docs of 2026-10-02 – 10-03 (MASTER_DOC, HANDOFF, DEVELOPMENT_SUMMARY, PROJECT_STATUS, INTEGRATION_HANDOFF, UNIFIED_EXPERIENCE, TEAM_ALIGNMENT, neural-band-feasibility, pitch docs, DEMO_VIDEO_PLAN, RELEASE_NOTES draft, round2-device-test) and the 2026-10-06 repo status doc. In the repo, `CLAUDE.md` holds the binding rules and device facts; read it first.

## Team and scenario

- Hyun: compulsion agent (camera), `core-glasses`, repo owner. Riya: obsessive agent (voice in, voice + display out), wrote MASTER_DOC. Two UX students: interface / journal / cat states; story / pitch.
- Scenario (proposed, team to lock): a senior leaving for a first in-person job interview gets stuck re-checking the door lock (and stove). Hyun's agent notices the loop and the cat offers a pause; Riya's agent names the doubt without reassuring and offers one step ("Head out. We'll log it."); the journal shows the episode in the evening.

## Modules

- `core-glasses/`: `GlassesSessionManager` (interface) + `DatGlassesSessionManager` (DAT 1.0.0) — the only class that opens a session; display + inputs, camera on demand, attach retry 750 ms × 40, auto-reconnect, wear state, registration + camera permission. `DisplayArbiter` — the only path to the screen; SAFETY > OBSESSIVE > COMPULSION > IDLE; never re-sends an identical card; an empty card draws nothing. Card rendering: dark background, mood line, title/body, ≤ 3 buttons, first = next action, 400 ms press guard, send retry ×2, redraw after reconnect. I420 → upright ARGB; 2 s camera cool-down + one retry.
- `agent-compulsive/`: `LeavingMode` (Off → Registering → Watching → Ended; camera only in Registering/Watching, default 5 min), `MediaPipeCheckingVision` + `ContactTracker` (fingertip dwell 400 ms in the centre box while the view matches the registered object or did within 1.5 s; 700 ms release; occlusion box 1.6×), `CheckingLoopDetector` (k in t, default 3 in 10 min; contacts < 2 s apart merge), `CompulsionAgent` (one nudge per loop, quiet after any answer, journal rows), `NudgeCopy` (+ `FORBIDDEN` test), `CheckTarget` (door lock, stove knob, light switch, faucet, appliance plug, window lock; `URGE` log-only; `RETURN_HOME`), `ReturnWatch` + `ReturnDetector` + `WifiReturnSource` (Wi-Fi lost ≥ 30 s and back within 30 min = likely return; `ACCESS_NETWORK_STATE` only), `ReplayRunner` + `AccuracyScoring` (T4: ≥ 90 % of expected checks, no nudge below threshold).
- `agent-obsessive/` (Riya): safety backstop, script library (`BANNED` includes "?", "just", "should"), keyword classifier, decision table, engine, quick-pick, C1 cloud classifier (off by default), G6 DAT speech dictation, G7 speech-rate cadence, `SessionNote`, patterns.
- `journal/`: Room `JournalEntry` — the shared contract; labels, counts, times only; change only when both agree; no schema change before the demo.
- `app/`: dev panel (Phone-only / Glasses modes, leaving-mode controls, vision preview never saved, replay, journal, Simulate leave/return) — kept behind "Developer" once the real UX lands. `MomentConductor` + Story mode on `team/one-app`.

## Cards (exact copy, round 2 + D2/D3/D7)

"Ready to head out" / "Tap Start at the door." [Start] → "Look at the door lock." [Register] [Cancel] → "Leaving mode until 8:52 AM" / "Camera on for the door lock." [Head out] → "3rd door check." / "Pause here if you want." [Pause] [Not now] [Wrong] → "Pausing with you." / "Notice the urge. You don't have to act on it." [Head out] [Done] → "Camera off." 3 s → blank. Log-only: [Log check] [Head out]. Urge: "3rd urge noticed." Return: "2nd return home."

## Unified experience ("one moment, one cat") — design, partly built

One episode from Start at the door to the end of the return watch; setup once on the phone, the moment on the glasses with zero phone touches, one evening note. Talk it out on the pause card hands the moment to Riya's agent with context (`door_check_x3`, `urge_x3`, `return_home_x2`, `leaving_home`); every path ends at Head out (camera off, return watch starts quietly). Quiet gate: while Riya's engine is listening/thinking/responding/closing/safety, compulsion nudges are dropped, not queued. Safety freezes the episode. Episode key as a `JournalEntry.id` prefix (`ep_<ms>/c/…`). Decisions (UNIFIED numbering): D2, D3, D7 approved and built; D5 integration moved to Riya's session; D1, D4, D6, D8–D20 open as of 10/3 (recommended next bundle: D9, D10, D11, D12, D14, D15). Team-level conflicts T1–T26 in TEAM_ALIGNMENT section 4.

## Device results (S25 / Android 16, firmware 128.0.0.172.321, Meta AI 293.0.0.17.161, Neural Band, 2026-10-03)

- Session, permission, display, inputs, camera attached on try 1; reconnect ~0.5–1 s.
- Stream 360 × 640 I420, ~10 fps processed; hands 20–77 ms, embedding 1–11 ms.
- Door recognition: 0.52–0.98 at the registered door vs 0.04–0.21 facing away (threshold then 0.5). Door test 2: hands-free registration 266 ms, **4 real touches → 4 checks**, 0 duplicates, a 6.8 s hold = 1 check, nudge at 3 (re-nudged at 4 and 5 before D2). One person, one door, one session — not an accuracy rate.
- Neural Band pressed Start, Register, Cancel, End, Pause, Not now, Done natively; no accidental presses during real checks (Hyun reports).
- Neural Band motion probe: 8,783 samples, all GLASSES (head), 0 band → the band is an input, never a detector; wrist sensing = roadmap (Wear OS watch, or band motion if Meta ships it; probe archived on branch `hyun/neural-band`, draft PR #2, do not merge).
- Phone-only (automated mirror): log-only ×3 → nudge, journal "(self-logged)"; urge ×3; return watch via Simulate.

## Problems hit (and fixes)

JDK 25 vs Gradle 8.13 → JBR 21 · parallel agents started from `origin/main` → check out the branch explicitly · session ended ~4 s after End → read `camera.stream` before `stop()`, always `close()` · replay at ~1 fps → sequential MediaCodec · a check counted during registration → count only in Watching, register from hand-free frames · unexplained counts → touch start/count/release logs + per-second `inRegion` · temple taps dead on cards → leave CAPTOUCH to the display (E1, unverified) · a door test confounded by a switch left on by automation → restore dev-panel settings.

## Submission prep (drafts, 10/3)

RELEASE_NOTES draft (`v0.2.0-hackix` suggested on the PR #1 merge; versionName still 0.1.0; no LICENSE; empty `mwdat_*` keys in any public APK; check DAT redistribution terms), DEMO_VIDEO_PLAN (10 beats in 2:00; return beat simulated and captioned), pitch-service-animal and pitch-neural-band (say/don't say), TEAM_ALIGNMENT (doc corrections, T1–T26, P1–P19).
