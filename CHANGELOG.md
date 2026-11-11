# Changelog

## 2.6.1 - 2026-11-10
### Changed
- Rest-timer completion now plays two groups of three alternating, longer tones with increased Web Audio gain, making the foreground alert more noticeable. Actual loudness remains subject to Android media volume and audio focus.
- Vibration now uses two separated pulse groups when the browser and Android permit vibration.
- The completion bar now remains visible with `REST COMPLETE – NEXT SET` until Reset or a new timer is started; it no longer disappears after three seconds.
- The timer continues to use its absolute end timestamp, so the remaining time is recalculated against wall-clock time when JavaScript execution resumes after background suspension.
### Files
- app.js (completion alerts, persistent completion state and timer-bar message), index.html (footer `v2.6.1`), sw.js (cache `strength-log-v14`), CHANGELOG.md.
### Test results
Verified:
- JavaScript syntax validation and targeted source assertions for repeated alert scheduling, vibration pattern, persistent completion state, timestamp-based countdown, footer version and service-worker cache version.
- Existing workout storage and backup code were not modified.
Not verified:
- Physical Samsung/Chrome sound loudness, vibration while backgrounded, or exact alert timing with the screen locked. Android/Chrome may suspend page execution and restrict background audio/vibration; this browser-only PWA cannot guarantee an exact screen-off alert.
- A scheduled system notification was not added: the current offline PWA has no push server or native Android scheduling facility to reliably deliver a notification while its page is suspended.
- Full interactive regression and installed service-worker update on the user's device.

## 2.6.0 - 2026-11-10
### Added
- A persistent rest-timer bar appears across app screens while the timer is running, with Pause/Resume and Reset controls. The bar remains available while paused so the timer can be resumed from any screen. The full timer remains on the workout form.
- Timer completion now provides a short two-tone beep when Sound is enabled, vibration when Vibrate is enabled and supported, and a visible "Go!" completion state.
- Added independent Sound and Vibrate switches. Both default to on and are remembered locally on the device.
- Added custom rest duration in seconds (1–7200); the last selected duration is remembered. Existing 2:00, 2:30 and 3:00 presets remain.
- The timer bar is positioned below the workout-delete undo banner when both are visible. Timer preferences do not affect workout records or JSON backups.
### Files
- app.js (timer controls, persistence, cross-screen bar and alerts), style.css (timer bar and switches), index.html (footer `v2.6.0`), sw.js (cache `strength-log-v13`), CHANGELOG.md. Existing workout storage and backup formats are unchanged.
### Test results
Verified:
- Source-level checks and JavaScript syntax validation were run for app.js and sw.js.
- Static checks confirm the independent preference keys, custom duration bounds, existing preset buttons, cross-screen timer bar controls, separate alert toggles, and version/cache updates.
Not verified:
- Actual sound output and vibration on a physical Android or iPhone device.
- Timer behavior while the phone is locked or the browser is backgrounded; mobile browsers may delay sound and vibration.
- Installed-app service-worker update from cache v12 to v13 and visual layout on the user's Samsung phone, including dark theme and gesture navigation.
- Full interactive browser regression suite; no browser automation suite was included in the uploaded ZIP.

## 2.5.1 - 2026-10-31
### Changed
- The undo window after deleting a workout is now 30 seconds (was 10). The UNDO banner stays for 30 seconds, and the delete confirmation now says "about 30 seconds". Nothing else changes.
### Files
- app.js (timer and confirmation wording), index.html (footer `v2.5.1`), sw.js (cache `strength-log-v12`), CHANGELOG.md. All other files are unchanged from 2.5.0.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked):
- `node --check` passes for app.js and sw.js.
- The 12 delete and undo checks from 2.5.0 pass with the new window: the confirmation says "about 30 seconds", the banner disappeared on its own after about 30.0 seconds and the workout stayed deleted, UNDO restores the identical workout (tested right after deleting), a second deletion replaces the banner, and a page reload or deleting an exercise clears it. Footer shows `v2.5.1 · © 2026 KeroCare LLC`. No page errors were logged.
- The 26 timed-exercise checks from 2.4.0 still pass.

