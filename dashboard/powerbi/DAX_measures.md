# DAX Measures — Olist Sales Performance (Power BI)

Model tables: `FactOrderItems`, `FactOrders`, `FactPayments`, `DimCustomer`,
`DimSeller`, `DimProduct`, `DimDate`, `DimState`.
Create a dedicated measure table (`_Measures`) in Power BI and paste these in one by one.
All measures respond to the Date, State, Category and Seller slicers unless stated otherwise.

**Sales scope:** the report's headline measures use *delivered* orders, matching
the web edition. `FactOrderItems` and `FactOrders` carry every order with an
`OrderStatus` column, so the delivered scope is applied inside the measures
below (or equivalently with a page-level filter `OrderStatus = "delivered"`).

---

## Core sales measures (FactOrderItems)

```dax
Total Revenue =
CALCULATE ( SUM ( FactOrderItems[Price] ), FactOrderItems[OrderStatus] = "delivered" )
```

```dax
Total Freight =
CALCULATE ( SUM ( FactOrderItems[FreightValue] ), FactOrderItems[OrderStatus] = "delivered" )
```

```dax
Freight Share % =
DIVIDE ( [Total Freight], [Total Revenue] ) * 100
```

```dax
Items Sold =
CALCULATE ( COUNTROWS ( FactOrderItems ), FactOrderItems[OrderStatus] = "delivered" )
```

## Order measures (FactOrders)

```dax
Delivered Orders =
CALCULATE ( DISTINCTCOUNT ( FactOrders[OrderID] ), FactOrders[OrderStatus] = "delivered" )
```

```dax
Average Order Value =
DIVIDE ( [Total Revenue], [Delivered Orders] )
```

```dax
Avg Items per Order =
DIVIDE ( [Items Sold], [Delivered Orders] )
```

```dax
Avg Delivery Days =
CALCULATE ( AVERAGE ( FactOrders[DeliveryDays] ), FactOrders[OrderStatus] = "delivered" )
```

```dax
Avg Review Score =
CALCULATE ( AVERAGE ( FactOrders[ReviewScore] ), FactOrders[OrderStatus] = "delivered" )
```

```dax
Late Orders =
CALCULATE ( COUNTROWS ( FactOrders ), FactOrders[OrderStatus] = "delivered", FactOrders[IsLate] = "Yes" )
```

```dax
On-Time Delivery % =
( 1 - DIVIDE ( [Late Orders], [Delivered Orders] ) ) * 100
```

```dax
Canceled Orders =
CALCULATE ( COUNTROWS ( FactOrders ), FactOrders[OrderStatus] = "canceled" )
```

## Payment measures (FactPayments)

```dax
Total Paid =
SUM ( FactPayments[PaymentValue] )
```

```dax
Payment Share % =
DIVIDE ( [Total Paid], CALCULATE ( [Total Paid], ALL ( FactPayments[PaymentType] ) ) ) * 100
```

```dax
Avg Installments (Credit Card) =
CALCULATE ( AVERAGE ( FactPayments[Installments] ), FactPayments[PaymentType] = "credit_card" )
```

```dax
Orders Paying by Card % =
VAR CardOrders =
    CALCULATE ( DISTINCTCOUNT ( FactPayments[OrderID] ), FactPayments[PaymentType] = "credit_card" )
RETURN
    DIVIDE ( CardOrders, [Delivered Orders] ) * 100
```

## Time-intelligence measures (DimDate)

```dax
Revenue PY =
CALCULATE ( [Total Revenue], SAMEPERIODLASTYEAR ( DimDate[Date] ) )
```

```dax
Revenue YoY Growth % =
DIVIDE ( [Total Revenue] - [Revenue PY], [Revenue PY] ) * 100
```

```dax
Orders PY =
CALCULATE ( [Delivered Orders], SAMEPERIODLASTYEAR ( DimDate[Date] ) )
```

```dax
Revenue Cumulative =
VAR MaxDate = MAX ( DimDate[Date] )
RETURN
    CALCULATE ( [Total Revenue], FILTER ( ALL ( DimDate ), DimDate[Date] <= MaxDate ) )
```

## Concentration & rank measures

```dax
State Revenue Share % =
DIVIDE ( [Total Revenue], CALCULATE ( [Total Revenue], ALL ( DimState ) ) ) * 100
```

```dax
State Revenue Rank =
RANKX ( ALL ( DimState[StateCode] ), [Total Revenue],, DESC, DENSE )
```

