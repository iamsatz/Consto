# 01 · Consto POS

> Billing and checkout for the store counter. The data foundation for everything that follows. Plus an embedded Agent panel that shows staff "who this customer is" the moment they walk up.

| | |
|---|---|
| **Phase** | 1.1 |
| **Priority** | Critical (foundation for all other apps) |
| **Platform** | Electron desktop (production) + Web (fallback / dev) |
| **Primary user** | Store cashier / floor staff |
| **Build status** | Not started |
| **Dependencies** | Shared Supabase schema must exist. Claude API key (Haiku + Sonnet). |
| **Depends on** | Nothing. Built first. |

## What it is

A locked-down desktop application running on the in-store billing terminal. Handles every transaction. Talks to the receipt printer, barcode scanner, cash drawer, UPI/AEPS payment device. Works offline if internet drops.

The **left two-thirds** is a standard POS billing screen — product lookup, cart, payment, receipt. The **right one-third** is the **Agent Panel**: the moment a customer is scanned or selected, this panel populates with their profile, recent rhythm, "what to mention" prompts, and quick actions.

## Who uses it

| User | What they do |
|---|---|
| **Cashier / floor staff** | Operate the POS all day. Scan items, take payment, print receipts. |
| **Manager** | Override discounts, void transactions, end-of-day reconciliation. |
| **Owner** | Open/close the till, set staff permissions. |
| **Agent (AI)** | Reads every transaction in real time to update customer memory. |

## Why it exists

Without a clean transactional layer, none of the other apps work. The POS is where every piece of customer data originates. Quality of the memory layer is bounded by quality of POS data capture.

Plus: the in-store relationship moment lives here. When Priya walks up, the cashier should see *"Priya. Family of 4. Last visit Tuesday. Bought paneer Wednesday + Saturday. Aarav's birthday in 8 days."* That is the moment the brand earns its name.

## Tech stack

| Layer | Tool |
|---|---|
| Desktop wrapper | Electron 28+ |
| Frontend | React 18 + Tailwind CSS |
| Local DB | SQLite (via better-sqlite3) for offline queue |
| Cloud DB | Supabase (Postgres) |
| AI | Claude Haiku (fast suggestions), Claude Sonnet (Agent Panel insights) |
| Receipt printer | escpos-php / node-thermal-printer (USB / network) |
| Barcode scanner | Keyboard wedge (default) or USB HID |
| UPI | Razorpay UPI Collect API |
| AEPS | Selected vendor SDK (decision pending — see BACKLOG) |
| Distribution | Electron auto-updater, signed builds |

## Core capabilities

### MVP (must ship)

| | |
|---|---|
| **Product lookup** | By barcode scan, product name fuzzy search, or quick-pick grid for fresh items |
| **Cart management** | Add/remove items, quantity adjust, line discount, total discount |
| **Customer linking** | Scan customer ID card, search by phone, or "walk-in" (no link) |
| **Payment methods** | Cash, UPI, AEPS withdrawal, store credit (deferred), split payment |
| **Receipt printing** | Thermal 80mm receipt, QR code with txn ID, GST breakdown, "Saved Rs X vs Zepto" line |
| **Offline mode** | Full functionality offline. Queue transactions locally in SQLite. Auto-sync when online. |
| **Day-end reconciliation** | Cash count, UPI total, AEPS total, credit issued. Manager closeout. |
| **Agent Panel (read-only Phase 1)** | When customer linked: profile, recent rhythm, "what to mention" (Sonnet-generated), family context, special dates. |
| **Staff login** | Per-staff login. All transactions stamped with staff ID. |
| **Hold transactions** | Pause a cart, switch to another customer, return to it. |

### Phase 2 additions

- Customer-initiated actions from Agent Panel (one-click "send today's deal" via Notify)
- AEPS service flow (cash withdrawal as a service, not just payment)
- Bill payment integration (BBPS)
- Mobile recharge integration
- Receipt-on-WhatsApp option (once Notify Phase 2 ships)
- Self-checkout mode (subscribers only, locked to verified phones)

### Future

- Multi-store inventory check ("Is this in Store 2?")
- Voice billing for accessibility ("Add 2 kg onions")
- AI-suggested items mid-cart ("usually adds coriander")

## Key screens

