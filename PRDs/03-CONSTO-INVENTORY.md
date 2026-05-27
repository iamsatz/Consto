# 03 · Consto Inventory

> Stock control, expiry tracking, reorder intelligence, and AI-driven waste prediction. Tablet-friendly web app for the store manager. Adapts what's stocked based on customer signal from Agent.

| | |
|---|---|
| **Phase** | 2.1 |
| **Priority** | High |
| **Platform** | Web PWA (tablet-friendly, used on shop floor) |
| **Primary user** | Store manager + central inventory team |
| **Build status** | Not started |
| **Dependencies** | Consto POS live, capturing transactions. Consto Agent live, generating "recent misses" signal. Shared `products` and `stock` tables in Supabase. |
| **Depends on it** | Notify (uses "low stock" triggers for restocking messages), Predict (uses inventory history), HQ (reads inventory dashboards) |

## What it is

The system that tells the store WHEN to reorder, WHAT to add, WHAT to drop, and WHERE waste is happening. Pulls real-time stock from POS, expiry data from receiving entries, customer signal from Agent.

Key innovation: stock is not a fixed catalog. **Customer signal adapts what's stocked.** If 5 Beeramguda households ask for ragi flour this week (signal from Agent), Inventory surfaces it and the manager decides whether to add it to the catalog.

## Who uses it

| User | What they do |
|---|---|
| **Store manager** | Daily stock counts, weekly reorder, expiry rotation, waste review |
| **Central inventory team** | Manages SKU catalog across all stores, supplier relationships |
| **Receiving staff** | Mobile-phone-based GRN (goods receipt note) when stock arrives |
| **Agent (AI, indirect)** | Reads stock to know what's available when generating customer nudges |
| **Predict (AI, indirect)** | Reads inventory history to build forecasts |

## Why it exists

A 1,000-1,500 sqft store with 1,000-1,500 SKUs and 15-25% margin cannot afford waste. Fresh produce spoilage alone can kill the model. Inventory must be:

- Realtime (POS sales decrement stock instantly)
- Predictive (AI flags items likely to spoil before they do)
- Adaptive (customer signal drives catalog changes weekly)
- Disciplined (every "yes to a new SKU" is reviewed before adding)

## Tech stack

| Layer | Tool |
|---|---|
| Frontend | React 18 + Tailwind CSS, PWA-enabled (installable on tablet) |
| Mobile receiving | Same React app, camera access for barcode scan |
| DB | Supabase (shared `products`, `stock`, plus Inventory-specific tables) |
| AI | Claude Sonnet for waste prediction + reorder intelligence |
| Hosting | Vercel |

## Core capabilities

### MVP (Phase 2.1 ship)

| | |
|---|---|
| **Live stock dashboard** | All SKUs with current quantity, last sale, days of cover. Sort/filter by category, freshness, urgency. |
| **Daily fresh count** | Morning routine: tap each fresh item, enter count, compare to expected. Variance flagged. |
| **Expiry tracker** | Items with expiry dates flagged 3/2/1 days ahead. Routing suggestions: Waste-to-Wealth soup, discount sale, donation. |
| **Receive goods (GRN)** | Scan supplier invoice or manual entry. Verify quantity, set expiry per batch, link to supplier. |
| **Reorder triggers** | When stock < threshold, trigger flagged for manager review. Suggested quantity based on rhythm. |
| **AI waste prediction** | Sonnet flags items likely to spoil before they sell. Confidence score + action suggestion. |
| **Customer-signal queue** | "Recent misses" from Agent surface as candidate additions. Manager approves or dismisses. |
| **Supplier directory** | Per-supplier contact, lead time, payment terms, last delivery rating. |

### Phase 2.5 additions

- Multi-store transfer (move stock between Beeramguda stores)
- Supplier scoring (reliability, freshness, price competitiveness)
- Bulk-buying suggestions (when 2 stores can pool an order)
- Seasonal stocking templates (festival-week reorder doubles)

### Future

- Direct supplier ordering (one-click reorder via WhatsApp/email)
- Cold-chain monitoring for dairy / batter
- Auto-pricing based on competitor watch

## Key screens

### Screen 1 — Live dashboard (default)

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO INVENTORY · Beeramguda          Manager: Ravi    │
├──────────────────────────────────────────────────────────┤
│  Alerts:  ⚠ 7 items low stock   ⚠ 3 items expire today   │
│           ⚠ 5 misses this week                            │
├──────────────────────────────────────────────────────────┤
│  Categories: [All] [Fresh] [Dairy] [Staples] [Snacks]    │
│              [Services] [Customer Signal]                 │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  FRESH PRODUCE (47 SKUs)               Action needed: 4   │
│                                                            │
│   Item           Stock    Days cover   Expiry    Action   │
│   Palak 500g     12      0.8 days      Today    Sell/Soup │
│   Tomato 1kg     8       1.2 days      —         Reorder  │
│   Coriander      24      Restock 6am   —         OK       │
│   ...                                                      │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 2 — SKU detail

Item history graph (7/30/90 days), sales trend, waste history, supplier, current batch expiry, last reorder, customer-mention count.

### Screen 3 — Daily fresh count (tablet-optimised)

Big-tap UI. Walk around store with tablet, tap each item, enter count. Reports variance from yesterday's expected.

### Screen 4 — Receive goods (camera-based)

Mobile-friendly. Scan supplier invoice barcode (if any), or manually add items. Each item: quantity, expiry date, condition photo. Generates GRN.

