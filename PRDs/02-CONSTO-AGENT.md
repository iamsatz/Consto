# 02 · Consto Agent

> The customer memory and intelligence layer. Lives inside Consto POS as a panel, plus a separate web admin for the central ops team. Reads every transaction, builds memory, generates insights, drafts messages. Does NOT send messages directly. Humans review and approve.

| | |
|---|---|
| **Phase** | 1.2 (immediately after POS) |
| **Priority** | Critical (the soul of Consto) |
| **Platform** | Embedded panel inside Consto POS (Electron) + separate Web admin dashboard |
| **Primary user** | POS cashier (sees panel), Central ops team (uses dashboard to review drafts) |
| **Build status** | Not started |
| **Dependencies** | Consto POS must be live and capturing linked transactions. Claude Sonnet API access. |
| **Depends on it** | Notify (uses drafted messages), HQ (reads Wisdom outputs), Loyalty (uses customer rhythm) |

## What it is

The system that turns transactions into memory. After every linked purchase, Agent updates that customer's `customer_memory` profile. Before every in-store visit, Agent generates "what to mention" nudges that appear in the POS Agent Panel. Once a week, Agent generates category-head insights for the central team. Once a quarter, Agent produces Wisdom-layer recommendations for the owner.

Agent is NOT a WhatsApp app. Agent is a layer that lives behind POS and writes to other apps.

## The three layers Agent produces

| Layer | What | Cadence | Audience |
|---|---|---|---|
| **Memory** | Per-customer profile updates after each transaction | Real-time | POS Agent Panel (during visits) |
| **Knowledge** | Category-head insights ("27 working moms asked for ragi this week") | Weekly | Central ops team via web dashboard |
| **Wisdom** | Strategic recommendations ("Open Store 2 in Manikonda") | Quarterly | Owner via HQ |

## Two inputs

1. **Company-trained baseline (Input A):**
   - Brand voice rules (always use name, never ALL CAPS, Telugu phrases where natural, under 40 words)
   - Festival calendar (Ugadi pachadi kit, Sankranti ellu + jaggery, Bonalu items, Diwali, etc.)
   - Persona archetypes (working mom, gig driver, retiree, tech professional)
   - Cultural sensitivities (don't push expensive items at retirees, don't message before 7am or after 9pm)
   - Inventory / pricing data (today's stock, today's specials)

