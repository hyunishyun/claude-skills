# Knowledge log

Dated record of where the knowledge in these skills came from, which sources disagreed and how that was settled. Newest entries at the bottom of each section. Sources: repo status docs, team docs, project skills, artifacts (LiftLens case study, CONDUCTOR), the capstone pitch deck and PoC proposal, Claude.ai chats.

## Timeline

| Date (2026) | Project | Event | Source |
| --- | --- | --- | --- |
| 9/14 | Capstone | Fall quarter W1 | PoC proposal |
| by 9/26 | LiftLens | Day 1–4: sticky controls, Context Engine, Production HUD, JSON store, sessions, programs + schema v3; DAT 0.9.0; 27 commits; 84-case state simulation | LiftLens case study, `liftlens-project` |
| 9/24 | SDK | Meta Wearables DAT Android 1.0 released | PoC slide 8 |
| 9/26–9/28 | HackIX (hack-ix-2026) | Repo, team workflow, SDK 0.9 setup, device connection, display session test (PRs #1–5); device-tested by Hyun 9/27–28 | STATUS_2026-10-06 |
| 9/27 | CONDUCTOR | Web App gesture tutorial v0.6 (personal work) | artifact |
| W3 (9/28 week) | Capstone | PoC proposal narrows Fall to LiftLens + Production HUD | PoC proposal |
| ~9/30–10/1 | Fridge | F6 speech (English only), Back reached the app once (9/30), 0.7.0 committed + device-tested, 0.8.0 delivered (commit + device test pending), paused 10/1 | `fridge-assistant-project` |
| 10/2 | HackIX (hack-ix-2026) | DAT 1.0.0 upgrade on `main`; Bee There R1–R4 device-tested (animation, band petting, Steady + Muse, rooms, focus); NEURAL_BAND attach failure, middle-pinch closes app, text flicker, send times | STATUS, `ray-ban-display-app-dev` |
| 10/2 night | HackIX (hackix26) | Riya's MASTER_DOC; round 1 skeleton, 20 tests | MASTER_DOC, HANDOFF |
| 10/3 | HackIX (hackix26) | Round 2 compulsion agent: door-lock loop detection, door test 4/4, Neural Band probe (8,783 GLASSES samples), band presses cards, log-only, 3 additions, UNIFIED D2/D3/D7, 300 tests, PR #1; team and pitch docs; integration handed to Riya's session (usage limit) | DEVELOPMENT_SUMMARY, PROJECT_STATUS, INTEGRATION_HANDOFF |
| 10/3 late–10/4 | HackIX (hackix26) | `team/one-app`: MomentConductor, Story mode, sprites, Tempo, HandsWatch; `SERVICE_NOT_FOUND` display failure on 10/4 early morning | REPO_STATUS_2026-10-06 |
| 10/4 | HackIX (hack-ix-2026) | Restart from the early base → Ailo on `hyun/ailo-base` (tag `ailo-demo-2026-10-04`) with Riya's agent, core-glasses, journal, agent-compulsive, sprite cat, breathing, camera step | STATUS_2026-10-06, Hyun 10/6 |
| 10/4 noon | HackIX | Submission deadline (what was submitted: to confirm) | MASTER_DOC |
| 10/5 | Skills | First skill harvest (SDK facts, HUD rules 14–18, harvest step, playbook, Bee There skill); Back rule made conditional | chat "프로젝트 학습 내용 재사용 방법" |
| 10/6 | Skills | This repo: 9 skills, hackix26 knowledge added, conflicts below settled | chat in project "Hyun's Capstone" |
| 10/6 | Skills | Repo moved to GitHub (`~/.claude/skills`); third-party skills added (antfu, Google android/skills); plugins: Meta DAT + Web App + game, chrisbanes, superpowers. Hyun chose superpowers as the process (Korean 요약 kept). Cross-check of all skills: Web Apps routed to Meta's plugin, explicit input sources, release-channel gating, per-project Gradle/AGP, Windows tool notes, standing answers for superpowers, descriptions shortened, 5 web skills parked (description budget) | Claude Code session 10/6 |
| 10/12–10/13 | Hand Hero (MetaAwards A_4) | Round 9 (Z0–Z4: run/comment minors, playtest kit, 4 APKs; 1731 EditMode tests) and round 10 (127 asset slots, scene groups, `_Legacy` move, `.gitattributes`); PR #2 had merged r9 before Z2, so neither is in `main`; dungeon skin on `hyun/dungeon-skin`; no headset test yet | Claude Code session 0e007893 |
| 10/13–10/14 | ASL Love | Rounds 1–3: Massey templates 72.6%; ASL Citizen 83,399 videos, 2,731-sign Top-1 65.4%; learning mode; on-device signs on Quest (255 Unity tests, measure APK); collaborator onboarding doc; headset tests pending | Claude Code session c5a05a62, `docs/session-logs/` |
| 10/14 | Hand Hero | Pivot spec: hand-flown 3D aerial quidditch, 3v3, 6-team tournament run (`hyun/quidditch` 944bebd); repo made private | Claude Code session 0e007893, this session |
| 10/14 | Skills | Language rule: English by default, Korean chat on request, files always English (`## Summary` replaces `## 한국어 요약`); desktop app lost its session list — recovery steps added | this session |
| 10/18 | Skills | Harvest from gstack (MIT, Garry Tan; 8640ab4), read against the Hand Hero cleanup rounds, ideas only: silent-run, gate-edit, sibling, safe-deletion and debugging checks, standing decisions in specs, deny-rule check, checklist sources, handoff reconcile (`plan-approve-build-loop`); rulings vs Hyun's direction, flaky tests, unattended debugging (`user-instructions.md`); history secret scan, retro format (`hackathon-sprint-playbook`). No new skill, no gstack tools | Claude Code session 10/18 |

## Conflict ledger

| # | Topic | Source A | Source B | Settled as |
| --- | --- | --- | --- | --- |
| C1 | Subscribing Neural Band inputs | Bee There 10/2: `NEURAL_BAND` → attach failed, no input; drag pinch x/y = 0 | hackix26 10/3: attach worked; pinch → Select, swipe → Nav, drag x/y up to ~0.35; band stopped pressing cards | Keep both (device, different app/config). Rule: a subscribed source is taken from the UI; card apps don't subscribe band sources. |
| C2 | Back gesture | Fridge 9/30: Back reached the app once | Bee There 10/2: middle pinch closed the app | Back check before designing; no-Back design only when the check fails (Hyun, 10/5). |
| C3 | DAT distribution and minimums | MASTER_DOC: v0.7, GitHub Maven + token, firmware v21, Meta AI v254, "band gestures not exposed" | Device + Meta docs: 1.0.0 on Maven Central, no token, V128 / V290, band presses cards | B. MASTER_DOC lines are historical. |
| C4 | hackix26 ownership | STATUS 10/6 calls it "Riya's repository" | HANDOFF: Hyun created and owns it, Riya is a collaborator | HANDOFF. |
| C5 | Which HackIX build is final | hackix26 `team/one-app` (10/4) | hack-ix-2026 `hyun/ailo-base`, tag `ailo-demo-2026-10-04` | Ailo is the later restart (Hyun, 10/6); submitted build/tag still to confirm. |
| C6 | Product name | Bee There / OCD Companion / Ailo / "Bee____" | — | Use the name per repo; don't invent new ones. |
| C7 | Branch name vs content | `chore/dat-1.0-upgrade` | still holds 0.9.0; the real upgrade is `f17b182` on `main` | Read Gradle files, not branch names. |
| C8 | Test counts | 20 → 222 → 282 → 300 across hackix26 docs | README and older docs lag | Latest: 300 on `hyun/round2` (10/3). |
| C9 | LiftLens numbers in the PoC | PoC: "2 working weeks", schema v2 | Case study: "4 working days", schema v3 | Schema v3; recount days/commits from the reflog. |
| C10 | PoC Gantt bars | Earlier text-only estimate (W6–9 offsets) | Slide image: Inputs W7, Fridge W8, playtest W9, final W10, midterm between W4 and W5 | Slide image. |
| C11 | "Hardware-verified" claims for 10/3 card changes | Some docs list D2/D3/D7 among results | Release notes: unit-tested only | Unit-tested only until a device check is recorded. |
| C12 | Skill install state (10/6) | 10/5 zips: HUD rules 14–18, harvest step, two new skills | Installed: only `ray-ban-display-app-dev` updated | This repo restores and supersedes the 10/5 versions. |

## Open confirmations

- Exact XR 455 midterm date.
- LiftLensMini commits after 9/26; whether the DAT 1.0 migration and round B started.
- HackIX: submitted repo, build and tag; whether `SERVICE_NOT_FOUND` was resolved; device results for the 10/4 work.
- Firmware version for the HackIX Bee There device notes (assumed 128.0.0.172.321).
- Fridge: capstone W8 item or portfolio only.

- 2026-10-26 (Hand Hero, NB-R123-08): plan-approve-build-loop lessons (workflow declines/restarts/TaskStop/watchdog cost, usage budget, look slice first, Quest recording frames) and references/quest-dashboard.md (UNTRUSTED packages, age group, Data Use Checkup, release channels, headset accounts); evidence-and-claims 5b asset, font and music licences.