Not verified:
- Clicking UNDO late in the window (for example after 25 seconds) was not tested separately; the banner was confirmed to stay for the full 30 seconds.
- Appearance on the real Samsung phone, and behaviour when the phone is locked or the app is switched away during the 30 seconds.
- The service worker update path (v11 to v12) on an installed app.


## 2.5.0 - 2026-10-29
### Changed
- Deleting a single workout (History > open a workout > DELETE WORKOUT): the confirmation now names the exercise, the date and the number of sets, for example "Delete the Squat workout from Oct 1, 2026 (3 sets)?", and says UNDO is available for about 10 seconds.
### Added
- Undo banner after a workout is deleted: "Squat workout deleted" with an UNDO button for 10 seconds. UNDO puts back the identical workout (same id, date, sets, comment and timestamps). Only the latest deletion can be undone, the banner stays visible when you move between screens, and it disappears after 10 seconds, when you delete an exercise, or when the page is reloaded or the app is closed. After that the deletion is permanent.
### Files
- app.js (confirmation text, undo banner), style.css (banner styling), index.html (footer `v2.5.0`), sw.js (cache `strength-log-v11`), CHANGELOG.md. exercise-presets.js, README.md, IDEAS.md, manifest.json, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked); 12 of 12 delete/undo checks passed:
- The confirmation text names the exercise, date and sets and mentions UNDO. Cancelling deletes nothing and shows no banner. Confirming deletes only the opened workout and shows the banner.
- UNDO restores a workout that is identical to the original (all fields, 3 sets, comment) and removes the banner. The banner disappeared by itself after about 10.4 seconds and the workout stayed deleted.
- A second deletion replaces the first banner, and UNDO then restores only the latest one. The banner stays visible after navigating to Home. After a page reload the banner is gone and the deletion stays. Deleting an exercise clears the banner.
- Footer shows `v2.5.0 · © 2026 KeroCare LLC`. No page errors were logged. A screenshot of the banner was checked.
- Re-run of earlier tests: the 26 timed-exercise checks, the delete-and-restore test (workouts identical after restore) and the Progression table fit at 390 px all pass. The exercise-delete test passes except its check of the old footer text (`v2.3.0`).

Not verified:
- Appearance and tap size of the banner on the real Samsung phone, in dark theme and with the phone's gesture bar.
- The service worker update path (v10 to v11) on an installed app.
- Undo after switching apps or locking the phone for more than 10 seconds (the banner would have expired).


## 2.4.1 - 2026-10-28
### Changed
- The footer now shows the owner under the version, on the same line: `v2.4.1 · © 2026 KeroCare LLC`. Display only: no app logic or data changes.
- No license text was added; ownership of the app and the choice of license are for KeroCare LLC and its legal adviser to decide.
### Files
- index.html (footer text), sw.js (cache `strength-log-v10` so installed apps load it), CHANGELOG.md. All other files are unchanged from 2.4.0.

### Test results
Verified (headless Chromium, project served over localhost, service workers blocked):
- The footer shows `v2.4.1 · © 2026 KeroCare LLC` on Home, Add exercise, Settings and History at 360 px wide; a screenshot shows it on one line, small and muted, and the page never scrolls sideways.
- Re-ran the 26 timed-exercise checks from 2.4.0 (workouts, history, import/export, delete, upgrade); all 26 pass with the new footer text. No page errors were logged.

Not verified:
- Appearance on the real Samsung phone and in dark theme.
- The service worker update path (v9 to v10) on an installed app.
- The older delete and restore tests were not re-run for this release; nothing in that code changed.


