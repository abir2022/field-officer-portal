# NexaField — Field Officer Performance Portal

**One-liner:** A single-file browser demo of an internal portal in which a
regional reviewer scores every field officer against a weighted six-criterion
rubric whose inputs are visit coverage, plan attainment and return on field
spend.

## 1. Abstract

Field-force performance is usually reviewed from three disconnected ledgers: a
route/visit log, a sales-attainment sheet and a marketing-spend pool. Each is
credible on its own, and none answers the question a reviewer actually asks —
what score does this officer earn this cycle, and what should happen next? This
artifact rebuilds such a review portal as a static, self-contained demo. It
encodes a weighted rubric (coverage 20 %, attainment 25 %, field investment
return 20 %, reporting discipline 15 %, initiative 10 %, capability 10 %) that
maps each observed KPI onto a 1–5 scale, normalises the marketing increment by
unit margin so dissimilar business units are comparable, and rolls the sheet out
of 100 points into a band with a standard recommendation. Ratings start
auto-derived from a three-month KPI window and stay overridable by the reviewer,
with the recompute done live in JavaScript. All names, codes, territories and
figures are synthetic and generated deterministically in the browser. The
contribution is the decision model plus a dense, honest UI around it — not a
claim about any real company's performance.

## 2. Problem Framing

A sales organisation spreading field officers across territories, product lines
and business units accumulates review data faster than it can interpret it. In
the original working tool the reviewer assembled each cycle by hand: pull visits
from the field log, attainment from the sales sheet, spend from the marketing
pool, then reconcile them in a spreadsheet and write a recommendation. Three
failure modes followed. Weak territories surfaced late, because nothing ranked
officers continuously. Comparisons across units were unfair, because a cement
tonne and a kilowatt-hour do not carry the same profit. And scores were opaque,
because the arithmetic lived in a private file rather than on screen. The portal
exists to make the review sheet itself the product: one screen per officer where
the criteria, weights, thresholds and resulting band are visible and disputable.

## 3. Related Practice

Analysts doing this work reach for a small set of standard instruments. Sales
operations teams run **management by exception** on coverage and attainment, so
only off-plan territories consume reviewer attention. Trade-spend measurement
uses **incremental-vs-baseline accounting** — spend is judged against an organic
run-rate or a prior-year period, never against gross sales — and expresses the
result as a return-on-spend multiple. Where units differ in profitability,
practice normalises an increment into **contribution margin** before comparing
units, the same correction a portfolio review applies before ranking SKUs or
branches. Multi-criterion staff review is standard **weighted scoring** with
behaviourally anchored rating scales: a small number of criteria, explicit
weights, and one to five anchors so different reviewers converge. Distribution
management adds the **forced-band** idea — scores map to named outcomes with a
default action, which is what turns a number into a decision. The rubric here is
a plain composition of those four instruments, deliberately kept legible rather
than clever.

## 4. Methodology & Data Design

Data enters conceptually as three tables that the shipped tool kept in a hosted
database: an officer roster (code, unit, territory, grade, tenure, reporting
manager, mapped outlets, product lines, monthly plan), a monthly activity ledger
(reports filed, visits, forms, intel items, coaching modules), and a spend pool
per officer-month. In the demo all three are seeded as JavaScript constants and
the monthly series is **generated deterministically**: each officer's twelve
months are drawn from a seeded hash of the officer code, modulated by a seasonal
sine and by a per-officer baseline multiplier, so the same roster renders the
same numbers every visit and every visitor.

Transformation is three steps. (1) Aggregate the last **three closed months**
into the review window. (2) Derive the KPIs:

- attainment `A = sales_actual / sales_target`
- coverage `C = visits_done / visits_target`
- reporting `R = forms_done / forms_assigned`
- increment `I = F − B` (realisation minus organic baseline `B`)
- spend pool `O`, and `t = (I − O) / O`; margin-normalised `N = I × m` and `b = (N − O) / O`
- group return `= (ΣI − ΣO) / ΣO`

(3) Map each KPI onto a 1–5 anchor by published thresholds (e.g. attainment
≥110 % = 5, ≥100 % = 4, ≥88 % = 3, ≥72 % = 2, else 1; return ≥2.00x = 5 down to
below zero = 1). The sheet is

`score = Σ (rating / 5) × weight × 100`, weights summing to 1.00.

The margin `m` is a per-unit constant (9 %–24 %), which is the normalisation
step: `N` puts every unit's increment on a common profit axis before `b` is
compared across the group. Bands: ≥85 Outstanding, ≥70 Strong, ≥55 Meets
expectations, ≥40 Needs improvement, else At risk — each carrying one default
recommendation. Every threshold above is stated verbatim on the Rubric page.

