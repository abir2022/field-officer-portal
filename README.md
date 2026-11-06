# NexaField — Field Officer Performance Portal (demo)

A single self-contained `index.html` that rebuilds an internal field-force review
portal as a static demo: a reviewer signs in, sees a group dashboard of weighted
review scores, opens any officer's territory assignment and 12-month KPI trend,
and drags a six-criterion rubric whose 100-point score, band and recommendation
recompute on every keystroke. Everything is computed in the browser from a
synthetic roster — no backend, no accounts, no network calls beyond the chart
library and web fonts.

[![Status][status-badge]][demo-link]
[![Data][data-badge]][demo-link]
[![Deploy with Vercel][vercel-badge]][vercel-link]

[status-badge]: https://img.shields.io/badge/status-live%20demo-3B82F6
[data-badge]: https://img.shields.io/badge/data-synthetic-D97706
[vercel-badge]: https://vercel.com/button
[demo-link]: https://field-officer-portal.vercel.app
[vercel-link]: https://vercel.com/new

![Portal overview dashboard](assets/banner.png)

## Quick start

```bash
# 1. open it directly — no install, no build
start index.html                       # Windows (macOS: open index.html)
# 2. or serve the folder
python -m http.server 8000             # then http://localhost:8000
```

Sign in with **any** portal ID and password, or press **Use demo credentials**.
That sign-in is UI-only: nothing is transmitted or verified. Reviewer overrides
and simulated survey responses persist in `localStorage` under
`nexafield.portal.v2`; **Reset saved state** in the sidebar restores the seed.

## What is in it

- **Group overview** — six KPI tiles (roster, mean review score, plan attainment,
  field investment return, flag count, forms closed), score by business unit,
  band distribution, 12-month plan-vs-realisation chart, unit scorecard,
  watch list and top performers.
- **Officer directory** — search, unit and band filters, sort control, card grid
  with band colour chips and coverage bars, plus a dense sortable table view.
- **Officer review sheet** — assignment facts, KPI tiles, 12-month visits and
  attainment trend, six weighted criteria with live sliders, auto-vs-manual
  rating badges, save/reset, monthly field-investment ledger and report log.
- **Surveys & links** — issued forms per unit, progress bars, owner codes and an
  inert demo link; "simulate response" only advances a local counter.
- **Rubric page** — weights, evidence per criterion, auto-rating thresholds, the
  increment/return formulas and the score bands, exactly as coded.
- Dark-navy design system with a light toggle (Fira Sans / Fira Code), sticky
  product bar, keyboard-focusable rows and cards, and reduced-motion support.

## Demo data disclaimer

NexaGroup, its units (Nexa Cement, Nexa AgroFeeds, Nexa Steel, Nexa Power), all
officer names, employee codes, territories, managers, survey forms and every
figure are **fictional**, generated deterministically in the browser. The login
is a UI state, not authentication. This is the open demo of a confidential
internal tool — the original branding, data and integrations have been removed.
Read **[PROFILE.md](PROFILE.md)** for the rubric methodology, formulas and
system design.

## Deploy

Import the repo on Vercel — framework preset **Other**, no build command, no
output directory; `index.html` is served as-is.

## License

Portfolio demo, no licence file ships with it: the code is my own work and the
data is fictional, so treat it as reference-only.

_Built by Abir._
