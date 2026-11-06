# Field Officer Performance Portal

**One-liner:** A single-file browser demo of an internal portal where a regional
reviewer tracks every field officer's territory assignment, KPI trend and
weighted review score across four strategic business units.

## Problem

Field officers are spread thin across territories, product lines and business
units. Their numbers lived in three places — a route/visit log, a sales-attainment
sheet and a marketing-spend pool — so nobody could answer, in one screen, "who is
this officer covering, is that territory earning back what it costs, and what
score does that add up to?" Reviews were rebuilt by hand every cycle, and the
weakest territories surfaced late.

## Solution

A rebuild of the original hosted, database-backed Streamlit portal as a
**static demo**: one `index.html`, no backend, no accounts, no network calls.
It keeps the real workflows — officer directory, per-officer assignment and KPI
trend, the review rubric with its weighted scoring, the investment-return engine
and the survey/link issue tracking — but runs them on synthetic demo data that
the browser generates.

## Features

- **Simulated sign-in** — any credentials work, plus a one-click "Use demo
  credentials" button. Labelled UI-only: nothing is transmitted or verified.
- **Group overview dashboard** — officers on file, mean review score, plan
  attainment, group field-return, flag count and forms closed; score by business
  unit, band distribution and a 12-month plan-vs-realisation chart (Chart.js);
  unit scorecard, watch list and top performers.
- **Officer directory** — free-text search (name, code, territory), filters by
  business unit and review band, sortable columns, coverage bars. Click a row for
  the full profile.
- **Officer detail** — assignment card (territory, outlets, product lines,
  reporting manager, tenure, monthly plan), KPI tiles, 12-month visits and
  attainment trend, monthly report log.
- **Live review scorecard** — six weighted criteria whose ratings start
  auto-derived from the KPI window and can be overridden with sliders; the
  weighted score, band and recommendation recompute in JavaScript on every move.
  Save/reset write to `localStorage` only.
- **Investment engine** — `I = F − B`, `N = I × margin`, `return = (I − O) / O`,
  shown per month with break-even and negative returns flagged rather than hidden.
- **Surveys & links** — issued forms per unit with progress bars, owner codes and
  an inert demo link; "simulate response" advances the demo counter.
- **Rubric page** — weights, evidence for each criterion, the auto-rating
  thresholds, the formulas and the score bands, exactly as coded.

## Tech stack

- HTML, CSS and vanilla JavaScript — one file, no framework, responsive.
- **Chart.js 4 via CDN** for the dashboard and trend charts (renders as text
  tables if the CDN is unavailable).
- Originally **Python + Streamlit** (pandas for the KPI maths, a hosted Postgres
  backend for the data); the demo replaces all of it with local computation.

## Architecture note

The shipped app was a **Streamlit** front end reading and writing a hosted
**Supabase** (Postgres) project: one table for the officer roster and territory
assignments, one for filed activity and survey links, plus review logic in a
Python calculation module that the UI called per officer. The demo has **zero
backend** — the roster, monthly KPI series and rubric are seeded as JS constants,
the 12-month series is generated deterministically from a hash of each officer
code, and every aggregation (scores, bands, returns, roll-ups) is computed in the
browser. `localStorage` holds only the reviewer's overrides and demo response
counters.

## Synthetic data disclaimer

NexaGroup, its units (Nexa Cement, Nexa AgroFeeds, Nexa Steel, Nexa Power), all
territories, officer names, employee codes, managers, survey forms and every
figure are **fictional**, generated in the browser to demonstrate the workflow. No
real company, person, location data or credentials appear anywhere. Sign-in is a
UI state, not authentication. This is the open demo of a confidential internal
tool: the original branding, data and integrations have been removed.

## Run locally

```bash
# from this folder
python -m http.server 8000     # then open http://localhost:8000
# or just double-click index.html — it works from file://
```

Use **Reset saved state** in the sidebar to restore the seed.

## Deploy on Vercel

1. Push this folder to a Git repository.
2. Vercel → **Add New → Project** → import the repo.
3. Framework preset **Other**; no build command and no output directory — it is a
   static single page, `index.html` is served as-is.
4. Deploy.

## Files

- `index.html` — the whole app (markup, styles, data and logic).
- `README.md` — quick start.
- `PROFILE.md` — this summary.

_Built by Abir._
