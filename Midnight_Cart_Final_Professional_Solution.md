# Midnight Cart: The 2 AM Problem

**DSAI Challenge — IIT Roorkee × Masai · Campus Immersion 2026**  
**Prepared for:** Rohan Mehta, City Operations Head  
**Purpose:** Diagnose cancellations, assess contribution pressure, evaluate cancellation prediction, and recommend practical changes.

## 1. Executive summary

Midnight Cart's cancellation problem is concentrated in specific operating conditions rather than being evenly distributed across all orders. In the cleaned analysis, the overall cancellation rate is **14.7%**. The clearest operational warning is the **11 PM peak**, when **96 of 241 orders were cancelled (39.8%)**. Orders with a displayed ETA above 20 minutes had a **64.3% cancellation rate (74 of 115 orders)**. The FIRST50 offer is another area to investigate: its cancellation rate was **31.6% (112 of 354 orders)**, compared with **11.9% (266 of 2,230 orders)** for orders without a coupon.

**Recommended first move:** run a controlled FIRST50 coupon-policy test while preparing a targeted late-night operations pilot. The analysis shows where to test; it does not establish causality or prove that any one change will automatically improve profit.

## 2. Problem and questions

Midnight Cart is a quick-commerce dark store serving hostels and nearby housing. Orders are growing, but profit is flat and late-night complaints are rising. Rohan Mehta can make one major change next month: hire more night riders, change coupon policy, or change night-time stock and prioritisation.

This analysis answers:
1. Who cancels, and what patterns separate cancelled from delivered orders?
2. When and where do cancellations and contribution pressure appear?
3. Can a logistic regression model identify higher-risk orders at checkout?
4. What three decisions should the company test, and how should their ₹ impact be measured?

## 3. Data quality and cleaning

The source file contained **2,784 raw rows**. The documented cleaning process removed **84 exact duplicate rows** and excluded **15 zero-value baskets** from the main contribution analysis, leaving **2,685 analysis rows**. The observed cancellation count in that analysis was **394**, or **14.7%**.

Cleaning principles:
- Parse mixed timestamp formats and derive hour-of-day consistently.
- Standardise known spelling variants in categories and hostel zones.
- Remove exact duplicate records and document each removal.
- Exclude zero-value baskets from contribution calculations, while keeping the cleaning count visible.
- Check missing values, impossible numeric values, and unexpected category labels before modelling.
- Do not silently assign an unknown hostel zone to a known zone. Keep an explicit `Unknown` bucket or investigate the source values.
- Retain the original raw data and create a separate cleaned analysis frame for reproducibility.

**Important:** an exact duplicate row and a repeated `order_id` are not automatically the same thing. Duplicate IDs should be inspected before dropping records solely on `order_id`.

## 4. Main findings

### Finding A — 11 PM is the highest-risk hour

At 11 PM, **96/241 orders were cancelled: 39.8%**. At midnight, **35/139 orders were cancelled: 25.2%**.

**So what?** Start any rider-coverage or service-level pilot around the 11 PM peak. Compare completed orders, ETA, rider utilisation and cancellations before and during the pilot; do not assume the correlation alone proves a staffing shortage.

### Finding B — long displayed ETA is associated with cancellation

For orders where `eta_shown_min > 20`, **74/115 orders were cancelled (64.3%)**.

**So what?** Monitor the checkout ETA as an early-warning signal. Review whether ETA inflation reflects rider capacity, picking delays, stock availability or distance. The observed association does not prove ETA itself causes cancellation.

### Finding C — FIRST50 orders cancel more often

FIRST50 had **354 orders**, with **112 cancellations (31.6%)**. Orders without a coupon had **2,230 orders**, with **266 cancellations (11.9%)**.

**So what?** Audit offer eligibility, customer intent, basket economics and fulfilment experience. Use a controlled test before permanently changing the offer; coupon users may differ from non-coupon users for reasons beyond the coupon.

### Finding D — exam weeks are higher risk

During exam week, **120/480 orders were cancelled (25.0%)**. Outside exam week, **274/2,205 orders were cancelled (12.4%)**.

**So what?** Plan a specific exam-week operating playbook: night essentials, ETA monitoring, and staffing coverage based on observed demand.

### Finding E — late-night COD has elevated cancellation

For late-night orders, COD had **48/115 cancellations (41.7%)**, compared with **10/57 (17.5%)** for Card payments.

**So what?** Treat payment mode as a segment to investigate, not proof of customer intent. Review COD confirmation and failed-delivery reasons before adding friction to checkout.

## 5. SQL analysis added

The final notebook includes SQLite queries over the cleaned analysis frame:
- Hourly cancellation rate, cancellations, average ETA and riders on duty.
- Midnight (12 AM–2 AM) comparison for exam week versus non-exam week.
- FIRST50 versus no-coupon cancellation rates split by basket size.
- Delivered-order contribution proxy and discounts by coupon.

Each query includes group counts so percentages can be interpreted with their denominators. SQL findings are descriptive and should be read alongside the charts and modelling results.

## 6. Contribution analysis

The contribution proxy used is:

`Contribution proxy = basket_value_inr × margin_pct / 100 − discount_inr`

For delivered FIRST50 orders:
- Delivered orders: **242**
- Average contribution proxy: **−₹18.95 per delivered order**
- Total contribution proxy: **−₹4,586.75**
- Total discounts on those delivered orders: **₹12,055**

