# Bee There (repo hack-ix-2026) — detail

Carried over from the `hackix2026-bee-there-project` skill (2026-10-05, from the readiness doc, brief, discussions, dev plan, round 1–4 status, Figma plugin v1, UI v1 status). State lines are as of 2026-10-03; re-check the repo before acting.

## Concept

A cat on the glasses that sits beside graduating seniors (especially neurodivergent students) when the next step feels risky. Keyword: **body doubling** — the cat works on its own little task; it never watches or grades. Demo cat **Steady** (worry / uncertainty). Demo moment: right after an internship meeting → the cat sorts what was said into **Known** / **Not sure yet** → the student clarifies one point in writing (cat drafts, student edits and sends from their own mail app) → room item labelled with the real action ("Asked about the deadline"). No streaks, scores, ranks or diagnosis labels.

## Where things are

- Repo https://github.com/hyunishyun/hack-ix-2026 · local `C:\Users\AISTUDIO\Desktop\Hyun's Playground\HackIX2026` (path is context, not access).
- Package `com.hyunishyun.hackix2026` at `app/src/main/java/com/hyunishyun/hackix2026/` — 8 levels deep, beyond the bridge limit: ask Hyun to connect it as a second folder.
- Read first: `CLAUDE.md`, `README.md`, `docs/WORKFLOW.md`, `docs/TEAM_SETUP.md`, then the newest round status doc. README / CLAUDE.md said "SDK not integrated" long after it was — prefer source + Gradle files.
- DAT 1.0.0 (core, display, inputs, speech; camera / motion / mockdevice aliases added in the upgrade), Kotlin 2.2, Compose material3 1.4.0. No new dependencies on this branch (SharedPreferences + `org.json` + `HttpURLConnection`); Ailo later brought in the hackix26 modules (Room, MediaPipe).
- Design: `design/figma-plugin/` (generator + "Export selected frames for Claude"), `design/specs/` (exported JSON + @2x PNG). Figma file on the team account; Claude cannot open Figma links from the school account.
- Secrets: `muse_api_key`, `muse_model` in `local.properties` → BuildConfig. Never read, paste or commit.

## Architecture

| Package | Owns |
| --- | --- |
| `wearables/`, `display/` | Registration, connection, session lifecycle (from preparation; keep working, extend only) |
| `companion/` | `CompanionState`, `CompanionEngine` (single owner of transitions, outside Compose), `CompanionRules` (`wantsBandDrag = !isChoiceScreen`), `CompanionPrefs` |
| `glasses/` | Renderer: state → pure card model → send only on change; inputs mapping; press guard 400 ms (900 ms "steady hands") |
| `sprite/` | `CatSprites`, `CaptionedFrames` (one shared crop, 288 px cat, captions drawn into a 384 × 360 bitmap), single-flight animator |
| `muse/` | `MuseClient`, `SteadyAssistant.system(style)` — one persona prompt; `sort`, `draft`, `line`; never invent facts |
| `speech/` | `GlassesTranscriber` (DAT speech, wearer, English) |
| `ui/` | `BeeThereApp` shell: Onboarding → Home · Listen · Room (floating pill), Settings from the cat avatar, Developer tools = original connection screen |

## Flows (device-verified unless noted)

- Idle (animated) — pinch = pet; capture SHORT = listen on/off; capture HOLD = Today card.
- Listening → Sorting (Muse) → Review (Ask now · Later · I'll decide / Got it) → Working (phone draft → mail app) → "I sent it" → Complete + room item. Problem card (Try again · Cancel).
- Today (2 tasks + Not now) → Focus (working cat, "task · m:ss", optional check-ins 10/25 min, default off) → pinch = menu (Keep going · I'm done · Stop for now); HOLD = next small step (Back to work · Smaller step). Stop ≥ 1 min → Lamp "Worked on … · N min".
- Room items: Plant = asked a question, Book = submitted, Letter = reached out, Lamp = started/worked on.
- Six personas (Starter, Reader, Quiet, Steady, Buddy, Gentle) = support-need presets; Steady fully built. Settings: tone, talk amount, help mode, less movement, band pinch, steady hands, Muse consent, supervisor email.

## Decisions

Mock Blackboard/email data with the real shape · demo transcript bundled, live DAT speech optional (the other person's voice is not captured by the glasses mic) · app never sends email itself (`ACTION_SENDTO`) · consent switch before Muse · audio never stored · no Back in flows — chosen because the R4 Back check failed (middle-finger pinch closes the app); re-run the Back check after an SDK/firmware update and revisit.

## State as of 2026-10-03

- Rounds 1–4 device-tested by Hyun (2026-10-02).
- UI v1 phone redesign (from Figma exports): written to the folder, uncommitted at the time, not built in Android Studio, not device-tested; 38 JVM tests passed offline. The 10/4 commit `678457a` "WIP: Bee There restyle, moments and task screens" on `feature/companion-glasses` probably holds it — confirm.
- Roadmap ideas not built: reply loop ("not mentioned" → Known), "Help me figure it out" hint ladder, phone-mic listening source, privacy centre, head nod via mwdat-motion, whiteboard snapshot, foreground service.
