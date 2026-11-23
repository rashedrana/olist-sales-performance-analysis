# Business Questions & Chart Problem-Solving
## Olist Sales Performance Dashboard — 20 questions, 20 visual types

Dataset: Brazilian E-Commerce Public Dataset by Olist (Kaggle, CC BY-NC-SA 4.0) —
**99,441 orders · 112,650 order items · 103,886 payment rows · 99,224 reviews ·
32,951 products · 3,095 sellers**, orders placed 4 Sep 2016 – 17 Oct 2018.
Every answer below was computed from the project data in `data/`.
**Scope, stated openly:** sales figures use *delivered* orders only — 96,478 of
99,441 orders (97.0%) holding 110,197 items. The 625 canceled orders and the
orders still in transit at the October 2018 snapshot are excluded, so purchase
months in this scope run Sep 2016 – Aug 2018. Revenue = item price; freight is
tracked separately. Where a comparison involves the partial years 2016
(Sep–Dec) or 2018 (Jan–Aug), the answer says so.

---

**Q1. How big is the business, at a glance?**
Visual: **KPI cards** (Item Revenue, Delivered Orders, Average Order Value,
Avg Delivery Days, Avg Review Score, Freight Share) with previous-year deltas.
Answer (all delivered orders, Sep 2016 – Aug 2018): **R$13,221,498 item
revenue** · **96,478 orders** · **R$137.04 average order value** · **12.6 days**
average delivery (median 10.2) · **4.16 / 5** average review · freight adds
**R$2,198,276 — 16.6%** on top of item revenue (R$22.79 per order). Customers
paid R$15,422,462 in total across these orders — items + freight, reconciled
against the payments table.

**Q2. How did sales grow — and when was the single biggest month?**
Visual: **Line chart** (monthly item revenue, metric-switchable to orders /
AOV via bookmarks).
Answer: Revenue climbed from R$111,798 in Jan 2017 to a plateau of
R$0.85–0.99M per month in 2018. The peak month of the whole dataset is
**November 2017 — R$987,765 and 7,289 orders, +63% more orders than October
2017 (4,478)**: the Black Friday spike. Full-year revenue grew from
R$5,962,902 (2017) to R$7,218,125 in just the first eight months of 2018.
Orders grew 43,428 (2017) → 52,783 (Jan–Aug 2018) while AOV stayed flat
(R$137.31 → R$136.75) — growth is volume, not basket size.

**Q3. Where does the money come from geographically?**
Visual: **Clustered column chart** (item revenue by customer state).
Answer: **São Paulo state alone is R$5,067,633 — 38.3% of all revenue** —
more than the bottom 24 states combined. Rio de Janeiro follows at
R$1,759,651 (13.3%) and Minas Gerais at R$1,552,482 (11.7%); the top 3 states
hold **63.4%** of revenue. The smallest market, Roraima, contributes
R$7,057 (0.1%) from 41 orders — a 718× gap to São Paulo.

**Q4. What sells?**
Visual: **Bar chart** (item revenue by product category, top 12 + other).
Answer: **Health & beauty leads with R$1,233,132 (9.3%)**, then watches &
gifts R$1,166,177 (8.8%) and bed, bath & table R$1,023,435 (7.7%). By order
count the order flips: bed, bath & table is in the most orders (9,272),
health & beauty second (8,647). The catalogue is long-tailed — 74 categories,
and the top 10 take 62.4% of revenue while 50 categories each hold under 1%.

**Q5. How do customers pay — and how much is bought on credit?**
Visual: **Donut chart** (share of amount paid by payment type).
Answer: **Credit card carries 78.5% of all money paid** (R$12,101,095 across
74,304 orders), boleto (bank slip) 18.0% (R$2,769,933, 19,191 orders),
voucher 2.2% and debit card 1.4%. Credit is installment-driven: the average
card payment is split into **3.5 installments**, **67% of card orders use
more than one installment** (up to 24), and only 20% of card value is paid
in a single installment. Boleto tickets average R$144.33 vs R$162.24 for
credit card — cash payers buy smaller baskets.

**Q6. Does slow delivery actually hurt satisfaction?**
Visual: **Scatter / bubble chart** (average review score by delivery-time
band, bubble size = orders).
Answer: Yes — monotonically. Average score falls band by band: **4.45**
(0–5 days, 13,430 orders) → 4.36 (6–10) → 4.25 (11–15) → 4.10 (16–20) →
3.61 (21–30) → **2.18** (31+ days, 4,117 orders). The sharpest cut is
punctuality, not speed: orders delivered **after the promised date average
2.57 stars vs 4.29 for on-time orders**, and late orders took 31.5 days on
average vs 10.9 for on-time ones. Overall distribution is top-heavy —
59.2% of reviewed orders give 5 stars — so the tail is a delivery problem,
not a product problem.

