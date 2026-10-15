---
name: "hackix2026-project"
description: "Use for SCAD Hack iX 2026 work: Bee There (repo hack-ix-2026, folder HackIX2026), OCD Companion (repo hackix26), the Ailo build — repos, architecture, decisions, device results, open items; also when reusing their session, display-arbiter, camera or companion patterns."
---

# HackIX 2026 — Bee There, OCD Companion (hackix26) and Ailo

SCAD Hack iX 2026, "Game Your Brain: The Bee___ Challenge" (with the SCAD Neurodiversity Office). Prompt: help students, especially neurodiverse students, navigate a moment of challenge and take one meaningful next step. Track 2 "The Moment That Matters" (proposed Challenge 5 "Reset and Re-enter"); Diversifier Award (best use of wearable technology). Deadline: deployed or archived online by **Sunday 2026-10-04, noon** (public repo, release tag, ≤ 2-minute video). Hyun = main developer and repo owner. Chat language per `user-instructions.md`; code, docs, commits, UI text and teammate messages in English.

Work loop `plan-approve-build-loop` · screens `glasses-hud-design-rules` · SDK facts `ray-ban-display-app-dev` · process `hackathon-sprint-playbook` · wording `evidence-and-claims`.

## Two repos, one event — why

| Repo | Local folder | Period | What |
| --- | --- | --- | --- |
| `hyunishyun/hack-ix-2026` | `…\Hyun's Playground\HackIX2026` | 9/26 – 10/4 | Hyun's first repo: team workflow, DAT setup (PRs #1–5), DAT 1.0 upgrade on `main` (10/2), **Bee There** companion (`feature/companion-glasses`), then **Ailo** on `hyun/ailo-base` (10/4, tag `ailo-demo-2026-10-04`) |
| `hackix26` (owned by Hyun; Riya, two UX students) | `C:\Users\AISTUDIO\Desktop\hackix26` | 10/2 – 10/4 | Team **OCD Companion** built to Riya's MASTER_DOC: `core-glasses`, `agent-compulsive` (Hyun), `agent-obsessive` (Riya), `journal`, `app`; integration on `team/one-app` |

Why two: the team switched to Riya's two-agent plan on 10/2 and built it in `hackix26`. On 10/4, when the integrated build got stuck, Hyun **restarted from the early known-good base** of `hack-ix-2026` (`main`, DAT 1.0) and brought Riya's agent, `core-glasses`, `journal` and `agent-compulsive` over → Ailo (Hyun, 2026-10-06). Neither integration branch is merged into its `main`.

Names in use: Bee There (first concept; also the name on Hyun's LinkedIn post), OCD Companion (working title in hackix26 docs), Ailo (10/4 app), "Bee____" (team name, undecided). Don't invent new names.

## Timeline

- **9/26–9/28** hack-ix-2026: team setup, SDK 0.9 config, device connection, display session test (Hyun device-tested 9/27–28), shared Figma folder; untracked handoff doc 9/28.
- **10/2** hack-ix-2026: DAT 1.0.0 upgrade (`f17b182`, straight to `main`); Bee There rounds R1–R4 device-tested by Hyun (animated cat with captions, Neural Band petting, Steady flow with glasses speech + Muse, onboarding/home/cat room, work-with-me sessions); Figma UI generator. Details: `references/bee-there.md`.
- **10/2 night** Riya's MASTER_DOC → hackix26 round 1 skeleton (20 tests, runs on Hyun's phone).
- **10/3** hackix26 round 2 (compulsion agent): MediaPipe door-lock loop detection, leaving mode, door tests, Neural Band probe, band as input, log-only, more objects / urge log / return watch, UNIFIED D2/D3/D7 (300 tests), PR #1; team and pitch docs. Evening: Hyun's Claude near its usage limit → integration handed to Riya's session (INTEGRATION_HANDOFF). Details: `references/ocd-companion-hackix26.md`.
- **10/3 late – 10/4** hackix26 `team/one-app`: MomentConductor, Story mode, cat sprites, Tempo session screen, HandsWatch, glasses as default mode (`3b0ebc9`). `main` has only PR #3 (Riya's obsessive-core). 10/4 early morning: glasses display `SERVICE_NOT_FOUND`, Meta's sample app also blank (resolution not recorded).
- **10/4** hack-ix-2026 `hyun/ailo-base`: Ailo — Riya's agent wiring, single DAT owner, developer screen and Ailo home, animated sprite cat on the lens, breathing after a calm talk, strength card, camera step with real hand landmarks, scripted camera check for the pitch recording, lens screens exported as PNG frames, clean session end on swipe-away.
- **10/5–10/6** skill harvest; LinkedIn post (Ailo flow: the cat listens to a looping thought and writes it down → with permission the camera notices a repeated behaviour → box breathing).

