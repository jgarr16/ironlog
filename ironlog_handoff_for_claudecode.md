# IronLog — Claude Code Handoff Prompt

## Project Overview
I am building **IronLog**, a workout tracking web application for two users (John and Kyong) who train together 5 days/week at a 24 Hour Fitness gym. The app tracks exercises, weights, sets, reps, and provides AI-powered coaching.

---

## Current Tech Stack

### Frontend
- **Single static HTML file** (`index.html`) — all HTML, CSS, and JavaScript in one file
- No frameworks, no build process, no npm — vanilla JS only
- Hosted on **GitHub Pages** at `https://jgarr16.github.io/ironlog/`
- Mobile-first design, optimized for iPhone use at the gym
- Custom dark theme using CSS variables, Barlow Condensed + Barlow fonts from Google Fonts

### Backend / Data
- **Firebase Realtime Database** — stores workout sessions, exercise weight progression history, machine image URLs
- **Firebase Storage** — stores photos of gym machines (uploaded by user, displayed during workouts)
- Firebase SDK loaded via CDN (v10.7.0 compat mode) — no npm install
- All Firebase credentials stored in browser `localStorage` — NOT hardcoded in the file

### AI Coach
- Vendor-agnostic LLM integration — supports Claude (Anthropic), ChatGPT (OpenAI), Gemini (Google), and Custom/Local (Ollama etc.)
- Provider and API key stored in `localStorage`
- All calls made directly from the browser via `fetch()` to the respective API endpoints
- System prompt is built dynamically, injecting full workout program + current weights + recent session history

---

## Firebase Data Structure

### Realtime Database
```
/sessions/{pushId}
  date: "YYYY-MM-DD"
  dayNum: 1-7
  workoutName: "Push A"
  duration: 45  (minutes)
  t: 1772496000000  (Unix timestamp ms — used for sorting)
  log:
    {exerciseId}:
      john: { weight: 120, sets: [10, 10, 9], notes: "" }
      kyong: { weight: 20, sets: [10, 10, 10], notes: "" }
  cardio:
    john: { laps: 101, unit: "yards", swam: true }
    kyong: { elliptical: true, treadmill: false, stairway: false }
    notes: ""

/bodyweight/{YYYY-MM-DD}
  john: { morning: 185, post: 183, unit: "lbs" }
  kyong: { morning: 130, post: 129, unit: "lbs" }
  t: 1772496000000

/progress/{exerciseId}/{person}
  w: 120  (current working weight in lbs)
  t: 1772496000000  (timestamp of last update)

/imgs/{exerciseId}
  (string) Firebase Storage download URL for machine photo
```

### Firebase Storage
```
/machines/{exerciseId}  — JPEG/PNG photo of the gym machine
```

---

## Current Firebase Security Rules (TEMPORARY — needs tightening)

### Realtime Database
```json
{
  "rules": {
    ".read": "now < 1775451600000",
    ".write": "now < 1775451600000",
    "sessions": {
      ".indexOn": ["t"]
    }
  }
}
```
This uses a time-based expiry (expires ~April 6, 2026). This is temporary and needs to be replaced with proper authentication-based rules.

### Storage
```
service firebase.storage {
  match /b/{bucket}/o {
    match /machines/{imageId} {
      allow read, write: if true;
    }
  }
}
```
This is fully open. Needs to be locked down.

---

## Security Hardening — TODO (not yet implemented)

The app currently has no Firebase Authentication. The security roadmap is:

1. **Add Firebase Authentication** — simplest approach for a private 2-person app is Email/Password auth or Google Sign-In
2. **Lock Realtime Database rules** to authenticated users only:
```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null",
    "sessions": {
      ".indexOn": ["t"]
    }
  }
}
```
3. **Lock Storage rules** to authenticated users only:
```
service firebase.storage {
  match /b/{bucket}/o {
    match /machines/{imageId} {
      allow read, write: if request.auth != null;
    }
  }
}
```
4. **Add login screen** to the app — before the setup/home screen, check `firebase.auth().currentUser` and redirect to login if not authenticated
5. **Optional:** scope data per user with UID paths (e.g. `/users/{uid}/sessions/`) if you ever want to support more than 2 users independently

---

## Workout Program — All 5 Days

### Day 1 — Push A (Chest · Shoulders · Triceps)
| Exercise | John | Kyong |
|---|---|---|
| Chest Press (Machine — horizontal handles) | 120lb × 3×6–10 | 20lb × 3×8–12 |
| Shoulder Press (Machine) | 50lb × 3×6–10 | 10lb × 3×10 |
| Triceps Extension (Machine) | 65lb × 3×8–12 | 10lb × 3×8–12 |
| Plank (Core) | 3×60 sec | 3×45–60 sec |
| Alt: Pallof Press | 50lb 3×10–12/side | 10lb 3×10–12/side |

### Day 2 — Lower A (Quads · Hamstrings · Glutes)
| Exercise | John | Kyong |
|---|---|---|
| Leg Press | 250lb × 3×6–10 | 60lb × 3×10–12 |
| Leg Curl | 80lb × 3×8–12 | 20lb × 3×8–12 |
| Leg Extension | 110lb × 3×8–12 | 25lb × 3×8–12 |
| Plank (Core) | 3×60 sec | 3×45–60 sec |
| Alt A: Cable Crunch | 135lb 3×10 | 50lb 3×10 |
| Alt B: Pallof Press | 50lb 3×10–12/side | 10lb 3×10–12/side |

### Day 3 — Pull A (Back · Biceps · Rear Delts)
| Exercise | John | Kyong |
|---|---|---|
| Lat Pulldown | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Seated Row | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Face Pull (Cable/Rope) | 40lb × 3×12–15 | 30lb × 3×10–15 |
| Pallof Press (Core) | 50lb × 3×10–12/side | 10lb × 3×10–12/side |

