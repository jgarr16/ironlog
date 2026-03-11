# IronLog 🏋️

A lightweight, mobile-first workout tracking app for two people training together. Built as a single HTML file, hosted on GitHub Pages, with Firebase for data persistence and a pluggable AI coaching layer.

**Live app:** [jgarr16.github.io/ironlog](https://jgarr16.github.io/ironlog/)

---

## Features

- **5-day push/pull/legs program** — pre-loaded for John and Kyong with individual starting weights and rep targets
- **Progressive overload tracking** — automatically flags weight increases when you hit the top of your rep range across all sets
- **Exercise preview mode** — tap any exercise to view details (weights, reps, photos) without starting a workout
- **Stepper controls** — adjust weights (+/- 5 lbs) and reps (+/- 1) with tap-friendly buttons, no keyboard needed
- **Flexible workout navigation** — Previous/Next buttons to move between exercises, exit button to leave mid-workout
- **Machine photos** — upload photos of your gym equipment; they display during each exercise
- **Cardio tracking** — 7-day cycle with a dedicated cardio card; John logs swim laps (yards/meters toggle + "did swim" checkbox), Kyong logs elliptical/treadmill/stairway; shared notes field; Day 7 is cardio-only
- **Daily body weight tracking** — morning and post-workout weight for each person, seeded from the previous day's values
- **Exercise notes** — optional per-exercise notes field logged with each session
- **Day completion indicators** — day pills on the home screen show a checkmark when that day's workout has been completed this week
- **Session history detail** — tap any history entry to see the full breakdown: sets, reps, weights, cardio, and body weight for that session
- **AI Coach** — multi-turn chat with full program context and session history injected automatically
- **Vendor-agnostic LLM** — works with Claude, ChatGPT, Gemini, or any local model (Ollama etc.)
- **Session history** — every workout logged to Firebase, never drifts or guesses
- **Mobile-first** — designed for iPhone use at the gym

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML/CSS/JS — single `index.html` |
| Hosting | GitHub Pages |
| Database | Firebase Realtime Database |
| File Storage | Firebase Storage (machine photos) |
| AI | Anthropic / OpenAI / Google / Custom API |

No build tools. No npm. No frameworks. Just one file.

---

## Getting Started

### 1. Fork or clone this repo
```bash
git clone https://github.com/jgarr16/ironlog.git
```

### 2. Set up Firebase
1. Create a project at [console.firebase.google.com](https://console.firebase.google.com)
2. Enable **Realtime Database** and **Storage**
3. Grab your project credentials from Project Settings → General → Web App

### 3. Deploy to GitHub Pages
- Push `index.html` to your repo
- Go to **Settings → Pages → Branch: main** → Save

### 4. Configure the app
Open the live URL, and on first load you'll see a setup screen. Enter:
- Firebase API Key, Project ID, Database URL, Storage Bucket
- Your LLM provider and API key (Claude, OpenAI, Gemini, or custom)
- Your program start date

All credentials are stored in your browser's `localStorage` — nothing is hardcoded in the source.

---

## Firebase Security Rules

### Realtime Database
The app requires an index on the `t` field for session sorting. Add this to your rules:

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

> ⚠️ During initial setup, you may use time-based or open rules temporarily. Lock these down with `auth != null` before sharing the app with anyone.

### Storage
```
service firebase.storage {
  match /b/{bucket}/o {
    match /machines/{imageId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## Data Structure

```
Firebase Realtime Database
│
├── sessions/
│   └── {pushId}/
│       ├── date: "YYYY-MM-DD"
│       ├── dayNum: 1–7
│       ├── workoutName: "Push A"
│       ├── duration: 45
│       ├── t: 1772496000000  (Unix ms timestamp — for sorting)
│       ├── log/
│       │   └── {exerciseId}/
│       │       ├── john: { weight: 120, sets: [10, 10, 9], notes: "" }
│       │       └── kyong: { weight: 20, sets: [10, 10, 10], notes: "" }
│       └── cardio/
│           ├── john: { laps: 101, unit: "yards", swam: true }
│           ├── kyong: { elliptical: true, treadmill: false, stairway: false }
│           └── notes: ""
│
├── bodyweight/
│   └── {YYYY-MM-DD}/
│       ├── john: { morning: 185, post: 183, unit: "lbs" }
│       ├── kyong: { morning: 130, post: 129, unit: "lbs" }
│       └── t: 1772496000000
│
├── progress/
│   └── {exerciseId}/
│       ├── john: { w: 125, t: 1772496000000 }
│       └── kyong: { w: 25, t: 1772496000000 }
│
└── imgs/
    └── {exerciseId}: "https://firebasestorage.googleapis.com/..."

Firebase Storage
└── machines/
    └── {exerciseId}  (JPEG or PNG)
```

---

## Workout Program

### Day 1 — Push A · Chest · Shoulders · Triceps
| Exercise | John | Kyong |
|---|---|---|
| Chest Press | 120lb × 3×6–10 | 20lb × 3×8–12 |
| Shoulder Press | 50lb × 3×6–10 | 10lb × 3×10 |
| Triceps Extension | 65lb × 3×8–12 | 10lb × 3×8–12 |
| Plank / Pallof Press | 3×60 sec | 3×45–60 sec |

### Day 2 — Lower A · Quads · Hamstrings · Glutes
| Exercise | John | Kyong |
|---|---|---|
| Leg Press | 250lb × 3×6–10 | 60lb × 3×10–12 |
| Leg Curl | 80lb × 3×8–12 | 20lb × 3×8–12 |
| Leg Extension | 110lb × 3×8–12 | 25lb × 3×8–12 |
| Plank / Cable Crunch / Pallof Press | 3×60 sec | 3×45–60 sec |

### Day 3 — Pull A · Back · Biceps · Rear Delts
| Exercise | John | Kyong |
|---|---|---|
| Lat Pulldown | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Seated Row | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Face Pull | 40lb × 3×12–15 | 30lb × 3×10–15 |
| Pallof Press | 50lb × 3×10–12/side | 10lb × 3×10–12/side |

### Day 4 — Push B · Shoulders · Triceps · Chest (volume)
| Exercise | John | Kyong |
|---|---|---|
| Shoulder Press | 50lb × 3×10–12 | 10lb × 3×10–12 |
| Triceps Pushdown | 60lb × 3×10–12 | 20lb × 3×10–12 |
| Pec Fly | 110–120lb × 3×8–12 | 10lb × 3×10–12 |
| Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 5 — Lower B · Quads · Hamstrings · Calves (volume)
| Exercise | John | Kyong |
|---|---|---|
| Leg Press | 190lb × 3×10 | 50lb × 3×10 |
| Hamstring Curl | 65lb × 3×10 | 20lb × 3×10 |
| Leg Press Calf Raise | 150lb × 3×10 | 50lb × 3×10 |
| Cable Crunch | 135lb × 3×10 | 50lb × 3×10 |

---

## Progression Rules

- **Upper body:** hit top of rep range all 3 sets → **+5 lbs** next session
- **Lower body:** hit top of rep range all 3 sets → **+10 lbs** next session
- **Timed holds:** hit max time all 3 sets → **+5 sec** next session
- Below minimum → maintain current weight

---

## Roadmap

- [ ] Firebase Authentication (lock down security rules)
- [ ] Test mode (write to `/test/` path prefix, delete without affecting real data)
- [ ] Image compression before upload
- [ ] Firebase offline persistence for poor gym signal
- [ ] AI-driven program evolution at weeks 4–5 (new exercise suggestions)
- [ ] CSV/JSON export of session history
- [x] Day completion indicators on home screen
- [x] History detail view (tap to see full session breakdown)
- [x] Cardio tracking (swim laps + Kyong's cardio modes + notes)
- [x] Daily body weight tracking (morning + post-workout)
- [x] Optional per-exercise notes

---

## Importing Historical Data

Sessions can be bulk-imported via Firebase Console → Realtime Database → Import JSON. See the data structure above for the required format.

---

## License

Personal use. Not intended for public distribution.