## 5. Analytical Framework

The UI encodes a decision rule, not just a display. An officer is only as strong
as the weakest weighted criterion, so the scorecard keeps all six visible at
once and shows the points each contributes. Two flags override the headline
number: a sheet under 55, or a top-line field return below zero — an officer can
hit plan while buying that plan at a loss, and the watch list exists to surface
exactly that. Cross-unit ranking is read from `return_bottom` rather than
`return_top`, because margin normalisation is what makes the comparison honest.
Reviewer judgement enters through overrides, and the framework keeps the audit
trail visible: the auto-derived score stays on screen beside the manual one, and
each row badges itself *auto* or *manual*. The rubric therefore separates three
things that get conflated in most review decks — measured performance, the
mapping of performance onto a scale, and human adjustment — and lets a
disagreement be located in whichever layer caused it.

## 6. System Design

The shipped application was a Python/Streamlit front end reading and writing a
hosted Postgres project, with the scoring logic in a separate calculation
module. This demo is **one file** — markup, tokens, styles, data and logic —
served statically. Structure: seed constants (roster, units, forms, rubric,
bands) → a memoised per-officer 12-month series → a `REVIEW` object computed once
at load → five pure view functions returning HTML strings → one delegated
event layer → Chart.js 4 for four charts. Every aggregation runs in the browser,
so the demo needs no build step, no environment variables and no server, and
opens from `file://`. State is split deliberately: the review window is derived
and read-only, while the reviewer's overrides and simulated form responses are
the only writes, persisted to `localStorage`. Presentation is a token layer —
dark navy surfaces, Fira Code for figures and headings, Fira Sans for prose, one
radius scale, a fixed chart palette with an amber accent, and a light-theme
variant that remaps the same custom properties. The roster table and the card
grid render the same records so the layout choice is genuinely a presentation
concern. Motion is feedback only: 2 px hover lifts, focus-visible rings and a
sub-450 ms first-paint stagger that `prefers-reduced-motion` suppresses. If the
CDN is unreachable, charts degrade to a labelled empty state and every figure
remains in the tables.

## 7. Findings & Design Decisions

On the synthetic seed, the model produces a usable spread — twelve officers from
39 to 90 out of 100 — which was the design target: a rubric that parks everyone
in one band ranks nothing. The heaviest criterion (attainment, 25 %) and the ROI
criterion (20 %) pull in different directions for exactly the cases reviews get
wrong, so the "high attainment, negative return" officer is the most interesting
row on the dashboard and the reason the flag exists. Margin normalisation changes
rankings visibly: a high-turnover steel territory with a 9 % margin can beat a
low-volume power account on `return_top` and lose on `return_bottom`. Design
decisions follow from that. Charts are secondary to numbers — figures set in Fira
Code with tabular numerals, right-aligned, so columns compare by eye. Sliders
carry live point readouts and auto/manual badges because a control that hides its
effect invites distrust. Sections get uppercase eyebrows so a reviewer can scan
for the criterion they dispute. The login was reduced to one focused card, since
credential hints in a demo read as real accounts.

## 8. Limitations & Validity

Every name, code, territory, target, spend figure and margin in this build is
invented; the monthly series is a seeded random walk, not an observation. Nothing
here evidences how any real force performs, and the band boundaries, weights and
thresholds are defensible defaults rather than validated calibration. The review
window is three months, so seasonality is only partly absorbed and short-tenure
officers are judged on thin data. Baseline `B` is assumed rather than measured —
in production a proper control or matched territory would replace it, and the
demo says so on the rubric page. The sign-in is a UI state with no
authentication, and the survey links are inert by design. The original tool's
write-path to a hosted database, its per-unit access rules and its audit log are
absent; `localStorage` stands in for none of them. Finally, the rubric encodes
choices a manager could reasonably reject, so it is presented as an editable
model with visible weights, not as an authority.

## 9. Reproducibility & Deployment

No dependencies to install and no build step: open `index.html` directly
(`start index.html` on Windows, `open index.html` on macOS) or serve the folder
with `python -m http.server 8000` and load `http://localhost:8000`. The roster,
KPI series and scores are seeded in the file, so two browsers show identical
figures; only reviewer overrides and simulated responses differ, and **Reset
saved state** clears them. Internet access is used solely for Chart.js and the
Fira web fonts from CDNs; offline, the page still renders with tables in place of
charts. To publish: push this folder to a repository, then on Vercel choose
**Add New → Project → Import**, set framework preset **Other**, leave build
command and output directory empty, and deploy — `index.html` is served as-is.
No environment variables are read. The scoring logic is plain JavaScript inside a
single `<script>` block, so the same rubric can be lifted into a server-side
implementation unchanged.

_Built by Abir._
