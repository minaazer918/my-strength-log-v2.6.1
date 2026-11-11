# My Strength Log

Personal strength workout log. Users add the exercises they want to track. Plain HTML + CSS + JavaScript, no server, no accounts, no analytics.
Data lives in your browser's IndexedDB and survives closing the browser and restarting the phone.

## Files
index.html, style.css, app.js, exercise-presets.js, manifest.json, sw.js (offline), icon.svg, icon-192.png, icon-512.png

## Try it on a computer
Service workers need https or localhost, so don't just double-click index.html.
    cd my-strength-log
    python3 -m http.server 8000
Open http://localhost:8000

## Put it online (free, simplest first)
1. Netlify Drop: go to https://app.netlify.com/drop and drag the whole `my-strength-log` folder in. You get an https URL.
2. GitHub Pages: upload the files to a new repo, Settings > Pages > deploy from main branch.
3. Cloudflare Pages: create a project and upload the folder.
Any static host with https works.

## Install on your Samsung phone
1. Open the https URL in Chrome (or Samsung Internet) once, while online.
2. Chrome menu (three dots) > "Add to Home screen" / "Install app".
3. Launch it from the icon. After the first load it works offline.

## Backup
Settings > Export Data (JSON or CSV). Settings > Import Data restores a JSON export.
A weight of 0 means bodyweight and is shown as BW.
Clearing browser/site data deletes your workouts, so export regularly.

## Data model
Each workout record: id, exercise, date (YYYY-MM-DD), comment, createdAt, updatedAt, and a sets array
of { workoutId, setNumber, weight (always stored in lb), reps, performance }. Sets of timed exercises also store `seconds` (and reps 0); weight 0 means bodyweight.
The exercise field is an exercise id. The user's exercise list (id, name, type, hidden, and kind `time` for timed exercises) is kept in localStorage and included in JSON exports; the suggestion list is in exercise-presets.js (ids are permanent).
Sets are embedded in the workout so a save is a single atomic write.

## Adding or changing suggested exercises
The suggestions in the Add exercise screen come from `exercise-presets.js`. Users can also type any exercise name, so a missing suggestion never blocks anyone.

1. Open `exercise-presets.js` and add a row to the list, in the form `['id', 'Name', 'Type']`, for example `['cable-fly', 'Cable Fly', 'Push']`. For a timed exercise (a hold, measured in seconds instead of reps) add `'time'` as a fourth value, for example `['wall-sit', 'Wall Sit', 'Squat', 'time']`.
   - **id:** lowercase letters, digits and hyphens only, up to 40 characters, and unique. Capitals, spaces or underscores make the exercise disappear from the user's list when the app loads.
   - **Name:** up to 40 characters, and different from every other suggestion (capitals are ignored).
   - **Type:** one of Squat, Hinge, Push, Pull, Core, Other (the list is `CATS` in `app.js`).
2. **Never change, reuse or delete an id.** Saved workouts point to it. To rename a suggestion, change only the name.
3. Existing users are not affected by edits: when someone adds a suggestion, its name and type are copied into their own list. Changes only affect what new additions look like.
4. Release the change as a PATCH version: add an entry at the top of `CHANGELOG.md`, update the version in the footer of `index.html` (the `ver` element), and change the cache name in `sw.js` (for example `strength-log-v8` to `strength-log-v9`) so installed apps load the new list.
5. Check it: serve the folder locally (see above), open Add exercise, confirm the new name appears as you type, add it, and confirm it shows on Home.
