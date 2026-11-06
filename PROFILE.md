# Revenue Assurance & Marketing Forecast — Project Profile

## 1. Abstract

This project converts a monthly group revenue-assurance extract into a self-contained, zero-backend
decision dashboard for a fictional 16-business-unit portfolio ("NexaGroup"). For each unit it
computes quarter-to-date achievement against a phased target, a like-for-like **Marketing Revenue**
(current-year minus prior-year same-period revenue) and **Marketing Profit** (that growth at prior-year
gross margin), classifies marketing productivity as revenue-growth per Crore of marketing spend, and
projects the remaining nine months with a seasonal × trend method. Fourteen derived measures feed an
action engine, early-warning flags and a spend-reallocation simulator, all rendered from one embedded
synthetic JSON dataset with Chart.js. The interface follows a v2 design contract: dark navy tokens, a
light-theme toggle, KPI cards with delta chips, hover tooltips that restate each metric's formula,
sticky-header tables and reduced-motion support. Everything is deterministic: opening the file offline
reproduces the identical view. The artifact demonstrates analyst-grade marketing governance as a
single portable HTML file rather than a recurring manual rebuild of spreadsheet models.

## 2. Problem Framing

A holding-company strategy team receives, each month, a workbook consolidating every business unit's
revenue actuals, marketing spend and annual targets. The workbook states numbers but answers no
questions: which units are genuinely growing versus merely on-plan in a seasonally easy quarter;
which are spending while revenue falls; where each unit lands at year-end if the current run-rate
persists; and what action that implies per unit. Answering these by hand means re-deriving
comparisons, re-forecasting and re-tabling every month — hours of brittle spreadsheet work whose
output is stale by the next close. The artifact exists to encode that recurring analysis once: a
static dashboard that ingests the extract, applies the documented method, and keeps the reasoning
(hover definitions, assumptions tab) visible beside the numbers.

## 3. Related Practice

The analytical moves here are standard marketing-analytics practice rather than inventions.
Marketing profit pools and like-for-like growth bridges (current vs prior-year same-period revenue)
are common in CPG/holding-company marketing P&L reviews. Attainment-versus-growth 2×2 matrices echo
BCG-style portfolio grids applied to sales effectiveness. "Revenue per marketing Crore" is a ROMI-adjacent
productivity ratio; reallocation simulators formalise the marginal-return logic behind zero-based
marketing budgeting. Seasonality-preserving naïve forecasts (prior-year month × trend factor) are a
workhorse of short-horizon business planning when data history is thin. Run-rate versus
required-run-rate gap math is the standard pace-to-plan calculation in quarterly business reviews.

## 4. Methodology & Data Design

**Data (conceptual).** One JSON object (`const DATA`) mirrors a revenue-assurance extract: general-ledger
sales lines (local + foreign, ÷1e7 → BDT Crore), a marketing-spend GL line (4210001), FY27 targets with
quarterly phasing `q`, prior-year monthly history, and quarter-to-date actuals with Sep as a 9-day MTD
compared like-for-like against the same 9 days of Sep prior-year. In this portfolio copy all values are
synthetic — deterministic, realistically scaled inventions; no real ledger is referenced.

**Metric definitions (formulas).**

- `Marketing Revenue (MR) = CY QTD revenue − PY same-period revenue` (like-for-like).
- `Q1 YoY trend g = MR / PY QTD revenue`; used only if `PY QTD ≥ 0.4 × CY QTD`, capped at ±100%.
- `Marketing Profit (MP) = MR × FY26 gross margin`, where `gross margin = (Revenue − COGS) / Revenue`.
- `Rev per Cr spend = MR / CY QTD marketing spend` (undefined when spend ≈ 0).
- `Spend intensity = marketing spend / revenue` (CY and PY reported separately).
- Forecast: remaining month *m* = `PY(m) × (1 + g)` (seasonal × trend); fallback for ramp/new units =
  `flat run-rate = (Jul actual + Aug actual) / 2`; scenarios: `Repeat FY26` (g=0), `Current trend` (g),
  `Plan` (target).
- `Required run-rate = (Target − QTD actual) / 9` remaining months; `lift = required / current run-rate`.
- Efficiency class: spend-bearing units with `MR ≥ 0 ∧ rev/spend ≥ 1` → *efficient grower*;
  `MR ≥ 0 ∧ rev/spend < 1` → *costly grower*; `MR < 0` → *declining on spend*; tiny-base → *pre-launch*.
