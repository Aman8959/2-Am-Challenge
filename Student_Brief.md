# DSAI Challenge — Midnight Cart: the 2 AM problem

**IIT Roorkee × Masai · Campus Immersion 2026**

> Students order Maggi, energy drinks and phone chargers at 1 AM during exam week.
> Midnight Cart keeps losing those orders. Find out why, and tell the boss what to do.

---

## 1. The story

Midnight Cart is a quick-commerce **dark store** — a small warehouse used only for fast home delivery — serving hostels and housing near a large university campus. It promises delivery in about 15 minutes, and students love it, especially in exam week, when they order Maggi, energy drinks and phone chargers at 1 AM.

Over the last 90 days orders have kept growing. But **profit is flat and late-night complaints are rising.**

**Rohan Mehta**, the City Operations Head, can make **one big change** next month:

- hire more night riders, **or**
- change the coupon policy, **or**
- change what the store stocks and prioritises at night.

He has no time to read thousands of rows. He wants your team of data consultants to tell him **what is really going on, what to do, and what it is worth in ₹.**

---

## 2. What you must answer

1. **Who cancels?** What separates cancelled orders from delivered ones?
2. **When and where does Midnight Cart lose money?** Look at hour of day, hostel zone, coupons, product categories and exam week.
3. **Can you predict a cancellation?** Build a logistic regression model and check how well it works.
4. **What should Rohan do?** Give **3 decisions**. Each needs an action, a ₹ estimate with your working, and the evidence behind it.

There is **no single right answer.** Different teams can recommend different things — what matters is that your evidence supports your advice.

---

## 3. The data

One file: **`midnight_cart_orders.csv`** (about 2,800 rows of orders from one store over about 90 days). You receive it at the start of Day 2.

| Column | What it means |
|---|---|
| order_id | Order number |
| order_datetime | When the order was placed |
| hostel_zone | Delivery area |
| items_count | Number of items in the order |
| basket_value_inr | Order value in ₹ (before any coupon) |
| discount_inr | Coupon discount in ₹ |
| top_category | Main product category (Snacks, Drinks, Instant food, Electronics, Stationery, Health) |
| margin_pct | Share of the basket value that is profit for the store |
| payment_mode | UPI, Card or Cash on delivery |
| coupon_code | NONE, FIRST50 (₹50 off a first order) or EXAM20 (20% off, up to ₹60) |
| is_first_order | Y/N — customer's first order? |
| exam_week | Y/N — was it an exam week? (The store's day runs 10 AM to 2 AM, so an order after midnight counts as part of the previous day.) |
| rain_mm | Rainfall around that hour |
| riders_on_duty | Riders working at that time |
| eta_shown_min | Delivery time (minutes) the app showed the customer at checkout |
| cancelled | **1 = cancelled, 0 = delivered** |
| rating | Customer rating, 1–5 |
| refund_inr | Refund given, in ₹ |

**Real data is messy.** Expect problems. Part of your job is to find and fix them.

**A rule of thumb for any prediction model:** only use information you would really have *at the moment you need the prediction*. Ask this about every column.

---

## 4. What you show on stage — 5 slides, 6 minutes

| Slide | Content |
|---|---|
| 1 | The problem, and **the problems you found in the data and how you fixed them** |
| 2–3 | **Insights.** Each with one chart, one number, and a "so what?" |
| 4 | **Your prediction model** (logistic regression): the columns you used, how well it works on data it has not seen, in plain words, the top drivers, and one honest limitation |
| 5 | **3 decisions for Rohan.** Each with: the action · ₹ impact with your working · which finding supports it |

Then **3 minutes of questions** from the judges.

**How to submit:** put your Colab notebook and your slides in **one shared folder** ("Anyone with the link can view") and give the folder link to your mentor by the deadline.

---

## 5. How you are scored (100 marks)

| Part | Marks | What the judges look for |
|---|---|---|
| **Data cleaning** | 10 | Which problems you found in the data and how you handled them |
| **Insights** | 20 | The judges have a list of key patterns hidden in the data. Each one you show **with a number** earns marks. |
| **Prediction model** | 20 | Tested on data the model has not seen · recall **and** precision explained · only information known at order time · top drivers in plain words · one honest limitation |
| **Recommendations** | 30 | For each of your 3 decisions: specific action (4) · ₹ calculation shown (4) · linked to a finding (2) |
| **Presentation** | 20 | Clear and easy to follow (10) · 4 or more team members speak (5) · you finish within the time (5) |

**The best team is not the one with the fanciest model.** It is the one that gives Rohan the clearest decision, backed by numbers.

Ties are broken by the Recommendations score, then Insights, then one surprise question from the judges.

---

## 6. Rules

- **Tools:** Google Colab (starter notebook provided), Pandas, NumPy, Seaborn, scikit-learn, and SQL through `sqlite3` inside Colab. Sheets are fine for quick checks.
- **GenAI tools** may help with code or wording — but you must be able to explain every number on your slides.
- **Data:** use only the file provided. No outside data.
- **Slides and time:** 5 slides maximum. You have 6 minutes, with a warning at 5:00 and 6:00. You will be stopped at 6:30. Finishing by 6:30 earns the time marks.
- **Fair play:** no sharing results between teams. Mentors will help with *how*, not with *what to conclude*.

---

## 7. Your Day 2 clock

| Milestone | Time |
|---|---|
| Data released, build starts | ______ |
| Folder link (notebook + slides) due | ______ |
| Showcase starts | ______ |

*(Your mentor will write the times on the board.)*

---

## 8. Five tips from people who've done this

1. **Look before you model.** A simple chart often beats a complicated model.
2. **Split the work on Day 1 evening.** Everyone should know their role before Day 2 starts.
3. **Write your "so what?" in plain words.** "Cancellations jump when X" is better than "coefficient = 0.8".
4. **Do the ₹ maths early.** Teams that leave recommendations for the last 10 minutes lose marks.
5. **One working model beats three half-finished ones.**

Good luck, consultants.
