# 00 · Consto OS — Master PRD

> The architecture, philosophy, and shared layer behind all 8 products. Read this before any individual product PRD.

## What Consto is

Consto is India's first AI-backed neighbourhood store. A 1,000-1,500 sqft physical store in Beeramguda, Hyderabad (Phase 1), with three pillars holding it up: Memory, Services, Community.

**Four format variants** adapt to where the customer actually is:

- **Daily** (400-600 sqft for highways and IT corridors)
- **Home** (1,000-1,500 sqft for residential, the anchor format, where Beeramguda starts)
- **Hub** (2,000-3,000 sqft for community clusters, later phase)
- **Route** (200-400 sqft packing hub for daily home delivery — subscription-based, Sid's Farm-style economics)

Plus one future concept: **Travel** (Phase 4+). Hygienic home-style food ordered by travellers, picked up from highway/IT-corridor Daily stores or delivered to railway platforms and bus stops. Curated kitchen network of women-led home-cooked food businesses. Currently concept-stage, not in any Phase 1-3 plan.

**Consto OS is the digital backbone.** 8 integrated AI products that together let a small store remember every customer like family, scale across multiple locations without losing local soul, and produce institutional wisdom over years.

## Two-sided architecture

What this document describes is not one business but the foundation of two.

**Side A · Consto, the retail brand.** Physical stores. Customers. Beeramguda first, then franchise. The format variants (Daily, Home, Hub, Route) and the in-store experience. This is what shows up in the public portfolio.

**Side B · The product company (name TBD).** A separate design-led software company that takes the 8 Consto OS products and sells them externally as a B2B platform — first to kiranas, then restaurants, clinics, salons, and other Indian SMBs. POS is the flagship. The same memory + knowledge + wisdom architecture, generalised.

**Relationship between the two.** Consto dogfoods the OS. The product company sells the OS. Consto is the showcase, the proof, the brand halo. The product company is the long-term scalable revenue engine. Linear retail scaling on Side A; software scaling on Side B.

**Brand decision.** Consto stays retail-only. The product company gets its own name (TBD). They are operated as related but independent entities. The naming, legal structure, and team split will be decided after Consto Store 1 validates the underlying thesis.

**What this document does.** Continues to describe the Consto build sequence. The 8 PRDs below serve Side A's immediate operational needs. The strategic thinking for Side B (the product company) lives in `Reference/strategy-product-company.md`.

## The three layers of intelligence

1. **Memory** — what each AI agent remembers about each customer (purchases, timing, family, festival rhythm)
2. **Knowledge** — patterns category heads find across customer groups ("27 working moms asked for ragi this week")
3. **Wisdom** — strategic judgment that emerges across years, stores, and outcome data ("Open Store 2 in Manikonda, not Kompally")

Phase 1 builds Memory. Phase 2 builds Knowledge. Phase 3 surfaces Wisdom.

## Build phases

### Phase 1 — Foundation (Months 1-3)

| Product | What it proves |
|---|---|
| **01 Consto POS** | We can capture every transaction with hardware integration, offline support, and customer linking. Foundation for everything else. |
| **02 Consto Agent** | Customer memory layer works. Staff get real-time "what to mention" insights when a customer walks in. Drafts messages but doesn't send any yet. |

**Validation gate:** 50 launch customers visit the store, the Agent panel surfaces non-trivial insights for 80%+ of visits, retention at month 3 is measurable.

### Phase 1.5 — Transactional comms (Month 3-4)

| Product | What it proves |
|---|---|
| **04 Consto Notify** (SMS only) | We can send delivery confirmations, recharge receipts, credit reminders via SMS. Establishes the comms infrastructure without WhatsApp dependency. |

### Phase 2 — Operations + Engagement (Months 5-8)

| Product | What it proves |
|---|---|
| **03 Consto Inventory** | AI-driven stock and waste prediction reduces waste by measurable %. SKU adaptation based on customer signal. |
| **04 Consto Notify** (WhatsApp added) | Emotional messaging via WhatsApp opt-in. Comparison: is WhatsApp ROI better than SMS? |
| **05 Consto Loyalty** | Points, tiers, churn prediction. Drives repeat visit rate. |

**Validation gate:** Store 1 unit economics positive. 500 customers active. Subscribe model running.

### Phase 3 — Scaling intelligence (Months 9-12)

| Product | What it proves |
|---|---|
| **06 Consto Ops** | Daily ops at scale. Staff checklists, manager review, photo proofs. |
| **07 Consto Predict** | SKU-level demand forecasting reduces over-ordering and stockouts. Tested against actuals weekly. |
| **08 Consto HQ** | Owner can run the business from a single dashboard. Natural language queries. Quarterly Wisdom reports for strategic decisions. |

**Validation gate:** Ready to franchise. Consto OS provably reduces cognitive load on the owner. Wisdom layer producing actionable strategic recommendations.

## Architecture

```
                    ┌──────────────────────────┐
                    │      CONSTO AGENT        │
                    │   (Memory · Knowledge ·  │
                    │       Wisdom layer)      │
                    └─┬───┬───┬───┬───┬───┬───┬┘
                      │   │   │   │   │   │   │
        ┌─────────────┘   │   │   │   │   │   └─────────────┐
        │                 │   │   │   │   │                 │
        ▼                 ▼   ▼   ▼   ▼   ▼                 ▼
   ┌────────┐      ┌─────────┐ ┌──────┐ ┌─────────┐    ┌─────────┐
   │  POS   │      │Inventory│ │Notify│ │ Loyalty │    │   Ops   │
   └────────┘      └─────────┘ └──────┘ └─────────┘    └─────────┘
                                  │
                                  ▼
                              ┌───────┐
                              │  SMS  │
                              │WhatsApp│ (Phase 2)
                              └───────┘

                    ┌──────────────────────────┐
                    │   Predict   ·   HQ       │   ← read across all
                    └──────────────────────────┘
```

**Agent is the brain.** It reads from POS (every transaction), Inventory (stock), Loyalty (tier), Ops (incidents). It writes to POS (in-store nudges), Notify (drafted messages), Inventory (suggested restocks), HQ (Wisdom outputs). It is **embedded** in POS as a panel — not a separate WhatsApp app.

## Shared data model (Supabase, single project)

### Core tables (shared across all 8 apps)

```sql
-- Customers
customers (
  id uuid primary key,
  phone_encrypted text not null,
  name_first text,
  name_full text,
  preferred_language text default 'te', -- te (Telugu), en, hi
  consent_sms boolean default false,
  consent_whatsapp boolean default false,
  consent_log jsonb,
  member_since date default current_date,
  is_subscriber boolean default false,
  notes text,
  created_at timestamp default now(),
  updated_at timestamp default now()
)

-- Customer memory profile (the Agent layer)
customer_memory (
  customer_id uuid primary key references customers,
  purchase_rhythm jsonb, -- {day_of_week: [items]}
  brand_preferences jsonb,
  family_context jsonb, -- {spouse, children: [{name, age}], festivals}
  recent_misses text[], -- items they asked for that we didn't have
  birthday_calendar jsonb, -- {self, spouse, children}
  last_visit timestamp,
  visit_count int default 0,
  lifetime_value_paise bigint default 0,
  agent_notes text -- AI-maintained running notes
)

-- Products / SKUs
products (
  id uuid primary key,
  sku text unique,
  barcode text,
  name text,
  category text,
  subcategory text,
  brand text,
  unit text, -- kg, g, L, ml, piece, pack
  pack_size text,
  mrp_paise int,
  selling_price_paise int,
  hsn_code text,
  is_active boolean default true,
  is_fresh boolean default false,
  shelf_life_days int,
  reorder_threshold int,
  created_at timestamp default now()
)

-- Stock (per-store)
stock (
  id uuid primary key,
  store_id uuid references stores,
  product_id uuid references products,
  quantity int default 0,
  batch_expiry date,
  last_restocked timestamp,
  updated_at timestamp default now()
)

-- Stores
stores (
  id uuid primary key,
  name text,
  format text, -- 'daily', 'home', 'hub', 'route', 'travel' (Phase 4+ concept)
  area text,
  city text,
  state text,
  opened_at date,
  is_active boolean default true
)

-- Transactions
transactions (
  id uuid primary key,
  store_id uuid references stores,
  customer_id uuid references customers, -- nullable for walk-ins
  staff_id uuid references staff,
  total_paise bigint,
  payment_method text, -- cash, upi, aeps, credit
  payment_ref text,
  is_credit boolean default false,
  credit_due_date date,
  created_at timestamp default now()
)

-- Transaction items
transaction_items (
  id uuid primary key,
  transaction_id uuid references transactions,
  product_id uuid references products,
  quantity decimal,
  unit_price_paise int,
  total_paise int
)

-- Staff
staff (
  id uuid primary key,
  store_id uuid references stores,
  name text,
  phone_encrypted text,
  role text, -- cashier, agent_human, manager, owner
  is_active boolean default true,
  language text default 'te'
)

-- Consent audit log (DPDP)
consent_log (
  id uuid primary key,
  customer_id uuid references customers,
  channel text, -- sms, whatsapp
  action text, -- granted, revoked, message_sent, deletion_requested
  context text,
  staff_id uuid references staff,
  created_at timestamp default now()
)
```

App-specific tables are defined in each product's PRD.

## AI integration rules

### Haiku (speed tasks, under 1 second)

- POS product lookup / fuzzy match
- POS suggested items at checkout ("frequently bought with")
- Loyalty point calculation
- Ops checklist flag-bys
- Quick stock lookups

### Sonnet (thinking tasks, 2-3 seconds)

- Agent customer-memory updates after each transaction
- Agent "what to mention" generation when customer enters
- Agent message drafting (SMS / WhatsApp)
- Inventory demand prediction
- Inventory waste prediction
- Predict SKU-level forecasts
- HQ natural-language queries
- Wisdom-layer quarterly recommendations
- Festival calendar pre-stocking suggestions

**Rule:** Each product PRD specifies which model is used for which feature. Never swap. Sonnet is 8x the cost of Haiku per token — speed tasks must use Haiku.

## DPDP Act 2023 compliance (non-negotiable)

| Requirement | How we comply |
|---|---|
| Explicit consent for digital messaging | Captured at first store visit. Stored in `consent_log` with timestamp + staff witness. |
| Granular consent per channel | Separate `consent_sms` and `consent_whatsapp` flags. |
| Right to withdraw | Customer can revoke via SMS/WhatsApp reply (STOP) or in-store. Logged immediately. |
| Right to deletion | 30-day deletion function. Customer requests via store visit or message. All PII purged across all tables. |
| Encryption at rest | Phone numbers encrypted in DB. Decryption only at point of communication. |
| No third-party data selling | Hardcoded into product. Not negotiable for any revenue stream. |
| Breach notification | Standard incident response. Notify within 72 hours per DPDP rules. |

## Brand voice for every AI-generated customer message

| Rule | Example |
|---|---|
| Always use the customer's name | `Priya, fresh paneer just came in...` |
| Telugu phrases where natural | `Lakshmi gaaru, baagunnara?` (How are you?) |
| Under 40 words per message | Period. Shorter is better. |
| Never "Dear Customer" | That's how banks talk, not family. |
| Never ALL CAPS | Reads as shouting. |
| Sound like a human, not a system | A human would not say "We have noticed your recent activity." |
| Reference specific personal details | `Aarav's birthday is in 8 days. Gift ideas?` |
| Always offer an action | A reply, a tap, a visit. Not a broadcast. |

## What Consto explicitly is not

- Not a discount store (we compete on care, not price)
- Not a tech startup pretending to be a shop (technology is invisible to customers)
- Not a rigid format (30% local soul per location, replicate the method across regions)
- Not a general shopping app (zero install friction for non-subscribers; opt-in app only for power users)
- Not dependent on WhatsApp in Phase 1 (SMS first, WhatsApp as Phase 2 optional channel)

## Where Consto starts

- **Store #01:** Beeramguda, Hyderabad (residential, Consto Home format)
- **Founder:** Sateesh (15 years inside the family kirana in Gadwal, Telangana, ages 7-22; left in 2007; 18+ years of Indian retail observation; February-March 2026 trip to Thailand and Taiwan connected the dots; product designer + AI builder today)
- **First 50 customers:** known contacts from Beeramguda colony belt
- **Validation window:** 60-day pilot with manual AI-assisted care before any capital deploys

## How the 8 PRDs relate

The 8 PRDs are independently buildable but follow the strict order in `README.md`. Each PRD specifies:

- What it reads from other apps (dependencies)
- What it writes to other apps (downstream effects)
- What can be tested in isolation
- What requires Phase 1 to be live before it can be built

Read each PRD's "Dependencies" section before starting build.
