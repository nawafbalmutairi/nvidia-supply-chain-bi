# NVIDIA AI-GPU Supply Chain — BI Dashboard

> Power BI dashboard tracking six supply-chain KPIs for NVIDIA H100 / H200 across EMEA and North America. Built for the KV6011 Business Intelligence module at Northumbria University; combines Soft Systems Methodology, causal-loop modelling, and Balanced Scorecard strategy alignment.

[![Status](https://img.shields.io/badge/status-completed-success)]()
[![Forecast Accuracy](https://img.shields.io/badge/forecast%20accuracy-96.4%25-e64d2e)]()
[![On-time Delivery](https://img.shields.io/badge/on--time%20delivery-95.92%25-blue)]()
[![Power BI](https://img.shields.io/badge/Power%20BI-Desktop%20%2B%20Service-yellow)]()
[![License](https://img.shields.io/badge/license-MIT-lightgrey)]()

---

## TL;DR

| | |
|---|---|
| **Brief** | Build a BI artefact tracking AI-GPU supply-chain performance against forecasts. |
| **Scope** | 2 products (H100, H200) · 2 regions (EMEA, NA) · ~12 months of demand & inventory data. |
| **Output** | 1 Power BI dashboard · 8 DAX measures · 6 KPI cards · 4 trend visualisations. |
| **Headline** | 96.4% forecast accuracy · 24.3% MAPE · 42.3K backorders against 255.8K inventory · 95.92% on-time delivery. |
| **Methodology** | Soft Systems (rich picture + causal loop) → Balanced Scorecard → KPI dashboard. |

---

## The six headline KPIs

| KPI | Value | What it tells the operator |
|---|---:|---|
| **Forecast Accuracy** | 96.4% | How closely predicted demand matches actual orders shipped. |
| **MAPE** | 24.3% | Mean Absolute Percentage Error — the inverse view. |
| **Total Backorders** | 42.3K | Demand we received but couldn't immediately fulfil. |
| **Capacity Utilisation** | 57.0% | Headroom available across production lines. |
| **Avg Supplier Lead Time** | 59 days | Average days from supplier order placed to receipt. |
| **On-time Delivery Rate** | 95.92% | Fulfilled orders that hit their committed date. |

### The inventory / backorder split

| Region | Inventory units | Backorders |
|---|---:|---:|
| EMEA | 127,898 | 21,137 |
| NA | 127,898 | 21,137 |
| **Total** | **255,796** | **42,274** |

The even split is structural — production allocation is balanced across regions, but so is the under-fulfillment, meaning the constraint is upstream of regional planning.

---

## What I built

### 1. Power BI dashboard

The deliverable: a single-page `.pbix` artefact with six KPI cards across the top, three chart panels (Average Lead Time by Product, Forecast vs Actual Demand with 3-month moving-average overlay, Inventory & Backorders by Region table), and a 12-month Production Output vs Actual Demand bar chart along the bottom. Two slicer controls (Product, Region) cross-filter every visual.

### 2. DAX measures

Eight measures power the dashboard. Examples:

```dax
Forecast Accuracy =
    1 - DIVIDE(
        SUMX( Demand, ABS( Demand[Forecast] - Demand[Actual] ) ),
        SUMX( Demand, Demand[Actual] )
    )

MAPE =
    AVERAGEX(
        Demand,
        DIVIDE(
            ABS( Demand[Forecast] - Demand[Actual] ),
            Demand[Actual]
        )
    )

3M Moving Avg Demand =
    CALCULATE(
        AVERAGE( Demand[Actual] ),
        DATESINPERIOD(
            Demand[MonthEnd],
            LASTDATE( Demand[MonthEnd] ),
            -3,
            MONTH
        )
    )
```

### 3. Soft Systems Methodology (SSM) analysis

The dashboard isn't built in a vacuum — it sits on top of a deliberate systems-thinking model:

- **Rich picture** identifies the key actors (NVIDIA Ops & Planning at the centre; TSMC and CoWoS upstream; cloud hyperscalers and enterprise downstream; geopolitics and competitors as external pressures; backorders as the visible pain).
- **Causal loop diagram** maps four feedback loops driving the system:
  - **R1 · Demand Surge** — AI demand ↑ → backorders ↑ → customer pressure ↑ → strain ↑ (reinforcing)
  - **R2 · Forecast Volatility** — volatile orders → forecast error → unstable inventory (reinforcing)
  - **B1 · Capacity Constraint** — production limits → unmet demand → revised forecasts (balancing)
  - **B2 · Lead Time Adjustment** — long lead times → delayed shipments → re-planning (balancing)

### 4. Balanced Scorecard alignment

KPI dashboards risk becoming "interesting numbers". The Balanced Scorecard maps each KPI back to a strategic layer:

```
Learning & Growth  →  Internal Processes  →  Customer  →  Financial
   (capability)        (engine)              (loyalty)    (result)
```

So *MAPE* doesn't just sit on a card — it lives under Internal Processes and feeds Customer outcomes via lead-time reduction, which protects Financial result through predictable revenue.

---

## Repository structure

```
.
├── README.md                                  ← you are here
├── LICENSE                                    ← MIT
├── .gitignore
├── dashboard/
│   ├── NVIDIA_Supply_Chain.pbix               ← Power BI Desktop file
│   ├── data_model.png                         ← star-schema screenshot
│   └── dax_measures.md                        ← all 8 DAX measures
├── analysis/
│   ├── 01_rich_picture.png                    ← SSM ecosystem diagram
│   ├── 02_causal_loop.png                     ← 4-loop feedback model
│   └── 03_strategy_map.png                    ← Balanced Scorecard
├── data/
│   ├── demand.csv                             ← monthly forecast & actual
│   ├── inventory.csv                          ← regional inventory snapshots
│   └── lead_times.csv                         ← supplier lead-time samples
└── docs/
    └── KV6011_Report.pdf                      ← full submitted report
```

---

## Reproduce the dashboard

### Prerequisites

- **Power BI Desktop** (free) — [download here](https://powerbi.microsoft.com/desktop/)
- Windows 10/11 (Power BI Desktop is Windows-only)

### Steps

```powershell
# 1. Clone the repo
git clone https://github.com/nawafbalmutairi/nvidia-supply-chain-bi.git
cd nvidia-supply-chain-bi

# 2. Open the dashboard
start dashboard\NVIDIA_Supply_Chain.pbix
```

Power BI Desktop will open the file. The underlying CSVs in `data/` are referenced as relative paths — refresh the model via **Home → Refresh** and the visuals will repopulate.

To export the dashboard as PDF or PowerPoint: **File → Export → Export to PDF**.

---

## Insights (what the dashboard actually surfaced)

The single most interesting finding came from the **3-month moving-average overlay on forecast-vs-actual demand**: a recurring **May/November under-shoot pattern**. Forecasts consistently overstated actuals in those two months, suggesting a calendar-driven enterprise budgeting cycle that the forecast model doesn't account for. The recommendation: add a categorical "fiscal-quarter-end" feature to the forecast model.

The second finding: the **57.0% capacity utilisation** is suspiciously low given a 95.92% on-time delivery rate and 42.3K backorders. Either capacity is mis-measured, or the bottleneck is somewhere other than production — likely CoWoS packaging, which became the explicit constraint on the rich picture.

---

## Tech stack

**BI / modelling:** Power BI Desktop · DAX · M (Power Query) · Power BI Service
**Analysis:** Soft Systems Methodology (SSM) · Causal Loop Diagramming · Balanced Scorecard
**Data:** CSV (cleaned in Power Query) · star-schema data model

---

## Author

**Nawaf Almutairi** — BSc Computer Science, Northumbria University · Class of 2026
Module: KV6011 Business Intelligence

[Portfolio](https://nawafbalmutairi.github.io) · [LinkedIn](https://linkedin.com/in/nawaf-almutairi-907766290/) · [Email](mailto:NawafBAlmutairi@outlook.sa)

---

## License

[MIT](LICENSE) — data is synthetic / illustrative; not affiliated with NVIDIA Corporation.
