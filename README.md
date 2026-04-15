# IronLog

A lightweight, mobile-first workout tracker for two people training together. Single HTML file, no build tools, no backend — all state in `localStorage`.

**Live app:** [jgarr16.github.io/ironlog](https://jgarr16.github.io/ironlog/)

---

## Architecture

No Firebase. No npm. No frameworks. One file.

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML/CSS/JS — single `index.html` |
| Hosting | GitHub Pages |
| Storage | Browser `localStorage` (all data stays on device) |
| PWA | `manifest.json` with portrait orientation lock |

---

## Features

### Layout
- **5-day tab layout** — Day 1 through Day 5, plus a Notes tab
- **Header** — "IRONLOG" left, "Week of [date]" right
- **PWA-ready** — installable on iOS/Android, portrait lock via manifest

### Each Day Tab
- **Collapsible top section** containing:
  - John's daily weight stepper
  - Cardio card: John (swim laps + unit toggle + did-swim checkbox) and Kyong (treadmill / stairway checkboxes)
  - Pull-ups and dips checkboxes for John
- **Weights grid** below — all exercises displayed at once, transposed layout with John | Kyong columns
- **Auto-✓ on tab** when all exercises for that day are checked — no lock button needed
- **Editable weights** — tap to adjust; machine image URL supported per exercise

### Notes Tab
- Weekly notes field
- Full session history — tap any entry to expand details
- **Start New Week** button — auto-downloads cumulative `ironlog-history.json` before resetting

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

## Data

All data lives in `localStorage`. No sync, no cloud, no accounts.

On **Start New Week**, the app exports a cumulative `ironlog-history.json` to your Downloads folder before clearing the current week. Keep that file — it's your only backup.

---

## Getting Started

1. Fork or clone this repo
2. Push `index.html` and `manifest.json` to your repo
3. Enable GitHub Pages (Settings → Pages → Branch: main)
4. Open the live URL — no setup screen, no credentials needed

---

## License

Personal use. Not intended for public distribution.
