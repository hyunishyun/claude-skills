# claude-skills — Hyun's Claude skills

Everything learned across Hyun's projects (LiftLens / Production HUD, Fridge Assistant, HackIX 2026, CONDUCTOR, the XR 455 capstone), kept as Claude skills so every new Claude.ai chat or Claude Code session starts from what was already proven. This repo is the **single source of truth**; installed copies are just copies.

Dated history and conflict decisions: `KNOWLEDGE_LOG.md`.

## The skills

| Skill | Kind | Use for |
| --- | --- | --- |
| `ray-ban-display-app-dev` | General — SDK and hardware facts | Meta Ray-Ban Display / DAT 1.0: setup, session, display, inputs, Neural Band, motion, speech, camera, on-phone vision, Web Apps, build environment, off-device verification, Figma track |
| `glasses-hud-design-rules` | General — screen design | Rules 1–23 for glasses cards: cursor reset, ticking/animated screens, Back check, band input modes, display arbiter, nudges, quiet ends |
| `plan-approve-build-loop` | General — how we work | Korean plans → approval → build → verify → device test → commit → devlog → skill harvest; working with Hyun; team repos; handoffs |
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
| `vue`, `nuxt`, `pinia`, `nitro` | Vue 3, Nuxt, Pinia state, Nitro server toolkit |
| `vite`, `vitest`, `vitepress` | Vite build/config, Vitest tests, VitePress docs sites |
| `unocss`, `pnpm` | UnoCSS atomic CSS, pnpm workspaces and catalogs |

### Third-party: Android / Kotlin / debugging (for the glasses apps' phone side)

Copied unchanged on 2026-10-06; each folder keeps its source license as `LICENSE.source`. Don't edit these here; to update, download the source repo again and replace the folder.

| Skill | Source (license) | Use for |
| --- | --- | --- |
| `android-cli` | [android/skills](https://github.com/android/skills) @42dc227 (Apache-2.0, Google) | Google's `android` CLI: devices, install/run, screenshots, UI interaction from the terminal |
| `android-profiler` | android/skills | Perfetto trace recording and analysis: jank, CPU, memory, power |
| `testing-setup` | android/skills | Test strategy and setup: unit, Compose UI, screenshot tests, Hilt testing |
| `camerax` | android/skills | Phone camera with CameraX (lifecycle, low light, thermals, ML Kit) |
| `ml-kit-genai-prompt-api` | android/skills | On-device Gemini Nano via ML Kit Prompt API: prompts, structured output |
| `android-permissions-security` | android/skills | Manifest permissions and exported-component audit |
| `kotlin-concurrency-and-flow` | [chrisbanes/skills](https://github.com/chrisbanes/skills) @f872f97 (Apache-2.0) | Coroutine scope ownership, cancellation, StateFlow/SharedFlow |
| `compose-state-and-effects` | chrisbanes/skills | Compose state hoisting, `LaunchedEffect`/`DisposableEffect`, Flow collection |
| `compose-ui-testing-patterns` | chrisbanes/skills | Compose UI and screenshot tests, semantics assertions |
| `systematic-debugging` | [obra/superpowers](https://github.com/obra/superpowers) @8ca22db (MIT) | Root cause first: reproduce, trace, one hypothesis at a time |
| `verification-before-completion` | obra/superpowers | Run the check and read its output before saying "done" |

### Plugins (installed through Claude Code, not stored in this repo)

Re-run after a PC reset:
```powershell
claude plugin marketplace add facebook/meta-wearables-dat-android
claude plugin install mwdat-android@mwdat-android-marketplace --scope user
claude plugin marketplace add facebook/meta-wearables-webapp
claude plugin install meta-wearables-webapp@meta-wearables --scope user
```
- `mwdat-android` (Meta): DAT SDK for Android — session, display, inputs, camera, speech, motion, MockDevice, debugging.
- `meta-wearables-webapp` (Meta): Ray-Ban Display **Web Apps** — build, UI Toolkit, on-device testing, performance, publish, WebMCP. Same marketplace also has `meta-wearables-webapp-game` (Three.js games), not installed.

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

**Claude.ai** (also used by the desktop app's chat): Settings → Capabilities → Skills → upload each changed skill as a zip of its folder, replacing the old one. Remove `hackix2026-bee-there-project`. Make a zip with:
```powershell
Compress-Archive "$env:USERPROFILE\.claude\skills\<skill-name>" "$env:USERPROFILE\Downloads\<skill-name>.zip" -Force
```

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
5. Say **"스킬 확인"** in a new chat → Claude compares the installed copies with this repo and lists anything stale or missing (installed skills load when a chat starts).
6. Every few projects: prune what is stale, move long detail into `references/`, keep each SKILL.md under ~500 lines, and add a line to `KNOWLEDGE_LOG.md`.

Never put keys, tokens, `local.properties` content, personal data about other people, or licensed art in this repo.
