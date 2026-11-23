# Olist Sales Performance — Interactive Dashboard

Project 4 of the Power BI dashboard series — and the first one not tied to a
research paper. It explores the **Brazilian E-Commerce Public Dataset by
Olist**: R$13.2M of item revenue across 96,478 delivered orders, 27 states,
74 product categories and 2,970 sellers, from the marketplace's first orders
in September 2016 to August 2018.

## Web edition (live, no install)
Open `index.html` in a browser (or via GitHub Pages). Features:

- **Period buttons** — All / 2016 / 2017 / 2018, with KPI deltas against the
  previous year (All compares 2018 vs 2017)
- **State and category slicers** — every KPI and chart re-drives from the
  selection; clicking a state or category bar slices the whole page to it
- **Metric switch** — the trend chart flips between item revenue, delivered
  orders and average order value
- **6 KPI cards** — revenue, orders, AOV, average delivery days, average
  review score, freight share of revenue
- The **Black Friday spike** (Nov 2017: R$987,765, +63% more orders than
  October), revenue-by-state and by-category bars, calendar-month
  seasonality, a **delivery-speed vs review-score** bubble chart, the
  payment mix donut (credit card = 78.5% of money paid), weekday/hour
  ordering patterns and the cumulative revenue curve (R$10M crossed in
  May 2018)

## Power BI edition (build guide)
Everything needed to rebuild this as a native Power BI report sits in `powerbi/`:

- `powerbi/data/` — star-schema CSVs (3 facts, 5 dimensions; see
  `powerbi/model_diagram.md`): **FactOrderItems 112,650 rows** (one row per
  order item, with price and freight), FactOrders 99,441 (one row per order,
  with delivery and review measures), FactPayments 103,886 — **315,977 fact
  rows in total, the largest model of the series so far** — plus DimCustomer,
  DimSeller, DimProduct, DimDate and DimState.
- `powerbi/DAX_measures.md` — all measures, copy-paste ready (revenue,
  freight share, AOV, delivery and on-time %, review score, payment mix and
  installments, YoY and cumulative time intelligence, state/category ranks,
  repeat-customer rate)
- `powerbi/QUESTIONS.md` — 14 business questions, each solved with a
  different Power BI visual type (KPI cards, line, clustered column, bar,
  donut, scatter, waterfall, treemap, filled map, matrix, gauge, area,
  ribbon, decomposition tree), with the verified answers
- `powerbi/layout_spec.md` — page-by-page layout in modern end-to-end
  project style: slicer header, KPI row, bookmark metric switching,
  drill-through state page, navigation buttons, exact color palette

On a Windows PC with Power BI Desktop: load the CSVs, set the relationships
in `model_diagram.md`, paste the DAX, and place the visuals per the layout spec.

## Data
Brazilian E-Commerce Public Dataset by Olist (Kaggle,
`olistbr/brazilian-ecommerce`, CC BY-NC-SA 4.0) — real, anonymized orders
from the Olist marketplace, 4 Sep 2016 – 17 Oct 2018. Kaggle requires an
account, so the CSVs were downloaded from a public GitHub mirror
(AC020907/olist-ecommerce-analytics-bi, `data/raw/`) and **verified table by
table against the canonical sizes** before use: orders 99,441 · order items
112,650 · payments 103,886 · reviews 99,224 · customers 99,441 · sellers
3,095 · products 32,951 · category translation 71 · geolocation 1,000,163.
The raw files live in this project's `data/` folder; `prep.py` rebuilds
every deliverable from them.

**Scope and limitations, stated openly:**
- All sales figures use **delivered orders only** — 96,478 of 99,441 orders
  (97.0%). The 625 canceled orders and the orders still in transit when the
  dataset was snapshotted (Oct 2018) are excluded, so in this scope purchase
  months run **Sep 2016 – Aug 2018**; Sep–Oct 2018 purchases exist in the raw
  data but had not been delivered yet.
- **2016 is a stub year** (Sep–Dec, 267 delivered orders) and contains a
  near-empty November — a known gap in the source data, kept as-is.
- Revenue is **item price**; freight is measured separately (16.6% on top).
  Total payments across delivered orders (R$15,422,462) reconcile with
  items + freight to within 0.02%.
- Delivery/review figures are order-level; an order containing several
  categories counts once in each of them (and once in All). Review score per
  order is the mean of its review rows — 551 orders carry two rows.
- The geolocation table (1,000,163 rows) is ZIP-prefix aggregates with many
  lat/lng samples per prefix; it is included in `data/` for completeness
  but is not part of the star schema and is not charted.
