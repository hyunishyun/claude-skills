# Hyun's instructions for every Claude Code session

Loaded from `~/.claude/CLAUDE.md` (`@~/.claude/skills/user-instructions.md`). These are user instructions: they rank above skills. Where a skill asks "ask your human partner" about something answered here, this is the answer — don't ask again.

## Process: superpowers first

- The **superpowers** workflow leads every coding task: `brainstorming` → `writing-plans` → `executing-plans` / `subagent-driven-development` → `verification-before-completion` → `finishing-a-development-branch`; `systematic-debugging` for any bug or unexpected behaviour. Keep its approval gates.
- `plan-approve-build-loop` adds Hyun-specific rules on top (Windows/PowerShell guidance, off-device verification, device test checklist, commit style, dev logs, skill harvest). Where it differs on *process*, superpowers wins.
- Domain skills come first for design: glasses → `ray-ban-display-app-dev`, `glasses-hud-design-rules`, `mwdat-android:*`, the project skill; glasses Web Apps → `meta-wearables-webapp:*` / `meta-wearables-webapp-game:*`.
- Fact conflicts: a [DEVICE] fact in Hyun's skills wins for that firmware/phone (note both); Meta's plugin skills win for API names and setup not yet device-tested.
- Don't use chrisbanes `to-plan`, `deliver-spec`, `implement-with-subagents`, `run-github-project` or `shepherd` — superpowers owns planning, execution and PRs. His `kotlin-*`, `compose-*` and `gradle-run` skills are fine.
- If a skill appears twice (`anthropic-skills:<name>` and `<name>`), use the unprefixed copy from `~/.claude/skills`; the prefixed one is the Claude.ai upload and may be stale.

### Skill map (the skill list runs out of description space; these may show name-only)

- `superpowers:brainstorming` — before building anything new; `writing-plans` — after the spec is approved; `executing-plans` / `subagent-driven-development` — run the approved plan.
- `superpowers:systematic-debugging` — any bug, failing test, crash or odd device behaviour, before proposing a fix.
- `superpowers:test-driven-development` — pure logic (see TDD exceptions below); `verification-before-completion` — before saying done/fixed/passing or committing.
- `superpowers:requesting-code-review` / `receiving-code-review` — big rounds and PR feedback; `finishing-a-development-branch` — after the device test.
- `chrisbanes-skills:kotlin-concurrency-and-flow`, `compose-state-and-effects`, `compose-ui-testing-patterns`, `compose-performance`, `compose-animations`, `gradle-run` — phone-side Kotlin/Compose work and Gradle runs.
- `mwdat-android:*` — Meta's DAT SDK reference (display-access, inputs, camera-streaming, session-lifecycle, mockdevice-testing, debugging, live-debugging-mcp).

### Standing answers for superpowers

- **Worktrees:** Android projects get no worktree — Hyun builds from the folder open in Android Studio, and a new worktree has no `local.properties`. Before the spec is written, create branch `hyun/<feature>` (team repos: `<name>/<feature>`) in the main checkout; that branch is the consent. Never implement on `main`. Web-only repos may use `.worktrees/`.
- **TDD exceptions (standing permission):** DAT session/display/camera/input wiring, glasses DSL screens, Compose layout, manifest and Gradle config, generated code, spikes and capability probes. For these the plan step is "compile + a device-checklist item". Pure logic (state machines, parsers, display arbiter, card models, schema migrations) keeps TDD. Never delete existing working code to satisfy TDD; add a characterization test instead.
- **Execution method:** `executing-plans` (inline) unless the plan has 8+ independent tasks.
- **Rulings never overturn Hyun's direction** (any unattended run: `executing-plans`, `subagent-driven-development`, workflow scripts). Concluding that a stated direction or standing decision (a freeze, scope, difficulty, a tool or platform) should change is a challenge, not a ruling: keep building his direction and report it under "Challenges to your direction" with the reason and the cost if wrong. If it cannot be built at all, that is superpowers' stop case.
- **Flaky tests:** no retry, wider tolerance or timeout, or `[Ignore]` / `@Ignore` until the cause is named (timing, test order, shared or static state, clock, random seed). Run it N ≥ 10 times on the base and on the branch (alone where the runner can filter, e.g. Gradle `--tests`; otherwise the suite, and say so), report `failed/N` for both, and prove the fix with `verification-before-completion`'s revert check.
- **Unattended debugging:** where `systematic-debugging` says to stop and discuss (3 failed fixes; for a bug only the device shows, 3 failed hypotheses), add dev-build logging and park the bug: a blockers entry (hypotheses tried, logging added), a device-checklist capture item, status "next step, blocked by device capture". Then move on.
- **Fewer stops:** batch independent brainstorming questions into one message, each with a recommended answer. In hackathons, one spec + one plan per round, not per feature; capability probes take the Spike path.
- **Finish order:** final review → device test checklist (`plan-approve-build-loop` §5) → wait for Hyun's results (or "skip device test") → `finishing-a-development-branch`. While a device test is pending, or in team repos, don't offer "merge locally"; team repos go through a PR.
- **Commits:** Claude commits in Claude Code, in the repo's style (`Area: what changed`; Conventional Commits only where the repo already uses them) — this overrides the `feat: …` examples in plan templates. PR titles follow the same style. For glasses UI, visual evidence = phone screenshots or photos Hyun supplies; a missing glasses capture goes under Verification and doesn't block a PR (antfu-create-pr).
- **Skill harvest ("update skills"):** these are fact/rule edits. From `writing-skills` apply only the frontmatter, description and size rules; skip pressure-test baselines unless Hyun asks or it's a new process skill.

