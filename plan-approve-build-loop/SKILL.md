---
name: "plan-approve-build-loop"
description: "Use for any coding session with Hyun (Android, glasses, Unity, web; solo or team). Hyun layer on top of superpowers: language rule, PowerShell guidance, off-device verification, device test checklist, commit style, dev logs, handoffs, session recovery, skill harvest (update skills / check skills, 스킬 업데이트)."
---

# Plan → approve → build → verify → device test → commit

Working agreement with Hyun (SCAD BFA Immersive Reality). Language follows `user-instructions.md` (since 2026-10-14): chat in English unless Hyun asks for Korean in that session; every file — specs, plans, code, commits, docs, skills — in English. Messages for English-speaking teammates: English, ready to paste.

**With superpowers (Claude Code, since 2026-10-06):** Hyun chose the superpowers workflow as the process. `brainstorming` and `writing-plans` are the plan step, their gates are the approval step, `executing-plans` / `subagent-driven-development` build, `verification-before-completion` and `finishing-a-development-branch` close. This skill then only adds what superpowers does not know: sections 0, 2 (superpowers bullet), 3, 4–7, status docs, team repos, handoffs and lessons — skipping bullets marked [Claude.ai/Cowork]. Standing answers to superpowers' questions (worktrees, TDD exceptions, finish order, commit style) are in `user-instructions.md`. Every spec and plan starts with a `## Summary` (see `~/.claude/skills/user-instructions.md`). **Without superpowers** (Claude.ai, Cowork): follow sections 1–5 as written.

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

