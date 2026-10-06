---
name: "evidence-and-claims"
description: "Use whenever Hyun's work turns into words other people will read or hear: research briefs, pitch decks, capstone proposals and midterm/final presentations, case studies, release notes, README and status docs, demo videos, LinkedIn posts, team docs. Covers how to source and re-check claims, how to state build/test status honestly, say/don't-say wording, health and wellbeing copy, and keeping docs in sync. Use it even for a single slide or caption that states what a product does, how well it works or what research shows."
---

# Evidence and claims

Hyun's projects are judged on what they claim as much as on what they do. Every public sentence should survive a judge, a teammate or a clinician asking "how do you know?". Rules distilled from HackIX 2026 (research brief, team alignment, pitch and video docs), the LiftLens case study and the XR 455 PoC.

## 1. Status words (use exactly one per feature)

| Word | Means | Example |
| --- | --- | --- |
| Device-tested | Ran on the real hardware; who and when is recorded ("Hyun reports …, 2026-10-03") | "4 real touches → 4 checks in one door test" |
| Unit-tested / simulated | Logic passes tests; never ran on hardware | "one nudge per loop (unit-tested; device check pending)" |
| Compiled | Builds; nothing more. Never call it tested | — |
| Built, not yet tested | Code exists | — |
| Proposal / designed for | Decided or drafted, no code | "designed for a phone-buzz mode" |
| Next step, blocked by X | Wanted, investigated, blocked | "Neural Band sensing: next step, blocked by developer access" |

Technical claims carry [DEVICE] / [JAR] / [SAMPLE] / [SIMULATED] / [UNVERIFIED] labels (`ray-ban-display-app-dev`). A feature can be "built" in one doc and "tested" in another only if both are true at their dates — write the date.

## 2. Research briefs

- Source tiers: (1) clinical or official authorities for definitions and headline stats, (2) peer-reviewed research for frequencies and outcomes, (3) HCI/sensing research (ACM, IEEE, arXiv — label preprints) for methods, (4) lived experience for empathy and quotes only, never stats. Avoid content farms, listicles, social video and AI summaries with no primary source.
- Open the source; a search snippet is not a source. If only the abstract was read, say so.
- Every number gets: link, year, sample size, population, country. Mark preprints, non-US samples and data older than 10 years.
- Re-check every claim against the opened source before it reaches a slide (hackix26: 80 claims, 66 supported as written, 14 corrected). Keep the corrections list — it is evidence of rigor.
- Name the gaps ("no trial of X for Y was found") instead of filling them with nearby evidence.

## 3. Wording rules that keep coming back

| Say | Don't say | Why |
| --- | --- | --- |
| "Frames never leave the phone." | "Nothing leaves the device." / "The app sends nothing." | Libraries (e.g. MediaPipe) send image-free usage stats. |
| "Feasibility: one test at one door counted 4 of 4." | Any accuracy % | Without scored data there is no accuracy. |
| "The user sets the cue and the limit." | "It learns your patterns." | Fixed models + user rules are not learning. |
| "Designed for …", "next step" | "It does …" | Not built. |
| "A controlled study of N people with X was linked to …" | "A randomized trial showed … led to …" | Say the design; association is not causation. |
| Evidence about the same behaviour and sensor | Borrowing results from another population, behaviour, species or sensor as if they applied | Transfer is an inference, not a finding. |
| "We tested it on hardware: today developers get band gestures, not band motion." | "Meta said it's not planned." | Never attribute a statement nobody found. |
| "Modeled on what psychiatric service dogs are trained to do." | "Service animal", "service cat", "therapy animal" | Legal term (ADA: dogs only). Check legal terms before using them. |
| Measured numbers exactly as measured | Battery, latency or accuracy figures nobody measured | Unmeasured = unknown. |
| "Calm companion", "notices a loop", "offers a pause" | "Detects OCD", "treats", "reduces symptoms" | Products that notice behaviour never diagnose. |

Build a project-specific say/don't-say table for every pitch and video; pitch writers and UX teammates use it directly.

## 4. Health and wellbeing copy (on screen and in public)

- Never diagnose, never reassure ("you're fine", "it's locked"), never confirm a check for the user, never flag self-regulation (stimming, fidgeting) as a problem.
- Cards state, they don't ask (hackix26 banned "?" by test), they don't instruct or lecture, and the user can always ignore them.
- In public posts, describe behaviour without diagnosis labels ("looping thoughts", "repeated checks") and say it is a prototype, not a treatment.
- Self-report is labelled self-report ("self-logged"), inference is labelled inference ("likely came back").

## 5. Demos, videos and posts

- Every card or screen in a video must be one the app actually produced in that take; captions say when a beat is simulated, scripted, a phone mirror or sped up.
- Use the exact on-screen copy from the code. Show a report line exactly as printed, never rounded up.
- Posts: credit teammates and the challenge host, ask before tagging people, state Hyun's own role in one line ("main developer: …"), mark anything unverified as `[Check: …]` until confirmed, disclose scripted demos.
- Keep footage, logs and test media out of git; copy them off the school PC right away.

## 6. Keep documents in sync

- When a status, test count, version or schema number changes, update every doc that states it in the same commit (README, status docs, release notes, CLAUDE.md, proposal slides); grep for the old value.
- Flag stale docs explicitly ("README still says 'SDK not integrated'") instead of silently trusting them; prefer source and Gradle files over prose.
- For capstone reporting, say what question each experiment answered, not only what was built.

## Changelog

- 2026-10-06: created from HackIX 2026 docs (research brief rules, TEAM_ALIGNMENT section 5, pitch-service-animal, pitch-neural-band, demo video honesty rules, release notes), the LiftLens case study and the XR 455 PoC review.
