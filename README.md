# ShiftTracker

ShiftTracker is a static, serverless team roster dashboard for viewing daily and monthly schedules. It runs in the browser with HTML, vanilla JavaScript, Tailwind CSS, and JSON roster files; it requires no backend or build step.

## Features

- **Daily roster:** Shows each shift in its own color-coded card and places the selected member’s shift first. Cards summarize the on-shift, WFO, WFH, Pune, and Noida counts when the source roster includes the relevant tags.
- **Monthly roster:** Switch between a selected member’s calendar/list and the full team’s shiftwise schedule. Shiftwise mode shows one selectable shift at a time and defaults to the selected member’s most common shift for the month.
- **Daily shiftwise totals:** The team table includes per-day totals for on-shift members, Pune, Noida, WFH, and WFO. The selected member’s row is highlighted.
- **Status summaries:** The individual monthly view shows counts for statuses such as WFO, WFH, WO, FH, leave, holidays, and CO.
- **Responsive layout:** The header controls fit on one row on mobile. The monthly calendar adapts to smaller screens; the shiftwise table can be scrolled horizontally to reach every date.
- **Light and dark themes:** The initial theme follows the device preference. The theme button switches themes, and the choice is saved in the current browser.
- **Roster cleanup and sorting:** Names are normalized (including removal of `(Self Commute)`), and shift groups are ordered chronologically, followed by non-working statuses.
- **Month caching:** Once loaded, a month’s JSON data is cached in memory for the current page session.

Pune/Noida counts rely on city tags in the roster data. When tags are missing, the dashboard indicates that city counts are unavailable or incomplete.

## Repository structure

Roster JSON files are stored by year and named with the first three letters of the month:

```text
shift-dashboard/
├── index.html
├── README.md
└── 2026/
    ├── Aug.json
    ├── Sep.json
    ├── Oct.json
    └── Nov.json
```

Add future months to the matching year folder, for example `2027/Jan.json`. The dashboard uses the selected date or month to fetch the matching file.

## Run locally

Serve the repository over HTTP so the browser can fetch the JSON files:

```sh
python3 -m http.server 8000
```

Then open [http://localhost:8000/](http://localhost:8000/) in a browser. Opening `index.html` directly as a `file://` URL may prevent roster data from loading.

## Roster JSON format

Each date is a `YYYY-MM-DD` key. Its value maps shift/status names to arrays of team members:

```json
{
  "2026-11-01": {
    "6 AM - 3 PM IST": [
      "Yash Chauhan | WFO | Noida",
      "Example Teammate | WFH | Pune"
    ],
    "WO": [
      "Example Teammate | Pune"
    ]
  }
}
```

Use `WFO` or `WFH` to identify work location and `Pune` or `Noida` to identify office city. Keep city tags on non-working assignments too when known. The dashboard recognizes statuses such as `WO`, `Leave`, `CO`, `FH`/`F H`, and `Holiday`.

## Monthly roster update workflow

1. Open the `Roster` sheet in the new Excel workbook and identify the row containing the schedule dates.
2. Convert each employee’s daily assignment into the JSON structure above. Exclude totals and constraint rows.
3. Format each date as `YYYY-MM-DD`, include the correct shift/status, and preserve WFO/WFH and Pune/Noida tags where available.
4. Save the JSON file as `<Mon>.json`, such as `Dec.json`, under the correct year folder.
5. Serve or deploy the updated repository. Select a date in that month to load the new roster.

## Deploy with GitHub Pages

1. Push the repository to GitHub.
2. In the repository, open **Settings → Pages**.
3. Under **Build and deployment**, select **Deploy from a branch**, choose the `main` branch and `/ (root)` folder, then save.
4. Open the published GitHub Pages URL after deployment completes.

## Customization

- Change the default selected team member by editing `selectedUser` near the start of the script in `index.html`.
- Update shift names, colors, and icons in the `shiftStyles` object.
- Add or adjust chronological shift ordering in `shiftOrder` and non-working status ordering in `nonWorkingShiftOrder`.
- The page title and visible `ShiftTracker` heading are set in `index.html`.