### Day 4 — Push B (Shoulders · Triceps · Chest volume)
| Exercise | John | Kyong |
|---|---|---|
| Shoulder Press (volume focus) | 50lb × 3×10–12 | 10lb × 3×10–12 |
| Triceps Pushdown (Cable) | 60lb × 3×10–12 | 20lb × 3×10–12 |
| Pec Fly (Machine) | 110–120lb × 3×8–12 | 10lb × 3×10–12 |
| Pallof Press (Core) | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 5 — Lower B (Quads · Hamstrings · Calves volume)
| Exercise | John | Kyong |
|---|---|---|
| Leg Press (volume focus) | 190lb × 3×10 | 50lb × 3×10 |
| Hamstring Curl (variation) | 65lb × 3×10 | 20lb × 3×10 |
| Leg Press Calf Raise | 150lb × 3×10 | 50lb × 3×10 |
| Cable Crunch | 135lb × 3×10 | 50lb × 3×10 |

---

## Exercise IDs (used as Firebase keys)
```
chest_press, shoulder_press_a, triceps_ext, plank_a,
leg_press_a, leg_curl_a, leg_ext_a, plank_b,
lat_pulldown, seated_row, face_pull, pallof_c,
shoulder_press_b, triceps_pushdown, pec_fly, pallof_d,
leg_press_b, ham_curl_b, calf_raise, cable_crunch
```

---

## Progression Logic
- **Upper body:** hit top of rep range all 3 sets → +5 lbs next session
- **Lower body:** hit top of rep range all 3 sets → +10 lbs next session
- **Timed holds:** hit max time all 3 sets → +5 sec next session
- Below minimum reps → maintain weight
- Progression badges shown during workout when last session earned an increase

---

## App Screens & Navigation
1. **Setup** — first-time Firebase + LLM credentials entry (stored in localStorage)
2. **Home** — shows today's day in 7-day cycle (Days 1–5 = lifting, Days 6–7 = cardio/rest), day pill selector override with completion checkmarks, tappable exercise preview list, cardio card, body weight card
3. **Exercise Preview Modal** — tap any exercise from Home to see details (weight, reps, photo) without starting a workout
4. **Active Workout** — one exercise at a time, machine photo, weight/target for John and Kyong with +/- stepper buttons, set-by-set logging with +/- rep steppers, optional notes field per exercise, Previous/Next navigation, exit button; cardio card and body weight card appear at the bottom
5. **Workout Complete** — duration, sets logged, progressions earned with next weights
6. **History** — last 30 sessions from Firebase, reverse chronological, with cardio and body weight mini-blocks per entry; tap any entry for full session detail
7. **Session Detail Modal** — full breakdown of a history entry: sets/reps/weights per exercise, notes, cardio log, body weight
8. **Coach** — multi-turn AI chat with full program context injected as system prompt
9. **Settings** — update Firebase credentials, LLM provider/key, start date, upload machine photos

Bottom navigation: Home · History · Coach · Settings (hidden during active workout)

---

## Cardio Tracking
Cardio is now tracked in the app (not just AI coach context). The cardio card appears on the Home screen and Active Workout screen:
- **John:** swim lap counter with yards/meters toggle + "Did swim today" checkbox (pre-fills last session's unit preference)
- **Kyong:** checkboxes for elliptical, treadmill, stairway
- **Notes:** shared text field for both users
- **Day 7:** cardio-only day in the 7-day cycle — lifting cards are hidden, only cardio and body weight are shown
- Cardio data is saved with the session under `sessions/{pushId}/cardio`

---

## Weekly Schedule
- **Program start date:** March 3, 2026 (Tuesday)
- Day index auto-calculated from start date using `(daysSinceStart % 7)`
- Days 1–5: lifting days (Push A, Lower A, Pull A, Push B, Lower B)
- Days 6–7: cardio/rest — no lifting cards shown, only cardio and body weight
- User can override day manually via day pill selector on Home screen

---

## GitHub Repo
- Repo: `https://github.com/jgarr16/ironlog`
- Live site: `https://jgarr16.github.io/ironlog/`
- Single file: `index.html`
- No CI/CD — manual upload via GitHub web UI or git push

---

## Known Issues / Next Steps
1. **Security** — Firebase rules need auth gating (highest priority before sharing app with anyone)
2. **Test mode** — add a toggle that writes to `/test/` path prefix in Firebase so dummy data can be tested without polluting real session history
3. **Progressive overload evolution** — around week 4–5, AI coach should proactively suggest new exercises to add to the rotation based on session history
4. **Image optimization** — iPhone photos are 3–5 MB; consider compressing before upload to Firebase Storage to reduce bandwidth
5. **Offline support** — app currently requires internet; could add Firebase offline persistence for gym use in poor signal areas
6. **Export** — add ability to export session history as CSV or JSON for external analysis

### Completed
- [x] Day completion indicators — checkmarks on day pills (1–7) when workout is done for that day
- [x] History detail view — tap any history item for full session breakdown (sets, reps, weights, cardio, body weight)
- [x] Cardio tracking — 7-day cycle with swim laps, Kyong's cardio modes, notes
- [x] Daily body weight tracking — morning and post-workout weights, seeded from yesterday
- [x] Optional per-exercise notes field

---

## Important Constraints
- Must remain a **single HTML file** — no build tools, no bundler, no separate JS/CSS files
- Must remain **vendor-agnostic** for LLM — no hardcoded dependency on any single AI provider
- Firebase SDK must be loaded via CDN, not npm
- No credentials hardcoded in source — always stored in localStorage
- Must work on iPhone Safari as primary mobile browser
