# workout-tools

A collection of standalone workout-assist apps. Each tool is a single self-contained HTML file — no build step, no dependencies, no install required. All data is stored locally in the browser via `localStorage`.

## Tools

### 5×5 Tracker (`index.html`)

Tracks progress for the [StrongLifts 5×5](https://stronglifts.com/5x5/) barbell program.

**Program structure:**
- Alternates between Workout A and Workout B
- **Workout A:** Squat · Bench Press · Barbell Row
- **Workout B:** Squat · Overhead Press · Deadlift

**Progression:**
- All 5 sets completed → weight increases next session (+10 lb squat/deadlift, +5 lb upper body)
- Any set missed → weight holds, failure count increments
- 3 consecutive failures → 10% deload, failure count resets
- Rest timer: 3 min after a successful set, 5 min after a failed set

**Data:**
- All state is saved to `localStorage` under the key `fivebyfive_data`
- Settings → Export Save Data downloads a dated `.json` backup file
- Settings → Import Save Data loads a previously exported `.json` file; prompts for confirmation before overwriting existing data
- Settings → Reset All Data clears everything after confirmation

## Running locally

Open any file directly in a browser:

```
open index.html
```

Or serve with a local server (needed if you hit CORS issues):

```
python -m http.server 8000
```
