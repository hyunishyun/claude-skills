---
name: "hackathon-sprint-playbook"
description: "Use for a hackathon, design sprint or game jam with Hyun: prep, brief/prompt card → concepts, quick surveys, team contracts, build rounds with a known-good demo, inclusive checks, demo video, release. XR 455 milestones: xr455-capstone leads."
---

# Hackathon sprint playbook (from SCAD Hack iX 2026)

Goal: a demo that works on stage and a story the judges can follow, built on things already proven on hardware. Sources: HackIX2026 / Bee There (solo prep + team, 2026-09-26 – 10-02) and hackix26 / OCD Companion (two developers + two UX students, 2026-10-02 – 10-04). Wording rules for pitches and posts: `evidence-and-claims`.

## 1. Before the prompt drops

- Readiness checkpoint doc: repo branch + last commit, what is uncommitted, what was built, what is verified on device and on which SDK/firmware/phone.
- Upgrade the SDK and smoke-test it **before** the event (Start → Stop → Start), merge it so the team starts from one base.
- Capability evidence list labelled [DEVICE] / [JAR] / [UNVERIFIED], with gaps named. Concepts are mapped against this list, not against hopes.
- Give Claude access early (deep source folders, design exports); fix stale README / CLAUDE.md. Install vendor tooling for Claude (e.g. Meta's `mwdat-android` plugin).
- Know your build environment (JDK vs Gradle version, wrapper present or not) before the clock starts.

## 2. Brief intake

- Transcribe the brief verbatim into a doc; note missing pages or unreadable parts instead of guessing.
- Extract: core question, tracks, audience, challenge cards, **submission requirements** (e.g. "deployed or archived online by Sunday noon": public repo + release tag + video), judging criteria, side awards. Track each requirement to a deliverable from day one.
- Pick the track deliberately: a focused "moment" track rewards depth on one scenario (before → during → after); a "system" track rewards progression and loops. Two features covering everything reads as "a collection of features" — tie every agent/feature to one scenario.

## 3. Concepts

- 2–3 concepts, each: moment of need, what the user does, how it responds, the meaningful next step, which verified capabilities it uses, the riskiest unverified piece, demo fallback. Then one recommendation.
- Choose detection targets from evidence and from what the sensor can actually see at a predictable moment (hackix26 chose repeated door checks: predictable time, in front of the wearer, countable; rejected targets the forward camera can't see).
- Award test for wearables: would it still make sense without the device? The device must add hands-free / in-the-moment / private value.
- Let the user decide the important thing (their own limits, their own labels); the system proposes, never decides for them.

## 4. Quick evidence

- **Survey:** ~90 seconds on a phone, mostly taps, 3 optional free-text questions, anonymous (no email collection, no forced sign-in), self-identification optional ("no diagnosis needed"). Neutral wording, shuffled options with "None"/"Other" last, ask awareness of a term **before** describing it, quote consent as its own question (no answer = no). Report counts with the base ("14 of 22"), never population rates; stated intent overstates use; never pair quotes with identifying details; point to support services in the confirmation message. Every question maps to one pitch use; drop questions that don't.
- **Research brief:** Claude writes it with primary sources it actually opened and re-checks every claim (hackix26: 80 claims, 14 corrected). Rules in `evidence-and-claims`.

## 5. Team setup (two developers or more)

- One shared repo, one module per owner, plus shared modules nobody owns alone (session manager, display arbiter, journal/data model). The contract section is copied verbatim into each owner's master doc and changes only when both agree.
- Exactly one class opens the device session; one arbiter owns the screen with explicit priorities; features meet in a shared data model, not by importing each other. An integration "conductor" is the only code that knows both features.
- Build each part standalone first, but schedule integration early (not the last night). Put the integrated flow behind a switch ("Story mode") with the verified standalone flow as the fallback demo.
- One integration branch. Two parallel integration branches (2026-10-04) made it unclear which build was final.
- If integration gets stuck, restarting from an earlier known-good base and carrying modules over is legitimate (Hyun did this on 2026-10-04) — write down why and what was carried.
- Write a team alignment doc when several team docs exist: compare each doc's claims with the build, list corrections, list decisions for the team (T-numbers), and a say/don't-say table for pitch writers.

## 6. Build in rounds

- Round 0 prerequisites, then rounds that each end in a device checklist, a status doc and one merged branch (see `plan-approve-build-loop`). Batch safe stages; fewer approvals.
- In Claude Code each round is **one superpowers cycle**: one spec + one plan for the whole round (not per feature); capability probes take the Spike path (no spec); TDD only for pure logic (standing exceptions in `~/.claude/skills/user-instructions.md`).
- Order: core moment end-to-end on hardware first, polish later. Keep a **known-good demo APK tied to a commit**; feature freeze before the demo.
- Mock external integrations with the real data shape; disclose mocks and pre-recorded clips in the pitch and the archive.
- Design and code in parallel: designers own frames, the developer owns SDK/session/renderer; designs reach Claude as exported specs (JSON + PNG), not screenshots. Licensed art may ship inside the APK but is never committed to a repo that will go public.
- Keep risky experiments on their own branch (hackix26 `hyun/neural-band`: probe kept as an archive, never merged into the demo build).
- Watch Claude usage limits: write a handoff before a session runs out (`plan-approve-build-loop` → Handoffs).

## 7. Inclusive and responsible checklist (run on every screen)

Understandable · more than one way to participate · not dependent on one sense or mode · user controls pace, sound, motion, notifications · no shaming, diagnosing, ranking or pressure · data restraint and explicit consent before anything leaves the device · sensors only in a mode the user starts, with the state shown · opt-in, pause, stop, retry without penalty · useful without any label (no diagnosis needed) · never flag self-regulation (e.g. stimming) as a problem. Rewards name the real action, not points or streaks.

## 8. Demo, video and release

- Demo script: the moment, what changes, where it goes next; offline fallback (cached AI responses, bundled transcript, replayed clips through the real pipeline); rehearse clean install + reconnect; know the platform traps (a gesture that closes the app, display services failing — test the vendor sample first).
- Demo video (≤ 2 min if required): beats with timestamps, on-screen card copy taken verbatim from the code, sources on screen, an honesty list (which beats are simulated or scripted, said on screen), a recording checklist (restore settings, phone screen on, no bystanders in camera view), a backup take.
- Release: make the repo public only after an audit (no secrets, keys, test media, licensed art, personal emails in commit metadata), decide a LICENSE, match `versionName`/`versionCode` to the tag, build any public APK with empty credentials, check SDK redistribution terms, and write release notes with "Verified on hardware" and "Not yet tested" as separate sections.
- Archived version: demo video + clickable web prototype of the key screens, published before the deadline, with disclosures.

## After the event

Write a short retrospective, record which build/tag was actually submitted, then run the skill harvest (`plan-approve-build-loop` step 7) so the next project starts from what this one proved.

## Changelog

- 2026-10-06 (b): rounds map to one superpowers cycle; a round ends in a merged branch.

- 2026-10-06: team setup, integration (one branch, Story-mode fallback, restart from base), research brief, demo video, release audit, usage-limit handoffs — from hackix26 / OCD Companion docs.
- 2026-10-05: created from HackIX2026 (readiness checkpoint, brief, survey v3, dev plan, rounds 1–4).
