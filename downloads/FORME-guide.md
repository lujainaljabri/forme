# FORME 🤍

Strength in Your Own Way

A private, local-first personal fitness PWA for personal use. React, TypeScript, Vite,
Dexie/IndexedDB, Recharts, Lucide, and self-hosted open-source fonts. No account,
paid API, backend, tracking script, advertising, or external font request.

## Run and build

Install Node.js 22 LTS or newer (free). In this folder:

```sh
npm ci
npm run dev
```

Open the URL Vite prints. For production:

```sh
npm test
npm run build
```

`dist/` contains the complete static app, manifest, PNG icons, fonts and generated
precache service worker. Serve using HTTPS in production; localhost works for
local development. Opening index.html with a file:// URL will not work.

## Free iPhone hosting: GitHub Pages

GitHub Pages for a **public repository on GitHub Free** does not require billing
information. The source becomes public, but personal tracking data is never
included in the repository or sent to GitHub by this app. GitHub serves code and
assets and receives ordinary hosting requests; there is no health-data backend.
Do not commit your personal JSON backups.

1. Create a free GitHub account if needed. Do not start a paid trial or enter a card.
2. Create a new public repository named `forme`.
3. Upload the contents of this source folder, including `package.json`,
   `package-lock.json`, `src/`, `public/`, `scripts/`, `index.html`,
   `tsconfig.json`, `vite.config.ts`, and `.github/workflows/deploy.yml`.
   Do not upload `node_modules/` or any personal backup files.
4. In the repository, open **Settings → Pages → Build and deployment → Source**,
   and select **GitHub Actions**.
5. The included workflow runs on pushes to `main`. If it did not run after the
   Pages setting was changed, open **Actions → Deploy FORME → Run workflow**.
6. When the workflow succeeds, Pages shows your address:
   `https://YOUR-USERNAME.github.io/forme/`.
7. Open that exact HTTPS address in Safari on your iPhone.

Official free-hosting documentation:
https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages

The project uses relative asset paths, so repository subpath hosting works.
No domain purchase, paid service, database provisioning or credit card is needed.
If GitHub's terms or free plan change, keep running locally; do not accept a paid
upgrade. The built `dist/` directory can be moved to another free static HTTPS host.

### Simpler option: deploy the prebuilt static ZIP

No Node installation or build is needed for this option:

1. Extract `FORME-static.zip` on your computer.
2. Create a free public GitHub repository named `forme`.
3. Upload the extracted **contents**, including `index.html`, `sw.js`,
   `manifest.webmanifest`, icons, `assets/`, and `licenses/`, to the repository root.
   Upload the actual files, not the ZIP itself. Do not upload the private setup JSON.
4. In **Settings → Pages**, select **Deploy from a branch**, branch **main**,
   folder **/(root)**, then Save.
5. Wait for GitHub's Pages deployment to finish. Open the Pages URL shown in Settings.

## Personalize privately on first use

The public source and build deliberately contain no name, height, or starting weight.
A separate `FORME-private-setup.json` is included with the private delivery files.
**Never upload that file to a repository or hosting provider.** After opening the
hosted app, choose **More → Backup → Restore backup** and select that setup file
locally on your iPhone. This loads your requested starting profile and plans into
IndexedDB, without uploading its contents. Do this once before adding real logs,
since restore replaces existing data. Alternatively use **Set up profile** and
enter your details manually on the device.

Only the source ZIP or static ZIP is for hosting. Keep the private setup file and
any personal backups separate.

## Install and use offline

In Safari, tap **Share → Add to Home Screen → Add** (on some iOS versions this
appears under More). Open the installed icon while online and allow the first
load to finish. Reopen in airplane mode to check offline access before relying on
it. Initial caching and app updates need a connection. The burgundy F icon and
matching dark loading screen are included; iOS controls the native launch screen
and rounded icon mask.

Keep using the same installed app and address. Safari, other browsers, and a
changed hosting address can have separate storage. Do not assume they synchronize.

## Brief user guide

- **Home:** today’s editable plan, real weekly workout count, protein, hydration,
  next class, and quick logging. Fresh installations contain no tracking history.
- **Workout Plan:** edit activity, time, duration and notes. Rescheduling swaps two
  weekdays. Edit/add/remove exercise names, target sets and reps. Plans repeat
  weekly; a scheduled class can replace a workout on its specific date. The weekly template stays available for other dates.
