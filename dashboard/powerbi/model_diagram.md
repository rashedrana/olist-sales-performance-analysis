# Star Schema — Olist Sales Performance (Power BI model)

```
                    DimDate                        DimState
                 (DateKey PK)                   (StateCode PK)
              Date, Year, Month,                StateName, Region
              YearMonth, Quarter,                    |
              Weekday, IsWeekend                     |
                        |                            |
   DimCustomer          |        DimSeller           |         DimProduct
 (CustomerKey PK)       |       (SellerKey PK)       |        (ProductKey PK)
 CustomerID,            |       SellerID,            |        ProductID,
 CustomerUniqueID,      |       SellerCity/State     |        CategoryPT/EN,
 CustomerCity/State     |                            |        Weight, Dimensions
      |   \             |            /   |             |            /
      |    \            |           /    |            |           /
      |  FactOrders ----+----------+   FactOrderItems -----------+ 
      |  (OrderID,      |              (OrderID, OrderItemSeq,
      |   DateKey FK,   |               DateKey FK, CustomerKey FK,
      |   CustomerKey FK,             SellerKey FK, ProductKey FK,
      |   CustomerState FK,           CustomerState FK, SellerState,
      |   OrderStatus, ItemCount,     OrderStatus, Price, FreightValue)
      |   ItemRevenue, FreightTotal,
      |   PaymentTotal, ReviewScore,    FactPayments
      |   DeliveryDays, IsLate)         (OrderID, PaymentSeq,
      +---- FactPayments joins on        DateKey FK, CustomerKey FK,
            OrderID + shared dims        PaymentType, Installments,
                                         PaymentValue)
```

## Tables (CSVs in `data/`)
| Table | Grain | Rows | Contents |
|---|---|---|---|
| FactOrderItems | order × item | 112,650 | Every order item in the source: price and freight value, with customer, seller, product and purchase-date keys plus the order's status (filter to `delivered` for the sales scope) |
| FactOrders | order | 99,441 | One row per order: status, item count, item revenue, freight total, payment total, max installments, mean review score, delivery days vs estimate, late flag |
| FactPayments | order × payment row | 103,886 | Every payment row: type (credit card, boleto, voucher, debit card), installments, value |
| DimCustomer | customer (order instance) | 99,441 | The source's per-order customer row, carrying `CustomerUniqueID` — the real person (96,096 distinct people) — plus city, state, ZIP prefix |
| DimSeller | seller | 3,095 | Seller city, state, ZIP prefix (2,970 sellers have delivered sales) |
| DimProduct | product | 32,951 | Portuguese category + English translation, weight and dimensions; 610 products have no category in the source (`unknown`) |
| DimDate | day | 774 | 2016-09-04 → 2018-10-17 (first purchase → last recorded delivery): year, month, year-month, quarter, weekday, weekend flag |
| DimState | state | 27 | All 27 Brazilian states with full names and the five macro-regions |

## Design notes
- Three facts share conformed dimensions (DimDate, DimCustomer, DimState):
  an item-grain transaction fact for revenue, an order-grain fact for
  delivery/review/service measures, and a payment-grain fact for the money
  mix. Total fact rows: **315,977** — the largest model of the four projects
  in this series.
- The facts keep **all** orders with an `OrderStatus` column; the dashboard's
  sales scope (delivered only: 96,478 orders, 110,197 items) is applied in
  the DAX measures, exactly as documented in `DAX_measures.md`. Nothing is
  deleted, so canceled/in-flight analysis stays possible.
- Derived columns in FactOrders are computed in data prep: DeliveryDays =
  delivered-customer date − purchase timestamp (fractional days);
  EstimatedDays likewise from the promised date; IsLate = delivered after
  estimated (delivered orders only); ReviewScore = the order's mean review
  score (the source holds 99,224 review rows for 98,673 orders — 551 orders
  carry two rows; the prep averages them).
- PaymentTotal in FactOrders reconciles with FactPayments per order; across
  delivered orders, payments total R$15,422,462 ≈ items R$13,221,498 +
  freight R$2,198,276 (the small residual is source rounding/voucher
  bookkeeping, kept as-is).
- The geolocation table (1,000,163 rows) is deliberately **not** in the
  model: it is ZIP-prefix aggregates (many lat/lng rows per prefix), useful
  for mapping enrichment, not for sales facts. It ships in the project's
  `data/` folder for completeness.
- Source data: the 9 CSVs of the Olist Brazilian E-Commerce Public Dataset
  in this project's `data/` folder (Kaggle, CC BY-NC-SA 4.0; downloaded from
  a public GitHub mirror and verified row-by-row against the canonical
  table sizes) — so the dashboard, the QUESTIONS answers and the raw data
  always agree number for number.
