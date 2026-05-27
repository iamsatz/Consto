# 07 · Consto Predict

> SKU-level demand forecasting. AI-driven predictions for what will sell tomorrow, this week, next month. Festival-aware, weather-aware, customer-rhythm-aware. Feeds Inventory's reorder decisions and HQ's planning.

| | |
|---|---|
| **Phase** | 3.2 |
| **Priority** | Medium (high value once data is mature) |
| **Platform** | Web dashboard |
| **Primary user** | Store manager + central planning team + owner (via HQ) |
| **Build status** | Not started |
| **Dependencies** | At least 6 months of transaction history. Festival calendar. Stock + inventory data. Weather API (Phase 3.5+). |
| **Depends on it** | Inventory (uses forecasts for reorder), HQ (Wisdom layer reads forecasts) |

## What it is

A weekly + daily forecasting engine that predicts sales per SKU. Not "we'll do ₹X in total" but "we'll sell 8-12 kg of palak on Wednesday, +30% spike on Ugadi week, -20% during monsoon Tuesdays."

The output is **actionable**: it directly feeds Inventory's reorder triggers and Wisdom's strategic recommendations.

## Who uses it

| User | What they do |
|---|---|
| **Store manager** | Daily/weekly forecasts shown alongside Inventory. Decides reorder volumes. |
| **Central planning** | Cross-store, cross-region forecasts. Identifies trends. |
| **Owner (via HQ)** | Strategic forecast view: where demand is growing, where shrinking. |
| **Inventory (downstream)** | Uses forecast for reorder thresholds. |
| **Agent (downstream)** | Uses forecast to time outreach ("paneer running low, suggest to Wed-buyers"). |

## Why it exists

Without forecasting:
- Reorder is based on rules-of-thumb, leading to over-stocking and waste
- Festival surges are missed (lost sales)
- Weather impacts are unpredicted
- Customer-rhythm changes are noticed too late

With forecasting:
- Inventory holds the right amount
- Festival surges are anticipated and pre-booked
- Weather-sensitive items (cooling drinks in heat, hot snacks in cold) are timed
- Customer cohorts shifting (organic ragi demand rising) get visibility before crisis

## Tech stack

| Layer | Tool |
|---|---|
| Frontend | React + Tailwind + Vercel |
| DB | Supabase (read-heavy from transactions + stock + festivals) |
| AI | Claude Sonnet for forecasts. Sonnet's strength here is multi-factor synthesis. |
| Background jobs | Vercel Cron / Supabase Edge Functions (daily and weekly runs) |
| Optional: weather data | OpenWeatherMap API or IndianAPI weather |

## Core capabilities

### MVP

| | |
|---|---|
| **Daily SKU forecast** | For each fresh SKU, predict tomorrow's quantity sold. Confidence interval. |
| **Weekly SKU forecast** | For each SKU (fresh + staples), predict next 7 days. |
| **Festival adjustment engine** | Festival calendar drives multipliers per SKU (Ugadi → +50% on pachadi items, +30% on tulsi). |
| **Daily forecast accuracy tracking** | Yesterday's forecast vs actual sales. Daily accuracy report. |
| **Inventory integration** | Forecasts directly visible in Inventory's reorder screen. |
| **Trend detection** | Detect rising / falling demand for any SKU. Surface as alert. |
| **Customer cohort forecasts** | "Working moms buying X are growing 12% MoM." |

### Phase 3.5 additions

- Weather integration (rain on Tuesday → +40% snacks demand)
- Cross-store comparative forecasts (Store 2 vs Store 1)
- Supplier-lead-time-aware reorder suggestions
- Long-range planning (3-month, 6-month horizons)
- Festival-week pre-stocking optimisation

### Future

- Outside-event signal (festival in nearby city, IT corridor event, school exam season)
- Customer-individual forecasts (probability Priya buys paneer Wed)
- Pricing optimisation suggestions

## Key screens

### Screen 1 — Forecast dashboard

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO PREDICT · Beeramguda                              │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  TOMORROW (Wed)                                            │
│                                                            │
│  Fresh produce — predicted sales                           │
│   Palak 500g       9-12 kg     ±15%   ▲ usual              │
│   Tomato 1kg       6-8 kg      ±10%   = usual              │
│   Coriander        18-22 bunch ±12%   ▲ usual              │
│   Curry leaves     ...                                     │
│                                                            │
│  Dairy + batter — predicted sales                          │
│   Milk 1L          45-55       ±5%    = usual              │
│   Idli batter      8-12        ±20%   ▼ Wed dip            │
│                                                            │
│  Festival week (Ugadi in 9 days)                           │
│   Suggested pre-stocking: see Inventory                    │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 2 — Per-SKU forecast detail

For one SKU, a chart: history (90 days), predicted (30 days ahead), confidence band, festival markers, weather overlay (if enabled).

### Screen 3 — Accuracy report

Yesterday's forecast vs actual, per SKU. Aggregated weekly. Tracks model improvement over time.

### Screen 4 — Trend alerts

Auto-detected rising and falling SKUs:

```
RISING (last 4 weeks)
🔼 Organic ragi flour     +120% (low base, but signal real)
🔼 Filter coffee 500g     +35%
🔼 Goat milk              +22%

FALLING (last 4 weeks)
🔽 Generic biscuits A     -28%
🔽 Bru coffee             -15%
🔽 Refined sunflower oil  -12% (customers switching)
```

### Screen 5 — Cohort forecasts