### Screen 5 — Customer signal queue

```
┌──────────────────────────────────────────────────────────┐
│  CUSTOMER SIGNAL — Recent misses                          │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  Organic ragi flour                                        │
│  Asked for by: Priya, 4 other working moms                │
│  Frequency: 5 times in 2 weeks                            │
│  Estimated demand: 8-12 units/week                        │
│  Suggested supplier: 24 Mantra (verified organic)         │
│                                                            │
│  [Add to catalog] [Need more data] [Reject]               │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 6 — Expiry triage

Items expiring in 3/2/1/0 days. Per-item action: route to Waste-to-Wealth soup, mark down 30%, donate, or accept loss.

### Screen 7 — Supplier directory

List + per-supplier page: contact, payment, last delivery, reliability score.

## Data model (Inventory-specific tables)

```sql
-- Already in shared schema: products, stock

-- Suppliers
suppliers (
  id uuid primary key,
  name text,
  contact_phone text,
  category text, -- 'fresh', 'dairy', 'staples', etc.
  lead_time_days int,
  payment_terms text,
  reliability_score decimal default 5.0,
  notes text,
  is_active boolean default true,
  created_at timestamp default now()
)

-- Goods Receipt Notes (GRN)
grn (
  id uuid primary key,
  store_id uuid references stores,
  supplier_id uuid references suppliers,
  received_at timestamp default now(),
  received_by uuid references staff,
  invoice_ref text,
  total_paise bigint,
  notes text
)

grn_items (
  id uuid primary key,
  grn_id uuid references grn,
  product_id uuid references products,
  quantity decimal,
  unit_price_paise int,
  batch_expiry date,
  condition_rating int -- 1-5
)

-- Daily fresh count
daily_counts (
  id uuid primary key,
  store_id uuid references stores,
  count_date date,
  product_id uuid references products,
  expected_qty decimal,
  actual_qty decimal,
  variance decimal,
  variance_reason text, -- 'spillage', 'sample', 'theft', 'miscounted'
  counted_by uuid references staff,
  created_at timestamp default now()
)

-- Waste log
waste_log (
  id uuid primary key,
  store_id uuid references stores,
  product_id uuid references products,
  quantity decimal,
  reason text, -- 'expired', 'spoiled', 'damaged', 'soup', 'donation'
  cost_paise int,
  recovered_paise int default 0,
  logged_by uuid references staff,
  logged_at timestamp default now()
)

-- AI waste predictions
waste_predictions (
  id uuid primary key,
  product_id uuid references products,
  predicted_for date,
  spoilage_probability decimal, -- 0.0 to 1.0
  recommended_action text, -- 'discount', 'soup', 'donate', 'monitor'
  prediction_basis text, -- explanation
  actual_outcome text, -- 'sold', 'wasted', 'soup', null if pending
  created_at timestamp default now()
)

-- Customer signal queue (from Agent)
customer_signals (
  id uuid primary key,
  signal_type text, -- 'miss', 'request', 'switch'
  product_query text, -- what customer asked for
  matched_sku uuid references products, -- null if not in catalog
  customer_count int,
  customer_examples uuid[], -- customer_ids
  frequency_per_week decimal,
  status text, -- 'new', 'reviewing', 'added', 'rejected', 'needs_more_data'
  reviewed_by uuid references staff,
  reviewed_at timestamp,
  created_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Waste prediction (daily run) | Sonnet | Combines stock + days cover + weather + festival calendar + customer rhythm. Multi-factor. |
| Reorder suggestion | Sonnet | Weekly rhythm + upcoming festivals + supplier lead time. |
| Customer signal triage | Sonnet | Reads "recent misses" from Agent, dedupes, scores demand. |
| Stock variance explanation | Haiku | Quick classification (spillage, theft, miscounted). |
| Daily summary for manager | Sonnet | Morning briefing: what to expect today. |

## Build sequence for Claude Code

1. **Scaffold web app + Vercel deploy + Supabase wiring**
2. **Build live stock dashboard** — read from `stock`, simple list with filters
3. **Build SKU detail page** — history graph, sales chart
4. **Build daily fresh count screen** — tablet-friendly tap UI
5. **Build GRN flow** — supplier select, item add, batch expiry, save
6. **Build expiry triage** — surface items by days-to-expiry, action buttons
7. **Build reorder triggers** — threshold logic, manager review queue
8. **Wire AI waste prediction** — daily Sonnet job, surface predictions
9. **Build customer signal queue** — read from Agent's `customer_signals`, manager review
10. **Build supplier directory**
11. **PWA-enable** — manifest, service worker, offline read for stock dashboard
12. **Test on tablet** — actual stocktake by manager for 1 week

## Success metrics

| Metric | Target |
|---|---|
| Waste reduction | 30% lower fresh-produce waste vs untracked baseline |
| Stockout incidents | Under 2% of customer-asked items unavailable |
| Customer signal action rate | 60%+ of "recent misses" reviewed within 7 days |
| Reorder accuracy | Manager edits AI-suggested quantity less than 30% of the time after 3 months |
| Daily count completion | 100% of fresh SKUs counted by 9am every day |

## DPDP compliance

- Customer signal data references customer IDs but stores no PII directly
- Customer-facing data (LTV, purchase history) accessed via RLS policy

## Brand voice

Internal tool only. No customer-facing voice needed.

## Reading list before building

- Supabase RLS policies
- React PWA service worker patterns
- Indian wholesale fresh-produce supply chain basics
