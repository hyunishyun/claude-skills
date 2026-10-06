---
name: "plan-approve-build-loop"
description: "Use for any coding session with Hyun (Android, glasses, web; solo or team). Hyun layer on top of superpowers: 한국어 요약, PowerShell guidance, off-device verification, device test checklist, commit style, dev logs, handoffs, skill harvest (스킬 업데이트 / 스킬 확인)."
---

# Plan → approve → build → verify → device test → commit

Working agreement with Hyun (SCAD BFA Immersive Reality). Conversation and plans in **Korean**; code, comments, commit messages, dev logs, status docs and skill files in English. Messages for English-speaking teammates: English, ready to paste.

**With superpowers (Claude Code, since 2026-10-06):** Hyun chose the superpowers workflow as the process. `brainstorming` and `writing-plans` are the plan step, their gates are the approval step, `executing-plans` / `subagent-driven-development` build, `verification-before-completion` and `finishing-a-development-branch` close. This skill then only adds what superpowers does not know: sections 0, 2 (superpowers bullet), 3, 4–7, status docs, team repos, handoffs and lessons — skipping bullets marked [Claude.ai/Cowork]. Standing answers to superpowers' questions (worktrees, TDD exceptions, finish order, commit style) are in `user-instructions.md`. Every spec and plan starts with a `## 한국어 요약` (see `~/.claude/skills/user-instructions.md`). **Without superpowers** (Claude.ai, Cowork): follow sections 1–5 as written.

## 0. Working with Hyun

- XR developer (Unity / UE5; Android with Meta DAT across LiftLens, Fridge Assistant, HackIX), but **new to terminal / git command-line work**. For any command: say where to type it (which app, which folder — e.g. the prompt must end in `hackix26>`), give copy-paste commands for **Windows PowerShell** on his school Windows 11 PC, say what success looks like and what to send back if it fails. When he sends a terminal screenshot, check it is the right terminal (he once typed into the server/log one).
- Short, decisive answers: recommend one option and say why. He wants to hear when a better structure exists than the one he asked for.
- When he highlights a line from your reply and asks "what does this mean / how do I do this", answer that line concretely.
- **Hardware-dependent rules: check first, apply when they fit** (his words, 2026-10-05). Never turn one device result into an "always" rule.
- Do not invent product names or branding; use neutral working titles until the team decides.
- School PC may reset: remind him to push before leaving; on shared PCs, clear `git:https://github.com` from Windows Credential Manager.

## 1. Inspect before planning

- [Claude.ai/Cowork] Stage the current files from the device and diff them against the last copy you wrote; never plan from memory. Read `.git/HEAD` and `.git/logs/HEAD` to confirm the branch, last commit and that the tree is clean.
- State "confirmed current behaviour" separately from "proposed changes" and from "decisions that need Hyun's call".

## 2. Plan, then wait

- With superpowers: the spec and plan *are* the plan. Put the D1, D2… decisions and the commit message in the 한국어 요약, show that summary in the chat, and let superpowers' gates do the waiting.
- Explain what will change, why, which files, the state transitions/rules, tests, and the commit message. List open decisions as D1, D2… with a recommendation each.
- **Wait for explicit approval** ("승인", "go", "통과") before touching code.
- Hyun asked for **fewer checkpoints**: when several stages can be built and tested together safely, propose one round covering them (e.g. models + persistence) and say why. Detailed staged explanations are welcome; staged approvals are not. With superpowers, its required gates stay; where it leaves a choice (task classification, how many design sections, execution approach), pick the option with the fewest stops that is still honest.
- Be proactive: say when a better structure exists than the one requested, and recommend it with reasons. Hyun wants to hear it.
- Respect stated constraints literally (e.g. "planning only, no file changes", "do not move X", "schema belongs to a later stage").

## 3. Build

