# IronLog — LLM prompt for `ironlog-history.json` review

Use the **“Full prompt (copy below)”** section as a single paste into your LLM. It bundles app context, JSON semantics, the **current** exercise lineup (so recommendations match what the app tracks today), id-to-name resolution, and instructions for legacy weeks whose ids no longer exist.

**Maintainers:** When you change the `DAYS` program in `index.html`, update the *same* program block inside the fenced prompt so exported history reviews stay aligned with the live app. The canonical source is the `const DAYS = [...]` array in `index.html`.

---

## Full prompt (copy everything inside the next fence)

```
You are reviewing exported workout data from IronLog, a small browser-based training log.

## What IronLog is
IronLog is a single-page progressive web app (one HTML file, no backend) used by two people, John and Kyong, who train together on a fixed 5-day lifting split. Navigation uses **Day 1 through Day 5** tabs — these are **program days**, not calendar weekday names. It is tuned for quick use on a phone at the gym. All live data lives in the browser under the localStorage key `ironlog_v2`. There is no cloud sync or accounts.

Repository (source): https://github.com/jgarr16/ironlog  
Live app: https://jgarr16.github.io/ironlog/

## Goals of the app
- Remember each person’s machine weights for every programmed exercise and carry them forward across weeks.
- Track whether each lifting day’s exercises were completed (checkboxes), plus daily extras: cardio checkboxes/fields, John’s pull-up and dip “assist” checkboxes, and John’s daily weight on the cardio card.
- Capture qualitative context via weekly notes and optional per-exercise notes (entered on each exercise’s detail screen).
- When a new training week is started (“Start New Week”), archive the just-finished week and automatically download a cumulative JSON file named `ironlog-history.json`. That file is the user’s primary long-term backup; the app does not re-import it automatically.

## How the program behaves in the app
- **Five lifting days:** Day 1 … Day 5, each with a fixed exercise list (see “Current program canon” below).
- **Weights complete for a day:** every exercise on that day’s list has `done[exerciseId] === true` for that day in `days`.
- **Week tab “cardio complete” for a day:** John has “Swam” checked AND Kyong has both “Treadmill” and “Stairway” checked. John’s lap count and body-weight number on the cardio card are stored; the strict cardio-complete summary follows the three-check rule above.
- **Assist checkboxes:** `assistDone.pullups` and `assistDone.dips` refer to John’s daily pull-up/dip tracking, not the main lift checklist.
- **Start New Week:** appends one archived week object to the in-memory history, resets the current week’s completion and notes, preserves rolling weights, then downloads the full history array as `ironlog-history.json`.

## Current program canon (authoritative for forward-looking recommendations)
All recommendations for “what to do next” must refer **only** to this lineup — exercise **id** strings as stored in JSON, human **name**, and default target prescription (weights are defaults from the app template; the user’s logged weights in history override these).

**Day 1 — Push A** (Chest · Shoulders · Triceps · Arms)
| id | name | John | Kyong |
|----|------|------|-------|
| chest_press | Chest Press | 125 lb, 3×6–10 | 20 lb, 3×8–12 |
| shoulder_press_a | Shoulder Press | 50 lb, 3×6–10 | 10 lb, 3×10 |
| triceps_ext | Triceps Extension | 60 lb, 3×8–12 | 10 lb, 3×8–12 |
| arm_curl | Arm Curl | 50 lb, 3×8–12 | 10 lb, 3×8–12 |
| pallof_a | Pallof Press | 50 lb, 3×10–12/side | 20 lb, 3×10–12/side |

**Day 2 — Lower A** (Quads · Hamstrings · Glutes · Hips)
| id | name | John | Kyong |
|----|------|------|-------|
| leg_press_a | Leg Press | 260 lb, 3×6–10 | 60 lb, 3×10–12 |
| leg_curl_a | Leg Curl | 90 lb, 3×8–12 | 20 lb, 3×8–12 |
| leg_ext_a | Leg Extension | 115 lb, 3×8–12 | 25 lb, 3×8–12 |
| hip_abduction | Hip Abduction | 50 lb, 3×10–15 | 10 lb, 3×10–15 |
| hello_dolly_a | Hello Dollies (bodyweight) | 3×10 | 3×10 |

**Day 3 — Pull A** (Back · Biceps · Rear Delts)
| id | name | John | Kyong |
|----|------|------|-------|
| lat_pulldown | Lat Pulldown | 100 lb, 3×10–12 | 30 lb, 3×10–12 |
| seated_row | Seated Row | 100 lb, 3×10–12 | 30 lb, 3×10–12 |
| face_pull | Face Pull | 40 lb, 3×12–15 | 30 lb, 3×10–15 |
| bicep_curl | Bicep Curl | 50 lb, 3×8–12 | 10 lb, 3×8–12 |
| ab_crunch | Ab Crunch | 100 lb, 3×10–15 | 50 lb, 3×10–15 |

**Day 4 — Push B** (Shoulders · Triceps · Chest vol. · Delts)
| id | name | John | Kyong |
|----|------|------|-------|
| shoulder_press_b | Shoulder Press | 50 lb, 3×10–12 | 10 lb, 3×10–12 |
| lateral_raise | Lateral Raise | 20 lb, 3×10–15 | 10 lb, 3×10–15 |
| triceps_pushdown | Triceps Pushdown | 60 lb, 3×10–12 | 20 lb, 3×10–12 |
| pec_fly | Pec Fly | 115 lb, 3×8–12 | 10 lb, 3×10–12 |
| hello_dolly_b | Hello Dollies (bodyweight) | 3×10 | 3×10 |

**Day 5 — Lower B** (Quads · Hamstrings · Calves vol. · Hips)
| id | name | John | Kyong |
|----|------|------|-------|
| leg_press_b | Leg Press (vol.) | 200 lb, 3×10 | 50 lb, 3×10 |
| ham_curl_b | Hamstring Curl | 65 lb, 3×10 | 20 lb, 3×10 |
| calf_raise | Calf Raise (Leg Press) | 160 lb, 3×10 | 50 lb, 3×10 |
| hip_adduction | Hip Adduction | 50 lb, 3×10–15 | 10 lb, 3×10–15 |
| pallof_e | Pallof Press | 50 lb, 3×10–12/side | 20 lb, 3×10–12/side |

**Resolving names in JSON:** Map any `done` key, `exerciseNotes` key, or weight key using the **id** column above. If the README on GitHub disagrees with this table, treat **this table as correct for the version of IronLog that shipped with this prompt** (it is copied from `index.html`). Optional cross-check: https://github.com/jgarr16/ironlog/blob/main/README.md

**Legacy / program drift:** Older archived weeks may list exercise ids that **do not** appear in the table above (e.g. after the program was edited in the app). For those ids:
- Report them as **legacy exercises** (quote the raw id).
- Summarize completion and notes for them **historically**.
- Do **not** map legacy ids onto current exercises unless the user’s notes make that relationship obvious.
- Forward-looking load, progression, and exercise-selection advice must use **only** the current canon table.

## What you are looking at: `ironlog-history.json`
The file is a JSON **array** of archived week objects, **chronological order: oldest first, newest last** (each “Start New Week” appends and re-exports the full cumulative array).

Unless the user trimmed the file, it may span many months. For this task, analyze **only** the range the user specifies (typically the **last 1–2 elements** = most recent one or two closed weeks).

### Archived week object (newer exports)
Each element is one closed week and usually includes:

- `startDate` — string `YYYY-MM-DD` for the week label (“Week of …”). Treat it as the user’s week anchor, not proof of calendar training dates.
- `notes` — weekly notes (may be empty).
- `exerciseNotes` — object: exercise **id** → free-text notes from that week (may be empty `{}`).
- `days` — object with keys `"1"` … `"5"`. Each day typically has:
  - `done` — map exercise id → `true` if checked off; missing or not `true` means not logged complete.
  - `cardio` — `john` (`swam`, `laps`, `weight`, etc.) and `kyong` (`treadmill`, `stairway`).
  - `assistDone` — `pullups`, `dips` booleans (John).
- `weightsAtWeekStart` and `weightsAtWeekEnd` — snapshots `{ [exerciseId]: { john: number, kyong: number } }` at archive time. Use them for within-week weight movement. Older exports may omit these fields.

## Limitations (do not overclaim)
- No sets/reps/RPE in structured form — only completion booleans, numeric weights, cardio fields, and text notes.
- Empty notes do not prove “no issues.”
- You are not their physician; keep injury/medical language cautious and prefer training logistics and conservative progression.

## Your task
1. State exactly which week(s) you analyzed (`startDate` and position in the array if clear).
2. For each analyzed week, note **program alignment:** whether all `done` keys match the **current** canon; list any **legacy** ids separately.
3. Summarize adherence (how many of 5 days fully complete on lifts), patterns, cardio/assist alignment.
4. Weight commentary: use `weightsAtWeekStart` vs `weightsAtWeekEnd` when present; reference exercises by **name** (from canon) and id when helpful.
5. Integrate `notes` and `exerciseNotes` into priorities.
6. Give **concrete, conservative** next 1–2 week recommendations tied explicitly to **current canon exercises** only. If recent weeks were mostly legacy, say that limitation clearly.

## Input data
[PASTE THE JSON ARRAY HERE OR ATTACH THE FILE]

**Scope:** [e.g. “the last two array elements” / “weeks starting YYYY-MM-DD and YYYY-MM-DD”]
```

---

## Optional: shrink context for the LLM

If the JSON is huge, paste only the last two ` {...} ` week objects (with valid JSON commas) instead of the full file, and say so in the Scope line.
