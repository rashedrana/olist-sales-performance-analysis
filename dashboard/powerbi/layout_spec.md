# Power BI Layout Spec — Olist Sales Performance Dashboard

Design language follows modern end-to-end Power BI project style (cf. "The Developer"
YouTube channel's end-to-end dashboard projects): a clean canvas, a slicer header,
rounded KPI cards, bookmark-driven metric switching and page-navigation buttons.
The web edition in `dashboard/index.html` is the visual reference — build the PBIX
to match it page for page.

## Global formatting
- Page size: 16:9 (1280 × 720)
- Background: #F5F8F7 · Cards: white, subtle shadow, rounded-corner shape underlay
- Accent: deep teal #0F766E · Secondary amber #D97706
- Font: Segoe UI (titles bold 14–16 pt, KPI values bold 20–24 pt, body 10–11 pt)
- Growth bars: positive #059669 · negative #DC2626 · Payment colors: credit card
  #0F766E, boleto #D97706, voucher #0891B2, debit card #65A30D
- Currency: Brazilian real, `R$ #,##0` for axes, `R$ #,##0.00` in tooltips

## Data model (Get Data → the CSVs in `powerbi/data/`)
The full model: **FactOrderItems (112,650) + FactOrders (99,441) +
FactPayments (103,886) = 315,977 fact rows** with DimCustomer (99,441),
DimSeller (3,095), DimProduct (32,951), DimDate (774) and DimState (27).
Relationships (single direction, from dims to facts):
- DimDate[DateKey] → FactOrderItems[DateKey], FactOrders[DateKey], FactPayments[DateKey]
- DimCustomer[CustomerKey] → FactOrderItems[CustomerKey], FactOrders[CustomerKey], FactPayments[CustomerKey]
- DimSeller[SellerKey] → FactOrderItems[SellerKey]
- DimProduct[ProductKey] → FactOrderItems[ProductKey]
- DimState[StateCode] → FactOrderItems[CustomerState], FactOrders[CustomerState]
Hide all key columns. Paste all measures from `DAX_measures.md` into a `_Measures` table.
Apply the sales scope once, globally: report-level filter
`FactOrders[OrderStatus] = "delivered"` (the delivered-scope measures in the
DAX file already encode it; the filter keeps ad-hoc visuals honest too).

## Page 1 — Overview (the page from the video-style template)
Slicer header (top band, synced to all pages):
- Period: chiclet slicer (DimDate[Year]) + month-range slider (DimDate[YearMonth])
- Customer state: dropdown slicer (DimState[StateName]), "All Brazil" default
- Category: dropdown slicer (DimProduct[CategoryEN])
- Metric switch: three buttons (Revenue / Orders / Avg order value) wired to
  bookmarks that swap the main trend visual's measure (field parameter is the
  modern alternative — either is fine)

KPI card row (6 cards, each with its previous-year delta underneath):
1. Total Revenue · 2. Delivered Orders · 3. Average Order Value ·
4. Avg Delivery Days · 5. Avg Review Score · 6. Freight Share %

Visuals:
- Main trend: line chart, axis DimDate[YearMonth], values = bookmark-switched
  measure; annotate the Nov 2017 Black Friday peak (R$987,765)
- Right: clustered column — Total Revenue by DimState[StateCode], top 12,
  drill-through enabled to Page 3
- Bottom left: horizontal bar — Total Revenue by DimProduct[CategoryEN],
  top 12 + other
- Bottom right: bar — Total Revenue by calendar month (DimDate[MonthName]),
  November highlighted in amber (seasonality)

## Page 2 — Delivery & Satisfaction Deep-Dive
- Gauge: On-Time Delivery % with target 95 (actual 91.9%)
- Bubble/scatter: Avg Review Score by delivery-time band (FactOrders bucketed
  0–5 / 6–10 / 11–15 / 16–20 / 21–30 / 31+ days), size = Delivered Orders
- Clustered bar: Avg Review Score for on-time vs late orders (4.29 vs 2.57)
- Line: Late Orders share % by DimDate[YearMonth] — the 1.5% → 9.4% climb
- Map (filled): Avg Delivery Days by DimState — SP 8.8 days vs RR 29.4 days
- Small multiples / table: review-score distribution (1–5 stars) by year

## Page 3 — Geography & Categories (drill-through target)
- Filled map: Delivered Orders by DimState[StateCode]
- Treemap: Total Revenue by DimState[Region] → DimState[StateName]
- Ribbon: State Revenue Rank by DimDate[Year] (top 6 states)
- Matrix: Total Revenue, rows DimState[StateName], columns DimDate[Year],
  conditional-formatting data bars
- Decomposition tree: Total Revenue → Region → State → CategoryEN
- Waterfall: 2017 → 2018 revenue bridge by CategoryEN contributions
- Drill-through filter: DimState[StateCode]

## Page 4 — Payments, Customers & Sellers
- Donut: Total Paid by FactPayments[PaymentType] (credit card 78.5%)
- KPI/small cards: Avg Installments (Credit Card) 3.5 · Orders Paying by
  Card % · boleto average ticket R$144.33
- Bar: orders by DimDate[Weekday] (Monday peak) and by purchase hour
  (16:00 peak) — two small bars side by side
- Cards: Repeat Customer Rate % (3.0%) · active sellers 2,970 ·
  top-100 seller revenue share 45.5% (static reference values from
  QUESTIONS.md Q14, or build the seller measures on FactOrderItems[SellerKey])
- Area: Revenue Cumulative by DimDate[YearMonth] (R$10M crossed May 2018)

## Page 5 — About & Method
- Text box: headline findings (R$13.2M item revenue on 96,478 delivered
  orders; SP = 38.3% of revenue; Black Friday Nov 2017 peak; late deliveries
  average 2.57 stars vs 4.29 on-time; credit = 78.5% of money paid, 3.5
  installments on average; repeat-customer rate only 3.0%)
- Method note: sales scope = delivered orders only (96,478 of 99,441;
  canceled and in-transit-at-snapshot excluded, so purchase months run
  Sep 2016 – Aug 2018); revenue = item price, freight separate; review score
  per order is the mean of its review rows; geolocation table not modelled
  (ZIP-prefix aggregates)
- Data source: Brazilian E-Commerce Public Dataset by Olist (Kaggle,
  CC BY-NC-SA 4.0), 9 CSVs verified row-by-row; raw files in `data/`
- Links (buttons with web URL actions): Kaggle dataset page, portfolio site
- Note: Project 4 of the Power BI dashboard series by Rashed Rana

## Navigation
Left vertical button bar on every page (home icon style): Overview · Delivery ·
Geography · Payments · About — page navigator or buttons with "Page navigation"
actions, selected page highlighted in the accent color.

## Publish checklist
- [ ] Report-level delivered-scope filter set and tested against QUESTIONS.md totals (R$13,221,498)
- [ ] All slicers synced across pages 1–4
- [ ] Bookmarks tested (metric switch)
- [ ] Drill-through from Page 1 state bar to Page 3 works
- [ ] Mobile layout: KPI cards 2 × 3, trend chart full width
- [ ] File saved as `Olist_Sales_Performance.pbix` next to this spec
