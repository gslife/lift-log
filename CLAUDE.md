# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file, standalone web application for tracking Strong Lifts 5×5 workout progress. The entire app lives in `index.html` — no build process, no package manager, no dependencies except Google Fonts. The repo is intended to grow into a collection of similar workout-assist tools, each as self-contained HTML files.

## Running the App

Open directly in a browser:
```
open index.html
```

Or serve locally:
```
python -m http.server 8000
# then navigate to http://localhost:8000
```

There is no build step, no `npm install`, and no test suite. Testing is done manually in the browser (DevTools → Application → Local Storage).

## Architecture

Single HTML file with embedded CSS and JavaScript. All state is persisted to `localStorage` as JSON.

**Views:** `home`, `workout`, `history`, `settings` — toggled via `navigate(viewId)`, which shows/hides `<div id="view-{id}">` elements.

**State shape:**
```javascript
{
  weights: { squat, bench, row, ohp, deadlift },   // current working weights (lbs)
  failCounts: { squat, bench, row, ohp, deadlift }, // consecutive failures (deload at 3)
  nextWorkout: 'A' | 'B',
  history: [{ date, type, exercises: [{ id, name, weight, sets: [bool], success }] }]
}
```

**Data management** (Settings view):
- Export: downloads state as a dated `.json` file via Blob URL
- Import: reads a `.json` file, validates shape (`weights`, `history`, `nextWorkout`), prompts confirmation before overwriting — uses `pendingImport` variable to hold parsed data until confirmed
- Reset: clears all state after a two-step confirmation

**Key constants** (top of `<script>`):
- `PROGRAM` — exercise definitions per workout type
- `DEFAULT_WEIGHTS` — starting weights
- `FAIL_THRESHOLD` — consecutive failures before deload (default: 3)
- `REST_SECONDS` / `FAIL_REST_SECONDS` — timer durations

**Progression logic** (in `finishWorkout()`):
- All sets complete → weight increases by exercise increment
- Any set failed → weight unchanged, failCount incremented
- 3 consecutive failures → 10% deload, failCount resets

**Set states** cycle via `toggleSet(exerciseIdx, setIdx)`: `null → 'success' → 'fail' → null`

## Page Structure

The HTML body is structured as:
1. `.content-wrap > .page-intro` — SEO intro section with H1 and description (above the app)
2. `hr.content-divider` — 1px border divider
3. `#app` — the tracker (480px max-width, sticky header, main views)
4. `hr.content-divider` — 1px border divider
5. `.content-wrap > .content-sections` — editorial content below the app: "What is 5×5 Training?", "How the Tracker Works", and a 6-question FAQ

Content sections use `max-width: 680px` and are styled separately from the app. Do not modify `#app` or the `<script>` block when editing content sections.

## Design System

- Dark theme: background `#0a0a0b`, surface `#141416`, accent `#d4f534` (lime)
- Success: `#22c55e`, Fail: `#ef4444`
- Fonts: Outfit (body), DM Mono (numbers/weights)
- Max-width: 480px (mobile-first)
