# 08 · Consto HQ

> The owner's single-screen view of the business. Natural-language queries. Cross-store rollups. Wisdom-layer quarterly recommendations. The layer that lets the owner run multiple stores from anywhere without losing the relationship-driven feel.

| | |
|---|---|
| **Phase** | 3.3 |
| **Priority** | Critical for owner (the layer that makes scaling possible) |
| **Platform** | Web dashboard (desktop primary, mobile read-only) |
| **Primary user** | Owner (Sateesh + future co-founders) |
| **Build status** | Not started |
| **Dependencies** | All other 7 products live. Data is mature (6+ months of transactions). Wisdom layer in Agent producing quarterly reports. |
| **Depends on it** | This is the top of the stack. Nothing depends on it. |

## What it is

The owner does not read the POS, the Inventory app, the Notify queue, or the Predict dashboard. The owner reads HQ. HQ summarises everything: daily store health, customer health, financial health, ops health, strategic Wisdom reports. Plus a natural-language query interface: ask anything in plain English, get an answer with the data behind it.

This is the layer that lets Consto scale beyond one founder. By Year 2, when there are 3-5 stores, no human can read 7 dashboards per store. HQ consolidates.

## Who uses it

| User | What they do |
|---|---|
| **Owner (Sateesh)** | Daily 15-min review. Quarterly Wisdom report read. Decisions like "open Store 2 in X" come from here. |
| **Co-founder / ops head** | Same as owner, with different filtering. |
| **Investor / board (if applicable)** | Quarterly metrics export. |

## Why it exists

The current alternative is: log into 7 apps, manually correlate, write your own summary. That works for Store 1, Month 1. By Store 3, Month 18, it's impossible.

HQ closes that gap. Same data, single screen, AI-summarised, query-able.

## Tech stack

