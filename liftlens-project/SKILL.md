---
name: "liftlens-project"
description: "Use when working on LiftLens Mini / Production HUD (Meta Ray-Ban Display, DAT SDK; folder LiftLensMini): paths, architecture, models, schema, conventions and current state."
---

# LiftLens Mini — project context

SCAD XR 455 capstone by Hyun Ha. Two activities on one Real-Time Context Engine: **LiftLens** (sets / reps / rest / sessions / programs) and **Production HUD** (scene·shot·take, countdown, cues with roles). Built on Meta's `DisplayAccess` sample; `applicationId` unchanged (`com.meta.wearable.dat.externalsampleapps.displayaccess`).

Work in Korean. Follow the `plan-approve-build-loop` and `glasses-hud-design-rules` skills. This is the core of the XR 455 Fall PoC: schedule, progress format and reuse map in `xr455-capstone`; public wording in `evidence-and-claims`.

## Where things are

- Project root (Git, branch `master`): `C:\Users\AISTUDIO\Desktop\Hyun's Playground\LiftLensMini`
- Kotlin package (connect this folder separately — it is too deep for the file bridge): `…\LiftLensMini\app\src\main\java\com\meta\wearable\dat\externalsampleapps\displayaccess`
- Stack: Meta Wearables DAT `mwdat-core` / `mwdat-display` 0.9.0, Kotlin 2.2.21, Compose BOM 2026.05.01, minSdk 31. **No new Gradle dependencies** (persistence uses built-in `org.json`).
- Dev logs live in the project folder as `DEVLOG_<date>.md`.

## Architecture (do not reshuffle)

```
ui/ (Compose)               display/                        context/
AppScaffold (nav,           DisplayViewModel = thin action  ContextEngine singleton: activeActivity,
 LaunchedEffect follows      wrappers + glasses rendering +   workout: WorkoutController,
 activeActivity)             observeTimers(); GlassesScreen   production: ProductionController;
WorkoutScreen, HistoryScreen enum tracks what the glasses    own CoroutineScope for tickers.
ProductionScreen, CueEditor  show. Session/display lifecycle WorkoutHistoryStore observes the
ActivitySwitcher             from the sample is untouched.   controller and writes JSON.
```

Rules: controllers own rules/state (`StateFlow`) and know nothing about phone or glasses; the ViewModel mutates then renders; Compose screens take values + callbacks only. `WorkoutState.kt` still lives in `display/` (move only via Android Studio Refactor → Move; file tools cannot move files).

## Workout model (as of schema v3)

- `Exercise { DEADLIFT 225, SQUAT 185, BENCH_PRESS 135 }` lb defaults.
- `WorkoutProgram(id, name, exercises: List<ProgramExercise(exercise, sets, targetReps, id)>, isBuiltIn)`; `BuiltInPrograms.all` (Full Body A, Push · Pull, Bench Day). Editing a built-in saves a user copy with the same id; deleting the copy restores the built-in.
- `WorkoutSession(id, startedAtMs, exercise = plan.first, status {ACTIVE, COMPLETED, ENDED_EARLY}, endedAtMs, plan: List<SessionExercise(id, exercise, sets, targetReps)>, exerciseIndex, programId?, programName?)`. Every session has a plan; no program = one-line plan. `effectivePlan` synthesizes a 4×10 line for v2 sessions with an empty plan.
- `LoggedSet(exercise, setNumber, weightLb, reps, loggedAtMs, id, sessionId?, sessionExerciseId?)`.
- `WorkoutState`: `exercise/currentSet/totalSets/targetReps` mirror the current plan line (controller's `applyExercise`); `isComplete = currentSet > totalSets` means *exercise* complete; `isWorkoutComplete = isComplete && isLastExercise`; `canStartNextExercise = hasActiveSession && isComplete && !isLastExercise` (the EXERCISE_COMPLETE waiting state; no rest runs there); `pendingResume` (memory only); `selectedProgramId`.
- Controller rules: `startWorkout()` snapshots the program (fresh line ids); `logSet()` attaches session + line id, finalizes COMPLETED in the same state update on the last set of the last exercise, otherwise parks or starts rest; `startNextExercise()`; `selectExercise` refused during program sessions (ad-hoc session: replaces its one plan line, S1 behaviour); `resetWorkout` resets the current exercise only; `restoreHistory` keeps at most one resumable ACTIVE session, closes others and anything idle > 6 h (`STALE_SESSION_MS`) as ENDED_EARLY — the only silent finalization, keep it documented.

## Persistence

`filesDir/liftlens_history.json`, schema v3 `{version, sessions[], sets[], programs[]}` (user programs only). Reads v1/v2/v3; first read of an older version keeps `liftlens_history.v<n>.bak.json`; unparseable or unknown-version files are renamed `liftlens_history.corrupt-<epoch>.json`, never overwritten. Whole document written from one snapshot (temp file + rename). Never modify the on-device file from the desktop; user backs it up with `adb shell run-as <appId> cat files/liftlens_history.json`.

## Glasses screens (LiftLens)

`GlassesScreen { WORKOUT, REST, REST_SETTINGS, EXERCISE, ACTIVITY_CHOOSER, PRODUCTION, REPS, WEIGHT, HISTORY, SESSION, END_CONFIRM, RESUME }`. Active main: row 1 `Log set · N / Reps / Weight`, row 2 `Exercise / Rest / Session`. No-session: `Start workout / Weight` + `Exercise / Rest / History`. Rest: single `Start set`. Icons must come from the SDK `IconName` enum verified from the APK dump.

## State of play and next steps

Done and committed: S1 sessions, S2 round A (programs models + controller + schema v3, stale-session cleanup). Next: **round B** = S2.3 phone (program picker, editor, Start Next Exercise, exercise-complete banner, History header `programName ?: exercise`) + S2.4 glasses (`PROGRAM` picker screen, `EXERCISE_DONE` screen with `Start next exercise` first, `Exercise 2 / 3` line), then S2.5 regression + devlog. Later: session summaries/calories, persist Production cue lists with the same store, camera rep counting (blocked on CameraAccess preview issue).

Capstone-scheduled foundation work not started per records (2026-10-06, confirm in the reflog): migrate `mwdat` 0.9.0 → 1.0.0 with a full regression, measure `ActionRole.PRIMARY`, a Mock Display harness. For the migration, reuse the device-verified DAT 1.0 patterns recorded in `ray-ban-display-app-dev` (one session owner, attach retry, reconnect redraw, wear state, camera release order) rather than copying another project's files. After migrating, re-check the 1 s rest screen for SDK `text` flicker, and retry camera rep counting (the 0.9 CameraAccess preview problem may be gone — Fridge streams fine on 1.0).

## Verification that has worked here

SDK API from the APK's DEX (androguard) since Maven Central is blocked; Kotlin 2.2.21 CLI compiler from GitHub releases + minimal stubs (android.util.Log, SystemClock, Context, coroutines/flow) + org.json built from GitHub source to type-check and run state-machine simulations (`sim/Sim.kt`, 84 checks) before the user builds on device.

## Changelog

- 2026-10-06: linked to `xr455-capstone`; capstone foundation work (1.0 migration, PRIMARY, Mock harness) and migration notes added.