## 2.4.0 - 2026-10-28
### Added
- Timed exercises. When adding an exercise you choose "Measured in": Reps (default) or Time (seconds). Suggestions come with their type: Plank, Side Plank, Wall Sit and Dead Hang are timed; the others stay on reps.
- For a timed exercise the workout form shows "Sec" instead of "Reps" (default 30). Weight is still available (0 = bodyweight), so a weighted plank works. Time must be a whole number of seconds from 1 to 7200.
- Timed sets are shown as `BW × 45 s` in History and workout detail, and `BW×45s` in the Progression table and the saved screen. No weight-based "next progression" suggestion is shown for timed exercises.
- Timed sets are stored with a new optional `seconds` field (and reps 0). Existing workouts are not changed, and sets of reps exercises have no `seconds` field.
- An exercise that already exists in your list with no workouts yet (for example a Plank added earlier) takes its suggestion's type. An exercise that already has workouts keeps its type.
- JSON export is now file version 3: the exercise list includes `kind` for timed exercises and timed sets include `seconds`. Import accepts older exports and rebuilds the type from the workouts if the exercise list is missing. Timed sets with invalid seconds are skipped on import.
- CSV export has a new `seconds` column after `reps`.
### Documentation (bundled from earlier requests)
- README.md: new section "Adding or changing suggested exercises", plus the data model now describes `seconds` and `kind`.
- IDEAS.md: #2 marked done in 2.0.0; new ideas #9 to #16 added earlier, now #17 (timed exercises, done in 2.4.0) and #18 to #23 (distance, jump or box height, body measurements, per-side flag, assisted weight, tempo).
### Files
- app.js, exercise-presets.js (type column and the four timed suggestions), index.html (footer `v2.4.0`), sw.js (cache `strength-log-v9`), README.md, IDEAS.md, CHANGELOG.md. style.css, manifest.json, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked); 26 of 26 timed-exercise checks passed:
- `node --check` passes for app.js, exercise-presets.js and sw.js.
- Upgrade: existing reps workouts are byte-identical after loading 2.4.0; an existing Plank with no workouts becomes timed; Squat stays reps.
- Timed workflow: the form shows "Sec" with default 30; a bodyweight plank saves and is stored as seconds with reps 0; a trailing blank set is skipped; History, the saved screen and the Progression table show the seconds format; a weighted timed set shows `25 × 30 s`; blank, 0, text, decimal and over-7200 times are rejected; editing a timed workout opens with its seconds and saves a change.
- Add exercise: choosing a timed or reps suggestion sets "Measured in" and the movement type; a typed exercise can be set to Time and defaults to Reps.
- Reps exercises are unchanged: the form still says "Reps" with default 8 and sets have no `seconds` field.
- Backup: JSON export is version 3 with `seconds` and `kind`; it round-trips through import; an older export imports; a timed workout without an exercise list infers the Time type; invalid seconds are skipped; the CSV has the `seconds` column; deleting a timed exercise (typed DELETE) removes it and its workouts. No page errors were logged. Screenshot of the timed form checked.
- Re-run of earlier tests: the delete-and-restore test (workouts identical after restore) and the Progression table fit (4 sets fit at 390 px for lb, kg and bodyweight) still pass; the delete test's only failure is its hard-coded old footer version (`v2.3.0`).

Not verified:
- Appearance and the timed flow on the real Samsung phone, in dark theme, and the on-screen number keyboard.
- Progression table with timed sets in kg decimals or long times (for example 120s fits in tests only with ordinary values).
- The service worker update path (v8 to v9) on an installed app.
- The upgrade of an existing real data set on your phone (tested only with generated data).

Not built: a hold stopwatch, mm:ss input, changing the type of an exercise that already has workouts (delete and re-add it if it has none), a conversion of planks previously logged as reps, and timed-exercise progression suggestions.