**Q7. What drove revenue growth from 2017 to 2018?**
Visual: **Waterfall chart** (revenue bridge by category contribution).
Answer: Revenue rose **+R$1,255,223** (R$5,962,902 → R$7,218,125; note 2018
is Jan–Aug of delivered purchases vs the full 2017). The bridge is built by
health & beauty (**+R$281,892**), watches & gifts (+R$212,244) and housewares
(+R$169,305). Three categories dragged: cool stuff (**−R$153,671**), toys
(−R$130,933) and computers (−R$93,877) — the 2017 gadget fad faded while
everyday categories compounded.

**Q8. How concentrated is revenue by region?**
Visual: **Treemap** (item revenue by region → state).
Answer: The **Southeast takes 65.4%** of revenue (R$8,648,410), the South
14.4%, the Northeast 11.3%, the Central-West 6.4% and the North just 2.5%
(R$327,075). São Paulo state alone (38.3%) outsells the entire North,
Northeast and Central-West combined (20.2%) — the marketplace is a
southeastern business with a national storefront.

**Q9. Which states order most — and which are barely reached?**
Visual: **Filled map** (delivered orders by customer state).
Answer: All 27 states appear, but the spread is extreme: São Paulo 40,501
orders (42.0% of all orders), Rio de Janeiro 12,350, Minas Gerais 11,354 —
then a long drop to Roraima (41), Amapá (67) and Acre (80). The North's
seven states together place fewer orders (1,796) than the single
Distrito Federal market (2,080).

**Q10. Is the state ranking stable over time?**
Visual: **Matrix with conditional formatting** (revenue by state × year,
top states).
Answer: The podium never changes — **SP #1, RJ #2, MG #3 in 2016, 2017 and
2018** (SP: R$12,046 in the stub of 2016 → R$2,136,259 in 2017 →
R$2,919,328 in 2018). The only movement in the top 5 is Paraná climbing
#6 → #5 → #4, passing Rio Grande do Sul in 2018 (R$376,623 vs R$375,581 —
a 0.3% margin). Concentration is structural, not a phase.

**Q11. Are deliveries keeping the promise?**
Visual: **Gauge** (on-time delivery rate vs a 95% target).
Answer: **91.9% of delivered orders arrived on or before the estimated
date — below a 95% service target**, and the trend is wrong: the late rate
rose from 1.5% (2016, tiny base) to 6.6% (2017) to **9.4% (2018)** as volume
scaled. 7,826 orders arrived late. Given Q6 (late = 2.57 stars), the
growing late tail is the single biggest satisfaction risk in the data.

**Q12. How fast did the marketplace accumulate its revenue?**
Visual: **Area chart** (cumulative item revenue by month).
Answer: Cumulative revenue crossed **R$10M in May 2018**, 20 months after
the first orders, and closed at R$13.2M in Aug 2018. Half of all revenue
ever earned (R$6.6M) arrived in the final ten months of the window — the
curve is still bending upward at the snapshot; nothing in the trend
suggests saturation.

**Q13. Who is chasing São Paulo?**
Visual: **Ribbon chart** (state revenue rank by year, top 6 states).
Answer: Nobody, yet. SP, RJ and MG hold ranks 1–3 in every period. Below
them the ribbons cross once: RS starts 2016 at #4, PR passes it during 2018
to take #4, and SC holds #6 throughout. The competitive story is a fight
for fourth place — the gap from #3 MG (R$1.55M) to #4 PR (R$0.67M) is
larger than PR's entire revenue.

**Q14. Where exactly does each real of revenue come from?**
Visual: **Decomposition tree** (revenue → region → state → category).
Answer: Follow the biggest path: of R$13.22M total, the Southeast holds
R$8.65M (65.4%); within it, São Paulo state holds R$5.07M (58.6% of the
region); within São Paulo, the top category is bed, bath & table at
R$472,238 (9.3% of the state), just ahead of health & beauty (R$453,916).
Two more structural facts hang off the tree: repeat business is rare —
only **2,801 of 93,358 customers (3.0%) ever placed a second delivered
order** — and the seller side is a long tail too: the top 100 of 2,970
active sellers take 45.5% of revenue, while 488 sellers (16%) sold exactly
one item. Freight burden also splits by geography: it eats 28.1% of item
revenue in Roraima and 26.3% in Maranhão vs 13.9% in São Paulo — distance
is a tax on the poorest markets.

---

Coverage checklist (Power BI visual palette): KPI card ✓ · line ✓ ·
clustered column ✓ · bar ✓ · donut ✓ · scatter/bubble ✓ · waterfall ✓ ·
treemap ✓ · filled map ✓ · matrix ✓ · gauge ✓ · area ✓ · ribbon ✓ ·
decomposition tree ✓ — plus slicers, bookmarks and drill-through
(layout spec).

---

## Retrofit — six more questions (Q15–Q20, added 2026-11-23)

Six advanced visual types added to the web edition, each answering a new
question. Same delivered-orders scope (96,478) unless a question says
otherwise; every figure recomputed from `data/` and cross-checked against
the star-schema CSVs.

