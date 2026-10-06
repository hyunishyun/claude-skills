---
name: "fridge-assistant-project"
description: "Use when working on Fridge Assistant (Android app for Meta Ray-Ban Display, com.hyun.fridgeassistant, folder FridgeAssistance): paths, architecture, decisions, privacy rules, current state and next steps."
---

# Fridge Assistant — project context

Portfolio app for US companies (UI and code in English; conversation with Hyun in Korean). Kotlin + Jetpack Compose (Material 3), Meta Wearables DAT SDK 1.0.0. Independent of LiftLens / Production HUD (do not touch those). In the XR 455 capstone it is outside the Fall core but appears as the W8 item (camera frames + photo capture), which it already exceeds; its results feed `xr455-capstone`. Public wording: `evidence-and-claims`.

## Where things are

- Folder on Hyun's school PC: `C:\Users\AISTUDIO\Desktop\Hyun's Playground\FridgeAssistance` (git). applicationId `com.hyun.fridgeassistant`, minSdk 31, compile/target 36, Compose BOM 2026.05.01 (material3 1.4.0), Kotlin 2.2.
- Read first: `docs/PROJECT_HISTORY.md` (timeline from the reflog, architecture, facts), `docs/ROADMAP.md` (phases and decisions D18–D43), the newest `docs/*-test-log.md`, `README.md`.
- Design: `design/figma-plugin/` (generator for the Figma file on Hyun's personal account: phone screens, glasses cards v1 and v2), `design/README.md`. Colors: `ui/theme/StatusTokens.kt` + `Theme.kt` mirror the Figma "FA Color" variables.

## Architecture (app/src/main/java/com/hyun/fridgeassistant)

- `glasses/` GlassesController (app-scoped session, camera, recovery by process restart), GlassesCamera.
- `hud/` HudCards (pure card model: HOME, CAPTURING, CAPTURED, USE_FIRST lists, REVIEW, MUSE_CONFIRM, ASKING_MUSE, SAVED, ITEM, NUTRITION, LAB; classic and rich), GlassesHud (display + inputs, one render path, press guard, Display lab), HudImages (dots, thumbnails), SpeechProbe.
- `inventory/` models, JSON store schema v1 (additive fields only), repository, PhotoStore, Nutrition (+ UsNutritionLabel, mergeScan), Backup.
- `analysis/` ML Kit OCR (Korean model reads Latin too; `readDetailed` keeps line boxes), OcrRows, ExpiryDateParser, NutritionParser (`parseBest`), barcode + Open Food Facts, Gemini Nano, MuseClient/MuseProtocol (Meta Model API, opt-in).
- `ui/` AppScaffold (bottom navigation Fridge / Add / Soon / More), `ui/components`, `ui/theme` (FridgeTheme, AppIcons copied path data — no icons-extended dependency), `ui/inventory` screens and bottom sheets (Edit item, NutritionSheet with Scan label).

## Rules that must hold

- Meta Model API key only in `local.properties` (git-ignored); never commit, paste or open it. `local.properties.example` keeps empty values (Gradle guard).
- Nothing is saved without an explicit Save (phone, or the glasses REVIEW card). Muse is opt-in with a confirmation before every send; uploads are 1024 px JPEG without EXIF; the Cloud log never holds images or keys; barcode lookups send the number only; scanned label photos are not stored.
- No new dependency without Hyun's approval. Work loop: skill `plan-approve-build-loop`; glasses screens: skill `glasses-hud-design-rules`; SDK facts: skill `ray-ban-display-app-dev`.

## State (paused 2026-10-01)

- 0.7.0 UI refresh: committed (`97e3025`, also carried the F6 code), device test passed.
- 0.8.0 delivered to the folder, **commit and device test pending**: N5 nutrition label scan (phone camera / photo / glasses, OCR rows from line positions, Nutrition photo role, Muse fallback) and G1 rich glasses cards (status tiles, item rows with photos, ITEM, NUTRITION, Back stack, setting "Rich glasses cards", Display lab). Checklist `docs/G1-N5-test-log.md`; the Display lab numbers (send time, rows that fit, dots / thumbnails readable) decide G2.
- F6 speech result: English exact, Korean not supported by DAT speech → English-only voice (D21).
- 2026-09-30: Back once reached the app (gesture source not recorded) — the only Back-arrived record across projects; run the Back check (`ray-ban-display-app-dev`) before relying on it.
- Next: G2 + F7 together — add-flow cards with the photo, item actions Used 1 / Opened with Undo and Discard behind a confirm card (D37), English voice commands ("milk two bottles", "expires October third", "opened today", "save it" only on REVIEW, D43). Later F8 voice questions, F9 smart kitchen.
- Verification toolchain: compile the whole app with the Kotlin CLI against the real jars and run JUnit (164 tests at 0.8.0).

## Changelog

- 2026-10-06: capstone relation (W8) and the 2026-09-30 Back record added.
