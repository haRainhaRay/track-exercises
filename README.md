# Workout log

A single-page tracker for a dumbbell routine and the food that goes with it. Log sets, reps and weights, get a rest timer between sets, log what you eat and see calories and protein add up, and watch your progress over time. It is one HTML file with no build step, no server and no account. Your data stays in your own browser.

## What it does

- **Workout tab.** Start Workout A, Workout B, one of the four split days (Upper A, Lower A, Upper B, Lower B) or a custom workout. Enter kg and reps for each set and tick it off. Exercises open one at a time, and the next unfinished exercise opens when you complete one.
- **Weight chips.** Tap 5, 10, 20 or 30 kg to fill every open set. The chips show which plates go on each end of the dumbbell. Bodyweight exercises offer BW, +5, +10 and +15 kg for a loaded backpack.
- **Last time.** Each exercise shows what you did in your previous session and uses those reps as the starting number.
- **Load suggestions.** If you hit the top of the rep range on every set last time, the page suggests the next step up.
- **Rest timer.** It starts when you tick a set, uses the rest time set for that exercise, and beeps and vibrates when it ends. `+15 s` and `Skip` are on the bar. The screen is kept awake during a workout where the browser allows it.
- **Intake tab.** Pick foods from a list of common Indian foods and the page adds up calories and protein for the day against your daily targets (2,800 kcal and 140 g by default). Details are in the Intake section below.
- **History tab.** Every finished workout, with edit and delete.
- **Progress tab.** Lifts per week against the target of 3, body weight against the plan pace of +0.25 kg a week, and a chart per exercise (top weight or weight × reps, best set or total reps for bodyweight moves).
- **Backup tab.** JSON backup and restore, CSV exports for workouts and food, a sound switch and erase all.

A workout in progress is saved as you go, so closing the tab or reloading the page picks up where you left off.

## Intake

The Intake tab logs food for one day at a time.

- **Day.** It opens on today. Use the arrows or the date box to look at or fill in another day.
- **Totals.** Two bars show calories and protein against your targets, with how much is left or over.
- **Meals.** Breakfast, Mid-morning, Lunch, After lifting and Dinner. Tap **Add food** under a meal to open the picker.
- **Picker.** It opens full screen. Search by name (roti, dahi, paneer and a few other common names work), or filter by group. Each tap adds one serving, and the square shows how many are in that meal. Tap **Done** when you are finished.
- **Quantities.** Back on the page, change a quantity with the minus and plus buttons or type it in, for example 1.5 for one and a half cups of rice. Pressing minus at the lowest quantity removes the food.
- **Your own foods.** If something is not on the list, open **Not on the list? Add your own**, enter calories and protein for one serving, and it is added and kept under the **Mine** group. Foods already logged keep the numbers they were logged with, even if you later delete the food or change the list.
- **Last 7 days.** A chart of protein or calories per day against your target, with the average over the days you logged.
- **Targets.** Open **Daily targets** to change them. **Use plan numbers** puts back 2,800 kcal and 140 g.

The food numbers are one household serving of home-cooked food, taken from the plan page's table. They are estimates, rounded, and oil, ghee and portion size can move them by 10 to 20 percent.

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
- **Never commit your exports.** The JSON and CSV files contain your sessions, body weight and food log. Keep them out of the repository, or add `*.json` and `*.csv` to `.gitignore`.

## Backup and restore

- **Export backup (JSON)** saves `workout-log-YYYY-MM-DD.json` with all sessions, weigh-ins, food entries, your own foods and your targets.
- **Import a backup** reads that file back. Sessions are merged by id, so importing the same file twice does not create duplicates, and a session you have edited is replaced by the version in the file. Food entries are merged the same way, by id. Weigh-ins are matched by date, and the targets in the file replace yours. A backup made before the Intake tab existed still imports; it just has no food in it.
- **Export workout sets (CSV)** saves one row per set for a spreadsheet, with the columns `date, workout, exercise, set, kg, reps_or_seconds, unit`. For timed exercises the reps column holds seconds and `unit` is `sec`.
- **Export food log (CSV)** saves one row per food entry, with the columns `date, meal, food, serving, quantity, kcal, protein_g`. Calories and protein are already multiplied by the quantity.

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

## Change the food list

The foods, meals and default targets sit in the same `<script>` block, right after `PACE_KG_PER_WEEK`. Each food is one line:

```js
{ key: 'dal', cat: 'dal', name: 'Dal (toor, moong or masoor)', srv: '1 cup, medium-thick, with tadka', kcal: 195, p: 10, step: 0.5, alt: 'lentil' },
```

| Field | Meaning |
| --- | --- |
| `key` | A short id that is unique in the list. Do not reuse one. |
| `cat` | Group: `staples`, `dal`, `dairy` or `extras` |
| `name`, `srv` | What the food is, and what one serving means |
| `kcal`, `p` | Calories and grams of protein in one serving |
| `step` | How far the minus and plus buttons move: `1` for countable foods like an egg, `0.5` for portions like a cup |
| `alt` | Optional extra search words |

`MEALS` is the list of meal slots and `DEFAULT_TARGETS` holds 2,800 kcal and 140 g. Editing a food changes what you get from then on. It does not change entries you have already logged.

## Notes

- Your data (workouts, food, targets) is all in the one `workout-log.v1` entry in the browser, so a single backup file covers everything.
- The "kg × reps" figure is the sum of weight × reps for each set. It uses the load per dumbbell, so it is a consistent trend measure rather than the total mass you lifted. Timed exercises and sets with no added weight are left out of it.
- The beep uses the browser's audio, so it will not sound if the phone is on silent or the tab is muted. Vibration is separate and works on Android only.
- It works in current Chrome, Edge, Firefox and Safari, on phone and desktop.
