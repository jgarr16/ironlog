# IronLog — Claude Code Handoff Prompt

## Project Overview
**IronLog** is a workout tracking web application for two users (John and Kyong) who train together 5 days/week at a 24 Hour Fitness gym. The app tracks exercises, weights, sets, reps, and cardio.

> **Architecture note:** The app was rebuilt (April 2026) from a Firebase-backed architecture to a fully self-contained, localStorage-only app. There is no backend, no Firebase, no authentication, and no AI coach screen. All data lives in the browser.

---

## Current Tech Stack

### Frontend
- **Single static HTML file** (`index.html`) — all HTML, CSS, and JavaScript in one file
- No frameworks, no build process, no npm — vanilla JS only
- Hosted on **GitHub Pages** at `https://jgarr16.github.io/ironlog/`
- Mobile-first design, optimized for iPhone use at the gym
- Custom dark theme using CSS variables, Barlow Condensed + Barlow fonts from Google Fonts

### Backend / Data
- **No backend.** All data stored in browser `localStorage` under the key `ironlog_v2`
- No Firebase, no cloud database, no external API calls of any kind
- State is serialized as JSON and persisted on every mutation via `saveState()`

### Data Structure (in-memory / localStorage)
```json
{
  "weights": {
    "<exerciseId>": { "john": <number>, "kyong": <number> }
  },
  "images": {
    "<exerciseId>": "<base64 or URL string>"
  },
  "currentWeek": {
    "startDate": "YYYY-MM-DD",
    "notes": "<string>",
    "days": {
      "1": {
        "cardio": {
          "john":  { "weight": 195, "swam": false, "laps": 101 },
          "kyong": { "treadmill": false, "stairway": false }
        },
        "assistDone": { "pullups": false, "dips": false },
        "done": { "<exerciseId>": true }
      }
    }
  },
  "history": [
    {
      "startDate": "YYYY-MM-DD",
      "notes": "<string>",
      "days": { ... }
    }
  ]
}
```

---

## App Screens & Navigation

The app uses a flat tab-bar navigation model. There is no login screen, no setup screen, and no coach screen.

### Tabs
1. **Day 1–5** — one tab per lifting day; each shows exercise cards for that day
2. **Notes** — weekly summary, notes textarea, "Start New Week" button, previous weeks history list

### Day Panel (per lifting day tab)
- Day name, focus group label
- Collapsible **Lifts** section: one card per exercise
  - Exercise name
  - John and Kyong weight steppers (+5 / −5 for upper; +10 / −10 for lower)
  - Done checkbox per exercise per user — marks the exercise complete
  - Exercise done when all exercises in the day are checked → tab shows ✓
- Collapsible **Cardio** section
  - John: swim lap counter (integer stepper) + "Did swim" checkbox
  - Kyong: treadmill + stairway checkboxes
- Collapsible **Assist Work** section (Days 2 and 4 only)
  - Pull-ups and dips checkboxes (bodyweight)

### Notes Panel
- Week-of date, X/5 days complete indicator
- Textarea for weekly feedback and weight change notes
- "Start New Week" button — archives current week to `state.history`, resets `currentWeek`, auto-downloads `ironlog-history.json`
- Previous weeks list showing week-of date, days completed, and notes

---

## Workout Program — All 5 Days

### Day 1 — Push A (Chest · Shoulders · Triceps)
| Exercise ID | Name | John | Kyong |
|---|---|---|---|
| chest_press | Chest Press | 120lb × 3×6–10 | 20lb × 3×8–12 |
| shoulder_press_a | Shoulder Press | 50lb × 3×6–10 | 10lb × 3×10 |
| triceps_ext | Triceps Extension | 65lb × 3×8–12 | 10lb × 3×8–12 |
| pallof_a | Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 2 — Lower A (Quads · Hamstrings · Glutes)
| Exercise ID | Name | John | Kyong |
|---|---|---|---|
| leg_press_a | Leg Press | 250lb × 3×6–10 | 60lb × 3×10–12 |
| leg_curl_a | Leg Curl | 80lb × 3×8–12 | 20lb × 3×8–12 |
| leg_ext_a | Leg Extension | 110lb × 3×8–12 | 25lb × 3×8–12 |
| hello_dolly_a | Hello Dollies | bodyweight × 3×10 | bodyweight × 3×10 |