## 2.3.0 - 2026-10-27
### Changed
- Deleting an exercise that has workouts now opens a confirmation screen instead of the browser OK/Cancel box. The screen shows the exercise name, how many workouts will be deleted and their date range, and says it cannot be undone. The Delete button stays disabled until `DELETE` is typed (any capitals, spaces ignored); the back link cancels.
- The screen offers "Download backup first (recommended)", which downloads the same JSON backup as Settings > Export. It is optional and cannot confirm the file was saved.
- Deleting an exercise with no workouts still uses the simple OK/Cancel confirmation. Hide is unchanged and keeps all history.
### Files
- app.js (new confirmation screen and route; the JSON export is now a shared function used by Settings and the new screen), style.css (one rule so a disabled button looks disabled), index.html (footer `v2.3.0`), sw.js (cache `strength-log-v8`), CHANGELOG.md. exercise-presets.js, manifest.json, README.md, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked):
- `node --check` passes for app.js and sw.js.
- Delete with history opens the new screen with no browser dialog; the screen shows the count (3 workouts), the date range and "cannot be undone"; the button is disabled at first and for wrong words (`delet`, `DELETEX`, `del ete`, blank) and enabled for `delete` with spaces; clearing the box disables it again.
- Cancelling (the Settings back link) and downloading the backup delete nothing. The backup file contains the exercise's workouts.
- Typing DELETE removes the exercise and only its own workouts; other exercises' workouts are unchanged, and it stays deleted after a reload. Direct links to a deleted or unknown exercise return to Settings.
- Exercise with no workouts: simple confirmation (dismissing keeps it, accepting deletes it). Hide still keeps history. Adding an exercise and saving a bodyweight workout still work. No page errors were logged. A screenshot of the new screen was checked.
- Restore: after downloading the backup and deleting an exercise, importing that backup brought back every workout and the exercise (Home shows it again). In the test data the stored set `workoutId` values were normalised by the import to each workout's own id, which matches how the app saves them.

Not verified:
- Appearance on the real Samsung phone, in dark theme, and the on-screen keyboard behaviour on the phone.
- A real download on the phone (file location and browser prompts).
- The earlier broad regression checks (upgrade from v1.1.0 data, fresh install, export/import round trips, History and Progression displays) were not re-run in full for this release because the test scripts were lost when the working environment reset.
- The service worker update path (v7 to v8) on an installed app.


## 2.2.1 - 2026-10-27
### Changed
- Progression table: the date now shows in a compact form (`Oct 5 ’26`) and the table uses a slightly smaller font and tighter cell padding, so a workout with 4 sets (weight×reps) fits on a phone screen without sideways scrolling. This follows 2.2.0, where the table needed a short swipe to see the last set.
- Only the Progression table changes; History, the workout screens and stored dates keep the full date format.
### Files
- app.js (new `fmtShort` date helper, used only in the Progression table), style.css (two rules scoped to `.tw`, the Progression table container), index.html (footer `v2.2.1`), sw.js (cache `strength-log-v7`), CHANGELOG.md. exercise-presets.js, manifest.json, README.md, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked):
- `node --check` passes for app.js and sw.js.
- Fit: with 4 sets, the table content width equals the available width (356 px, no sideways scroll) for ordinary values in lb (e.g. 225×8, 315×5, 135×12, 405×3, a December date), in kg (100×8, 102.5×10 style values entered in kg) and for bodyweight (BW×15). The page itself never scrolls sideways.
- Screenshot of the lb table checked: all four sets are visible, the text is smaller but legible, and highlighting is unchanged.
- Worst case (kg values converted from lb with decimals such as 225.97×10, or 3-digit reps such as ×100) is still wider than the screen (about 397 px) and scrolls sideways inside the table box.
- History still shows full dates (e.g. `Oct 1, 2026`), the footer shows `v2.2.1`, and no page errors were logged.

Not verified:
- Appearance on the real Samsung phone and in dark theme, and on phones narrower than 390 px.
- The earlier regression checks (upgrade from v1.1.0 data, fresh install, export/import, delete exercise, bodyweight save) were not re-run for this release because the test scripts were lost when the working environment reset; this release changes only the table display, a date helper and two CSS rules.
- The service worker update path (v6 to v7) on an installed app.


