# 05 · Consto Loyalty

> Points, tiers, retention, and churn prediction. Embedded in POS for staff use, plus a web admin for the central team. Drives the "this store gets to know me" loop into measurable retention.

| | |
|---|---|
| **Phase** | 2.3 |
| **Priority** | High |
| **Platform** | Web admin + embedded in Consto POS Agent Panel |
| **Primary user** | Store staff (via POS), central ops team (via admin), customer (passive — sees on receipt) |
| **Build status** | Not started |
| **Dependencies** | Consto POS live. Consto Agent tracking memory. At least 3 months of transaction data for churn prediction. |
| **Depends on it** | Notify (sends tier-upgrade messages, redemption reminders), HQ (reads retention metrics) |

## What it is

A point-based loyalty engine designed for *relationship* retention, not gamification. Points are a numeric signal of activity. Tiers unlock genuine benefits (free delivery on subscribe, festival pre-booking priority, credit limit increase). Churn prediction surfaces customers who haven't visited recently *and* haven't responded to nudges.

The loyalty narrative is: **"the longer you stay with us, the better we know you, and the more flexibility we extend you."** Not "spend ₹500, get ₹50 off."

## Who uses it

| User | What they do |
|---|---|
| **Cashier (POS)** | Sees current points + tier on Agent Panel when customer linked. Can apply redemption. |
| **Central ops** | Reviews tier graduations, sets benefit rules, monitors program health |
| **Customer (indirect)** | Receives points on receipt. Sees tier on Subscribe app (if enabled). |
| **Agent** | Reads tier when generating nudges. Higher tier gets richer treatment. |

## Why it exists

Without measurable retention signal:
- Owner can't tell if memory layer is working
- HQ can't surface "Priya hasn't visited in 21 days, she's at risk"
- Notify has no priority signal for who to message first
- Subscribe has no "lock-in benefit" to offer

Loyalty makes retention *visible* and *actionable*.

## Tech stack

| Layer | Tool |
|---|---|
| Frontend admin | React + Tailwind + Vercel |
| Embedded in POS | Same React, rendered inside POS Electron app |
| DB | Supabase (`customer_loyalty`, `loyalty_transactions`, etc.) |
| AI | Haiku for points calc, Sonnet for churn prediction + tier benefit recommendations |

## Core capabilities

### MVP

| | |
|---|---|
| **Point earning** | 1 point per ₹10 spent, plus bonus for: subscribe, fresh purchases, festival pre-booking. |
| **Tier system** | 4 tiers: Welcome (0-99 pts), Regular (100-499), Family (500-1999), Pillar (2000+) |
| **Tier benefits** | Each tier unlocks real benefits (see Tier benefits section below) |
| **Redemption** | Points can be redeemed for: subscribe-month free, fresh basket, festival kit, donation |
| **Points display in POS** | Agent Panel shows current points + tier + recent activity |
| **Points on receipt** | Every receipt shows balance + tier + "next tier in X visits" |
| **Tier graduation messages** | When customer crosses a tier, Notify drafts an upgrade message |
| **Manual adjustment** | Manager can add/remove points with reason logged |

### Phase 2.5 additions

- AI churn prediction (Sonnet, weekly run)
- "At-risk customer" queue for human outreach
- Custom benefits per persona (working moms get different perks than gig drivers)
- Family accounts (Priya's points pool with husband's)
- Birthday and anniversary bonuses

### Future

- Multi-store points (visit any Consto, points stack)
- Referral bonus (Priya brings a friend, both get 50 pts)
- Tier challenges ("Visit 5 times this month, earn Pillar status faster")

## Tier benefits (Phase 1 design)

| Tier | Points | Benefit |
|---|---|---|
| **Welcome** | 0-99 | Standard pricing. Personalised greeting. |
| **Regular** | 100-499 | Free home delivery within 1 km. Priority festival pre-booking window (24 hr ahead). |
| **Family** | 500-1999 | Free delivery within 2 km. Subscribe price 5% off. Credit limit ₹500 (interest-free, 30 day terms). |
| **Pillar** | 2000+ | Everything in Family + Subscribe price 8% off + Credit limit ₹2000 + Free monthly fresh basket sample. |

