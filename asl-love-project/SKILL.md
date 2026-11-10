---
name: "asl-love-project"
description: "Use for ASL Love (personal ASL ↔ non-signer app; repo ASL_Love: Unity Quest bench, glasses/ Android app for Ray-Ban Display, ml/ Python): where state lives, architecture, rules that hold, device results; also when reusing its sign-recognition, pose-crop, golden-twin or pre-registered-test patterns."
---

# ASL Love — reading a signer through the glasses

Personal research project (not a competition entry): a non-signer wears Meta Ray-Ban Display glasses; the glasses camera streams to the phone, MediaPipe finds the signer's hands, letter and sign-word models read them, and the glasses show a letter, a word, or nothing. The wearer replies by speech recognised on the phone. Quest 3 was the first platform and is now the bench (D178: the glasses app is the product). Research prototype: 50 provisional words, never "translation", no review by an ASL user yet.

**The repo's `CLAUDE.md` is the only memory that carries over between PCs and accounts** — its "Status and next steps" is the current state; this skill holds only what stays true. Read next: `docs/PROJECT_BRIEF.md` (honesty rules), `docs/DECISIONS.md` (D1–D225+), `docs/DATA_POLICY.md`, `docs/HARDWARE_FACTS.md`. Chat with Hyun in **Korean** (project rule, 2026-10-14); every file in English.

Work loop `plan-approve-build-loop` · SDK facts `ray-ban-display-app-dev` · screens `glasses-hud-design-rules` · wording `evidence-and-claims`.

## Layout

- `unity/` Unity 6000.6.4f1, Quest 3, OpenXR Meta, XR Hands, Sentis. `Assets/AslLove/Core` pure C# (no UnityEngine) holds every rule; Runtime MonoBehaviours stay thin; scenes only from `AslLoveSceneBuilder`.
- `glasses/` one Gradle build (Gradle 9.6.0, AGP 9.4): `core/` = Kotlin port of Core (JVM), `app/` = the phone app (DAT 1.0.0, MediaPipe Tasks, ONNX Runtime Android 1.31.0).
- `ml/` Python: hand format twins, extraction (logger-free MediaPipe copy, `ASLLOVE_MEDIAPIPE_PATH`), training (Conv1D + attention "convformer"), export, evaluation scripts. `shared/golden/` = golden files every language must pass.
- `data/` git-ignored: datasets (ASL Citizen, WLASL, How2Sign, DailyMoth, ChicagoFSWild+, FSboard, OpenASL…), derived landmarks, models. Never committed, never `git add -f`.

## Rules that hold

- One hand format: 21 MediaPipe points in signer space; every platform converts at the edge. C#, Python and Kotlin are held to the same golden CSVs; a rule change regenerates them and runs all three suites.
- Honesty: unknown is valid ("?"), confirm before voicing, user correction wins, never claim translation. Words show only above a precision-fitted threshold (`glasses-hud-design-rules` 24–26).
- Data: ASL Citizen and derived weights stay on Hyun's own PCs and headsets (non-commercial research); presentations never show dataset clips or participant faces (one recorded exception, D225); dataset-clip replays on a phone run in airplane mode and are deleted afterwards. Licence checks are loose for non-commercial research (note one line per source).
- Every test is pre-registered (list SHA-256, bars, decision per outcome) before any number; a test set read once is spent (`evidence-and-claims` §7).
- adb only on Hyun's devices, by serial, never `kill-server`; headset and glasses sittings, runtime permissions, airplane mode and live people are Hyun's.

## What was learned (reusable)

- **Viewpoint gap:** a model trained on webcam signers offered 0 words in 33 tries on the wearer's own Quest-tracked hands (D31 → read the partner instead). Quest hand tracking from the wearer's view gets the thumb-hidden letters A E M N S T right ≤ 31% in every hand pose measured (2026-10-22).
- **Split the loss on a rig:** Quest on a mannequin in front of a monitor replaying validation clips; Android MediaPipe cost 0.1 points, monitor + camera 12.2; final rig result 53 / 75 right, 0 wrong (a pipeline check, not real-world accuracy).
- **Wide lens, tiny hands:** person-first crop (`ray-ban-display-app-dev` → On-phone vision). Coverage was also lost to the replay protocol (a 1 s lead fixed it) and to the fixed signing hand (automatic hand choice).
- **Vocabulary vs coverage:** 50 words at a fitted threshold: precision 97.9%, out-of-list shown ≤ 0.3%, coverage 80% / 75% on unseen signers replayed on the S25; 250 words kept precision (95%) but coverage fell to 36% / 31%, because the threshold that keeps never-trained signs quiet rises with the vocabulary.
- **Fluent fingerspelling** needs a sequence model (hold-based letters top out far below 5–6 letters/s); a CTC student reached ~5% character error on FSboard, but continuous signing produced false spelling until a separate detection gate; it stays off on the display.
- **Glasses field lessons:** ask for LOW, keep the app's own log file, restart the process on a stuck link, no letters before the signing hand is chosen, a phone demo view for presentations (all in `ray-ban-display-app-dev`).
- First user test (2026-11-04, classmates who don't sign): 0 words shown for 41 beginner sign attempts — the design working as intended for out-of-list input, and two real bugs found (letter hand setting, lost logs).

## Changelog

- 2026-11-09: created from the ASL Love repo (CLAUDE.md, DECISIONS, HARDWARE_FACTS, test logs, the 2026-11-11 presentation fact sheet).