### Screen 1 — Main billing screen (default)

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO POS · Beeramguda · Cashier: Ravi       Online ●  │
├─────────────────────────────────────────┬────────────────┤
│                                          │                │
│   Search products / scan barcode...      │  AGENT PANEL   │
│   ┌────────────────────────────────┐    │                │
│   │ Onion 1kg                ₹40   │    │  (empty when   │
│   │ Tomato 500g              ₹25   │    │   no customer  │
│   │ Coriander bunch          ₹15   │    │   linked)      │
│   │ ... 3 more items                │    │                │
│   └────────────────────────────────┘    │  [Link customer]│
│                                          │                │
│   Subtotal              ₹385             │                │
│   GST (5%)              ₹19.25           │                │
│   ───────────────────────────────        │                │
│   TOTAL                 ₹404.25          │                │
│                                          │                │
│   [Cash] [UPI] [AEPS] [Credit] [Split]   │                │
│                                          │                │
└─────────────────────────────────────────┴────────────────┘
```

### Screen 2 — Agent Panel (after customer linked)

```
┌─────────────────────────────────────────┬────────────────────────┐
│   Search products / scan barcode...     │  PRIYA · 35            │
│   [billing UI as above]                 │  Family of 4 · Member   │
│                                          │   6 months              │
│                                          │ ─────────────────────── │
│                                          │  RECENT RHYTHM          │
│                                          │  • Mon: milk + bread    │
│                                          │  • Wed: paneer + greens │
│                                          │  • Sat: full basket     │
│                                          │ ─────────────────────── │
│                                          │  WHAT TO MENTION TODAY  │
│                                          │  → Iron-rich greens     │
│                                          │    just came in         │
│                                          │  → Aarav's birthday in  │
│                                          │    8 days — show gifts  │
│                                          │ ─────────────────────── │
│                                          │  FAMILY                 │
│                                          │  Husband: Ravi          │
│                                          │  Children: Aarav (10),  │
│                                          │   Diya (6)              │
│                                          │ ─────────────────────── │
│                                          │  CREDIT: ₹0 due         │
│                                          │  Last visit: 3 days ago │
└─────────────────────────────────────────┴────────────────────────┘
```

### Screen 3 — Customer search / link

Modal that pops over the billing screen. Search by phone, name, or ID card scan. If no match, "Create new customer" with phone + name + consent capture flow.

### Screen 4 — Payment confirmation

After payment method selected — UPI QR display, AEPS biometric prompt, cash drawer open trigger, etc.

### Screen 5 — Day-end reconciliation

End of shift / day. Cash count, payment-method totals, credit issued, variance flag. Manager sign-off.

### Screen 6 — Held transactions

Quick-access list of paused carts. Resume any with one click.

## Data model (POS-specific tables)

```sql
-- Already in shared schema: products, stock, transactions,
-- transaction_items, customers, staff, stores

-- POS-specific session table
pos_sessions (
  id uuid primary key,
  store_id uuid references stores,
  staff_id uuid references staff,
  started_at timestamp default now(),
  ended_at timestamp,
  opening_cash_paise bigint,
  closing_cash_paise bigint,
  variance_paise bigint -- closing - opening - expected_cash
)

-- Held / paused carts
pos_held_carts (
  id uuid primary key,
  store_id uuid references stores,
  customer_id uuid references customers,
  staff_id uuid references staff,
  cart_data jsonb, -- {items: [...], discount, notes}
  held_at timestamp default now(),
  resumed_at timestamp,
  completed boolean default false
)