- Reallocation estimate: `ΔMR = amount × (revPerCr_dest − revPerCr_source)` — directional, assumes no
  diminishing returns or lag.

**Transformation steps.** Load `DATA` → derive per-SBU metrics (pure functions at render time) →
aggregate portfolio totals → classify → project → render tables/charts. No server, no pipeline.

## 5. Analytical Framework

The UI encodes one decision tree per unit. First, pace: Q1 achievement vs phased target (RAG: ≥90 %
on track, 60–90 % at risk, <60 % behind). Second, direction: YoY growth sign. Third, efficiency:
marketing-productivity class. Recommended moves follow the conjunction — declining-on-spend units are
flagged *freeze & reallocate*; behind-but-growing units are *fund & accelerate*; behind-and-shrinking
units are *root-cause first* (spend cannot fix a shrinking base); on-plan growers are *protect &
scale*; pre-launch units get launch-milestone guidance. The growth–achievement matrix visualises the
same quadrants; the scenario band frames portfolio planning risk as the spread between repeat-FY26,
current-trend and plan. Two open confirmations (one target below its own base, and a portfolio-total
mismatch vs the summary deck) are surfaced rather than silently resolved.

## 6. System Design

Single `index.html`: one embedded dataset constant, ~700 lines of vanilla ES2020 render functions,
Chart.js 4.4.1 from CDN, and a hand-written CSS custom-property token layer (`--bg/--surface/--line/
--ink/--brand/--accent/--ok/--warn/--bad`, radius/space/shadow scales, `Fira Code` for numerals and
headings, `Fira Sans` for prose). Seven tab panes render lazily on click; charts are destroyed and
rebuilt per view to stay memory-clean. Static rendering was chosen deliberately: the analysis is a
point-in-time review, so determinism, offline `file://` opening, zero credentials and trivial hosting
outweigh live data. The dark-navy theme is the default with a `data-theme="light"` toggle that remaps
tokens and re-renders charts with theme-aware gridline/palette variables. Interaction budget: hover
tooltips restate formulas as footer lines; tables use sticky headers with right-aligned tabular
numerals; motion is limited to 150–300 ms feedback states and one ≤450 ms load-in stagger, disabled
under `prefers-reduced-motion`.

## 7. Findings & Design Decisions

The synthetic story the dashboard tells: a ≈2,076 Cr projected shortfall against an 11,845 Cr plan
concentrated in a handful of large units; several units "on track" in Q1 yet below prior year
(achievement and growth disagree — hence the 2×2); and productivity spread from −25.8× to +104×
growth per Cr of spend, which makes the reallocation simulator the most actionable control.
Design decisions: (1) KPI cards carry hover definitions and delta chips so the headline numbers are
self-auditing; (2) every chart tooltip ends with the metric formula, trading density for validity;
(3) the palette is restricted to blue + amber + semantic green/red so status (RAG) never collides
with series identity; (4) tables scroll inside cards (no page-level horizontal scroll) and rows are
click-through to the unit deep-dive, keeping one navigation idiom; (5) methodology lives *in* the UI
(Assumptions tab + footer anchor link) instead of a side document.

## 8. Limitations & Validity

All names, units, codes and figures are synthetic; no inference about any real company is possible or
intended. The forecast is a seasonal naïve method — no promotions, supply, price or cannibalisation
effects; trend caps (±100 %) and the ≥40 % prior-year-base gate are judgement parameters, not
estimated ones. Reallocation assumes linear, instantaneous, lag-free spend response — directional at
best. Gross-margin-based Marketing Profit is meaningless where FY26 margin is negative (flagged).
Sep is a 9-day MTD; comparability relies on the like-for-like prior-year window. Two data-confirmation
items remain open by design (largest unit's target base; portfolio headline total).

## 9. Reproducibility & Deployment

Run locally: open `index.html` in any modern browser (double-click works; needs internet only for
the Chart.js CDN and Google Fonts, degrading gracefully to system fonts offline). Serve optionally
via `python -m http.server 8080`. Delivery checks used: extract the inline `<script>` and run
`node --check`; grep for forbidden brand terms (zero hits). Deploy: Vercel import → framework
**Other**, root `.`, no build command; identical on Netlify or GitHub Pages. To reuse with real data,
replace the `DATA` constant with an extract of the same schema (`meta`, `sbus[]` fields) — no other
code changes are required.