- **Track:** start a session, enter kg and reps, and tick each completed set. Previous
  loads use saved sessions. Add/remove sets and exercises, edit notes, pause/resume,
  and use 60/90/120-second rest timers. Finish saves history and strength records.
  Active sessions save on every edit. Closing a running workout keeps elapsed time
  running; pause it first when taking a longer break. The rest countdown is an
  in-app visual timer, not a notification/alarm; iOS can suspend apps in background.
- **Nutrition:** log breakfast/lunch/dinner/snacks and your estimated protein.
  Library foods can be edited, added or removed. No precise meal values are
  invented. Eggs and tuna are blocked, along with your additional exclusions.
- **Water:** use quick amounts or custom entry. Edit/delete entries in Nutrition’s
  water journal; “Undo last” removes the last recorded water entry.
- **Classes:** manually schedule a class and add notes. Mark attendance with the
  check button. The calendar button downloads an `.ics` reminder; open/import it
  into Calendar where supported. It is not a gym booking or a live timetable.
- **Sleep:** record actual bedtime/wake-up dates, quality and energy. Duration
  includes crossing midnight. Suggestions are flexible, not medical diagnoses.
- **Progress:** weekly/monthly saved activity, daily strength loads per selected exercise, all-time personal
  records by exercise, protein/water/sleep charts, optional measurements, and
  editable saved weights/reps. Delete a session if it was logged accidentally.
- **Settings:** edit your profile and goals. Preferred time is a profile preference;
  edit workout templates to change their actual schedule. Use English/Arabic to
  change the core interface and reading direction. Food and exercise names,
  user-entered content, and some informational catalog text remain in English.

## Back up and restore

In **More → Backup**, export a JSON backup regularly, especially before clearing
Safari data, moving to a new address, or changing phones. Keep it in a location you
trust: it contains personal information. Backup exports include profile, templates,
active and finished workouts, meals, water, sleep, classes and measurements.

To restore, open FORME at your chosen address and choose **Restore backup**. Select
a FORME JSON file, review the replacement warning and confirm. Import validates
schema version and record structure before replacing data. It is a full replacement,
not a merge. Invalid files leave existing data intact. Export the current data first
if you may want to return to it. The maximum backup size is 20 MB.

**Browser storage can be lost if it is cleared, Safari evicts site data, or your
phone is lost. A PWA is not a backup.** The “Request persistent storage” action is
best effort; the browser decides whether to grant it. Delete all personal data in
Backup requires confirmation and typing DELETE; exported copies are not deleted.

## Validation and limits

Automated tests cover IndexedDB close/reopen persistence, set weights, meals, water,
sleep and class data, timestamp-based elapsed time, local date/week boundaries,
empty initial histories, JSON backup round-trip and rejection of corrupt data, plus
a rendered interaction flow logging meals/water/sleep/classes, rescheduling plans, completing a workout, restoring a backup, and Arabic RTL.

A production TypeScript/Vite build and generated service-worker precache are checked.
The rendered flow uses jsdom, not a real mobile browser. This execution environment
had no callable browser QA tool or installed Sites publishing helper. Therefore no
production URL, real Safari offline launch, visual device test, native calendar
import, or real iPhone installation has been verified. Use the deployment steps
above, then perform the checklist below before relying on the app.

1. Log a workout weight, meal, water, class and sleep. Close and reopen the installed
   app and confirm all are present.
2. Start a workout, close it, reopen and check its elapsed time; pause/resume and
   finish, then check history and records.
3. Export a backup, make a temporary edit, restore the backup, and verify the edit
   was replaced. Cancel a restore once and verify existing data remains.
4. Switch English/Arabic and inspect bottom navigation, forms and narrow-screen layout.
5. After an initial online load, enable airplane mode, close/reopen, and log water.
6. Back up real data before changing hosting addresses or deleting website storage.

## Source map

- `src/data.ts`: schema, defaults, IndexedDB, dates, duration and restore validation.
- `src/main.tsx`: navigation and functional tracker flows.
- `src/style.css`: responsive burgundy/ivory design and safe-area navigation.
- `src/*.test.*`: data and rendered interaction tests.
- `scripts/pwa.mjs`: build-time local asset precache generation.
- `public/`: PWA manifest and icon assets.
- `.github/workflows/deploy.yml`: free static Pages build/deploy workflow.

Personal data is kept as a single versioned state document in IndexedDB. Writes are
serialized and failure messages surface in the interface. Backups use the same
versioned schema. This is a personal-use app, without cross-device or multi-tab
synchronization. It has no push notifications or gym integrations.