```
Customer cohort: Working moms (n=148)
  Paneer: 85% buy weekly (+5% MoM)
  Greens: 92% buy weekly (=)
  Organic items: 22% (+9% MoM, accelerating)

Customer cohort: Retirees (n=64)
  Horlicks: 78% buy monthly (=)
  Daily milk: 95% (=)
  Festival items: 100% (during festival weeks)
```

### Screen 6 — Festival forecast view

For each upcoming festival: 2-week-ahead prediction of expected demand spike per SKU. Pre-booking suggestions per customer (from Agent).

## Data model

```sql
-- SKU forecasts
sku_forecasts (
  id uuid primary key,
  store_id uuid references stores,
  product_id uuid references products,
  forecast_date date,
  horizon text, -- 'daily', 'weekly', 'monthly'
  predicted_quantity decimal,
  lower_bound decimal,
  upper_bound decimal,
  confidence decimal,
  factors_used jsonb, -- {weather, festival, rhythm, trend}
  generated_at timestamp default now()
)

-- Forecast accuracy log
forecast_accuracy (
  id uuid primary key,
  forecast_id uuid references sku_forecasts,
  actual_quantity decimal,
  error decimal,
  error_pct decimal,
  recorded_at timestamp default now()
)

-- Trend detections
trend_alerts (
  id uuid primary key,
  store_id uuid references stores,
  product_id uuid references products,
  direction text, -- 'rising', 'falling'
  magnitude_pct decimal,
  period_weeks int,
  status text, -- 'new', 'acknowledged', 'acted', 'dismissed'
  acknowledged_by uuid references staff,
  created_at timestamp default now()
)

-- Cohort definitions
cohorts (
  id uuid primary key,
  name text,
  description text,
  criteria_json jsonb, -- how to assign customers to this cohort
  customer_ids uuid[] -- snapshot, refreshed periodically
)

-- Cohort forecasts
cohort_forecasts (
  id uuid primary key,
  cohort_id uuid references cohorts,
  product_id uuid references products,
  period text, -- 'weekly', 'monthly'
  buy_rate decimal,
  trend_pct decimal,
  generated_at timestamp default now()
)

-- Festival demand multipliers
festival_multipliers (
  festival_id uuid references festivals,
  product_id uuid references products,
  multiplier decimal, -- 1.5 = +50%
  evidence jsonb, -- historical data backing this
  primary key (festival_id, product_id)
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Daily SKU forecast | Sonnet | Multi-factor synthesis: rhythm + recent trend + festival + cohort dynamics. |
| Weekly forecast | Sonnet | Same as daily but longer horizon. |
| Trend detection | Sonnet | Pattern recognition with explanation. |
| Cohort buy-rate forecast | Sonnet | Group-level patterns with confidence. |
| Festival multiplier inference | Sonnet | Learns from past-festival data, updates over time. |
| Accuracy diagnostics | Haiku | Compare forecast to actual, compute error. |

### Why not pure statistical / ML?

Traditional time-series (ARIMA, Prophet) work for stable patterns but fail at:
- Festival surges (irregular timing, lunar calendar)
- Cohort shifts (small-sample, contextual)
- New SKUs (no history)
- Weather + cultural overlay

Sonnet handles these because it can reason over the festival calendar, customer-rhythm context, and explain why. The Sonnet forecast is wrapped in a sanity-check layer that compares against a simple moving-average baseline — if Sonnet wildly disagrees, the forecast is flagged for human review.

## Build sequence for Claude Code

1. **Scaffold web app + Vercel + Supabase**
2. **Build basic SKU history view** — chart 90 days per product
3. **Build moving-average baseline forecast** — simple, deterministic, no AI
4. **Wire Sonnet for daily forecast** — daily Vercel Cron job
5. **Build forecast dashboard** — show tomorrow's predictions
6. **Build accuracy tracking** — yesterday's forecast vs actual
7. **Build trend detection** — rising/falling SKUs
8. **Build cohort forecasts** — per-customer-group buy rates
9. **Build festival multiplier engine** — read festivals table, apply per SKU
10. **Integrate with Inventory** — forecasts shown in Inventory's reorder screen
11. **Build per-SKU detail page** — history + prediction + confidence band
12. **Test with 3 months of transaction data** — verify forecasts converge to actuals

## Success metrics

| Metric | Target |
|---|---|
| Daily forecast accuracy (MAPE) | Under 20% for fresh SKUs, under 12% for staples after 6 months |
| Festival surge prediction accuracy | Within 25% of actual demand |
| Reorder over-stocking reduction | 30% fewer over-stock incidents (measured via waste) |
| Reorder stockout reduction | 50% fewer stockouts |
| Trend detection lead time | Detect rising/falling SKU before manager would notice manually |
| Forecast review time | Manager reviews dashboard under 10 min/day |

## DPDP compliance

- Cohort forecasts use customer IDs but produce aggregate predictions only (no per-customer PII surfaced)
- Customer-individual forecasts (Phase 3+) only shown to staff who already have access to that customer's profile

## Brand voice

Internal tool. No customer-facing voice.

## What Predict does NOT do

- Replace the manager's judgment (predictions are suggestions, manager decides reorder)
- Promise certainty (every forecast has a confidence interval)
- Over-engineer Phase 1 (skip weather and complex cohorts until basic forecasts work)

## Reading list before building

- Time-series forecasting fundamentals (ARIMA, exponential smoothing) — baseline
- Sonnet extended-thinking patterns for forecasting
- Indian festival calendar (Telugu primarily for Phase 1)
- Indian retail seasonality patterns
