# Monthly YoY Performance Reports

> **Every month, leadership opens a single HTML file and sees exactly how the business performed — vs last month, vs last year, across every outlet and every revenue stream.**

A recurring management reporting pipeline for a 25+-outlet, 2-brand entertainment chain. Three fragmented POS systems. One reconciled, drill-down report. Delivered every month without rebuilding.

Reports live: **May 2026 vs May 2025** · **June 2026 vs June 2025** · *(new report every month)*

---

## The problem

Leadership needed monthly performance answers:
- Are we growing vs last year?
- Which outlets improved? Which declined?
- Is the F&B business tracking up or down?
- Is footfall rising but spend-per-head falling? (That's a different problem than footfall + spend both falling.)

**The data to answer those questions was spread across three completely separate POS systems**, each with its own API, data model, and quirks — and none of them talking to each other. A typical monthly review meant someone manually exporting from multiple systems, pasting into Excel, and hoping nothing double-counted.

This pipeline replaces all of that.

---

## What gets measured

Each monthly report covers the full business picture — activity, food & beverage, and customer behaviour — with current-month vs same-month-last-year comparison on every metric:

**Revenue & Volume**
| Metric | What it reveals |
|---|---|
| Total Revenue (₹) | Top-line business health |
| Activity Revenue vs F&B Revenue | Revenue mix shift — are guests spending more on food or play? |
| New Card Sales | Acquisition — how many brand-new customers this month? |
| Recharge Revenue | Retention — how much are existing wallet customers topping up? |
| Voucher Sales | Campaign-driven revenue vs organic walk-ins |

**Customer Behaviour**
| Metric | What it reveals |
|---|---|
| Footfall | Volume — more guests or fewer? |
| Spend-per-Head (SPH) | Value — same guests spending more, or fewer guests spending more each? |
| Repeat Visit Rate | Loyalty — are customers coming back? |
| F&B Attach Rate | Cross-sell — what % of guests also spent on food? |

**Operations**
| Metric | What it reveals |
|---|---|
| Peak Hour Distribution | Staffing efficiency vs last year |
| Socks Attach Rate | Upsell compliance |
| Outlet-level breakdown | Which stores drove the growth? Which need attention? |

**YoY Deltas — the key question for every metric:**
- Is this better or worse than last year?
- By how much (absolute ₹ and %)?
- Is the trend consistent across outlets or driven by one location?

---

## The pipeline

```
Step 1 — Data Pull (automated)
├── Activity API     → bookings, footfall, card sales, recharges
├── PetPooja API     → F&B orders, categories, channel breakdown
└── Birthday API     → customer visit frequency, repeat rate

Step 2 — Reconciliation
├── Resolve Semnox arcade revenue (Raipur store only)
├── Align F&B date boundaries (PetPooja late-night rollover fix)
├── Deduplicate voucher records across POS export formats
└── Normalise outlet names across all three systems

Step 3 — YoY Computation
├── Pull same-month prior year from each API
├── Compute absolute delta (₹ and count)
├── Compute % change per metric per outlet
└── Flag significant deviations (>±15% vs prior year)

Step 4 — Report Generation
└── Single self-contained HTML file
    ├── No login, no server, no dashboard account
    ├── Works offline once downloaded
    ├── Executive summary at top, outlet drill-down below
    └── Print-ready for board/ops review meetings
```

---

## Key engineering decisions

**Why a standalone HTML file, not a live dashboard link?**

The live dashboard is always available — but a monthly report needs to be *frozen in time*. Leadership needs to say "in June 2026, revenue was ₹X" without the number shifting as July data comes in. A self-contained HTML file is the right format: it's the record of what that month looked like, shareable via WhatsApp or email, openable on any device, printable for a board pack.

**Why reconcile at report-generation time, not at storage time?**

Each POS has its own quirks that only matter for specific analyses. PetPooja's late-night rollover (orders from the next day appearing in today's count) matters for daily reporting but largely cancels out in a monthly sum — unless you're looking at the last day of the month. Semnox's Raipur data needs product-level classification to separate arcade card revenue from bowling/activities. Reconciling at generation time means the logic is explicit, auditable, and can be improved without reprocessing history.

**The F&B double-count bug — and why it matters for YoY**

PetPooja's API returns orders from the *next* calendar day's late-night shift when queried by date. In daily reporting this causes a significant overcount. In monthly reporting it's smaller — but it *compounds* in YoY comparison: if June 2025 was overcounted by 2% and June 2026 is overcounted by 2%, the YoY delta is clean. But if the fix is applied inconsistently — say, only to the current year — the apparent YoY growth is artificially suppressed. The reconciliation layer applies the same boundary logic to both years, so the comparison is apples-to-apples.

---

## Report output — what leadership sees

**Executive summary (top of report):**
- Total revenue vs prior year: ₹X (▲Y%)
- Footfall vs prior year: N guests (▲M%)
- Best-performing outlet this month
- Biggest YoY decline requiring attention
- F&B revenue as % of total (vs prior year %)

**Outlet breakdown:**
- Every outlet ranked by YoY revenue growth
- Traffic light: Green (>+10%), Amber (-10% to +10%), Red (<-10%)
- SPH shown alongside footfall so volume vs value is always visible

**F&B deep-dive:**
- Category mix (food vs beverages)
- Channel split (dine-in vs aggregator %)
- Top items by revenue

---

## Business impact

Before this pipeline: monthly performance review required manual exports from 3 systems, hours of Excel work, and the numbers still didn't reconcile cleanly.

After: the report is ready within minutes of the month closing. Leadership has the same version of the numbers. Outlet managers can be shown exactly where they stand vs last year, outlet by outlet, metric by metric.

**May 2026 report:** ₹X total revenue · N outlets covered · YoY delta computed across 25+ metrics  
**June 2026 report:** ₹X total revenue · Full F&B reconciliation including The Knockout sports bar

---

## Tech stack

`Node.js` · `JavaScript` · `Python` · `PetPooja API` · `Semnox/Parafait API` · `Activity API` · `Supabase/PostgreSQL` · `HTML/CSS`

---

## Related

The monthly report draws on the same data infrastructure as the live [FEC Analytics Platform](https://github.com/Souvikkundu369/fec-analytics-platform). The platform provides real-time operational visibility; this pipeline produces the frozen, auditable monthly record.