These are **real benefits**, not points-for-points-sake.

## Key screens

### Screen 1 — POS embed (inside Agent Panel)

When customer linked in POS, Agent Panel shows a Loyalty section:

```
LOYALTY
Priya · Family tier
Points: 642 · Next tier (Pillar) in 1358 pts
Last 5 visits: ₹2,140 spent · +214 pts
[Apply redemption]
```

Click "Apply redemption" → modal with available redemptions she qualifies for.

### Screen 2 — Central admin landing

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO LOYALTY · ADMIN                                   │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  PROGRAM HEALTH                                            │
│  487 active members · 12% Pillar tier · 28% Family       │
│  Average visits per active member (90 day): 11           │
│  At-risk customers: 24 (haven't visited in 21+ days)     │
│                                                            │
│  THIS WEEK                                                 │
│  Tier upgrades: 7 (3 to Family, 4 to Regular)            │
│  Tier downgrades: 1                                       │
│  Redemptions: 12                                          │
│  Bonus points awarded: 1,240                              │
│                                                            │
│  [At-risk queue] [Tier rules] [Redemptions catalog]       │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 3 — At-risk customer queue

```
At-risk customers (24)

🔴 Lakshmi Aunty — Pillar tier — 28 days since last visit
   Last 90-day pattern: visited every 4-5 days
   Recent draft messages: 0 in last 14 days
   [Outreach via Notify] [Phone call assigned] [Mark resolved]

🟡 Raju — Regular — 18 days since last visit
   ...
```

### Screen 4 — Tier rules editor

Lets central team adjust thresholds + benefits per tier. Changes log for audit.

### Screen 5 — Redemptions catalog

Manager-curated list of what points can be redeemed for. Each redemption: cost in points, value in ₹, eligibility.

### Screen 6 — Per-customer loyalty page

Full history: every point in, every point out, tier changes over time, redemptions claimed.

## Data model (Loyalty-specific tables)

```sql
-- Customer loyalty state
customer_loyalty (
  customer_id uuid primary key references customers,
  current_points int default 0,
  lifetime_points int default 0,
  current_tier text default 'welcome', -- welcome, regular, family, pillar
  tier_achieved_at timestamp,
  last_visit_for_loyalty timestamp,
  is_at_risk boolean default false,
  at_risk_since timestamp,
  family_account_id uuid references family_accounts -- nullable
)

-- Family accounts (Phase 2.5)
family_accounts (
  id uuid primary key,
  primary_customer_id uuid references customers,
  member_count int default 1,
  created_at timestamp default now()
)

-- Point transactions
loyalty_transactions (
  id uuid primary key,
  customer_id uuid references customers,
  transaction_id uuid references transactions, -- nullable for non-purchase events
  points_change int, -- + for earning, - for redemption
  reason text, -- 'purchase', 'subscribe', 'birthday', 'festival_prebook', 'redemption', 'adjustment'
  balance_after int,
  staff_id uuid references staff, -- nullable for automated
  notes text,
  created_at timestamp default now()
)

-- Redemptions catalog
redemptions_catalog (
  id uuid primary key,
  name text,
  description text,
  points_cost int,
  value_paise int,
  min_tier text,
  is_active boolean default true,
  redemption_count int default 0,
  created_at timestamp default now()
)

-- Redemptions claimed
redemptions_claimed (
  id uuid primary key,
  customer_id uuid references customers,
  redemption_id uuid references redemptions_catalog,
  transaction_id uuid references transactions, -- where applied
  points_used int,
  staff_id uuid references staff,
  claimed_at timestamp default now()
)

-- Tier history
tier_history (
  id uuid primary key,
  customer_id uuid references customers,
  from_tier text,
  to_tier text,
  reason text, -- 'graduated', 'demoted', 'manual'
  changed_at timestamp default now()
)

-- Churn predictions
churn_predictions (
  id uuid primary key,
  customer_id uuid references customers,
  prediction_date date,
  churn_probability decimal, -- 0.0 to 1.0
  basis text, -- explanation
  recommended_action text, -- 'outreach', 'wait', 'mark_lost'
  status text, -- 'pending_review', 'actioned', 'recovered', 'lost'
  reviewed_by uuid references staff,
  created_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Point calculation on transaction | None | Simple deterministic math (1 pt per ₹10). |
| Tier graduation check | None | Threshold comparison. |
| Churn prediction (weekly) | Sonnet | Combines visit rhythm + message engagement + tier trajectory + recent rejections. |
| At-risk customer outreach suggestion | Sonnet | Generates draft outreach message via Agent. |
| Tier benefit recommendation engine (Phase 2.5) | Sonnet | Suggests per-persona benefit changes based on observed engagement. |
| Redemption suggestion at POS | Haiku | Quick "this customer qualifies for X" lookup. |

## Build sequence for Claude Code

1. **Scaffold web admin + Supabase wiring**
2. **Build `customer_loyalty` + `loyalty_transactions` schema**
3. **Build point-earning trigger** — listens to `transactions` table, awards points
4. **Build tier graduation logic** — threshold check after each point change
5. **Build POS embed** — Loyalty section inside Agent Panel showing tier + points
6. **Build receipt integration** — points + tier appear on every receipt
7. **Build admin dashboard** — program health, this week's stats
8. **Build redemptions catalog** — admin can add/edit redemptions
9. **Build redemption flow at POS** — staff applies redemption, points deducted
10. **Build at-risk customer detection** — threshold rule first (21+ days since last visit)
11. **Build tier upgrade messages via Notify** — drafted by Agent, queued for review
12. **Wire AI churn prediction** — weekly Sonnet job, populate `churn_predictions`
13. **Build at-risk queue UI** — central team works through it
14. **Test with 50 simulated customers over 3 months of transaction history**

## Success metrics

| Metric | Target |
|---|---|
| 90-day retention rate | 60%+ after 6 months of program |
| Tier graduation rate | 40%+ of Welcome customers reach Regular within 3 months |
| At-risk recovery rate | 50%+ of at-risk customers re-engage after outreach |
| Average visits per active member (90-day) | 10+ |
| Redemption rate | 30%+ of eligible customers claim a redemption each quarter |
| Churn prediction accuracy | 70%+ of predicted-churn customers actually churn within 60 days |

## DPDP compliance

- Loyalty data references customer IDs; no PII in loyalty tables
- Customer can request loyalty deletion (data anonymised, points zeroed) along with full deletion request
- Family account links require both customers' consent

## Brand voice (tier graduation messages, drafted by Agent)

Examples that are **right**:

> "Priya, you just became Family tier. Free delivery within 2 km now, and 5% off Subscribe. Thanks for trusting us."

> "Lakshmi gaaru, you are now a Pillar customer. Highest tier. Your usual credit limit just went to ₹2000 and we will keep your Sankranti items 24 hours ahead next year. Dhanyavaadalu 🙏"

Examples that are **wrong**:

> "Congratulations! You've earned Family Status! Enjoy 5% off!" (too generic, exclamatory)

> "Dear Customer, you have crossed our Family tier threshold." (impersonal)

## Cost model

Direct messaging cost only (handled by Notify). Tier benefits (free delivery, credit) come out of margin, not cash.

Estimated annual impact:
- Pillar tier free fresh basket sample: ₹400/year/Pillar customer
- Free delivery: depends on volume, planned to be near-zero net since basket size compensates
- Credit lines: interest-free but only ₹500-₹2000, default rate from kirana experience under 2%

## What Loyalty does NOT do

- Pretend it's a points game (it's a retention measurement tool with real benefits)
- Replace human relationship (it informs Agent, doesn't substitute for it)
- Inflate metrics with vanity points (every point comes from real activity)
- Manipulate via dark patterns (no "lose your tier in 7 days" panic-pushes)

## Reading list before building

- Indian kirana credit-cycle norms (pension day, month-end settlement)
- Telugu festival calendar (for festival pre-booking benefit)
- Supabase RLS for customer-scoped data
- Churn modelling fundamentals (RFM analysis as a baseline)