-- Offline queue (local SQLite, syncs to Supabase)
pos_offline_queue (
  local_id integer primary key autoincrement,
  payload jsonb, -- the transaction + items + memory update
  created_at timestamp,
  synced boolean default false,
  synced_at timestamp,
  sync_error text
)
```

## AI integration (POS-specific)

| Where | Model | What it does |
|---|---|---|
| **Product search** | Haiku | Fuzzy match queries like "atta" to specific SKUs. Under 200ms target. |
| **Frequently-bought-with suggestions** | Haiku | Mid-cart suggestions based on customer history + general patterns. |
| **Agent Panel "what to mention"** | Sonnet | When customer linked, generates 2-4 personal nudges for the cashier. Cached for 5 minutes if same customer rescanned. |
| **Agent Panel family context** | Sonnet | Pulls + summarises stored memory into readable bullets. |
| **Memory update post-transaction** | Sonnet | Background job after every linked transaction. Updates `customer_memory` table. |

## Hardware

| Device | Connection | Notes |
|---|---|---|
| Receipt printer | USB or network (escpos) | 80mm thermal. Test with TVS/Epson models common in India. |
| Barcode scanner | Keyboard wedge (USB) | Cheap and reliable. Default approach. |
| Cash drawer | RJ11 from printer | Triggered by printer ESC/POS command. |
| UPI device | None (QR display on screen) | Razorpay UPI Collect generates QR. Customer pays from their phone. |
| AEPS device | USB biometric (Mantra, Morpho) | Vendor-specific SDK integration. Phase 2. |
| Customer-facing display | HDMI second monitor | Optional but recommended. Shows cart total + Welcome message. |

## Offline behaviour

| Scenario | What happens |
|---|---|
| Internet drops mid-transaction | Transaction continues. Saved to local SQLite. Receipt prints normally. |
| Offline > 1 hour | Top bar shows "Offline · 7 transactions queued". Cashier can keep working. |
| Internet returns | Auto-sync starts. Background. Progress indicator. |
| Conflict on sync | Server-wins for inventory. Local-wins for transaction record. Conflicts logged for manager review. |
| AI features offline | Disabled. Agent Panel shows last cached profile only. Product lookup falls back to local search (no fuzzy). |

## Build sequence for Claude Code

Each step should be its own commit and Vercel preview / Electron build. Test before next step.

1. **Scaffold Electron + React + Tailwind project** with Vite. Hello-world POS shell with sidebar and main area.
2. **Set up Supabase client** + auth (staff login). Connection test.
3. **Build product catalog table** + admin to add 50 test SKUs manually for dev.
4. **Build product search UI** (Haiku-powered fuzzy match) and the cart state management.
5. **Build cart UI** — add, remove, quantity, line discount, total discount, totals.
6. **Build customer link flow** — search modal, link to cart, new-customer creation with consent.
7. **Build payment flow** — Cash + UPI (Razorpay) only first. AEPS Phase 2.
8. **Build receipt printer integration** — test with one specific printer model.
9. **Build day-end reconciliation** + staff session management.
10. **Build Agent Panel (read-only)** — display from `customer_memory` table. Static content first.
11. **Wire Sonnet for "what to mention"** generation. Cache aggressively.
12. **Build offline queue** — local SQLite, queue, sync. Test by killing network mid-transaction.
13. **Wrap in Electron** — auto-updater, signed build, hardware passthrough.
14. **Day-in-the-life dogfooding** — staff member operates it for a full simulated day. Fix every friction point.

## Success metrics

| Metric | Target |
|---|---|
| Average transaction time | Under 90 seconds at peak |
| Agent Panel insight quality | 80%+ of linked transactions have non-trivial "what to mention" |
| Offline transaction integrity | 100% queued and synced |
| Receipt print success | 99%+ first-attempt |
| Staff confidence | Staff prefer Consto POS over a competitor's POS after 1 week |
| Customer linking rate | 60%+ of transactions linked to a customer profile (rest are walk-ins) |

## DPDP compliance notes (POS-specific)

- Customer consent for SMS/WhatsApp captured at first link, stored in `consent_log` with staff witness.
- New customer creation flow forces consent capture before saving the phone number.
- Phone numbers shown to staff are *masked* (last 4 digits visible) until staff actively reveal for service reasons.
- Deletion-request handler must reach POS — when a customer's `consent_log` shows a deletion request, their POS profile is purged within 30 days.

## Brand voice for Agent Panel content

The Agent Panel nudges are read by staff and *spoken* to the customer. Examples:

| Good | Bad |
|---|---|
| `Aarav's birthday in 8 days — gifts under ₹500?` | `CUSTOMER HAS A BIRTHDAY EVENT SCHEDULED.` |
| `Bought paneer last Wed + Sat. Fresh paneer just in.` | `Recommend the paneer SKU based on purchase frequency.` |
| `Lakshmi gaaru prefers Telugu. Greet slowly.` | `Note customer language preference is Telugu.` |

The cashier reads this and translates into warm conversation. The nudge sounds like a sibling whispering in their ear, not a system.

## Open questions / decisions pending

1. **AEPS vendor:** Mantra, Morpho, or another? Decision needed before Phase 2.
2. **Receipt printer model:** Standardise on one specific Indian-market model for reliability. Recommend trial with TVS RP3220 and Epson TM-T82.
3. **Cash drawer brand:** Most ESC/POS-compatible models work. Decide based on availability.
4. **Customer-facing display:** Required or optional for Store 1? Recommend optional in pilot.
5. **Staff training:** How many hours of training before going live? Recommend 8 hours over 2 days.

## Reading list before building

- Supabase docs on offline-first sync patterns
- escpos-php library
- Razorpay UPI Collect API docs
- Electron security best practices (especially context isolation)
- IndianGov DPDP Act 2023 summary
