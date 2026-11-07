![Revenue Leakage Auditor](assets/banner.svg)

# Revenue Assurance & Marketing Forecast — portfolio demo

A single-file, zero-backend dashboard that turns one group-wide revenue-assurance extract into a
quarter-to-date marketing review plus a year-end projection for 16 fictional business units of
"NexaGroup" — achievement, marketing productivity, recommended actions and early warnings, all
rendered client-side with Chart.js on a dark, data-dense design system.

[![Live demo](https://img.shields.io/badge/live%20demo-vercel-3B82F6?style=flat-square)](https://your-project.vercel.app)
[![Data](https://img.shields.io/badge/data-synthetic-D97706?style=flat-square)](#demo-data)
[![Build](https://img.shields.io/badge/build-none-10B981?style=flat-square)](#)

![Dashboard banner](assets/banner.png)

> **Demo data only.** Every company, unit, code and figure is synthetic — deterministic invented
> values shaped like a real extract. See [`PROFILE.md`](PROFILE.md) for the full methodology.

## Tabs

Overview · Forecast · Efficiency · Actions · Early Warnings · SBU Detail · Assumptions & Method.

## Quick start

```bash
# 1. no install, no build — just open the file
open index.html            # Windows: double-click, or start index.html
# 2. optional: serve it (identical result; useful for sharing)
python -m http.server 8080
# 3. optional: run the syntax check used during development
node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')
```

## Features

- **Portfolio KPI strip** — FY27 target, Q1 revenue, achievement, marketing revenue/profit,
  projected year-end and projected achievement, each with a hover definition and delta chip.
- **Sortable, click-through SBU table** — plan vs Q1-to-date, spend intensity, projected FY,
  RAG status pills; any row jumps to that unit's deep-dive.
- **Seasonal × trend forecast** — each remaining month = prior-year same month × (1 + Q1 YoY),
  with a flat-run-rate fallback for ramp/new units, a three-scenario band and gap attribution.
- **Marketing-productivity model** — growth (CY − PY) per Cr of spend, efficiency classes,
  spend-vs-growth scatter with a 1:1 reference line, and an interactive reallocation simulator.
- **Decision engine** — maps every unit to a recommended move (freeze, fund, root-cause,
  protect…) with owner, focus and priority; early-warning flags with run-rate vs required run-rate.
- **Design v2 system** — dark navy tokens with a light-theme toggle, Fira Sans/Fira Code via
  Google Fonts, sticky top bar, KPI hover lift, tooltips + legends on every chart, sticky-header
  tables with tabular numerals, focus-visible rings, reduced-motion support, one load-in stagger,
  responsive at 390 / 768 / 1024 / 1440.

## Demo data

`index.html` embeds one `const DATA = {...}` JSON object (16 SBUs, monthly paths, projections).
Names ("Nexa Cement Company Ltd.", "Nexa Staples Ltd.", codes like `NXCM`) and all figures are
invented; magnitudes mimic a real BDT-Crore group extract. No real ledger, warehouse or company
is referenced. Swap the `DATA` constant with a real extract of the same shape to reuse the tool.

## Tech

Vanilla JavaScript (ES2020) · Chart.js 4.4.1 (CDN) · hand-written CSS custom-property design
tokens · one embedded dataset · no build step, no backend.

## Deploy

Vercel: import the repo → framework preset **Other**, root **`.`**, no build command — the static
page is served as-is. (Netlify/GitHub Pages work identically.)

## License

MIT — demo content, no warranty, synthetic data.
