# IronLog — Project CLAUDE.md

## What This Is
A single-file PWA workout tracker. Deployed on GitHub Pages.

- Repo: https://github.com/jgarr16/ironlog
- Live: https://jgarr16.github.io/ironlog/
- Branch convention: feature branches (e.g. `claude/vigilant-spence`) merged to `main` via PR

## Architecture — Non-Negotiable Constraints
- **Single HTML file** (`index.html`) — no build tools, no bundler, no separate JS/CSS files
- **No Firebase** — all state in `localStorage` under key `ironlog_v2`
- **No frameworks** — vanilla JS only
- `manifest.json` for PWA; portrait orientation lock
- `index.html` + `manifest.json` are the only files that matter

## Program Structure
- 5-day weekly program: Day 1 through Day 5 (tabs, not weekday names)
- Days auto-complete (✓ checkmark) when all exercises are checked — no explicit lock button
- Weights persist across weeks; completion state resets on new week
- "Start New Week" auto-downloads a cumulative `ironlog-history.json` backup

## Data Model
- localStorage key: `ironlog_v2`
- History accumulates in the JSON export; current week state stays in localStorage

## Workflow
- Edit `index.html` directly — no compile step
- Test locally by opening in browser
- Push to `main` triggers GitHub Pages deploy automatically
- PRs from feature branches into `main`

## What Not to Do
- Do not introduce a build system, npm, or any bundler
- Do not add Firebase or any backend
- Do not split into multiple files
- Do not add a framework (React, Vue, etc.)
- Do not rename Day tabs to weekday names
