# Workout log

A single-page workout tracker for a dumbbell routine. Log sets, reps and weights, get a rest timer between sets, and see your progress over time. It is one HTML file with no build step, no server and no account. Your data stays in your own browser.

## What it does

- **Workout tab.** Start Workout A, Workout B, one of the four split days (Upper A, Lower A, Upper B, Lower B) or a custom workout. Enter kg and reps for each set and tick it off. Exercises open one at a time, and the next unfinished exercise opens when you complete one.
- **Weight chips.** Tap 5, 10, 20 or 30 kg to fill every open set. The chips show which plates go on each end of the dumbbell. Bodyweight exercises offer BW, +5, +10 and +15 kg for a loaded backpack.
- **Last time.** Each exercise shows what you did in your previous session and uses those reps as the starting number.
- **Load suggestions.** If you hit the top of the rep range on every set last time, the page suggests the next step up.
- **Rest timer.** It starts when you tick a set, uses the rest time set for that exercise, and beeps and vibrates when it ends. `+15 s` and `Skip` are on the bar. The screen is kept awake during a workout where the browser allows it.
- **History tab.** Every finished workout, with edit and delete.
- **Progress tab.** Lifts per week against the target of 3, body weight against the plan pace of +0.25 kg a week, and a chart per exercise (top weight or weight × reps, best set or total reps for bodyweight moves).
- **Backup tab.** JSON backup and restore, CSV export, a sound switch and erase all.

A workout in progress is saved as you go, so closing the tab or reloading the page picks up where you left off.

## Run it

Open `index.html` in a browser, or host it.

### GitHub Pages

1. Create a repository and add the file as `index.html` (rename `workout-log.html`).
2. In the repository, go to **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick `main` and the `/ (root)` folder, then save.
4. Open the address it shows after a minute or two.
5. On a phone, use **Add to Home Screen** to open it like an app.

Any other static host works the same way (Cloudflare Pages, Netlify and so on). There is nothing to configure.

The page loads two Google Fonts (Barlow Condensed and IBM Plex Sans). If they cannot load, it falls back to system fonts. To remove the third-party request completely, delete the two `<link>` lines for `fonts.googleapis.com` in the `<head>`.

To keep search engines from listing it, add this inside `<head>`:

```html
<meta name="robots" content="noindex">
```

## Your data

Everything is stored in the browser's `localStorage` under the key `workout-log.v1`. Nothing is sent anywhere, so a public repository exposes the code only, never your log.

What that means in practice:

- **One device, one browser.** A different phone or browser starts with an empty log. Use backup and restore to move it.
- **Clearing site data deletes the log.** So does a private window once it closes.
- **Safari on iPhone** can clear storage for sites you have not opened in about a week of use. Adding the page to the Home Screen avoids this. Export a backup now and then either way.
- **GitHub Pages shares storage across your sites.** Browser storage belongs to the whole address (`yourname.github.io`), not to one path. Another page under the same address could read this log if that page had a bug. If you host several projects there, use a custom subdomain for this one.
- **Never commit your exports.** The JSON and CSV files contain your sessions and body weight. Keep them out of the repository, or add `*.json` and `*.csv` to `.gitignore`.

## Backup and restore

- **Export backup (JSON)** saves `workout-log-YYYY-MM-DD.json` with all sessions and weigh-ins.
- **Import a backup** reads that file back. Sessions are merged by id, so importing the same file twice does not create duplicates, and a session you have edited is replaced by the version in the file. Weigh-ins are matched by date.
- **Export sets (CSV)** saves one row per set for a spreadsheet, with the columns `date, workout, exercise, set, kg, reps_or_seconds, unit`. For timed exercises the reps column holds seconds and `unit` is `sec`.

## Change the plan

The plan lives at the top of the `<script>` block in `index.html`, in two objects.

`EX` defines each exercise:

```js
'goblet-squat': { name: 'Goblet squat', sets: 3, lo: 8, hi: 12, rest: 90 },
'plank':        { name: 'Plank', sets: 3, lo: 30, hi: 45, rest: 45, unit: 'sec', bw: 1, nochips: 1 },
```

| Field | Meaning |
| --- | --- |
| `sets` | Number of sets to start with |
| `lo`, `hi` | Rep range (seconds for `unit: 'sec'`) |
| `rest` | Rest in seconds |
| `side: 1` | Logged per side |
| `bw: 1` | Bodyweight, so the kg box means extra load |
| `unit: 'sec'` | Timed exercise |
| `nochips: 1` | Hide the weight chips |
| `jump` | Reps needed on every set before suggesting more load, if different from `hi` |
| `note` | Short text shown instead of a rep range |

`PLAN` lists the exercises in each workout. An entry can override its defaults for that workout only:

```js
UA: { name: 'Upper A', sub: 'Heavier pressing and rows',
      items: [['floor-press', { sets: 4, lo: 6, hi: 10, rest: 120 }], ['one-arm-row'], ['shoulder-press']] },
```

Two more settings sit just below `PLAN`:

- `LADDER = [5, 10, 20, 30]` is the list of loads the suggestions step through. Edit it to match your plates. The chip buttons in the `chips()` function are written out by hand, so change those to match too.
- `PACE_KG_PER_WEEK = 0.25` is the dashed reference line on the body weight chart.

You can also add an exercise that is not in the plan from the box at the bottom of a workout. It is saved with that workout and shows up in History and the Progress charts.

## Notes

- The "kg × reps" figure is the sum of weight × reps for each set. It uses the load per dumbbell, so it is a consistent trend measure rather than the total mass you lifted. Timed exercises and sets with no added weight are left out of it.
- The beep uses the browser's audio, so it will not sound if the phone is on silent or the tab is muted. Vibration is separate and works on Android only.
- It works in current Chrome, Edge, Firefox and Safari, on phone and desktop.