## Language: English by default, Korean chat only on request (set 2026-10-14)

- **Chat in English by default.** Other people may use this PC and these settings. Switch the chat to Korean only when the user asks for it (e.g. "speak in Korean", "한국어로 말해"). The switch then holds for that session, until they ask for English again. It covers answers, questions, status, plan reviews, hand-overs and device test steps given in the chat. A project's CLAUDE.md may set the chat language for its own user.
- **Everything written to a file is English:** specs, plans, checklists, test logs, code, comments, commits, PRs, docs, CLAUDE.md and skill files. Hyun's own Korean words may appear only as quoted evidence (e.g. an approval). This overrides any skill or older instruction that asks for a Korean section in a file (e.g. a `한국어 요약` section) — write that part in English instead.
- Every spec (`docs/superpowers/specs/…`) and plan (`docs/superpowers/plans/…`) has a **`## Summary`** in English right after the H1 title (in plans, after the "For agentic workers" note): goal, what changes (files), decisions (D1: chosen option + reason, alternatives), tests/verification, what to check on device, risks. When asking for review, explain the summary in the chat, in the chat language.
- Approving a spec accepts every D not explicitly changed; plans carry no open decisions. "approved", "go", "ok", "looks good" (or the same in Korean, e.g. "승인", "권장대로") approve the stage just presented — only that stage.

## Stack routing (outranks third-party skills)

- Glasses Web Apps: Meta's webapp skills own the stack — React (games: Three.js) + Vite + TypeScript, **npm with a committed package-lock.json**, their own check/validate scripts. Never apply `antfu`, `vue`, `nuxt`, `pinia`, `unocss`, `pnpm`, @antfu/eslint-config, knip or simple-git-hooks there, and don't migrate the scaffold's Vite major.
- `antfu` and the Vue/pnpm/vitest skills apply only in JS/TS repos with a package.json, and only when Hyun asks for that setup or the repo already uses it — never for Android projects or Android tests (those use `testing-setup`, chrisbanes and superpowers TDD). Existing and team repos keep their package manager, lint, test and commit setup.
- PRs: `superpowers:finishing-a-development-branch` decides; `antfu-create-pr` writes the PR body only when a PR is chosen, with the title in the repo's existing style.
- Skill work: "update skills" / "check skills" → `plan-approve-build-loop` §7. `writing-skills` / `skill-creator` only for new skills or testing a description.
- Android tests: keep the project's stack (JUnit 4, plain-Kotlin logic tests, engine outside Compose). `testing-setup` defaults (Hilt, Robolectric, Jacoco, Dropshots, Truth, screenshot plugin, AGENTS.md) are proposals that need approval like any new dependency.
- Glasses camera = DAT `Stream` / `capturePhoto` + MediaPipe. `camerax` and the CAMERA runtime-permission flows in `android-permissions-security` are for the phone's own camera only; Meta permissions (camera/mic via the Meta AI app) are not Android runtime permissions; the DAT registration deep-link activity stays exported.
- Upgrades (`agp-9-upgrade`, Vite/Rolldown migrations, DAT version bumps) only when Hyun asks.

## This PC (school Windows 11, checked 2026-10-06, updated 2026-10-14)

- Shell: PowerShell 5.1 for commands Hyun runs — one command per line, never `&&`. Git Bash is available to Claude.
- Installed: Git, GitHub CLI (`hyunishyun`), Python 3.12.10 (`python`, `py`; checked 2026-10-14), JBR 21 (`JAVA_HOME = ~/.jdks/jbr-21.0.11`), Android Studio + SDK. **Not installed:** Node/npm/pnpm/`nr`, the `android` CLI, Vercel CLI.
- adb is not on PATH: `& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" …` (Git Bash: `"$LOCALAPPDATA/Android/Sdk/platform-tools/adb.exe"`).
- Gradle: the project wrapper — `.\gradlew.bat <task>` (PowerShell) or `./gradlew <task>` (Git Bash). With chrisbanes `gradle-run`, pass `gradlew.bat` as the command; if its wrapper fails, or `agp-9-upgrade` is active, run the wrapper directly.
- Python from Git Bash: pass Windows paths (`cygpath -w`), not `/c/...`. The console is cp1252, so Korean output crashes `print`; call `sys.stdout.reconfigure(encoding='utf-8')` (`-I` ignores `PYTHONIOENCODING`).
- Git: global `user.name hyunishyun` is set; `core.autocrlf=true` comes from Git for Windows' system gitconfig, so Unity repos produce line-ending-only changes unless the repo has a `.gitattributes` (Hand Hero added one in round 10).
- The PC restarts overnight for Windows Update; long unattended runs must be resumable.
- Never install tools or run remote install scripts (android CLI, Node, perfetto helpers, UI Toolkit skill installers) without asking Hyun first: say what, from where, and that the school PC may reset. If a skill needs a missing tool, say so and give Hyun the command.