**Q15. Where is the money on the map — and does the revenue map match the
basket-size map?**
Visual: **Choropleth map** (item revenue by customer state, IBGE boundaries).
Answer: The revenue map is a one-state story: **São Paulo R$5,067,633
(38.3%) is larger than the bottom 24 states combined (R$4,841,732)**, and
13 of the 27 states each hold under 1% of revenue (Roraima last at R$7,057).
But the basket map inverts it: average order value is **highest in Paraíba
(R$217.77)**, then Amapá (R$199.62), Acre (R$199.14) and Alagoas (R$198.63),
and **lowest in São Paulo (R$125.12)** — below the national R$137.04, with
Espírito Santo (R$134.66), Paraná (R$135.30) and Rio Grande do Sul (R$136.37)
just above it. Far-from-the-Southeast customers order rarely but big:
freight makes small baskets uneconomic (compare Q14's freight burden).

**Q16. What is the revenue actually made of?**
Visual: **Treemap** (item revenue by product category, top 15 + other).
Answer: The **top 15 of 74 categories hold R$10,093,410 — 76.3% of all
revenue**; health & beauty is the largest single block at R$1,233,132 (9.3%)
and the top 3 together are 25.9%. The surprise is the tail: the **59
remaining categories sum to R$3,128,088 (23.7%) — 2.5× the biggest single
category** — so assortment breadth, not just the hits, is a quarter of the
business.

**Q17. When exactly does demand happen, hour by hour?**
Visual: **Heatmap** (delivered orders by weekday × hour of purchase).
Answer: Demand is a weekday-afternoon phenomenon. The single busiest cell
is **Tuesday 14:00 with 1,095 orders**; Monday is the busiest day overall
(15,701 orders) and the week fades steadily to Saturday's low (10,555).
**62.0% of all orders are placed between 09:00 and 18:00**, only 4.8%
between midnight and 06:00. Weekends still take 23.0% of orders, and Sunday
evening out-shops Saturday evening (Sunday 20:00: 944 orders vs Saturday
20:00: 708) — the second shopping shift of the week starts on Sunday night.

**Q18. How many orders leak out of the pipeline?**
Visual: **Funnel chart** (order pipeline milestones, **all 99,441 orders** —
this question deliberately leaves the delivered scope, which would hide the
leaks).
Answer: Placed **99,441** → approved **99,281 (99.8%)** → shipped
**97,658 (98.2%)** → delivered **96,478 (97.0%)**. The leaks: 160 orders
were never approved; **1,637 approved orders never shipped** (most
cancellations die here); **1,182 orders handed to a carrier had not
reached the customer** at the snapshot. (Stage-to-stage differences are
14 and 2 smaller respectively — 14 orders carry a carrier date but no
approval timestamp, and 2 delivered orders lack a carrier timestamp in
the source.) **Canceled: 625 orders (0.6%)**, shown alongside the funnel. The
remaining 2,338 orders (2.4%) were still in the pipeline at the October
2018 snapshot — 1,107 shipped, 609 unavailable, 314 invoiced, 301
processing, 5 created, 2 approved. End-to-end completion: 97.0%.

**Q19. Bridge 2017 → 2018: which categories built the growth, and which
taxed it?**
Visual: **Waterfall chart** (revenue bridge by category contribution;
construction: top 5 gainers and top 3 decliners individually, the other 66
categories netted; categories present in only one year are zero-filled, so
the bridge closes exactly).
Answer: Revenue rose from **R$5,962,902 (2017) to R$7,218,125 (Jan–Aug
2018), +R$1,255,223**. The builders: health & beauty **+R$281,892**,
watches & gifts **+R$212,244**, housewares **+R$169,305**, auto
**+R$108,738**, computers & accessories **+R$104,483** — and the netted
other-66 bucket adds **+R$757,042**, so growth was broad, not just five
hits. The tax: cool stuff **−R$153,671**, toys **−R$130,933** and computers
**−R$93,877**. (Companion to Q7: same bridge, drawn as a waterfall with the
long tail netted instead of listed.)

**Q20. What do customers actually talk about in their reviews?**
Visual: **Word cloud** (word frequency in review comments, Portuguese
stopwords removed).
Answer: 39,073 of the delivered orders carry a written comment (40.5% of
review rows), with 14,162 distinct words after stopword removal. The
vocabulary is logistics, not products: **produto** (product) 17,237
mentions, then **prazo** (deadline) 8,262, **entrega** (delivery) 6,206,
**antes** (early) 5,592, **chegou** (arrived) 5,396, **recebi** (I received)
4,749, **bom** (good) 4,568, **recomendo** (I recommend) 4,275. And the
sentiment split matches Q6: comments mentioning *antes* average **4.69
stars** and *recomendo* 4.55, while comments about the *produto* itself
average just **3.60** — praise is earned by the delivery, complaints land
on the product.

---

Retrofit coverage: choropleth ✓ · treemap (by category) ✓ · heatmap ✓ ·
funnel ✓ · waterfall (category bridge) ✓ · word cloud ✓ — 20 questions in
total, each with its own visual type.