This is a **contribution proxy, not audited net profit**. It excludes delivery labour, picking and packing, spoilage, payment fees, refunds and other operating costs. A negative proxy supports investigation and controlled testing; it is not proof that removing FIRST50 will produce the same amount in savings.

## 7. Logistic regression model

The notebook uses logistic regression with a preprocessing pipeline and a **chronological holdout**, so the test data represents later orders than the training data. It uses features that could plausibly be known at checkout, and excludes `rating` and `refund_inr` because they may only be known after the order outcome.

Reported chronological-test metrics:
- Test rows: **672**
- Cancellation prevalence in test set: **18.45%**
- Precision: **36.6%**
- Recall: **78.2%**
- F1 score: **49.9%**
- ROC-AUC: **0.812**
- Confusion matrix: **97 true cancellations flagged, 27 missed cancellations, 97 delivered orders incorrectly flagged** (with the remaining test orders correctly classified as delivered).

**Plain-English interpretation:** at the selected threshold, the model catches about 78 out of every 100 cancellations, but only about 37 out of every 100 orders it flags are actually cancelled. This makes it more suitable for prioritising manual checks or operational attention than automatically blocking orders.

**Limitation:** these are historical test-set results. Model performance can change as demand, staffing, promotions and customer behaviour change. Precision/recall also depend on the chosen threshold.

## 8. Three decisions for Rohan

### Decision 1 — Test a safer FIRST50 coupon policy

**Action:** run an A/B test of the current offer against a minimum-basket threshold or a lower discount. Keep a control group. Measure cancellation rate, delivered orders, average basket value and contribution proxy per eligible order.

**Evidence:** FIRST50 cancellation rate is **31.6%**, versus **11.9%** for no coupon; delivered FIRST50 contribution proxy averages **−₹18.95/order**.

**₹ working:** an illustrative scenario is a **₹10 improvement per eligible delivered order × 300 eligible delivered orders/month = ₹3,000/month**. This is not a guaranteed outcome; replace the ₹10 and 300 assumptions with measured test results. Do not present the observed −₹4,586.75 proxy as guaranteed savings.

### Decision 2 — Pilot targeted rider coverage at the 11 PM peak

**Action:** run a time-bounded pilot focused on 11 PM and the 12–1 AM period. Compare pilot and control periods on ETA, cancellations, completed orders, rider utilisation and incremental operating cost.

**Evidence:** 11 PM cancellation rate is **39.8%**; orders with ETA above 20 minutes show **64.3% cancellation**.

**₹ working:** calculate the net impact from observed pilot data:  
`Incremental completed orders × contribution proxy per completed order − incremental rider cost`.  
The provided order file does not contain rider wages or a causal estimate of orders saved, so a credible absolute ₹ forecast cannot be calculated from the file alone. Do not invent a rider-cost estimate.

### Decision 3 — Prioritise a small night-time essentials assortment

**Action:** use hour × category × hostel-zone demand to define a small night-priority assortment, then monitor stock-outs, substitutions, cancellations and contribution. Prioritise only categories supported by observed data; do not assume every example product has the same demand.

**Evidence:** the brief identifies late-night and exam-week demand as the business context; the notebook includes category, zone and hour breakdowns for operational diagnosis.

**₹ working:** estimate after a pilot using:  
`Recovered delivered orders × contribution proxy per order − incremental inventory/holding/spoilage cost`.  
The dataset does not provide complete stock-out, spoilage or holding-cost fields, so the net ₹ impact is currently unquantified. Record those measures during the pilot.

### Decision priority

Start with **Decision 1 (coupon test)** because the dataset directly quantifies the FIRST50 cancellation difference and negative delivered-order contribution proxy. Run a targeted night operations pilot in parallel if operational capacity permits, but make the rider and inventory decisions conditional on measured pilot results.

## 9. Model and financial guardrails

- Do not use `rating` or `refund_inr` as checkout-time prediction features.
- Keep the chronological split; random-split metrics are not directly comparable.
- Do not interpret correlated or redundant model features as independent causes.
- Do not treat association as causation.
- Do not label the contribution proxy “profit” or “net profit.”
- Do not claim a guaranteed ₹ saving without a controlled test and operating-cost data.
- Use the model to prioritise investigation, not automatically reject customers.

## 10. Five-slide presentation plan

The challenge brief allows **five slides maximum and six minutes**:
1. Problem statement + data problems found and fixes.
2. Insight: 11 PM cancellation peak and late ETA.
3. Insight: coupon and exam-week patterns + contribution proxy.
4. Logistic regression: checkout-time features, unseen-data performance, precision/recall, top drivers and one limitation.
5. Three decisions for Rohan: action, ₹ working, and evidence.

All four or more team members should speak. Keep each slide to one chart, one key number and a plain-English “so what?” where possible.

## 11. Final message to the company

Midnight Cart should not respond to the cancellation problem with a broad, untested change. The evidence points to a combination of late-night service risk and coupon economics. Test FIRST50 policy changes first, pilot targeted night coverage around 11 PM, and use category/zone/hour analysis to design a night-priority assortment. Track outcomes with a control group and include full operating costs before scaling. The goal is not simply fewer cancellations; it is **more completed orders with positive, measured contribution**.
