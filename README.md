# claude-skills — Hyun's Claude skills

Everything learned across Hyun's projects (LiftLens / Production HUD, Fridge Assistant, HackIX 2026, CONDUCTOR, the XR 455 capstone), kept as Claude skills so every new Claude.ai chat or Claude Code session starts from what was already proven. This repo is the **single source of truth**; installed copies are just copies.

Dated history and conflict decisions: `KNOWLEDGE_LOG.md`.

**How Claude works with Hyun (Claude Code):** `user-instructions.md`, loaded by `~/.claude/CLAUDE.md` (one line: `@~/.claude/skills/user-instructions.md`). The superpowers workflow leads; Korean conversation and a `## 한국어 요약` in every spec/plan; standing answers (no worktrees for Android, TDD exceptions, finish order, commit style); stack routing; this PC's tools.

## The skills

| Skill | Kind | Use for |
| --- | --- | --- |
| `ray-ban-display-app-dev` | General — SDK and hardware facts | Meta Ray-Ban Display / DAT 1.0: setup, session, display, inputs, Neural Band, motion, speech, camera, on-phone vision, Web Apps, build environment, off-device verification, Figma track |
| `glasses-hud-design-rules` | General — screen design | Rules 1–23 for glasses cards: cursor reset, ticking/animated screens, Back check, band input modes, display arbiter, nudges, quiet ends |
| `plan-approve-build-loop` | General — how we work | Hyun layer on top of superpowers: 한국어 요약, PowerShell guidance, off-device verification, device test checklist, commit style, devlog, skill harvest; team repos; handoffs (full plan → approval loop where superpowers isn't installed) |
| `hackathon-sprint-playbook` | General — process | Hackathons, sprints, crits: prep, brief, concepts, survey, team contracts, integration, rounds, inclusive checklist, demo video, release |
| `evidence-and-claims` | General — words | Research briefs, pitches, presentations, release notes, posts: status words, sourcing, say / don't say, health copy, doc sync |
| `xr455-capstone` | Project | Capstone question, roadmap, Fall PoC scope and timeline, progress format, reuse map |
| `liftlens-project` | Project | LiftLens Mini / Production HUD repo context |
| `fridge-assistant-project` | Project | Fridge Assistant repo context |
| `hackix2026-project` | Project | Bee There, OCD Companion (hackix26), Ailo — both repos (replaces `hackix2026-bee-there-project`) |

Rule of thumb: facts that hold for every glasses app go in a general skill; paths, decisions and state go in a project skill. Two device results that disagree are both kept, with date and app, plus the safe rule.

### Third-party: Anthony Fu's skills (web stack)

Copied unchanged on 2026-10-06 from [antfu/skills](https://github.com/antfu/skills) (MIT, © Anthony Fu — `LICENSE.md` in each folder). Don't edit these here; to update, download the repo again and replace the folders.

| Skill | Use for |
| --- | --- |
| `antfu` | Anthony Fu's JS/TS tooling conventions (ESLint config, pnpm, monorepos, library publishing) |
| `antfu-create-pr` | Opening a GitHub PR: Conventional Commits title, evidence-based body, before/after screenshots |
| `vue` | Vue 3 |
| `vite`, `vitest` | Vite build/config, Vitest tests |
| `pnpm` | pnpm workspaces and catalogs |

Only in JS/TS repos that already use them (see `user-instructions.md`); never for glasses Web Apps, which follow Meta's React + npm stack.

**Parked** in `_parked/` (2026-10-06, not loaded — folders two levels deep are skipped): `nuxt`, `nitro`, `pinia`, `unocss`, `vitepress`. About 140 skills were loaded and the skill-description budget ran out (73 plugin skills showed no description), so unused skills wait here. To use one again: `git mv _parked/<name> .` and commit.

### Third-party: Android (for the glasses apps' phone side)

Copied unchanged on 2026-10-06; each folder keeps its source license as `LICENSE.source`. Don't edit these here; to update, download the source repo again and replace the folder.

| Skill | Source (license) | Use for |
| --- | --- | --- |
| `android-cli` | [android/skills](https://github.com/android/skills) @42dc227 (Apache-2.0, Google) | Google's `android` CLI: devices, install/run, screenshots, UI interaction from the terminal |
| `android-profiler` | android/skills | Perfetto trace recording and analysis: jank, CPU, memory, power |
| `testing-setup` | android/skills | Test strategy and setup: unit, Compose UI, screenshot tests, Hilt testing |
| `camerax` | android/skills | Phone camera with CameraX (lifecycle, low light, thermals, ML Kit) |
| `ml-kit-genai-prompt-api` | android/skills | On-device Gemini Nano via ML Kit Prompt API: prompts, structured output |
| `android-permissions-security` | android/skills | Manifest permissions and exported-component audit |
| `agp-9-upgrade` | android/skills | Moving a project to Android Gradle Plugin 9 (only when upgrading) |

### Plugins (installed through Claude Code, not stored in this repo)

Re-run after a PC reset:
```powershell
claude plugin install figma@claude-plugins-official --scope user
claude plugin marketplace add facebook/meta-wearables-dat-android
claude plugin install mwdat-android@mwdat-android-marketplace --scope user
claude plugin marketplace add facebook/meta-wearables-webapp
claude plugin install meta-wearables-webapp@meta-wearables --scope user
claude plugin install meta-wearables-webapp-game@meta-wearables --scope user
claude plugin marketplace add chrisbanes/skills
claude plugin install chrisbanes-skills@chrisbanes-skills --scope user
claude plugin marketplace add obra/superpowers
claude plugin install superpowers@superpowers-dev --scope user
```
- `figma` (official): Figma design ↔ code, FigJam diagrams, Figma track for glasses frames.
- `mwdat-android` (Meta): DAT SDK for Android — session, display, inputs, camera, speech, motion, MockDevice, debugging.
- `meta-wearables-webapp` (Meta): Ray-Ban Display **Web Apps** — build, UI Toolkit, on-device testing, performance, publish, WebMCP. 
- `meta-wearables-webapp-game` (Meta): Web App **games** for the glasses (Vite + TypeScript + Three.js): create, iterate with frame stats, validate, logging, game director critique.
- `chrisbanes-skills` ([chrisbanes/skills](https://github.com/chrisbanes/skills), Apache-2.0): Kotlin coroutines/Flow, Compose state, effects, animations, focus, performance, UI tests, Gradle runs, API design. His planning/spec skills (`to-plan`, `deliver-spec`, `implement-with-subagents`, `shepherd`, `run-github-project`) are manual-only and not used — superpowers leads planning.
- `superpowers` ([obra/superpowers](https://github.com/obra/superpowers), MIT): systematic debugging, verification before completion, TDD, brainstorming, plans, code review, worktrees. **Its SessionStart hook tells Claude to check skills before every reply.** User instructions (CLAUDE.md, direct requests) still win over its workflows. Turn it off with `claude plugin disable superpowers@superpowers-dev`.

## Set up (once)

All commands are for **Windows PowerShell**.

The repo is `https://github.com/hyunishyun/claude-skills` (private, created 2026-10-06). The working copy **is** `$env:USERPROFILE\.claude\skills`, so Claude Code (personal skills, every project) loads straight from it.

`.claude\skills\synced\` belongs to the Claude desktop app (its built-in and claude.ai skills). It is git-ignored. **Never rename, move or delete `.claude\skills` as a whole** — that breaks the app's synced skills.

**Set up on a new or reset PC:**
```powershell
cd "$env:USERPROFILE\.claude\skills"
git init
git remote add origin https://github.com/hyunishyun/claude-skills.git
git fetch origin
git checkout -f -t origin/main
```
If `.claude\skills` doesn't exist yet, a plain `git clone https://github.com/hyunishyun/claude-skills.git "$env:USERPROFILE\.claude\skills"` works too. Success: `$env:USERPROFILE\.claude\skills\xr455-capstone\SKILL.md` exists. If an older copy of `hackix2026-bee-there-project` sits in a project repo's `.claude/skills/`, remove it there so it doesn't load next to `hackix2026-project`.

**Claude.ai** (also used by the desktop app's chat): Settings → Capabilities → Skills → upload each changed skill as a zip of its folder, replacing the old one. Upload only Hyun's 9 skills there (not the third-party ones — they would crowd the description budget). Make a zip with:
```powershell
tar.exe -a -c -f "$env:USERPROFILE\Downloads\<skill-name>.zip" -C "$env:USERPROFILE\.claude\skills" <skill-name>
```
Don't use `Compress-Archive` in Windows PowerShell 5.1: it writes `\` into zip paths and Claude.ai rejects the zip ("Zip file contains path with invalid characters").

On shared PCs, clear `git:https://github.com` from Windows Credential Manager when you leave.

## Keep them alive

1. At the end of every work round, Claude adds a **Skill harvest** block to the status doc (new facts with evidence labels and dates, contradictions with existing skills, target skill).
2. Say **"스킬 업데이트"** → Claude rewrites the affected SKILL.md files with a dated changelog line and gives you the changed folders/zips.
3. Copy them into `$env:USERPROFILE\.claude\skills` (or let Claude Code edit them there), then:
   ```powershell
   cd "$env:USERPROFILE\.claude\skills"
   git add .
   git commit -m "Skills: <what changed>"
   git push
   ```
4. Claude Code picks up the change in the next session. On another PC: `cd "$env:USERPROFILE\.claude\skills"; git pull`. Claude.ai: re-upload only the changed zips.
5. Say **"스킬 확인"** in a new chat → Claude compares the installed copies with this repo and lists anything stale or missing (installed skills load when a chat starts). In Claude Code, Hyun's skills show twice: `<name>` (this repo) and `anthropic-skills:<name>` (the Claude.ai upload). Claude uses the unprefixed one; re-upload changed zips so the other one doesn't lag.
6. Every few projects: prune what is stale, move long detail into `references/`, keep each SKILL.md under ~500 lines, and add a line to `KNOWLEDGE_LOG.md`.

**After a PC reset:** set up the repo (above), re-create `~/.claude/CLAUDE.md` with the one `@` line, and re-run the plugin commands.

Never put keys, tokens, `local.properties` content, personal data about other people, or licensed art in this repo.
