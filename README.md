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
- **5-day tab layout** — Day 1 through Day 5, plus a Week tab
- **Header** — "IRONLOG" left, "Week of [date]" right
- **PWA-ready** — installable on iOS/Android, portrait lock via manifest

### Each Day Tab
- **Collapsible top section** containing:
  - John's daily weight stepper
  - Cardio card: John (swim laps + unit toggle + did-swim checkbox) and Kyong (treadmill / stairway checkboxes)
  - Pull-ups and dips checkboxes for John
- **Weights section** below — all exercises displayed at once
- **Auto-✓ on tab** when all exercises for that day are checked — no lock button needed
- **Stack-aware weight controls** — machine movements step through valid stack increments
- **Exercise thumbnails** — dark `_dk` machine images on day cards
- **Tap thumbnail for dedicated exercise page**

### Dedicated Exercise Page
- Full-size exercise image view (uses dark image variants)
- Left/right arrows to move across exercises **within the same day**
- Edge arrows stay visible but dim/disable at first/last exercise
- Per-exercise notes saved into current-week state and exported with history
- Stack-table reference pulled from `images/*.md` (when available), with current J/K weights highlighted

### Week Tab
- Weekly notes field
- Compact day-by-day status bars:
  - Cardio
  - Pull-ups
  - Dips
  - Weights
- Green checks for complete items; red `✕` for incomplete items
- Each day bar is clickable to jump directly to that day tab
- **Start New Week** button — auto-downloads cumulative `ironlog-history.json` before resetting
- Previous week history cards with weekly and per-exercise notes

---

## Workout Program

### Day 1 — Push A · Chest · Shoulders · Triceps
| Exercise | John | Kyong |
|---|---|---|
| Chest Press | 125lb × 3×6–10 | 20lb × 3×8–12 |
| Shoulder Press | 50lb × 3×6–10 | 10lb × 3×10 |
| Triceps Extension | 60lb × 3×8–12 | 10lb × 3×8–12 |
| Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 2 — Lower A · Quads · Hamstrings · Glutes
| Exercise | John | Kyong |
|---|---|---|
| Leg Press | 260lb × 3×6–10 | 60lb × 3×10–12 |
| Leg Curl | 90lb × 3×8–12 | 20lb × 3×8–12 |
| Leg Extension | 115lb × 3×8–12 | 25lb × 3×8–12 |
| Hello Dollies (bodyweight) | 3×10 | 3×10 |

### Day 3 — Pull A · Back · Biceps · Rear Delts
| Exercise | John | Kyong |
|---|---|---|
| Lat Pulldown | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Seated Row | 100lb × 3×10–12 | 30lb × 3×10–12 |
| Face Pull | 40lb × 3×12–15 | 30lb × 3×10–15 |
| Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

### Day 4 — Push B · Shoulders · Triceps · Chest (volume)
| Exercise | John | Kyong |
|---|---|---|
| Shoulder Press | 50lb × 3×10–12 | 10lb × 3×10–12 |
| Triceps Pushdown | 60lb × 3×10–12 | 20lb × 3×10–12 |
| Pec Fly | 115lb × 3×8–12 | 10lb × 3×10–12 |
| Hello Dollies (bodyweight) | 3×10 | 3×10 |

### Day 5 — Lower B · Quads · Hamstrings · Calves (volume)
| Exercise | John | Kyong |
|---|---|---|
| Leg Press (vol.) | 200lb × 3×10 | 50lb × 3×10 |
| Hamstring Curl | 65lb × 3×10 | 20lb × 3×10 |
| Calf Raise (Leg Press) | 160lb × 3×10 | 50lb × 3×10 |
| Pallof Press | 50lb × 3×10–12/side | 20lb × 3×10–12/side |

---

## Data

All data lives in `localStorage`. No sync, no cloud, no accounts.

On **Start New Week**, the app exports a cumulative `ironlog-history.json` to your Downloads folder before clearing the current week. Keep that file — it's your only backup.

For reviewing those exports with an LLM (program context, JSON semantics, current exercise ids), use the copy-paste prompt in [docs/llm-history-review-prompt.md](docs/llm-history-review-prompt.md).

---

## Getting Started

1. Fork or clone this repo
2. Push `index.html` and `manifest.json` to your repo
3. Enable GitHub Pages (Settings → Pages → Branch: main)
4. Open the live URL — no setup screen, no credentials needed

For local testing of `exercise-view.html` stack markdown loading, run a local server (instead of opening files directly):

```sh
python3 -m http.server 8080
```

Then open `http://localhost:8080/`.

---

## License

Personal use. Not intended for public distribution.
