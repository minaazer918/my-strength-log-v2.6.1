# My Strength Log: Ideas and Backlog

Place this file in the project root, next to `CHANGELOG.md`.

Last updated: 2026-10-27 (#2 done in 2.0.0; #17 timed exercises done in 2.4.0; added #9 to #23)

## How to use this file

- Add new ideas at the bottom of the matching section, with the next free ID.
- Status values: **Idea** → **Discussed** → **Approved** → **Done** (add the version, e.g. `Done in 1.2.0`).
- An idea is not implemented until its status is **Approved** and a release is requested.
- Every implemented change still follows the project rules (minimal change, `CHANGELOG.md` entry, SemVer, tests, completion report).
- Sections: Features, Fixes, Improvements. IDs are never reused.

## Features

### #1 Rest timer on the main screen
- **Status:** Idea
- **Suggested version type:** MINOR
- Move the timer to the main screen.
- Let the user enter the rest time.
- Play a sound and vibrate when the timer ends.
- **Notes for discussion:**
  - Sound needs a prior tap in browsers.
  - Vibration works on Android Chrome but not on iOS Safari (use sound plus a visual flash there).
  - Base the timer on an end timestamp so it stays accurate if the screen locks.
  - Open questions: remember the rest time between sessions? Auto-start after saving a set?

### #2 Custom exercises
- **Status:** Done in 2.0.0 (follow-up releases 2.1.0, 2.2.0, 2.2.1, 2.3.0)
- **Suggested version type:** MINOR (needs a data-safety plan first)
- Let the user add and enter their own exercises, not only Squat and Deadlift.
- **Notes for discussion:**
  - Keep Squat and Deadlift as built-in defaults and add a separate exercise list so existing records stay untouched.
  - If a database version upgrade is needed, plan a migration and a backup/export step first.
  - Open questions: per-exercise sets/reps (e.g. 4x8)? History kept per exercise?
- **Outcome (as built):**
  - Clean slate for every user: no built-in exercises. Squat and Deadlift are ordinary exercises; existing data was carried over automatically.
  - Add exercises by picking a suggestion or typing a name, with a movement type. The exercise list is stored in localStorage (not IndexedDB), and the suggestion list is in `exercise-presets.js`.
  - Hide keeps all history. Delete removes the exercise and its workouts after typing `DELETE`, with an optional backup download.
  - Bodyweight sets (weight 0) are shown as BW. The Progression table shows weight×reps.
- **Not built:** per-exercise sets and reps (every exercise starts at 4×8; sets can still be added or removed in the workout form). See #16.
- **Releases:** 2.0.0 (custom exercises), 2.1.0 (bodyweight fix, Delete), 2.2.0 (reps in Progression), 2.2.1 (Progression table fit), 2.3.0 (typed DELETE confirmation).

### #3 Per-set effort tracking (RIR and tags)
- **Status:** Idea
- **Suggested version type:** MINOR
- Track RIR (reps in reserve) for each set.
- Keep per-set feel tags: easy / medium / hard, fast / slow, form issues.
- **Notes for discussion:**
  - RIR and easy/medium/hard overlap. Keep both, or make RIR the main field with a label shortcut?
  - Add as new optional fields on each set. Old sets show them blank.
  - Use one-tap buttons so saving a set stays fast. All fields optional.

### #4 Cloud backup and sync
- **Status:** Idea
- **Suggested version type:** Split into steps (see below)
- Let users connect their preferred cloud service: Google Drive, Microsoft OneDrive, and possibly other storage providers.
- Requirements:
  - Secure authorization and permission management.
  - User-selected backup destination.
  - Upload, download, and restore.
  - Conflict handling when workout data changes on multiple devices.
  - Clear separation between backup and full two-way sync.
- **Suggested split:**
  - **#4a Manual export/import file (JSON).** No accounts or servers. MINOR.
  - **#4b Cloud backup/restore** to a user-chosen provider (upload, download, restore only). MINOR.
  - **#4c Full two-way sync** with conflict handling. Possibly MAJOR.
- **Notes for discussion:**
  - A PWA has no server of its own. Google Drive and OneDrive need OAuth sign-in and app registration with each provider, HTTPS hosting, and careful token storage.
  - Restore can overwrite workouts: require a confirmation step and an automatic local backup before any restore.
  - Conflicts (4c): "newest wins" vs. merging by set. Merging is safer but needs unique IDs and timestamps on every set.

### #5 Workout data analysis and graphs
- **Status:** Idea
- **Suggested version type:** MINOR (suggest splitting)
- Visualize weight, repetitions, sets, and training volume over time.
- Show progression and regression for each exercise.
- Track estimated one-repetition maximum (1RM).
- Compare workout sessions and identify trends.
- Summarize consistency, personal records, and training history.
- **Suggested split:** #5a per-exercise charts and PRs; #5b consistency summaries and session comparison.
- **Notes for discussion:**
  - Read-only on existing data, so low risk to records.
  - Works best after #2 and #3 (more exercises and effort data).
  - Estimated 1RM is a formula (e.g. Epley), most reliable for sets of about 10 reps or fewer. Label it "estimated".
  - Charts need a drawing method. A library adds a dependency, which conflicts with the no-new-dependencies rule, so decide explicitly or draw simple charts by hand.

### #6 AI coaching (CSCS and ACSM informed)
- **Status:** Idea
- **Suggested version type:** MINOR, possibly MAJOR for an AI-service version
- Develop an AI coaching capability informed by CSCS principles and American College of Sports Medicine (ACSM) exercise guidelines.
- Analyze workout history and training performance.
- Recommend progressive overload and appropriate weight increases.
- Suggest sets, repetitions, rest intervals, and training frequency.
- Identify plateaus, excessive fatigue, and potential under-recovery.
- Adapt recommendations to training goals, experience, and available equipment.
- Explain recommendations and their supporting rationale.
- **Important:** Recommendations should be evidence-informed, distinguish established guidelines from estimates, and account for safety and individual limitations.
- **Notes for discussion:**
  - **Rules-based coach (offline) first:** fixed, predictable, private, testable logic (e.g. progress weight when all reps are hit at a low RIR).
  - **True AI coach later:** needs an AI service, which means a server or API key, cost, privacy decisions, and sending workout data off the device. A PWA cannot safely hold an API key itself. Comes after cloud work (#4).
  - Tag each recommendation as "Guideline-based" or "Estimate".
  - Show a standing "not medical advice" note and a "stop and see a professional" prompt for pain or injury.
  - Fatigue and under-recovery flags need logged data (RIR, effort, possibly soreness), so #3 is a prerequisite.
  - With little history, say there is not enough data rather than guess.

### #7 Security and privacy
- **Status:** Idea
- **Suggested version type:** Review checklist plus small MINOR items (e.g. app PIN lock)
- Consider securing the app and protecting user privacy.
- **Notes for discussion:**
  - Current baseline is good: data stays on the device, with no accounts and no server.
  - Hosting: HTTPS only, plus security headers such as a Content Security Policy.
  - Code hygiene: no third-party scripts, and safe handling of anything the user types (exercise names, notes) so it cannot inject code.
  - Data protection: optionally encrypt exported files and cloud backups; optional app PIN or lock screen for shared phones (treat as a small separate feature).
  - Privacy: a short privacy policy stating what is stored and where. If selling to people in regions such as the EU, privacy law (e.g. GDPR) applies.
  - Cloud (#4) and AI coaching (#6) send data off the device, so they need consent screens and clear explanations.
  - Do this review before cloud features and before selling.

### #8 Locking the app for sale to individuals
- **Status:** Idea
- **Suggested version type:** MINOR or MAJOR depending on the approach
- Possibly lock the app so it can be sold to individuals (e.g. locked by phone number, a locking/license code, etc.).
- **Notes for discussion:**
  - A PWA runs on the user's own device, so any lock inside the app can eventually be bypassed. It deters casual sharing, not determined copying.
  - Phone-number locking is a poor fit: a PWA cannot read the phone number. It would need SMS verification (paid service, collects phone numbers, privacy burden). Email or a license code is simpler.
  - Options, simplest to strongest:
    1. License key sold through a platform (e.g. Gumroad, Lemon Squeezy) and entered once in the app. Easy, but keys can be shared.
    2. License key checked by a small server, limited to a number of devices. Stronger, but needs a server and upkeep.
    3. App store distribution (e.g. Google Play) wrapping the PWA. The store handles payment and licensing, but adds fees and a larger rewrite.
  - Open questions: one-time price or subscription? Free trial? Devices per buyer? How much copying is acceptable?
  - Depends on hosting and payment decisions, so place late in the build order. Needs #7 first.

### #11 Merge duplicate exercises
- **Status:** Idea
- **Suggested version type:** MINOR
- Let the user say that two exercises are the same (for example "BB press" and "Bench Press") so their history combines.
- **Notes for discussion:**
  - Safe way: record the merge as a link ("B counts as A") and show combined history, without rewriting any workout. It can be undone.
  - Most useful once graphs (#5) exist.

### #13 Recently deleted (undo for exercise deletion)
- **Status:** Idea
- **Suggested version type:** MINOR (needs a data-safety plan first)
- Keep a deleted exercise and its workouts in a holding area for a while so deletion can be undone.
- **Notes for discussion:**
  - Delete is currently permanent (typed `DELETE` confirmation plus an optional backup download). Undo would be the real safety net.
  - Changes how data is stored, so plan it before building. Related to backups (#4).

### #16 Per-exercise default sets and reps
- **Status:** Idea
- **Suggested version type:** MINOR
- Let each exercise have its own default number of sets and reps instead of 4×8 for all.
- **Notes for discussion:**
  - Relates to training type and periodization (strength vs hypertrophy). Fits with effort tracking (#3) and coaching (#6).
  - Open questions: a default per exercise only, or per workout? Show the plan in Progression?

### #17 Timed exercises (Reps or Time)
- **Status:** Done in 2.4.0
- **Suggested version type:** MINOR
- Exercises like planks and wall sits are measured in seconds. Each exercise is measured in Reps or Time, chosen when it is added (suggestions such as Plank come as Time). Timed sets store `seconds` (reps 0); weight 0 is bodyweight and a weight can still be added.
- **Not built:** a hold stopwatch, mm:ss input, a way to change the type of an exercise that already has workouts, and a progression suggestion for timed exercises.

### #18 Distance exercises
- **Status:** Idea
- **Suggested version type:** MINOR
- Farmer's walk, sled push, sprints: measure distance, often together with weight.
- **Notes for discussion:** next measurement type after Time, using its own optional field (e.g. meters) so existing workouts need no conversion. Needs a decision on the unit (metric or imperial) and whether it follows the lb/kg setting.

### #22 Jump or box height
- **Status:** Idea
- **Suggested version type:** MINOR
- Box jumps and other plyometrics. Relevant if power training matters. Lower priority.

### #23 Body measurements
- **Status:** Idea
- **Suggested version type:** MINOR
- Body weight, body fat, soreness or sleep. Not per set: a separate feature, useful for coaching (#6).

## Fixes

_None recorded yet._

## Improvements

### #9 Suggest-an-exercise link
- **Status:** Idea
- **Suggested version type:** MINOR
- Give users a way to ask for exercises missing from the suggestion list.
- **Notes for discussion:**
  - Google Form link preferred: no email address exposed, so less spam risk. A `mailto:` link to a dedicated address is the alternative. Needs your form URL or address.
  - Users can already type any exercise, so this only feeds the suggestion list. Editing the list in `exercise-presets.js` is a small PATCH release.

### #10 Similar-name warning when adding an exercise
- **Status:** Idea
- **Suggested version type:** MINOR
- Warn when a new name is close to an existing one ("Bench" vs "Bench Press"), with "Use existing" and "Add anyway".
- **Notes for discussion:** exact duplicates (ignoring capitals and spaces) are already blocked. This would catch near-misses and keep graphs (#5) accurate.

### #12 Permanent preset IDs and use of movement type
- **Status:** Idea
- **Suggested version type:** MINOR
- Exercises made from the suggestion list already have fixed IDs (for example `bench-press`) and every exercise has a movement type, but nothing uses them yet.
- **Notes for discussion:**
  - Needed for reliable graphs (#5) and coaching (#6). Typed custom exercises have generated IDs, so analysis would treat them as the user's own named exercise.
  - Preset IDs must never be changed or reused.

### #14 Hint that tapping a workout in History opens the edit form
- **Status:** Idea
- **Suggested version type:** PATCH or MINOR
- Editing and deleting a single workout already exist but are not obvious. Add a short hint on the History screen.

### #15 Progression suggestion for bodyweight and lighter exercises
- **Status:** Idea
- **Suggested version type:** MINOR
- The "+10 lb / +5 kg" suggestion is tuned for heavy lifts and is hidden when the top weight is BW.
- **Notes for discussion:** options are a per-exercise weight step, or a rep-based suggestion for bodyweight exercises. Fits with coaching (#6).

### #19 Per-side or per-hand flag
- **Status:** Idea
- **Suggested version type:** MINOR
- Mark a set as per side or per hand (lunges, single-arm rows, dumbbells) so volume is not misread.

### #20 Assisted weight label
- **Status:** Idea
- **Suggested version type:** MINOR
- Today a weight on a bodyweight exercise means added weight. Assisted exercises (assisted pull-up machine) need a way to show the weight is subtracted.

### #21 Tempo
- **Status:** Idea
- **Suggested version type:** MINOR
- Record tempo (for example 3-1-1) as a note or short text field on a set. Low priority.

## Suggested build order

Based on dependencies and risk (not yet approved):

1. #4a Manual export/import (safety net before data-affecting changes; basic JSON export/import already exists in the app, so this step is about extending it)
2. #1 Rest timer
3. #3 RIR and set tags
4. ~~#2 Custom exercises~~ (done in 2.0.0)
5. #7 Security and privacy review (before cloud and before selling)
6. #5 Analysis and graphs
7. #6 AI coaching (rules-based first)
8. #4b Cloud backup, then #4c two-way sync
9. #8 Licensing and locking for sale

## Workflow reminders

- One item per chat, started inside this Project, with the latest project ZIP attached.
- Ask for a plan first (files to touch, risk to workout data), then approve before any code.
- Complex items (#2, #4) get a plan-only chat before the build chat.