## 2.2.0 - 2026-10-27
### Changed
- The Progression table now shows reps with each weight (for example `225×8`, `BW×12`), so a change in reps at the same weight is visible. The saved screen's "Previous" and "Today" lines show weight×reps the same way, and the table note explains the format.
- Display only: stored workouts are not touched.
### Files
- app.js, index.html (footer `v2.2.0`), sw.js (cache `strength-log-v6`), CHANGELOG.md. style.css, exercise-presets.js, manifest.json, README.md, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked):
- `node --check` passes for app.js and sw.js; the diff against 2.1.0 contains only the intended display lines plus the cache name.
- Progression table shows weight×reps in lb and kg, and `BW×12` for bodyweight sets. After saving a squat workout the saved screen shows "Previous: 245×5 / 245×5 / 245×4 / 225×12" and "Today: 250×5". History still shows "225 × 8". No page errors were logged.
- Layout: the table sits in its own scroll container and the page itself never scrolls sideways (page width 390). With 4 sets, the table is wider than the phone screen: about 439 px of content in a 356 px container using ordinary values (225×8, 315×5, 135×12), so the last set needs a short swipe sideways. Worst-case values (kg decimals, 3-digit reps) are wider still. In a test, a smaller font and cell padding plus a shorter date (no year, or a two-digit year) made 4 sets fit; this is not included in 2.2.0.

Not verified:
- Appearance on the real Samsung phone and in dark theme.
- The earlier regression checks (upgrade from v1.1.0 data, fresh install, export/import, delete exercise, bodyweight) were not re-run for this release because the test scripts were lost when the working environment reset; this release does not change any of that code.
- The service worker update path (v5 to v6) on an installed app.


## 2.1.0 - 2026-10-26
### Fixed
- Bodyweight exercises (e.g. Push-Up) can now be saved: a weight of 0 is accepted. A blank or non-numeric weight is still rejected.
- Import accepted only weights above 0, which would have dropped bodyweight sets from a backup; it now accepts 0 (a missing or empty weight is still skipped).
### Added
- Weight 0 is shown as "BW" on the saved screen, History, workout detail and the Progression table. The weight-based "next progression" suggestion is not shown when the top weight is BW.
- Settings > My exercises: a Delete button next to Hide. Delete permanently removes the exercise and all of its workouts, after a confirmation that names the exercise and the number of workouts (cannot be undone). Hide still hides the exercise from Home and keeps all history.
### Files
- app.js, index.html (footer `v2.1.0`), sw.js (cache `strength-log-v5`), README.md (one line on BW), CHANGELOG.md. exercise-presets.js, style.css, manifest.json, the icons and the database structure are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked):
- `node --check` passes for app.js and sw.js.
- Bodyweight: weight 0 saves; the saved screen, History, Progression table and workout detail show BW; no "next progression" suggestion for BW; a blank or non-numeric weight is still rejected; an existing weight-0 workout reopens showing 0 and re-saves; a JSON export containing a weight-0 set imports back with all workouts.
- Delete: dismissing the confirmation deletes nothing; confirming removes the exercise and only its workouts (the confirmation text names the number of workouts and says it cannot be undone); other exercises' workouts are unchanged; the deleted exercise stays gone after reload and is gone from Home; deleting an exercise with no workouts uses a simple confirmation.
- Hide still removes the exercise from Home and keeps its history.
- Regression: the 24 checks from 2.0.0 (upgrade from v1.1.0 data, fresh install, export/import, fallback) all pass, including the footer showing `v2.1.0`. No page errors were logged.
- style.css, exercise-presets.js, manifest.json and the icons are unchanged from 2.0.0.

Not verified:
- Appearance and the delete/BW flows on the real Samsung phone, and in dark theme.
- The service worker update path (v4 to v5) on an installed app.
- Edit, delete workout, kg conversion and rest timer beyond the cases above.


## 2.0.0 - 2026-10-26
### Changed
- The app now starts with a clean slate: no exercises are built in. Users add the exercises they want to track. Squat and Deadlift are ordinary exercises like any other.
- Existing installs keep their data: workouts saved as `squat` / `deadlift` are recognised automatically and appear as "Squat" and "Deadlift" on Home. No workout record is rewritten and the IndexedDB structure (`strengthlog` v1, `workouts` store) is unchanged.
### Added
- Add exercise screen (Home and Settings): type a name or pick from suggestions, plus a movement type (Squat / Hinge / Push / Pull / Core / Other). Duplicate names are blocked (ignoring capitals and extra spaces); typed names are escaped everywhere they are shown.
- Empty Home shows quick-add buttons (Squat, Deadlift, Bench Press, Overhead Press, Barbell Row).
- Settings > My exercises: Hide / Show an exercise. Hidden exercises keep all history.
- History uses a dropdown filter when there are more than 2 exercises.
- Exercise list is stored in localStorage (`exercises`); suggestions live in the new file `exercise-presets.js` (ids are permanent).
- JSON export (file version 2) now includes the exercise list. Import accepts old v1.1.0 exports and recreates exercises from the workouts in the file; an exercise id with no known name appears as "Unknown exercise".
### Files
- app.js, index.html (script tag, footer `v2.0.0`), sw.js (cache `strength-log-v4`, new file cached), manifest.json and README.md (neutral wording), new exercise-presets.js, CHANGELOG.md. style.css and the icons are unchanged.