- Additive changes with defaults so untouched files keep compiling. Keep public APIs stable across refactors; check parity wrapper-by-wrapper.
- No new dependencies unless approved. Keep the Meta sample's session/display lifecycle untouched.
- [Claude.ai/Cowork] File tools cannot move/rename files; ask Hyun to do that in Android Studio.
- **Worktrees vs Android Studio:** Hyun builds and installs from the folder open in Android Studio, so Android work happens on a branch in the main checkout (no worktree; see `user-instructions.md`). If a web-only repo uses a worktree, tell him in Korean which folder to open before any test.

## 4. Verify off-device (Hyun cannot see your work run)

- SDK identifiers: check against the real enum (sample code or APK DEX dump) — never guess.
- In Claude Code, compile and test with the real build: `.\gradlew.bat :app:compileDebugKotlin` / `:app:testDebugUnitTest` (or through chrisbanes `gradle-run` with `gradlew.bat`).
- [Claude.ai/Cowork] Type-check with the exact Kotlin CLI compiler version (GitHub releases work when Maven is blocked) against minimal stubs; run stateful logic through a simulation (double presses, process kill/restore, schema migrations, boundaries).
- Scan for bracket balance, unused/duplicate imports, `continue` inside lambdas, and any line the UI reads that the change could break.

## 5. Deliver

- [Claude.ai/Cowork] Write back via the device bridge with `expectedMtimeMs` from the staging step so Hyun's edits are never overwritten. Also send the files into the chat.
- Give a **device test checklist** (numbered, with what to look for in Logcat and on the glasses), before `finishing-a-development-branch`, and wait for the results. In Claude Code, Claude makes the commits; give Hyun only the commands he runs himself (push, branch switch), one per line, never `&&` (PowerShell 5.1). In Claude.ai/Cowork, give the `git add` and `git commit -m "Area: what changed"` commands on separate lines.
- Before a schema change, ask for a backup of the data file: `& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" exec-out run-as <package> cat files/<file> > backup.json`.
- Commit message style: `Sessions: glasses session screens`, `Programs: … and history schema v3`. Use the repo's existing style over skill examples (superpowers plans show `feat: …`; Hyun's repos use `Area: what changed`). Team repos that already use Conventional Commits keep them.

## 6. Dev log

On request, write `DEVLOG_<date>.md`: what changed and why, confirmed facts learned from the code/hardware, files touched (and untouched), design rules added, open items. Take commit counts and names from the reflog, not from memory; correct earlier logs when they were wrong.

## 7. Harvest learnings into skills (end of every round)

After the device checklist comes back, before moving on, add a short **"Skill harvest"** block to the round's status doc / reply:

- New hardware or SDK facts, each labelled [DEVICE] / [JAR] / [SAMPLE] / [SIMULATED] / [UNVERIFIED] with SDK, firmware, phone, date.
- Facts that **contradict** an existing skill (quote the old line, give the new one). Contradictions win over additions; never leave both — unless two device results genuinely differ by project or configuration, then keep both with date and app and state the safe rule.
- Design rules or workflow habits that saved or cost time.
- Target skill for each item: general SDK facts → `ray-ban-display-app-dev`; screen rules → `glasses-hud-design-rules`; how we work → this skill; event/sprint process → `hackathon-sprint-playbook`; research, pitch and public wording → `evidence-and-claims`; capstone scope and progress → `xr455-capstone`; project paths/state → the project skill.

Harvest edits are fact/rule updates: from superpowers `writing-skills` apply only the frontmatter, description and size rules. Third-party skills installed by tools (e.g. `wearables-ui-toolkit-web`) are gitignored, not harvested.

When Hyun says "스킬 업데이트" (or approves the harvest), write the edited SKILL.md files (whole files, with a dated `## Changelog` line), keep each under ~500 lines, and move long detail into `references/`. Project-specific state never goes into the general skills.

