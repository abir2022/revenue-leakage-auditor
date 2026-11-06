# Revenue Assurance & Marketing Forecast (NexaGroup demo)

**One-liner:** A self-contained dashboard that turns a group-wide revenue-assurance extract
into a quarter-to-date marketing review plus a year-end projection for every business unit —
running here on fully synthetic data.

## Problem

A holding company's marketing team gets a monthly workbook that shows revenue, marketing
spend and gaps per business unit, but the numbers are a snapshot without a story: which units
are genuinely growing, which are spending while revenue falls, and where each unit lands by
year-end if the current run-rate holds. Answering those questions normally means rebuilding
the model by hand every month.

## Solution

A zero-backend dashboard that parses the per-unit extract, computes a like-for-like
"Marketing Revenue" (current-year vs prior-year same-period) and "Marketing Profit" (that
growth × gross margin), classifies each unit by efficiency, and projects the remaining nine
months with a seasonal × trend method. It then renders the whole thing — portfolio overview,
forecast, efficiency, recommended actions, early warnings and a per-unit deep dive — from one
embedded dataset. In this portfolio copy the same code path is fed invented numbers, so it
renders offline with no spreadsheet or credentials.

## Key features

* 16 business-unit portfolio table: plan vs quarter-to-date, marketing revenue/profit,
  spend intensity and projected year-end achievement, all sortable and click-through.
* Seasonal × trend forecast for the next 9 months, with a pessimistic / current-trend / plan
  scenario band and per-unit gap attribution.
* Marketing-productivity model: revenue-growth per Cr of spend, spend-vs-growth scatter and a
  interactive reallocation simulator (move spend from a declining unit to a growing one).
* Recommended-action engine that maps each unit to a move (freeze, fund, root-cause, protect…)
  from achievement, YoY growth and efficiency class.
* Early-warning flags (behind plan, declining on spend, ramp, burn gap, negative margin) with
  run-rate vs required run-rate.
* Hand-built Chart.js visuals, light theme, hover explanations of every metric, single file.

## Tech stack

Vanilla JavaScript (ES2020) · Chart.js 4 (inlined via CDN) · one embedded JSON dataset ·
no build step, no backend — deployable as a static page.

## Synthetic demo-data disclaimer

All names, codes and numbers are invented for this portfolio piece. "NexaGroup" and its
business units (Nexa Cement, Nexa Steel, Nexa Staples, Nexa Air Travel, Nexa Realty, etc.) are
fictional. Monetary figures are deterministic synthetic values of realistic magnitude — no
real company, ledger, data warehouse or transaction is referenced.

## Run locally

```bash
open index.html          # double-click works; no install, no build
```

## Deploy on Vercel

* Import the repository, **Framework Preset: Other** (static HTML).
* **Root Directory: `.`** (repo root) · Build Command: *empty* · Output Directory: *empty*.
* The page is fully self-contained; it needs only the Chart.js CDN at runtime.
