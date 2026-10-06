---
name: "xr455-capstone"
description: "Use for Hyun's SCAD XR 455 capstone (all-day smart glasses, Fall 2026 – Spring 2027): roadmap, Fall PoC scope (LiftLens + Production HUD, Context Engine), weekly timeline, progress checks, midterm/final presentations, reuse from other projects. 캡스톤, PoC."
---

# XR 455 Capstone — Hyun Ha (SCAD BFA Immersive Reality)

## Question and roadmap

"How do we make smart glasses useful enough that people want to wear them all day?" — glasses as a platform that follows the user across work, exercise and home, not a single-purpose app.

| Quarter | Platform | Layer | Apps |
| --- | --- | --- | --- |
| Fall 2026 | Meta Ray-Ban Display + Neural Band | Wearable foundation: glanceable HUD · input · state · data | LiftLens, Production HUD (core) |
| Winter | XREAL AURA | Spatial understanding: 6DoF · anchors · object recognition | LiftLens anatomy overlay / posture / stretch, Fridge inventory, Production cues anchored to cameras and stage |
| Spring | — | Intelligent context: memory + prediction | Visual Memory + Note On, history and prediction across activities, one context-aware system |

Original pitch deck (`Hyun_CAPSTONE_PITCH_FINAL.pdf`): four apps across all three quarters, team "TBD". The PoC proposal (`Hyun_PoC_Proposal.pdf`, W3) narrowed Fall to one claim: **one wearable foundation can run two unrelated real-time workflows**. Solo developer.

## Fall PoC scope

- Core: **LiftLens** (log set · rest · history · sessions · programs) and **Production HUD** (cue · roll · cut · take) on one Real-Time Context Engine (Kotlin, StateFlow). Project details: `liftlens-project`.
- Five mechanics to demonstrate: glanceable stateful HUD · next-action-first input (routine actions in one click, zero cursor moves) · context switching without losing state or timers · persistent data (sessions, history, crash recovery) · set up on the phone, act on the glasses.
- Interaction rules from the proposal: next likely action first; sticky ± controls; a ticking screen gets one button; ≤ 3 buttons per row, 2 rows; setup on the phone (full set: `glasses-hud-design-rules`).
- Outside the core: Fridge Assistant (portfolio app, `fridge-assistant-project`; appears in the PoC timeline as W8), HackIX 2026 (hackathon, `hackix2026-project`), CONDUCTOR (personal Web App — reference, onboarding and input comparison only, never a deliverable).

## Timeline (Sept 14 – Nov 19, 2026; bars read from the PoC slide image)

| Week | Start | Planned |
| --- | --- | --- |
| W1–2 | 9/14 | LiftLens + Production HUD core on glasses |
| W3 | 9/28 | Proposal; migrate LiftLens to DAT 1.0; full regression |
| W4 | 10/5 | Test ActionRole.PRIMARY focus; Mock Display harness; save cue lists — **Midterm marker sits between W4 and W5** (exact date to confirm) |
| W5 | 10/12 | LiftLens Workout Program (multi-exercise) |
| W6 | 10/19 | Session summary: duration, volume, estimated calories |
| W7 | 10/26 | Inputs, Speech, Motion experiments |
| W8 | 11/2 | Fridge: validate camera frames + photo capture |
| W9 | 11/9 | Playtest (gym + mock film shoot); fixes |
| W10 | 11/16 | Final PoC demo, video, documentation |

## How to report progress

For each timeline row: 완료 / 진행 중 / 미착수 / 계획보다 앞섬 / 범위 변경, judged against today's week. Give the source of each judgment (repo reflog and status docs first, then devlogs, skills, chats). Anything not confirmed is "확인 필요" — never guess. Separate built from planned and compiled from device-tested (`evidence-and-claims`).

## State snapshot (2026-10-06 — re-check the LiftLensMini repo before acting)