## Product rules that must survive any merge (hackix26 + Ailo)

Detect loops, not acts · never diagnose, reassure or confirm a check · never flag stimming · cards state, never ask ("?" banned by test) · one nudge per loop, quiet after any answer · the camera runs only in a mode the user started and is named on the card · frames never leave the phone; the journal holds labels, counts and times only · Head out is the one exit and ends quietly · safety outranks everything and freezes the episode · the cat shows attention, never a verdict · audio never stored · consent before any cloud call (Muse) · the app never sends email itself (`ACTION_SENDTO`).

Pitch: never "service animal"/"service cat"/"therapy animal" — "a calm companion, modeled on the interrupt task psychiatric service dogs can be trained for"; Neural Band sensing = "next step, blocked by developer access"; no accuracy number for door checks; the 2024 service-dog study was not randomized; no claims that the cat's breathing or blinking affects the user.

Security: never commit `local.properties`, keys, tokens, `test-media/`, `.env` or licensed cat sprites (they may ship in an APK, never in git — the repos are meant to go public).

## State (2026-10-06, from the repo status docs)

- hack-ix-2026: checked out `hyun/ailo-base` = origin, clean; 37 commits, all since 9/26, all authored `hyunishyun`. No DEVLOG files. Device results for the 10/4 Ailo work are **not recorded**.
- hackix26: 114 commits since 9/26 across branches; `team/one-app` is 45 commits ahead of `hyun/round2`; local checkout `hyun/round2`, clean. Room `JournalDatabase` / `ObsessiveDatabase` version 1, no migrations. `ActionRole.PRIMARY` used in `DatCardRenderer` (first button gets initial focus) and `TempoLensRenderer`. No Mock Device usage, no cue-list storage; `SessionNote` (template notes) exists.
- Not device-tested: UNIFIED D2/D3/D7 and the blank idle card, temple taps (E1), log-only with the real band, return watch with real Wi-Fi, other objects with the glasses camera, T4 replay on real clips (no clips recorded), everything from 10/4.

## Open items (확인 필요)

Which build, repo and tag were submitted (release notes suggest `v0.2.0-hackix`, but Ailo has tag `ailo-demo-2026-10-04`) · whether `SERVICE_NOT_FOUND` was resolved · LICENSE, branch cleanup, foreground service, arm64-only APK, MediaPipe telemetry opt-out · Riya's remaining items (MASTER_DOC copy still asks questions; her email in commit metadata).

## Reusable from this project

- Single DAT session owner + display arbiter with priorities (two features, one screen) — `ray-ban-display-app-dev`, `glasses-hud-design-rules` rule 19.
- Camera loop detection on the phone: register an object hands-free (embedding), count fingertip dwell inside the centre box (400 ms on, 700 ms off, merge < 2 s), user-set k-in-t rule, replay scoring of recorded clips.
- Captioned-bitmap sprite animation, band-drag switching by screen type, resumable focus timer, Muse "known / not sure, with quotes" sort, Figma export bridge (Bee There).
- Hardware probe method: phased, logged, decision gate written before the run (Neural Band "Wrist lab").

## Changelog

- 2026-10-06: replaces `hackix2026-bee-there-project`; adds hackix26 / OCD Companion and Ailo from the team docs and the 2026-10-06 repo status docs; Bee There detail moved to `references/bee-there.md`.
- 2026-10-05: `hackix2026-bee-there-project` created.