- With superpowers: the spec and plan *are* the plan. Put the D1, D2… decisions and the commit message in the `## Summary`, explain that summary in the chat, and let superpowers' gates do the waiting.
  - Standing decisions and freezes (linked memory files, the project's decisions/questions files, the project skill) go word for word into the spec's constraints, so the plan's Global Constraints carry them; subagents get at most the MEMORY.md index. A spec line that would change one goes under Risks.
  - Before an unattended run, check the deny rules: each matches one shell and one spelling, so mirror every `Bash(...)` deny as `PowerShell(...)` and add `git -C * push *` / `git -c * push *`. `Edit(...)` denies don't stop Unity, git or Python subprocesses (Claude Code permissions doc, 2026-10-18). Show Hyun the exact lines before changing settings.
- Explain what will change, why, which files, the state transitions/rules, tests, and the commit message. List open decisions as D1, D2… with a recommendation each.
- **Wait for explicit approval** ("승인", "go", "통과") before touching code.
- Hyun asked for **fewer checkpoints**: when several stages can be built and tested together safely, propose one round covering them (e.g. models + persistence) and say why. Detailed staged explanations are welcome; staged approvals are not. With superpowers, its required gates stay; where it leaves a choice (task classification, how many design sections, execution approach), pick the option with the fewest stops that is still honest.
- Be proactive: say when a better structure exists than the one requested, and recommend it with reasons. Hyun wants to hear it.
- Respect stated constraints literally (e.g. "planning only, no file changes", "do not move X", "schema belongs to a later stage").

## 3. Build

- Additive changes with defaults so untouched files keep compiling. Keep public APIs stable across refactors; check parity wrapper-by-wrapper.
- No new dependencies unless approved. Keep the Meta sample's session/display lifecycle untouched.
- [Claude.ai/Cowork] File tools cannot move/rename files; ask Hyun to do that in Android Studio.
- **Worktrees vs Android Studio:** Hyun builds and installs from the folder open in Android Studio, so Android work happens on a branch in the main checkout (no worktree; see `user-instructions.md`). If a web-only repo uses a worktree, tell him which folder to open before any test.

## 4. Verify off-device (Hyun cannot see your work run)

- SDK identifiers: check against the real enum (sample code or APK DEX dump) — never guess.
- In Claude Code, compile and test with the real build: `.\gradlew.bat :app:compileDebugKotlin` / `:app:testDebugUnitTest` (or through chrisbanes `gradle-run` with `gradlew.bat`).
- [Claude.ai/Cowork] Type-check with the exact Kotlin CLI compiler version (GitHub releases work when Maven is blocked) against minimal stubs; run stateful logic through a simulation (double presses, process kill/restore, schema migrations, boundaries).
- Scan for bracket balance, unused/duplicate imports, `continue` inside lambdas, and any line the UI reads that the change could break.
- **A silent run is not a pass.** A lane whose plan step runs tests and ran 0 has failed. Verification gives `tests ran N (base M), skipped S (base S0), deleted K` from the result files (Unity NUnit XML; Android `TEST-*.xml` after `--rerun`, since an UP-TO-DATE or FROM-CACHE task leaves old files). A reviewer that returned nothing is missing coverage, and a finding with no verdict stays open.
- **Gate edits:** list each hunk that weakens a check (deleted or skipped test, looser assertion, tolerance or timeout, suppressed warning, empty `catch`, edited baseline, approved spec/plan line changed later). Restoring it is a normal fix and a test deleted with its code is fine; keeping any other relaxation needs Hyun (unattended: open).
- Before fixing a confirmed finding, search for its siblings (same symbol, string, enum value, call shape) and fix the in-scope ones with it; in an SDD / executing-plans task, those outside the task's files go to the ledger. If one of two mirrored paths changes (two aim modes, phone/glasses renderers, a log writer/reader), check the other.
- Before deleting, renaming or merging code, read every caller; a missing grep hit is only a lead. Unity: search scene/prefab/asset YAML for the `.meta` GUID and `m_MethodName`, and check `SendMessage` / `Invoke` / `StartCoroutine` strings; renamed serialized fields get `[FormerlySerializedAs]`; serialized enums append (renumber only when every stored value is regenerated or migrated). Android: manifest, XML `onClick`, reflection, R8 keep rules.
- Debugging: first read what earlier rounds found about the same files or symptom (device-log analyses, known issues, review findings, `git log --oneline -- <file>`). Search the web by framework + version + error class, never with paths, account names, App IDs or keys.

## 5. Deliver

- [Claude.ai/Cowork] Write back via the device bridge with `expectedMtimeMs` from the staging step so Hyun's edits are never overwritten. Also send the files into the chat.
- Give a **device test checklist** (numbered, with what to look for in Logcat and on the glasses), before `finishing-a-development-branch`, and wait for the results. In Claude Code, Claude makes the commits; give Hyun only the commands he runs himself (push, branch switch), one per line, never `&&` (PowerShell 5.1). In Claude.ai/Cowork, give the `git add` and `git commit -m "Area: what changed"` commands on separate lines.
- Checklist items also come from what off-device checks faked (synthetic input, a fake room, MockDevice, mocked providers), undeclared behaviour changes and parked bugs (what to capture, where it shows). Measured items (thresholds, detection rates) fix the pass bar, decoys (inputs to reject) and held-out recordings before the test; moving a bar after seeing data makes a new test, and held-out results are reported apart.
- Before a schema change, ask for a backup of the data file: `& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" exec-out run-as <package> cat files/<file> > backup.json`.
- Commit message style: `Sessions: glasses session screens`, `Programs: … and history schema v3`. Use the repo's existing style over skill examples (superpowers plans show `feat: …`; Hyun's repos use `Area: what changed`). Team repos that already use Conventional Commits keep them.

## 6. Dev log

On request, write `DEVLOG_<date>.md`: what changed and why, confirmed facts learned from the code/hardware, files touched (and untouched), design rules added, open items. Take commit counts and names from the reflog, not from memory; correct earlier logs when they were wrong.

## 7. Harvest learnings into skills (every round)

Fill a short **"Skill harvest"** block in the round's status doc / reply as facts turn up (a session can end at a usage limit before the checklist returns), and finalise it after the device checklist comes back, before moving on; "none" is a written result:

- New hardware or SDK facts, each labelled [DEVICE] / [JAR] / [SAMPLE] / [SIMULATED] / [UNVERIFIED] with SDK, firmware, phone, date.
- Facts that **contradict** an existing skill (quote the old line, give the new one). Contradictions win over additions; never leave both — unless two device results genuinely differ by project or configuration, then keep both with date and app and state the safe rule.
- Design rules or workflow habits that saved or cost time.
- Target skill for each item: general SDK facts → `ray-ban-display-app-dev`; screen rules → `glasses-hud-design-rules`; how we work → this skill; event/sprint process → `hackathon-sprint-playbook`; research, pitch and public wording → `evidence-and-claims`; capstone scope and progress → `xr455-capstone`; project paths/state → the project skill.

Harvest edits are fact/rule updates: from superpowers `writing-skills` apply only the frontmatter, description and size rules. Third-party skills installed by tools (e.g. `wearables-ui-toolkit-web`) are gitignored, not harvested.

When Hyun says "update skills" / "스킬 업데이트" (or approves the harvest), write the edited SKILL.md files (whole files, with a dated `## Changelog` line), keep each under ~500 lines, and move long detail into `references/`. Project-specific state never goes into the general skills.

**Where the skills live:** Hyun's private GitHub repo `claude-skills` is the source of truth (one folder per skill + `README.md` + `KNOWLEDGE_LOG.md`). After an update: commit there, re-upload the changed zips to Claude.ai, `git pull` in `~/.claude/skills` for Claude Code. When he says "check skills" / "스킬 확인", compare the installed copies (Claude.ai: `/mnt/skills/...`; this PC's desktop-app cache: `%APPDATA%\Claude\local-agent-mode-sessions\skills-plugin\<id>\<id>\skills\`, shown in Claude Code as `anthropic-skills:<name>`) with the repo version and list what is stale or missing — installed copies are loaded when a chat starts, so changes show up only in a new chat.

## Status doc format (one per round, e.g. `round4-status.md`)

Branch + base commit · Flow (what the user does) · Decisions applied (D1…) · Carried over: N of M addressed (last round's Next, known issues, deferred items) · Device results with labels · Known issues · Code (new / changed files) · Verification (compile, test count from §4, device as separate lines) · Gate edits: none | count + open items · Undeclared behaviour changes: none | list (file, old → new) · Challenges to your direction (when any) · Next · Skill harvest. Where the project keeps a questions or decisions file, point to it instead of copying it.

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

When a session is close to its usage limit or work moves to another person's Claude, write a handoff before stopping: where things are (repo, branches + state + PRs), what is built, what is device-verified vs not, decisions (approved / open, with IDs), what was ruled out (what, why, evidence), rules that must survive a merge, build and test commands, open items with owners. Next session reads `CLAUDE.md`, then the handoff, then the newest status doc. If work restarts from an earlier known-good base, record why and what was carried over.

Name each handoff with its date (plus HHMM if several that day; keep the project's prefix) and record the branch, HEAD hash, pushed yes/no and uncommitted files; updating it within the round is fine. The next session first checks these against git (branch, `git merge-base --is-ancestor <hash> HEAD`, files) and reports a mismatch, e.g. commits never pushed from the other PC, before other work. Open steps end with `[ran: <outcome>]`, `[read]` or `[UNVERIFIED]`; writing steps (generators, APK builds, schema promotions, adb restores) not `[ran]` get a read-only check first. Newest = the date in the name, not file time.

## Lost or long sessions (2026-10-14)

- The desktop app can drop sessions from its sidebar (even from the archive). The transcripts stay on disk: `~/.claude/projects/<folder-slug>/<session-id>.jsonl`, with subagent and workflow logs in the `<session-id>/` folder beside it. Reopen with `claude --resume <session-id>` from the project folder, or read the tail of the `.jsonl` to report where the work stopped.
- To keep a session: copy the raw files to `Desktop\Hyun's Playground\session-archive\<project>\<date>_<id>\` and write a readable `transcript.md` (user and assistant text, one line per tool call). Commit only the readable part, to `docs/session-logs/`, after the project's data-policy check; raw logs hold tool output (ASL Love: dataset video names and participant IDs) and stay off git.
- Auto mode may block a commit or push of content from a sensitive source. Stop and ask Hyun in the chat; don't work around it.

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
- "Merged" means the branch tip is in `main`: check `git log --oneline main..<branch>` before saying so. Hand Hero PR #2 (10/12) merged `auto/2026-10-12-r9` before Z2–Z4 ran, so 11 later commits and every branch built on them stayed out of `main`.
- Check repo visibility (`gh repo view <owner>/<repo> --json visibility`) instead of assuming: `hand-hero` was public until 10/14. Contest submissions may need it public again.
- Unity Android dev builds are signed with a per-PC debug key: an APK built on a new PC cannot install over one from another PC. `adb pull` the app's `files/` first, then uninstall and install (wipes saved settings). A keystore committed to the project avoids it.
- Unattended workflow runs (Hand Hero 2026-10-25): a run started in a turn whose user message is an unrelated question can have a build agent decline ("the request was only a question"); start runs from a turn that asks for the work, or state in the prompt which standing pipeline started it. A desktop-app restart kills the run, and runs cannot be resumed across sessions: restart with a `startAt` argument. `TaskStop` takes the task ID, not the run ID. A session cron watchdog re-reads the whole chat each tick: delete it as soon as nothing is running.
- Usage budget (Hand Hero 2026-10-25): per-task review panels and long balance sweeps used most of the weekly limit. For look slices skip per-task reviews and review once at the end; for balance aim for a difficulty staircase with one quick simulation check and let the headset decide (Hyun: "시뮬레이션돌려서검사하는데에 너무 많은 시간 쓰지마").
- Build a small look slice with an OLD / NEW toggle before a full art pass: Hyun kept only the terrain of the 10/25 slice, which saved building the rest in a direction he did not want.
- Quest recordings are a centre crop of the view: table-top content sits at the bottom edge. Without ffmpeg, read frames by serving the video with HTTP Range support and drawing it into a canvas in the browser pane (crop to the region on the canvas; a hidden pane screenshots small).
- Meta Quest store and dashboard facts (untrusted packages, age group, Data Use Checkup, release channels, headset accounts): `references/quest-dashboard.md`.
- Two integration branches in parallel (hackix26 `team/one-app` and hack-ix-2026 `hyun/ailo-base`, 2026-10-04) left it unclear which build was final. Keep one integration branch and name the demo build by commit/tag.

## Changelog

- 2026-10-26: lessons from Hand Hero 10/25: workflow runs (declines, restarts, TaskStop, watchdog cost), usage budget (reviews, sweeps), look slice first, Quest recording frames; new `references/quest-dashboard.md`.

- 2026-10-18: §2 standing decisions and deny-rule check; §4 silent-run, gate-edit, sibling, deletion and debugging checks; §5 checklist sources and fixed pass bars; §7 harvest during the round; status doc and handoff fields. Ideas adapted from gstack (MIT, Garry Tan).
- 2026-10-14: language follows `user-instructions.md` (English files, `## Summary`; Korean chat only on request); lost-session recovery and archiving; lessons: "merged" check, repo visibility, Unity per-PC signing key.

- 2026-10-06 (b): superpowers leads the process in Claude Code; this skill is the Hyun layer (한국어 요약, worktree note, commit style).
- 2026-10-06: section 0 (working with Hyun), step 7 extended (skills repo, 스킬 확인, new target skills), team-repo and handoff sections, HackIX 2026 lessons.
- 2026-10-05: step 7 (skill harvest), status doc format, HackIX lessons.