| Row | Status | Source |
| --- | --- | --- |
| W1–2 core | 완료 | LiftLens case study (Day 1–4), `liftlens-project` |
| W3 proposal | 완료 | PoC PDF |
| W3 DAT 1.0 migration + regression | 미착수 per records (LiftLens still `mwdat` 0.9.0) — 확인 필요 | `liftlens-project` |
| W4 PRIMARY test | API known [JAR], used in hackix26 code, never measured on device | `ray-ban-display-app-dev`, hackix status 2026-10-06 |
| W4 Mock Display harness | 미착수 (hackix repos only declare the dependency) | status docs 2026-10-06 |
| W4 save cue lists | 미착수 ("Later") | `liftlens-project` |
| W5 Program | 앞섬 + 진행 중: round A (models, controller, schema v3) committed before 9/26; round B (phone S2.3, glasses S2.4, regression S2.5) not started per records | case study, `liftlens-project` |
| W6 session summary | 미착수 | `liftlens-project` |
| W7 Inputs/Speech/Motion | 앞섬, but done in other projects: Speech English-only (Fridge, Bee There, Riya); Inputs and band behaviour (Bee There 10/2, hackix26 10/3); Motion = head IMU only, 60 Hz (hackix26 probe) | `ray-ban-display-app-dev` |
| W8 Fridge camera | 앞섬 + 범위 변경: capture, OCR, nutrition scan on DAT 1.0 (Fridge 0.8.0, commit + device test pending, paused 10/1) | `fridge-assistant-project` |
| W9–W10 | 예정 | — |

Order inversion to watch: features (Program model, Fridge, HackIX experiments) ran ahead while foundation work (1.0 migration, PRIMARY, Mock harness) stayed behind. The 1.0 migration unlocks PRIMARY, LiftLens-side Inputs/Speech, and a retry of camera rep counting.

## Reuse map — results from other projects the capstone can use

- **LiftLens DAT 1.0 migration:** hackix26 `core-glasses` already runs DAT 1.0 with camera + display + inputs in one session, attach retry, reconnect redraw, wear state and the camera release order, all device-verified (2026-10-03). Reuse the patterns, keep LiftLens independently buildable, no new dependencies without approval.
- **Context Engine with two activities on one display:** the display-arbiter pattern (owner + priority, no identical resend, new card first, drop-not-queue) — `glasses-hud-design-rules` rule 19.
- **PoC slide 8 claims, now with evidence:** head nod = glasses IMU at 60 Hz works, gesture itself untested; "pause workout when glasses come off" = wear state is tracked in hackix26; hands-free "log set" = English only; "Hey Meta" launch = needs a Developer Center permission, untried; ActionRole.PRIMARY = measure it (test idea: make sticky ± keep a fixed button order and move PRIMARY to the last-pressed button instead of reordering).
- **Ticking screens after migration:** SDK `text` flickers on resend in 1.0 (Bee There) — check the rest-timer screen in the regression.
- **Neural Band:** gives apps gestures, not wrist motion; band presses cards natively when the app does not subscribe it.
- **CONDUCTOR:** comparison point for the input-constraint argument (Web App gets pinch/swipes/drag as key and pointer events vs native DAT 0.9 onClick only; DAT 1.0 adds Nav/Select/Drag), a gesture-onboarding step before playtests (W9), and a cue/conductor narrative link to Production HUD.

## PoC document items that are out of date

Slide 7 "schema v2" → v3 · slide 5 Fridge row "camera frames not yet validated" → 0.8.0 state · slide 5 "2 working weeks / 27 commits" vs the case study's "4 working days / 27 commits" → recount from the reflog · slide 5 caption saying sets/history/sessions came after the case study conflicts with the case study · slide 8 Motion "nod to confirm" and Device state "pause when glasses come off" are plans, not results · slide 9 risk 1 and 5 now have evidence (band gestures, Fridge camera on 1.0).

## Open questions (확인 필요)

Exact midterm date · LiftLensMini commits after 9/26 and whether the 1.0 migration started (run a repo status summary in Claude Code inside LiftLensMini) · whether Fridge stays a capstone W8 item or only a portfolio app.

## Changelog

- 2026-10-06: created from the pitch deck, the PoC proposal (timeline read from the slide image), the LiftLens case study, the project skills and the HackIX 2026 status docs.