### Day 3 — Pull A (Back · Biceps · Rear Delts)
| Exercise ID | Name | John | Kyong |
|---|---|---|---|
| lat_pulldown | Lat Pulldown | 100lb × 3×10–12 | 30lb × 3×10–12 |
| seated_row | Seated Row | 100lb × 3×10–12 | 30lb × 3×10–12 |
| face_pull | Face Pull | 40lb × 3×12–15 | 30lb × 3×10–15 |
| pallof_c | Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 4 — Push B (Shoulders · Triceps · Chest vol.)
| Exercise ID | Name | John | Kyong |
|---|---|---|---|
| shoulder_press_b | Shoulder Press | 50lb × 3×10–12 | 10lb × 3×10–12 |
| triceps_pushdown | Triceps Pushdown | 60lb × 3×10–12 | 20lb × 3×10–12 |
| pec_fly | Pec Fly | 115lb × 3×8–12 | 10lb × 3×10–12 |
| hello_dolly_b | Hello Dollies | bodyweight × 3×10 | bodyweight × 3×10 |

### Day 5 — Lower B (Quads · Hamstrings · Calves vol.)
| Exercise ID | Name | John | Kyong |
|---|---|---|---|
| leg_press_b | Leg Press (vol.) | 190lb × 3×10 | 50lb × 3×10 |
| ham_curl_b | Hamstring Curl | 65lb × 3×10 | 20lb × 3×10 |
| calf_raise | Calf Raise (Leg Press) | 150lb × 3×10 | 50lb × 3×10 |
| pallof_e | Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

---

## Progression Logic (stored in `state.weights`)
- Weights persist across weeks — they are NOT reset when a new week starts
- User manually adjusts weights via steppers during or after sessions
- Upper body steppers: ±5 lbs
- Lower body steppers: ±10 lbs
- No automatic progression calculation — progression is manual and note-driven

---

## Weekly Cycle
- Week model: Days 1–5 (Mon–Fri equivalent, not calendar-day-locked)
- `currentWeek.startDate` is set to the date "Start New Week" was pressed
- Days 6–7 (weekend) are not tracked in-app — no tabs for rest/cardio-only days
- Cardio data for each day lives inside `currentWeek.days[n].cardio`

---

## History Export
- On "Start New Week," the app auto-triggers a JSON download: `ironlog-history.json`
- The file contains the full `state.history` array (all archived weeks)
- This is the only data export mechanism — there is no import, no cloud backup

---

## GitHub Repo
- Repo: `https://github.com/jgarr16/ironlog`
- Live site: `https://jgarr16.github.io/ironlog/`
- Single file: `index.html`
- No CI/CD — manual upload via GitHub web UI or git push

---

## Removed Features (from prior Firebase architecture)
The following were present in the old Firebase-based version and have been removed in the current rebuild:

- Firebase Realtime Database and Storage
- Firebase Authentication (login screen, email/password sign-in, sign-out)
- AI Coach screen (multi-turn LLM chat with program context)
- Setup screen (Firebase credentials, LLM provider/key, start date, machine photo upload)
- Settings screen
- Firebase-hosted machine photos
- Body weight tracking card (morning + post-workout weights)
- 7-day cycle with Days 6–7 as explicit cardio/rest days
- Session history from Firebase (last 30 sessions with full detail modal)
- Automatic progression badges

---

## Constraints
- Must remain a **single HTML file** — no build tools, no bundler, no separate JS/CSS files
- No backend, no external dependencies beyond Google Fonts CDN
- Must work on iPhone Safari as primary mobile browser
- `localStorage` is the only persistence layer

---

## Known Issues / Next Steps
1. **Weight persistence across weeks** — `state.weights` survives new week starts, but there is no UI to review or bulk-edit historical weights
2. **History depth** — full set/rep logging is not captured (only done/not done per exercise); notes are the only qualitative record
3. **No import** — if localStorage is cleared, history is lost unless the user has the downloaded JSON; no restore path exists
4. **Cardio tracking completeness** — John's swim logs and Kyong's cardio mode selections are saved but not surfaced in the history list (only weekly notes appear)
5. **Re-add AI Coach** — the coach tab was removed in the rebuild; could be re-added as a standalone tab that calls Claude/ChatGPT/Gemini API with program context injected as system prompt
6. **Re-add body weight tracking** — a simple morning/post-workout weight card per day could be re-added to each day panel
