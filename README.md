# Field Officer Performance Portal — demo

One self-contained `index.html`: a field officer directory with territory
assignments, per-officer KPI trends, a weighted review scorecard that recomputes
live in JavaScript, a group overview dashboard and a survey/link tracker.

Read **[PROFILE.md](PROFILE.md)** for the full write-up — problem, solution,
features, tech stack, architecture and the synthetic-data disclaimer.

## Quick start

```bash
python -m http.server 8000   # then open http://localhost:8000
# or open index.html directly in a browser — no install, no build
```

Sign in with **any** username and password (or "Use demo credentials"). That
login is UI-only: there is no server, no account check and no data leaves the
browser. Reviewer overrides and demo survey responses live in `localStorage`;
"Reset saved state" in the sidebar restores the seed.

## Notes

- Charts use Chart.js from a CDN. Offline, the page still renders — the chart
  boxes stay empty and the same numbers appear in the tables.
- Deploying to Vercel: import the repo, preset **Other**, no build command,
  no output directory. `index.html` is served as-is.
- **Fictional data only.** NexaGroup, its business units, all officer names,
  employee codes, territories and figures are invented and generated in the
  browser — this is the open demo of an internal portal, with the original
  branding, data and integrations removed.

_Built by Abir._