**Where the skills live:** Hyun's private GitHub repo `claude-skills` is the source of truth (one folder per skill + `README.md` + `KNOWLEDGE_LOG.md`). After an update: commit there, re-upload the changed zips to Claude.ai, `git pull` in `~/.claude/skills` for Claude Code. When he says "스킬 확인", compare the installed copies (Claude.ai: `/mnt/skills/...`; this PC's desktop-app cache: `%APPDATA%\Claude\local-agent-mode-sessions\skills-plugin\<id>\<id>\skills\`, shown in Claude Code as `anthropic-skills:<name>`) with the repo version and list what is stale or missing — installed copies are loaded when a chat starts, so changes show up only in a new chat.

## Status doc format (one per round, e.g. `round4-status.md`)

Branch + base commit · Flow (what the user does) · Decisions applied (D1…) · Device results with labels · Known issues · Code (new / changed files) · Verification (compile, tests, device as separate lines) · Next · Skill harvest.

## Team repos (HackIX 2026 lessons)

- Branches `<name>/<feature>`, PR + review from the other anchor, no direct pushes to `main`. GitHub rulesets are **not enforced on free private repos** — it is an agreement until the repo goes public.
- Shared contracts (session owner, display arbiter, journal schema) live in one doc section that both anchors' docs copy verbatim; change them only when both agree.
- Prefix decision IDs with their source doc (`UNIFIED D2`, `band D2`) — two "D2"s in one project caused confusion.
- Parallel build agents / worktrees must check out the working branch explicitly (they started from `origin/main` and missed the new contracts).
- Run an adversarial review pass on large rounds; fix every confirmed finding and re-verify.
- After automating a dev panel or settings, restore every toggle (a left-on "Stove knob" switch confounded a real door test).
- When test counts or statuses change, update every doc that states them (README, status, release notes, CLAUDE.md) in the same commit; grep for the old number.
- Commit metadata can expose personal emails — check before a repo goes public.

## Handoffs (usage limits, new sessions, other accounts)

When a session is close to its usage limit or work moves to another person's Claude, write a handoff before stopping: where things are (repo, branches + state + PRs), what is built, what is device-verified vs not, decisions (approved / open, with IDs), rules that must survive a merge, build and test commands, open items with owners. Next session reads `CLAUDE.md`, then the handoff, then the newest status doc. If work restarts from an earlier known-good base, record why and what was carried over.

## Lessons that cost time here

- The glasses cursor resets on every redraw — design for it (see `glasses-hud-design-rules`).
- A slower refresh is not an acceptable fix for a ticking screen.
- Centered fixed-height Compose columns clip; use scroll + top alignment.
- Never overwrite a data file you could not read: back up before schema promotions, move corrupt files aside, treat unknown versions as unreadable.
- Test fixtures with fake old timestamps will trip time-based rules; fix the fixture, keep the rule.
- Source folders deeper than the bridge limit (7) cannot be staged: ask Hyun to connect the package folder as a second folder at the start (HackIX, LiftLens).
- Uncommitted work on a branch is not a contradiction with GitHub `main`; read `.git/HEAD` / reflog and say which ref every fact came from. A branch name can lie (`chore/dat-1.0-upgrade` still held 0.9.0): read the Gradle files.
- Hyun-confirmed device results are reported as "Hyun reports …" with the date; Claude never writes "tested" for something it only compiled.
- When a phone and the glasses show the same state, the engine (outside Compose) owns it; both renderers only read it. This made resume-after-close free in HackIX R4.
- Two integration branches in parallel (hackix26 `team/one-app` and hack-ix-2026 `hyun/ailo-base`, 2026-10-04) left it unclear which build was final. Keep one integration branch and name the demo build by commit/tag.

## Changelog

- 2026-10-06 (b): superpowers leads the process in Claude Code; this skill is the Hyun layer (한국어 요약, worktree note, commit style).
- 2026-10-06: section 0 (working with Hyun), step 7 extended (skills repo, 스킬 확인, new target skills), team-repo and handoff sections, HackIX 2026 lessons.
- 2026-10-05: step 7 (skill harvest), status doc format, HackIX lessons.