```dax
Category Revenue Rank =
RANKX ( ALL ( DimProduct[CategoryEN] ), [Total Revenue],, DESC, DENSE )
```

## Customer measures

```dax
Repeat Customers =
VAR OrdersPerPerson =
    ADDCOLUMNS (
        VALUES ( DimCustomer[CustomerUniqueID] ),
        "@n", CALCULATE ( DISTINCTCOUNT ( FactOrders[OrderID] ), FactOrders[OrderStatus] = "delivered" )
    )
RETURN
    COUNTROWS ( FILTER ( OrdersPerPerson, [@n] > 1 ) )
```

```dax
Repeat Customer Rate % =
VAR People =
    CALCULATE ( DISTINCTCOUNT ( DimCustomer[CustomerUniqueID] ),
        FILTER ( FactOrders, FactOrders[OrderStatus] = "delivered" ) )
RETURN
    DIVIDE ( [Repeat Customers], People ) * 100
```

## Display helpers

```dax
Selected State =
SELECTEDVALUE ( DimState[StateName], "All Brazil" )
```

```dax
Selected Category =
SELECTEDVALUE ( DimProduct[CategoryEN], "All categories" )
```

## Retrofit measures (Q15–Q20, added 2026-11-23)

```dax
State AOV =
DIVIDE ( [Total Revenue], [Delivered Orders] )
```
(Choropleth Q15: map [Total Revenue] by DimState[StateCode]; the AOV flip
uses this measure on the same visual's tooltip / a companion bar.)

```dax
Category Revenue Share % =
DIVIDE ( [Total Revenue], CALCULATE ( [Total Revenue], ALL ( DimProduct[CategoryEN] ) ) ) * 100
```
(Treemap Q16: [Total Revenue] by DimProduct[CategoryEN]; share labels use
this measure.)

```dax
Orders Placed =
COUNTROWS ( FactOrders )
```

```dax
Delivered Conversion % =
DIVIDE ( [Delivered Orders], [Orders Placed] ) * 100
```
(Funnel Q18: stages Placed → Delivered use [Orders Placed] and
[Delivered Orders]; [Canceled Orders] already exists above. The approved /
shipped milestones come from the source timestamps `order_approved_at` and
`order_delivered_carrier_date`, which the star schema does not carry — add
them as columns on FactOrders before import if the funnel needs all four
stages in Power BI. The web edition reads them from the raw orders table.)

```dax
Revenue 2017 =
CALCULATE ( [Total Revenue], DimDate[Year] = 2017 )
```

```dax
Revenue 2018 =
CALCULATE ( [Total Revenue], DimDate[Year] = 2018 )
```

```dax
Revenue Change 2017-18 =
[Revenue 2018] - [Revenue 2017]
```
(Waterfall Q19: waterfall visual by DimProduct[CategoryEN] with
[Revenue Change 2017-18], totals pinned by [Revenue 2017] / [Revenue 2018].)

Heatmap Q17 needs purchase *hour*: the star schema keeps the purchase date
(DimDate[Weekday] covers the rows of the heatmap) but not the time of day.
Add an `Hour` column to FactOrders from `order_purchase_timestamp` before
import (or a small DimTime table); the measure on the visual is simply
[Delivered Orders].

Word cloud Q20 uses the WordCloud custom visual over the review comment
text. Review *text* is not in the star schema (only the order's mean
ReviewScore), so import `olist_order_reviews_dataset.csv` as a standalone
table if the cloud is needed in Power BI; no DAX is involved — the visual
counts words itself. The web edition tokenises the 39,073 delivered-order
comments directly (Portuguese stopwords removed).

---

Notes
- KPI cards on Page 1 use: Total Revenue, Delivered Orders, Average Order
  Value, Avg Delivery Days, Avg Review Score, Freight Share % — each with its
  PY companion (Revenue PY, Orders PY, …) as the delta.
- The gauge on Page 2 uses [On-Time Delivery %] with the target set to 95.
- FactOrders[ReviewScore] is the order's mean review score (551 orders carry
  two review rows in the source; the prep takes the mean — see model_diagram).
- FactPayments is *not* scope-filtered by status in the CSV; restrict it to
  delivered orders on the Delivery & Payments page via the OrderStatus field
  of FactOrders (both facts share DimDate and DimCustomer; alternatively
  filter the page to orders in FactOrders with OrderStatus = "delivered").
- AOV uses item revenue only. An "AOV incl. freight" variant is
  `DIVIDE ( [Total Revenue] + [Total Freight], [Delivered Orders] )`
  = R$159.83 on the full dataset.