| Layer | Tool |
|---|---|
| Frontend | React + Tailwind, deployed to Vercel |
| Charts | Recharts or D3 (avoid heavy chart libraries) |
| DB | Supabase (reads from all other apps' tables) |
| AI | Claude Sonnet (extended thinking for Wisdom reports + natural-language queries) |
| Cache | Aggregations cached in `hq_summary` table, refreshed nightly |

## Core capabilities

### MVP

| | |
|---|---|
| **Daily summary** | Single-screen "how is the business today" — revenue, customers, alerts, anomalies. |
| **Cross-store rollup (when >1 store)** | Same view but aggregated across stores. Toggle per-store drill-down. |
| **Customer health** | Active customers, new this week, at-risk, churn-recovered, tier mix. |
| **Financial health** | Revenue, COGS, margin, services revenue share, top SKUs, payment-method mix. |
| **Ops health** | Yesterday's tasks done %, missed-task list, incident count, staff schedule. |
| **Inventory health** | Waste % this week, stockout incidents, days-of-cover by category. |
| **Natural-language query** | "Which customers haven't visited in 3 weeks?" "What was last Sankranti revenue?" "Why is paneer sales down?" — Sonnet generates SQL + presents data. |
| **Wisdom quarterly reports** | Every 3 months, a full Wisdom-layer report from Agent. Read + actioned + tracked. |
| **Alerts** | Anomalies surface as top-bar notifications. "Yesterday's revenue −18% vs Wed average." |
| **Anywhere access** | Mobile-readable. Owner can check from anywhere. |

### Phase 3.5 additions

- Export dashboards as PDF for investor / board
- Compare period vs period (this Sankranti vs last Sankranti)
- What-if scenarios ("if we drop Bru, what happens?")
- Multi-region dashboards (when Consto expands to Pune, Coimbatore)

### Future

- Voice queries ("Hey Consto, how was today?")
- Anomaly auto-investigation (Sonnet explains why a metric moved)
- Decision-support agent (proposes 3 options for any decision, with pros/cons)

## Key screens

### Screen 1 — Daily summary (default landing)

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO HQ · Sateesh · Beeramguda                  Today │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  TODAY'S PULSE                                             │
│  ₹54,200 revenue · 87 customers · ₹14,800 services        │
│  ▲ +12% vs Tue average · ▲ +5% YoY                        │
│                                                            │
│  THIS WEEK                                                 │
│  ₹3.2L revenue · 487 unique customers · 142 new           │
│  Subscribe: 38 active (▲ +4 this week)                    │
│                                                            │
│  ANOMALIES                                                 │
│  ⚠ Paneer waste up 40% Wed-Fri — investigate (Inventory)  │
│  ⚠ 24 at-risk customers (Loyalty) — 6 in Pillar tier      │
│                                                            │
│  WISDOM (latest, Q3 2026)                                  │
│  → Open Store 2 in Manikonda, not Kompally                │
│  → Promote semi-organic ragi to category feature           │
│  → Drop Bru coffee from catalog                            │
│  [Open full Wisdom report]                                 │
│                                                            │
│  ASK ANYTHING                                              │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ How was last Sankranti revenue compared to general? │ │
│  └─────────────────────────────────────────────────────┘ │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 2 — Customer health

Charts and lists: active customers over time, new vs returning, tier distribution, at-risk list, recently recovered list.

### Screen 3 — Financial health

Revenue trends (daily, weekly, monthly), margin breakdown (groceries vs services), top SKUs by revenue, payment method mix, AEPS volume.

### Screen 4 — Ops health

Yesterday's checklists by store, missed-task patterns, incident log, staff schedule.

### Screen 5 — Inventory health

Waste % over time, stockout incidents, days-of-cover by category, customer-signal queue depth.

### Screen 6 — Wisdom report (quarterly)

The full quarterly Wisdom-layer report from Agent. Read sequentially. Each recommendation has:
- The recommendation
- The evidence
- The suggested action
- A "track this" toggle (so HQ can monitor outcome)

### Screen 7 — Natural-language query

Ask any question in plain English. Sonnet:
1. Generates SQL or aggregation
2. Runs it against Supabase
3. Presents result as chart + brief narrative
4. Saves query for future reuse

```
You: "Which customers haven't visited in 3 weeks but were Family tier?"

HQ: 7 customers match.
[Lakshmi Aunty · Family · 28 days · last visit Oct 4]
[Ravi Kumar · Family · 24 days · last visit Oct 8]
...

[Send all to Notify outreach queue] [Export]
```

### Screen 8 — Cross-store comparison (Phase 3+)

Side-by-side metrics per store. Anomaly highlighting (Store 2 waste is 2× Store 1).

## Data model

```sql
-- Cached aggregations (refreshed nightly)
hq_summaries (
  id uuid primary key,
  store_id uuid references stores, -- null for all-stores rollup
  summary_date date,
  summary_data jsonb, -- {revenue, customers, services, etc.}
  created_at timestamp default now()
)

-- Saved queries
hq_saved_queries (
  id uuid primary key,
  user_id uuid references staff,
  query_text text, -- natural language
  generated_sql text,
  result_snapshot jsonb,
  name text,
  is_pinned boolean default false,
  last_run timestamp,
  created_at timestamp default now()
)

-- Tracked Wisdom recommendations (for outcome tracking)
wisdom_tracked (
  id uuid primary key,
  wisdom_report_id uuid references wisdom_reports,
  recommendation_text text,
  status text, -- 'pending', 'acted', 'rejected', 'inconclusive'
  acted_on timestamp,
  outcome_measured_at timestamp,
  outcome_notes text,
  outcome_metric_change decimal
)

-- Anomaly alerts
hq_alerts (
  id uuid primary key,
  alert_type text, -- 'revenue_drop', 'waste_spike', 'churn_spike', 'stockout_pattern'
  store_id uuid references stores,
  severity text, -- 'low', 'medium', 'high'
  message text,
  data jsonb,
  status text, -- 'new', 'acknowledged', 'resolved', 'dismissed'
  acknowledged_by uuid references staff,
  created_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Daily summary narrative | Sonnet | Synthesises across all apps into a paragraph the owner can read in 30 sec. |
| Natural-language → SQL | Sonnet | Converts plain English to SQL safely (with allowlist on tables). |
| Anomaly detection | Sonnet | Identifies and explains anomalies, not just flags them. |
| Wisdom report synthesis (quarterly) | Sonnet (extended thinking) | The deepest synthesis. Takes hours of context, produces strategic recommendations. |
| Anomaly explanation | Sonnet | When an alert fires, Sonnet drafts the "why" so owner doesn't have to investigate. |

### Safety on NL-to-SQL

- Read-only queries only (allowlist via Supabase RLS)
- Per-table access policy (HQ user can read aggregates, not raw PII)
- Query result sanitised (encrypted columns stay encrypted)
- All generated SQL logged for audit

## Build sequence for Claude Code

1. **Scaffold web app + Vercel + Supabase**
2. **Build daily summary view** — start with hardcoded queries, no AI yet
3. **Build customer / financial / ops / inventory health pages**
4. **Build nightly aggregation job** — populates `hq_summaries` table
5. **Wire Sonnet for daily summary narrative**
6. **Build anomaly detection** — start with simple threshold rules + Sonnet narration
7. **Build natural-language query interface** — Sonnet → SQL → execute → present
8. **Add safety on NL-to-SQL** — allowlist tables, read-only enforcement
9. **Build Wisdom report viewer** — reads from `wisdom_reports` (Agent generates these)
10. **Build Wisdom-recommendation tracking** — owner marks "acted on this," outcome tracked over time
11. **Build saved-queries feature** — pin frequently-used questions
12. **Mobile responsive layout** — owner can check from anywhere
13. **Test with 6 months of seeded data** — verify queries return sensibly

## Success metrics

| Metric | Target |
|---|---|
| Daily check-in time | Owner reads daily summary in under 5 min |
| Natural-language query usage | Owner runs at least 1 NL query per day |
| Wisdom recommendation action rate | 60%+ of Wisdom recommendations acted on |
| Wisdom recommendation outcome accuracy | When acted on, the predicted outcome materialises 70%+ of time |
| Anomaly response time | Anomaly acknowledged within 4 hours of firing |
| Mobile readability | Daily summary readable on phone in 30 seconds |

## DPDP compliance

- All PII access subject to standard RLS (owner has full access; future co-founders may have role-restricted access)
- Natural-language query results scrubbed of PII in shared views (e.g., when exporting reports for board)
- Audit log of every query run by every user

## Brand voice

Internal tool. Tone is concise, direct, founder-friendly. Daily summary narratives sound like:

> "Revenue ₹54.2K today, +12% above Tuesday average. Paneer waste spiked Wed-Fri — Inventory flagged the supplier issue. 6 Pillar customers haven't visited in 3+ weeks — Loyalty has them in the at-risk queue."

NOT:

> "Today's operational performance indicators suggest..."

## What HQ does NOT do

- Replace the founder's judgment (it surfaces data; the founder decides)
- Auto-execute Wisdom recommendations (humans always action)
- Display raw PII to anyone without role permission
- Send messages (Notify owns that)
- Override anything (it's read + recommend, not write)

## Reading list before building

- Sonnet extended-thinking patterns for strategic synthesis
- LLM-to-SQL safety patterns (text-to-SQL with allowlists)
- Recharts library docs
- Indian retail KPI conventions
- Sample owner dashboards (Shopify, Square, ChimplePOS)
