---
name: "evidence-and-claims"
description: "Use when Hyun's work becomes words others read or hear: research briefs, pitch decks, capstone presentations, case studies, release notes, README/status docs, demo videos, posts. Sourcing, honest build/test status, say/don't-say, health copy."
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
- Check every frame of a screen or camera recording before it goes anywhere: a monitor in the background can show licensed dataset clips or other people (ASL Love `learning.mov` showed dataset signers at 0–1.9 s and 4.0–5.4 s). Cut those frames; an uncut version is used only where a recorded decision allows it (one named venue), never in public posts.
- People who appear in footage (participants, testers) need a filming consent of their own; a study consent that says "no video" rules out recording that session.

## 5b. Third-party assets, fonts and music (licences)

- Every bought or downloaded asset must allow use in a distributed app **and** in its trailer / promo video; keep the licence file, receipt or store-page link beside the asset and list it in the project's `THIRD_PARTY_NOTICES.md`.
- OK: Unity Asset Store standard EULA (private repos fine; never redistribute the raw files), CC0 (Kenney, Quaternius), CC-BY (credit required), SIL OFL fonts, Mixamo animations. Avoid: CC-BY-SA, NC ("non-commercial": prize contests count as commercial), ND (no recolouring), "personal / editorial use only", ripped or fan assets, brands, logos, real people.
- Paid fonts need an app / embedding licence, not only "desktop". Store tiers like "Personal (under $100k revenue or funding) / Professional" usually differ by buyer size, not usage rights: an individual student buys Personal (Hyun 2026-10-26, Fab-style tiers).
- Music: check game use and online-video use separately, YouTube Content ID claims (whitelisting), and whether a subscription must stay active; check the contest rules before using AI-generated music or art.

## 6. Keep documents in sync

- When a status, test count, version or schema number changes, update every doc that states it in the same commit (README, status docs, release notes, CLAUDE.md, proposal slides); grep for the old value.
- Flag stale docs explicitly ("README still says 'SDK not integrated'") instead of silently trusting them; prefer source and Gradle files over prose.
- For capstone reporting, say what question each experiment answered, not only what was built.

## 7. Measured results (models, recognition, tests)

- **Pre-register before any number exists:** the data set (list + SHA-256), the pass bars, and what each outcome leads to ((a) ship, (b) keep the old default, (c) engineering fix). Run the test once; a held-out test set read once is spent. A run cut by a crash or a setup error is recorded as void with its reason, not silently rerun; a bar moved after seeing data is a new test.
- Report precision **with** coverage and the false-show rate (inputs outside the vocabulary shown as a word), with counts and an interval ("40 of 50 = 80.0% [67.0, 88.8]"). A model that shows 0 words for beginners or out-of-list signs is behaving as designed: report it as evidence, don't tune it away.
- Say which input produced the number: "dataset clips replayed on the phone", "validation clips on a monitor (a pipeline check)", "webcam videos of unseen signers (offline upper bound)" are three different claims, and none of them is "accuracy with a live user".
- Split a loss before fixing it: run the same clips through each stage (PC library, phone library, monitor + camera) and name the stage that costs the points (ASL Love: Android MediaPipe 0.1 points, monitor + camera 12.2).

## 8. Developer talks and platform-team audiences

- Structure a developer talk as problems met: for each, the symptom, the cause found, the fix, the measured before → after, and what is still unsolved. Keep a fact sheet in the repo where every number on a slide has its source (test log, decision row, script).
- For an audience from the platform vendor (e.g. Meta Wearables staff): a table "what I observed (on record) | what would unlock it". Every limit traces to a log line or test log with its date; asks are concrete (a quality floor, foreground-service streaming, in-SDK session recovery, a status page). No limit from memory or forums alone.
- Principles slide: state each as a rule you now follow with the incident that taught it, not as an adjective ("I fix the pass bar before I see the data").
- Slides artifact (claude.ai Slides type) notes: Korean text needs a Korean webfont (IBM Plex Sans KR); big numbers need `white-space:nowrap`; a connector inside a stretched flex row draws diagonally, so give it `height:2px; align-self:center`; GIFs go to the artifact's asset store and are copied between decks by asset id; read the deck again before editing when Hyun has edited it in the page.

## Changelog

- 2026-11-09: section 5 frame check of recordings and filming consent; section 7 measured results (pre-registration, precision with coverage, name the input, split a loss); section 8 developer talks for platform-team audiences and Slides artifact notes (ASL Love 2026-10/11, presentation for 2026-11-11).

- 2026-10-26: section 5b, licences for bought assets, fonts and music (Hand Hero asset purchase).

- 2026-10-06: created from HackIX 2026 docs (research brief rules, TEAM_ALIGNMENT section 5, pitch-service-animal, pitch-neural-band, demo video honesty rules, release notes), the LiftLens case study and the XR 455 PoC review.