2. **Customer behaviour signal (Input B):**
   - Every purchase logged (item, time, brand, quantity)
   - Timing patterns (Tuesday evening, Sunday morning)
   - Frequency and rhythm
   - Family context as captured by staff over time (mentioned daughter's exam once)
   - Misses and explicit requests (asked for organic ragi, we didn't have it)
   - Response patterns (which previous messages got engagement)

## Learning curve

| Stage | What the AI knows |
|---|---|
| Day 1 | Phone + first name. Uses persona archetype as a default. |
| Week 1 | First few purchases. Can suggest basics. |
| Month 1 | Shopping rhythm. Predicts next visit window. |
| Month 3 | Brand preferences + substitutes. Catches brand switches. |
| Month 6 | Family context, festival routine. Genuinely useful. |
| Year 1 | Life context. Anticipates needs days in advance. |
| Year 2+ | Knows the customer like a 30-year shopkeeper. |

## Tech stack

| Layer | Tool |
|---|---|
| Frontend (admin) | React 18 + Tailwind CSS, deployed to Vercel |
| Embedded panel | Same React code, rendered inside Consto POS Electron app |
| DB | Supabase (shared `customer_memory`, `consent_log`, plus Agent-specific tables) |
| AI | Claude Sonnet for all memory operations and drafts. Haiku only for simple lookups. |
| Background jobs | Supabase Edge Functions or Vercel Cron for weekly/quarterly rollups |

## Core capabilities

### MVP (Phase 1 ship)

| | |
|---|---|
| **Real-time memory update** | After each linked transaction, Sonnet updates `customer_memory` with the new signal. |
| **"What to mention" generation** | When customer scanned into POS, Sonnet generates 2-4 personal nudges. Cached 5 min. |
| **Profile display in POS Agent Panel** | Read-only panel showing customer profile + nudges + family + recent rhythm. |
| **Web admin dashboard** | Central ops team can browse all customer profiles, see flagged misses, review drafted messages. |
| **Message draft generation (held)** | Agent drafts messages but does NOT send them. Drafts sit in a review queue until Notify is live. |
| **Festival calendar engine** | Telugu festival calendar wired in. Pre-stocking suggestions per festival generated 2 weeks ahead. |
| **Persona archetype assignment** | Day 1, assigns archetype based on first interaction. Refines over time. |
| **Consent enforcement** | No message ever drafted for non-consented customer. Hardcoded gate. |

### Phase 2 additions

- Category heads (Working moms / Gig drivers / Retirees / Tech pros / Festival heads / Geography heads)
- Weekly category insights generation (Knowledge layer)
- Cross-store learning across multiple Beeramguda stores
- Customer-initiated agent conversations (customer asks question, agent drafts response for human review)
- Sentiment tracking on response patterns

### Phase 3 additions

- Wisdom-layer quarterly reports
- Senior Council synthesis (category heads + human team)
- Cross-region adaptation (Telugu → Marathi → Tamil etc.)
- Predictive churn detection per customer

## Key screens

### POS Agent Panel (embedded — see POS PRD Screen 2)

This is the right one-third of the POS screen. Phase 1 is read-only.

### Web admin dashboard

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO AGENT · ADMIN                                     │
├──────────────────────────────────────────────────────────┤
│  [Customers]  [Drafts Queue]  [Festivals]  [Insights]    │
├──────────────────────────────────────────────────────────┤
│                                                            │
│   ALL CUSTOMERS (487)                  Search: ___________ │
│                                                            │
│   ┌──────────────────────────────────────────────────┐    │
│   │ Priya · 35 · Family of 4 · Last visit 3 days ago │    │
│   │ Memory depth: 6 months · 12 visits · ₹3,200 LTV  │    │
│   │ ┌────────────────────────────────────────────┐   │    │
│   │ │ DRAFT WAITING: "Priya, fresh paneer in..." │   │    │
│   │ │ [Review →]                                  │   │    │
│   │ └────────────────────────────────────────────┘   │    │
│   └──────────────────────────────────────────────────┘    │
│                                                            │
│   ... 486 more                                             │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Customer detail page

Full profile: purchase history graph, family tree, festival calendar, special dates, sentiment over time, recent misses, all drafted messages with status (pending review / approved / sent / rejected).

### Drafts queue

Where the central ops team reviews AI-drafted messages before they get sent.
Each draft card shows:
- Customer name + memory depth
- The draft text
- Why this draft now (the trigger: "fresh paneer in, Priya's usual day")
- Channel (SMS in Phase 1.5, WhatsApp in Phase 2)
- [Approve and send] [Edit and send] [Reject and explain]

### Festivals view

Telugu festival calendar. For each upcoming festival, shows: pre-stocking checklist, customers likely to buy each item, suggested pre-booking messages.

### Category insights view (Phase 2)

Weekly digest: "27 working moms asked for ragi this week — we stock 0", "5 households switched from sunflower to groundnut oil in colony 4", etc.

## Data model (Agent-specific tables)

```sql
-- customer_memory (defined in master, shown here for reference)
customer_memory (
  customer_id uuid primary key references customers,
  purchase_rhythm jsonb,
  brand_preferences jsonb,
  family_context jsonb,
  recent_misses text[],
  birthday_calendar jsonb,
  last_visit timestamp,
  visit_count int default 0,
  lifetime_value_paise bigint default 0,
  agent_notes text
)

-- Drafted messages (Agent generates, humans review)
agent_drafts (
  id uuid primary key,
  customer_id uuid references customers,
  channel text, -- sms, whatsapp, in_store_only
  trigger_reason text, -- "fresh paneer arrival" etc.
  draft_text text,
  language text, -- te, en, hi
  status text, -- pending, approved, sent, rejected, expired
  reviewed_by uuid references staff,
  reviewed_at timestamp,
  sent_at timestamp,
  rejection_reason text,
  created_at timestamp default now(),
  expires_at timestamp -- auto-expire stale drafts after 24h
)

-- Festival calendar
festivals (
  id uuid primary key,
  name text,
  region text default 'telugu',
  date date,
  is_recurring boolean default true,
  recurrence_pattern text, -- annual, lunar
  stocking_checklist jsonb -- {items: [{sku, qty_multiplier, lead_days}]}
)

-- Per-customer festival preferences (learned over time)
customer_festival_prefs (
  customer_id uuid references customers,
  festival_id uuid references festivals,
  observes boolean,
  preferences jsonb, -- {sweets: ['boondi laddu'], decorations: [...]}
  last_year_purchases jsonb,
  primary key (customer_id, festival_id)
)

-- Category heads (Phase 2)
category_heads (
  id uuid primary key,
  name text, -- 'working_moms', 'gig_drivers', 'retirees', 'tech_pros', 'festival_telugu_ugadi'
  description text,
  active boolean default true
)

-- Customer-category assignments
customer_categories (
  customer_id uuid references customers,
  category_id uuid references category_heads,
  confidence decimal default 0.5,
  assigned_at timestamp default now(),
  primary key (customer_id, category_id)
)

-- Weekly insights (Phase 2)
weekly_insights (
  id uuid primary key,
  week_start date,
  category_id uuid references category_heads,
  insight_text text,
  data_backing jsonb, -- counts, customer IDs that triggered it
  action_recommended text,
  status text, -- new, reviewed, acted_on, dismissed
  reviewed_by uuid references staff,
  created_at timestamp default now()
)

-- Wisdom reports (Phase 3)
wisdom_reports (
  id uuid primary key,
  quarter text, -- '2026-Q3'
  report_text text,
  recommendations jsonb, -- [{title, evidence, action, confidence}]
  status text, -- draft, approved, distributed
  created_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Memory update after transaction | Sonnet | Requires understanding context, inferring family signals, updating notes coherently. |
| "What to mention" for POS panel | Sonnet | Synthesises memory + today's stock + brand voice into 2-4 actionable nudges. |
| Message draft generation | Sonnet | Requires brand voice, context awareness, language choice. |
| Festival pre-booking suggestions | Sonnet | Combines calendar + per-customer preferences + stock. |
| Persona archetype assignment | Sonnet (first time) → Haiku (refinement) | Initial classification needs nuance; refinement is pattern-match. |
| Category head insights (Phase 2) | Sonnet | Cross-customer pattern recognition with explanation. |
| Wisdom reports (Phase 3) | Sonnet (extended thinking) | Strategic recommendations requiring deep synthesis. |
| Customer profile summary for staff | Haiku | Fast read of existing memory, no new inference. |

### Prompt caching strategy

Brand voice rules, festival calendar, persona archetypes are static. They go in the **system prompt** with `cache_control` set, so they're cached and re-used across every Sonnet call. Per-customer memory is the dynamic part.

This is what makes the whole architecture cost-viable. Without caching, every "what to mention" call would re-pay the system prompt. With caching, you pay it once per 5-minute window.

## Build sequence for Claude Code

1. **Set up Agent admin web app** — Vercel deployment, basic auth (use Supabase Auth).
2. **Build customer browse + detail screens** — read-only from `customers` and `customer_memory`.
3. **Build memory-update background job** — listens to `transactions` table inserts, queues Sonnet calls.
4. **Implement memory-update prompt** — Sonnet call that takes current memory + new transaction, returns updated memory JSON.
5. **Test memory updates with 20 simulated transactions** — verify memory evolves sensibly.
6. **Build "what to mention" generation** — Sonnet call wrapped in cache. Returns 2-4 nudges.
7. **Wire to Consto POS Agent Panel** — when customer linked, fetch nudges and display.
8. **Build festival calendar** — populate 12 months of Telugu festivals with stocking checklists.
9. **Build festival pre-booking suggestion engine** — for each upcoming festival, generate per-customer suggestions.
10. **Build drafts queue** — Sonnet generates drafts based on triggers (fresh stock, festival, birthday). Drafts sit in queue.
11. **Build drafts review UI** — central team approves / edits / rejects.
12. **Build consent gate** — hardcoded check that no draft can be created for non-consented customer.
13. **Build customer detail page with full memory view** — debug surface for the team.
14. **Implement category heads** (Phase 2) — assign customers to categories, generate weekly insights.
15. **Implement Wisdom reports** (Phase 3) — quarterly synthesis across all data.

## Success metrics

| Metric | Target |
|---|---|
| Memory update latency | Under 3 seconds after transaction |
| "What to mention" quality | 80%+ of nudges rated useful by staff (manual review) |
| Memory depth after 6 months | Profile has family context, brand preferences, festival prefs for 70%+ of repeat customers |
| Draft approval rate | 70%+ of drafts approved without major edit (signals brand voice is correct) |
| Draft rejection reasons logged | All rejections must have a reason. Used to improve prompts. |
| Customer-facing perception | When asked "does Consto know you?" — yes from 80%+ of active customers after 6 months |
| Wisdom recommendation accuracy | When followed, recommended actions produce measurable outcome lift |

## DPDP compliance

- All memory operations check `consent_log` for active consent before generating any draft
- Memory itself is fine to maintain even without messaging consent (it's the in-store relationship layer)
- Deletion request triggers full purge of `customer_memory` + all `agent_drafts` for that customer
- Customer can request a "what do you know about me?" export — Agent generates a human-readable memory summary

## Brand voice rules (enforced in prompts)

```text
You are the Consto Agent. You draft messages for human review before sending.

Rules:
1. Always use the customer's name.
2. Telugu phrases where natural for Telugu-preferring customers (Baagunnara, Dhanyavaadalu).
3. Under 40 words. Always.
4. Never "Dear Customer". Never ALL CAPS.
5. Sound like a caring sibling, never a system.
6. Reference specific personal details from memory (not generic).
7. Always end with a clear action: a reply, a tap, a visit.
8. Never push expensive items at retirees.
9. Never message before 7am or after 9pm.
10. If you're not confident about a detail, omit it — don't fabricate.
```

These rules go into the system prompt with cache_control: ephemeral.

## What Agent does NOT do

- Send any message directly (humans approve every send)
- Talk to customers in real-time (no chatbot interface)
- Override staff judgment in-store (it's a "what to mention" panel, not a script)
- Operate without consent
- Fabricate details (if memory doesn't have it, agent doesn't invent it)

## Reading list before building

- Claude prompt caching documentation
- Anthropic best practices for multi-agent architectures
- Supabase Edge Functions for background jobs
- Telugu festival calendar (Bhakti texts + cultural references)
- DPDP Act 2023, especially consent and deletion sections