### Test results
Verified (headless Chromium, 390 px wide, project served over localhost, service workers blocked; 23 of 24 checks passed, see the failure below):
- `node --check` passes for app.js, sw.js and exercise-presets.js.
- Upgrade: test workouts written by v1.1.0 are byte-identical after loading v2.0.0; Home shows SQUAT and DEADLIFT; History shows both; the Squat form shows the previous workout; the kg unit setting is kept.
- Fresh install: empty Home with prompt; quick-add; typed "squat" matches the suggestion; duplicate, empty and `<img onerror>` names are handled safely; workouts save for added exercises; History dropdown filters; hiding an exercise removes it from Home but keeps its history.
- Export includes exercises; an old-format file imports into a clean profile and recreates Squat, Deadlift and an "Unknown exercise" fallback; a v2 export round-trips; the list is rebuilt from workouts if localStorage is cleared.
- Reported by the user (not run by Claude): after removing v1.1.0 and installing v2.0.0 on their Samsung phone, Squat and Deadlift appeared on the first screen with all previous workout history.
- No page errors were logged. style.css and the icons are byte-identical to the uploaded files.
- One check failed: the footer text in the upgrade test, because the test browser reused its cached v1.1.0 index.html. In a clean browser the footer shows `v2.0.0` and both scripts load.

Not verified:
- Appearance on the phone and in dark theme, the footer text on the phone, and the service worker v3 to v4 update path (the user instead removed v1.1.0 and installed v2.0.0).
- Edit, delete, kg conversion and rest timer after the change (code not changed, not run).
- Suggestion link/form, similar-name warning, merging and category-based features are not included.

## 1.1.0 - 2026-10-10
### Added
- App version (`v1.1.0`) is now shown unobtrusively in a footer at the bottom of every screen.
  - index.html: added a footer element (id `ver`) below `#app`, styled with the existing `--mu` color variable.
  - sw.js: cache name changed from strength-log-v2 to strength-log-v3 so installed apps load the updated index.html.
  - app.js, style.css and the database structure are unchanged; no workout data is touched.
- Version is kept in sync manually: update the footer text in index.html together with this changelog on each approved release.

### Test results
Verified (headless Chromium, 390 px wide phone-size window, project served over localhost):
- `node --check` passes for sw.js and app.js.
- Footer shows `v1.1.0` and is visible on Home, Squat entry, History, Settings and the "Workout saved" screen.
- Saving a one-set test workout still works ("Workout saved" appears) and no page errors were logged.
- After load, the only cache present is `strength-log-v3`.
- Screenshot of the saved screen checked: footer is small, muted and centered below the buttons.
- app.js, style.css, icon-192.png and icon-512.png are byte-identical to the uploaded 1.0.1 files (checksums match).

Not verified:
- Appearance on the real Samsung phone, and in dark theme.
- That an already-installed app upgrades from v2 to v3 and shows v1.1.0 after deployment.
- Edit, delete, import/export, kg mode and rest timer after the update (no code involved changed, but not run).

## 1.0.1 - 2026-10-10
### Fixed
- Individual sets can now be saved, so you can alternate between Squat and Deadlift.
  - app.js: blank unsaved trailing sets are skipped on save; opening an exercise resumes today's workout if it has fewer than 4 sets.
  - sw.js: cache name changed from strength-log-v1 to strength-log-v2 so installed apps load the new app.js.

## 1.0.0 - initial version
- First release.
