# Store 1 Break-Even Model — Beeramguda (Home format)

> **Status: first-pass model. Every number below is an assumption to verify with real quotes.** It is not market data. Replace each input with a Beeramguda rent quote, wage quote and supplier margin sheet before using it in a pitch.

## TL;DR

- At the blueprint's own gate (**120 bills/day × ₹200 basket**), Store 1 **loses about ₹18K a month** before any payback on the ₹25–45L build cost.
- Operating break-even needs **~137 bills/day at ₹200**, or **120 bills at ~₹228**.
- Paying back ~₹35L of capex in 3 years needs **~₹13.6L/month goods sales (~227 bills/day at ₹200)**.
- **Services add commission income, not revenue.** A ₹2,000 AEPS withdrawal earns the store roughly ₹8, not ₹2,000.
- The **agent model (₹15–20K + 10% commission on sales)** gives away more than half of gross margin. It can't work at an 18% margin.

---

## 1. Inputs (monthly, 30 trading days)

### Revenue

| Line | Assumption | Monthly |
|---|---|---|
| Goods sales | 120 bills/day × ₹200 basket | ₹7,20,000 |
| Blended gross margin on goods | **18%** (see §2) | **₹1,29,600** |
| Services commission | AEPS, bill pay, recharge, xerox, courier (see §3) | **₹18,000** |
| **Total gross profit** | | **₹1,47,600** |

### Operating costs

| Cost | Assumption | Monthly |
|---|---|---|
| Rent | ~1,200 sqft main-road shop, ~₹33/sqft *(get a quote)* | ₹40,000 |
| Staff | 4 counter/floor staff × ₹15K (two shifts, ~14 hrs open) + 1 store lead ₹25K | ₹85,000 |
| Power | Chillers, freezers, lights, 1 AC ≈ 1,800 units × ~₹9 | ₹16,000 |
| Waste + shrinkage | Fresh waste target <10% of fresh sales, plus theft/damage | ₹10,000 |
| Payment fees | UPI P2M mostly free; small card MDR | ₹2,000 |
| Software, internet, SMS | POS licence, broadband, SMS credits | ₹5,000 |
| Maintenance, packaging, misc | Bags, repairs, cleaning, stationery | ₹8,000 |
| **Total operating cost** | | **₹1,66,000** |

### Result

| | Monthly |
|---|---|
| Gross profit | ₹1,47,600 |
| Operating cost | ₹1,66,000 |
| **Operating profit** | **−₹18,400** |

---

## 2. Why 18%, not the blueprint's 25–30%

| Category | Typical gross margin (to verify with suppliers) | Share of Consto's basket |
|---|---|---|
| Staples (rice, dal, atta, oil, sugar) | 5–10% | High — this is what drives footfall |
| Packaged FMCG (biscuits, soap, shampoo) | 10–18% | High |
| Dairy, bread, eggs | 5–12% | High, daily |
| Fresh (batter, vegetables, flowers) | 25–35% before waste | Medium |
| Food-to-go, snacks, beverages, puja items | 30–50% | Low to medium |

High-margin lines are real but small. The daily basket is mostly staples, dairy and FMCG, which pulls the blend toward **15–20%**. Plan on 18% until real POS data says otherwise.

---

## 3. Services count as commission, not revenue

The blueprint books services as "25% of revenue at 60% margin". That treats the full transaction value as sales. The store only keeps the fee.

| Service | Volume/day (assumed) | Store earns per txn (verify with provider) | Monthly |
|---|---|---|---|
| AEPS cash withdrawal | 30 | ~₹8 (≈0.4% of ₹2,000) | ₹7,200 |
| Bill pay + mobile/DTH recharge | 30 | ~₹5 | ₹4,500 |
| Xerox, print, courier, misc | — | — | ₹6,300 |
| **Total** | | | **₹18,000** |

Services earn their place by **bringing people in**, not by paying the rent. Count them as a footfall driver.

---

## 4. Sensitivity — monthly operating profit

Formula: `bills × basket × 30 × 18% + ₹18K − ₹1.66L`

| Bills/day ↓ · Basket → | ₹200 | ₹250 |
|---|---|---|
| 80 | −₹61,600 | −₹40,000 |
| 120 | −₹18,400 | +₹14,000 |
| 150 | +₹14,000 | +₹54,500 |
| 200 | +₹68,000 | +₹1,22,000 |

**Read it like this:** basket size matters as much as footfall. Getting 120 customers to spend ₹250 instead of ₹200 is the same as finding 30 extra customers a day. This is where the memory layer has to earn its keep: "your usual Horlicks is here" raises the basket.

### Break-even points

| Target | Goods sales needed | = bills/day at ₹200 |
|---|---|---|
| Operating break-even | (₹1.66L − ₹18K) ÷ 18% ≈ **₹8.2L/month** | **~137** |
| + pay back ₹35L capex in 36 months (~₹97K/month) | ≈ **₹13.6L/month** | **~227** |

---

## 5. Why the agent's 10% commission doesn't pencil

Blueprint: agents get **₹15–20K base + 10% commission**.

Example: one agent looks after 50 households spending ₹4,000/month each.

| | Monthly |
|---|---|
| Sales from agent's households | ₹2,00,000 |
| Gross margin at 18% | ₹36,000 |
| 10% commission on sales | −₹20,000 |
| Base salary (midpoint) | −₹17,500 |
| **Left for rent, power, waste, profit** | **−₹1,500** |

A 10% commission on **sales** gives away 55% of the **gross margin** before paying the base salary. It only works in categories with 40%+ margin.

**Options that do work:**

| Model | How it pays | Why it's safer |
|---|---|---|
| Commission on gross profit | 10–15% of GP from their households | Scales with margin, not turnover |
| Retention bonus | Flat ₹X per household still active after 90 days | Pays for the memory behaviour you want |
| No agents, trained cashiers | Memory panel on the POS does the remembering | Cheapest. Tests the thesis without a new job role |

**Recommendation:** start with trained cashiers + the memory panel. Add a retention bonus only if the 60-day pilot shows remembered customers come back more.

---

## 6. What to verify before trusting this

1. Rent quotes for 3 shops of 1,000–1,500 sqft on Beeramguda main road.
2. Supplier margin sheets for your top 50 SKUs (distributor + cash-and-carry).
3. AEPS / BBPS provider commission cards (per-transaction payout).
4. Local wage rates for counter staff and a store lead.
5. Electricity load estimate from a refrigeration vendor.
6. Footfall count: stand outside 2 competing stores for a morning and an evening.

Update this file with real numbers as they come in.
