# Consto — Master Product Requirements Document

**Status**: Draft v1.0
**Owner**: Sateesh (Founder, Designer, Product)
**Last Updated**: April 2026
**Document Type**: Master concept PRD — strategic + buildable

---

## 1. Vision

**One-line**: India's first Indianised convenience store where every customer has a personal AI-backed agent who remembers them like family.

**Long-form**: Consto combines the warmth of a kirana with the operational efficiency of a 7-Eleven and adds an AI memory layer that no Indian retail format currently has. Built for Tier 2-3 suburbs and Indian cultural patterns — credit, festivals, pilgrim travel, family buying, regional language.

---

## 2. Problem Statement

### Primary Problem

In Indian suburbs and Tier 2-3 towns, no retail format remembers the customer. This creates four cascading pain points for daily life:

1. **Repetition** — customers explain their preferences, dietary needs, family events to staff every visit
2. **Reactive scrambling** — customers realise mid-cooking they're out of milk, miss festival prep, scramble for last-minute essentials
3. **Service fragmentation** — they juggle 3-4 apps and shops for groceries, bills, recharges, cash withdrawal
4. **Emotional disconnect** — every transaction feels like a transaction, not a relationship

### Why Existing Solutions Fail

| Solution | Why It Falls Short |
|---|---|
| Kiranas | Warm but limited — 50% stockouts, no services, no digital memory |
| Quick Commerce | Fast but faceless — no relationships, 20-30% quality fails |
| Supermarkets | Variety but impersonal — far, rotating staff, no services |
| Kirana Tech Apps | Failed — digitised the ledger but not the care |

### Validation Signals

- 50+ in-person interviews in Beeramguda showed unmet need for "someone who remembers me"
- India has ~0 organised convenience stores vs 80K+ in Japan, 13K+ in Thailand
- 200K+ kirana closures from quick commerce — but customers still crave relationship-based retail
- 88% of Indian retail remains unorganised — massive modernization opportunity

---

## 3. Target Users

### Primary Personas

**Priya — Working Mom, 35 (40% of target users)**
Needs: someone who tracks her family's needs proactively, saves her decision time
Success: She tells her family WhatsApp group about the store unprompted

**Lakshmi Aunty — Retiree, 55+ (20%)**
Needs: personal service, dignity, credit flexibility, no apps
Success: She brings her grandson to the store

**Raju — Gig Driver, 28 (25%)**
Needs: cash + snack + powerbank in 90 seconds, rest space, no judgment
Success: He brings 3 other drivers within a month

**Arjun — Tech Professional, 30 (15%)**
Needs: premium organic, pre-packed pickup, 3-minute efficiency
Success: He cancels BigBasket

### Why These Four

Together they cover the income spectrum, age spectrum, and tech-comfort spectrum of an Indian suburb. The personal agent model must work for all four — though through different interaction patterns (proactive WhatsApp for Priya, voice notes for Lakshmi, speed for Raju, curation for Arjun).

---

## 4. Solution Overview

### What We're Building

A **physical 600-800 sq ft neighbourhood store** + **8 AI-powered digital products** that together form Consto OS — the operating system for Indian neighbourhood retail.

### Core Solution Pillars

**Pillar A — Personal Agent System**
Every customer is assigned a human store agent backed by AI. The agent remembers preferences, sends proactive WhatsApp messages, researches non-paid recommendations (pilgrim stays, festival items), and treats customers like family.

**Pillar B — One-Stop Service Bundle**
Groceries + AEPS cash + bill payments + recharges + OTC medicines + phone accessories + powerbank rental + xerox. All in one visit. Services carry 60% margins and create daily footfall.

**Pillar C — Hyperlocal Variants**
70% core brand, 30% local customisation per location type. Colony format (families), highway format (drivers), premium format (professionals). Same OS, different emphasis.

**Pillar D — Community Anchoring**
Notice board, festival calendar, Hub Huddle events, chai counter, rest space. The store is the neighbourhood's living room, not just a shop. "Come rest even if you don't buy" — a policy that becomes publicity.

---

## 5. Consto OS — The 8 Products

| # | Product | Phase | Primary User | Core Job |
|---|---------|-------|--------------|----------|
| 1 | Consto POS | 1 | Staff at counter | Billing, checkout, customer linking |
| 2 | Consto Agent | 1 | Store agent | Customer relationship management |
| 3 | Consto Inventory | 2 | Store manager | Stock control, waste prevention |
| 4 | Consto Notify | 2 | Agent + system | Customer communication automation |
| 5 | Consto Loyalty | 3 | Customer + agent | Retention, referrals, credit ledger |
| 6 | Consto Ops | 3 | All staff | Daily SOPs, quality tracking |
| 7 | Consto Predict | 4 | Owner/manager | Demand forecasting |
| 8 | Consto HQ | 4 | Owner | Single dashboard, AI queries |

### Architecture Decisions

- **All products built with**: React + Tailwind, hosted on Vercel, deployed via Claude Code
- **Shared database**: Supabase (one source of truth across products)
- **AI layer**: Claude API — Haiku for speed tasks, Sonnet for thinking tasks
- **Customer channel**: WhatsApp Cloud API (zero adoption friction in India)
- **No mobile apps**: Web PWAs only — no downloads required for customers or staff

### Build Strategy

- **Phase 1 (Month 1-2)**: POS + Consto Agent — capture and use customer data
- **Phase 2 (Month 3-4)**: Inventory + Notify — reduce waste, drive engagement
- **Phase 3 (Month 5-6)**: Loyalty + Ops — systematise retention and daily ops
- **Phase 4 (Month 7-8)**: Predict + HQ — forecast demand, owner command view

Each phase is shippable standalone. Phase 1 works even if Phase 4 never ships.

---

## 6. Functional Requirements

### Must-Have Capabilities (MVP across products)

**Customer Memory**
- Capture customer details at first visit (name, phone, family, preferences)
- Store and retrieve preferences across all touchpoints
- Tag dormant customers (no visit in 7+ days)

**Proactive Communication**
- Send WhatsApp messages from store agent profile
- AI-drafted message suggestions (agent edits before sending)
- Track delivery, reads, and replies
- Festival/seasonal campaign templates

**Multi-Service Transactions**
- Process groceries + services (AEPS, bills) in one billing flow
- Multiple payment methods (UPI, cash, credit)
- Digital receipts via WhatsApp
- Customer linking at checkout for purchase history

**Inventory Intelligence**
- Real-time stock sync with every sale
- Expiry tracking with alerts
- Reorder suggestions based on velocity
- Waste tracking and reporting

**Owner Visibility**
- Daily summary of revenue, footfall, waste
- Drill-down into specific products or customers
- Natural language queries ("How did milk do this week?")

### Should-Have (Phase 2+)

- Loyalty points and tier progression
- Referral tracking
- Demand forecasting per SKU
- Festival-aware bundling
- Voice note support for messages
- Telugu/regional language UI

### Won't-Have (Out of scope for v1)

- Mobile apps (web PWAs only)
- Customer-facing app (WhatsApp is the channel)
- Multi-store franchise dashboard (Year 2+)
- Third-party integrations beyond payment gateways and WhatsApp

---

## 7. Non-Functional Requirements

### Performance
- POS billing: complete transaction in under 60 seconds
- AEPS withdrawal: under 2 minutes
- WhatsApp message delivery: under 30 seconds
- AI suggestion generation: under 1 second for speed tasks, under 3 seconds for thinking tasks
- Dashboard load: under 2 seconds on 4G

### Reliability
- 99.5% uptime during store hours (7 AM - 10 PM)
- Offline-capable POS (sync when connection returns)
- Daily automatic backups

### Security & Privacy
- DPDP Act 2023 compliance from day one
- Explicit consent before WhatsApp messaging
- Customer can request data deletion anytime
- No third-party data selling — contractual guarantee
- Phone numbers stored encrypted

### Scalability
- Single store: handle 250 customers/day, 200 transactions/day
- Multi-store ready by Phase 4 (separate Supabase instances or row-level security)
- AI costs scale linearly — no fixed AI infrastructure

### Accessibility
- Telugu + English language toggle
- Mobile-first responsive design (staff use phones/tablets)
- High contrast mode for older staff/customers
- Voice input support for agent message composition

---

## 8. AI Architecture

### When to Use Which Model

**Claude Haiku — Speed Tasks** (response under 1 second)
- POS product suggestions at checkout
- Stock level calculations
- Loyalty points math
- Operations checklist flagging
- Quick lookups and pattern matching

**Claude Sonnet — Thinking Tasks** (response 2-3 seconds)
- Customer message personalisation
- Demand forecasting analysis
- Dormancy reason inference
- Natural language queries in HQ
- Cross-product insights

### Human-AI Decision Boundaries

| Decision Type | Who Decides | Example |
|---|---|---|
| **AI acts alone** | Haiku (no approval needed) | Send order confirmation, calculate points, flag low stock |
| **AI suggests, human approves** | Sonnet drafts → agent taps Yes/No | Send marketing message, suggest reorder quantity |
| **Human decides, AI gives context** | Owner with Sonnet analysis | Hire decisions, new vendor, store layout changes |

---

## 9. Success Metrics

### Year 1 Targets (Single Store, Beeramguda)

**Customer Health**
- NPS > 70
- 6-month retention: 80%
- Average basket: ₹250-400 (vs ₹150 kirana baseline)
- Visit frequency: 3-5x per week

**Business Health**
- Monthly revenue: ₹8L by Month 6
- 40% revenue from non-grocery services
- Fresh produce waste: under 10%
- Break-even: Month 12-18

**Agent Performance**
- 50-100 customers per agent
- 20+ proactive messages per week per agent
- 30%+ conversion on AI-suggested recommendations
- 80% agent retention Year 1

**Community Impact**
- 500+ active agent customers
- 50% growth from referrals
- 10+ Hub Huddle events held

---

## 10. Risks & Mitigations

### Risk 1 — Founder dependency on warmth culture
The smiles, the "come rest" policy, the genuine goodbyes — these work because the founder models them daily. If hired staff don't carry that energy at Store 5 or Store 10, the brand dies.
**Mitigation**: Founder present daily in Store 1 for 6 months. Hire for warmth, not retail experience. Weekly culture huddles. Mystery shopper audits.

### Risk 2 — Agent burnout
One person managing 100 customer relationships is borderline impossible. Burnout = relationship resets = customer churn.
**Mitigation**: AI handles all rote tasks (calculations, drafting, dormancy detection). Agent focuses on emotional labour only. Cap at 75 per agent until validated. Generous commission structure (10% on their customers' purchases).

### Risk 3 — Quick commerce price war
Zepto and Blinkit subsidise prices to capture share. Consto can't compete on price alone.
**Mitigation**: Don't compete on price. Compete on care + services + bundling. Services revenue (40%) cushions grocery margin pressure. Focus on suburbs where quick commerce is structurally weak.

### Risk 4 — DPDP Act compliance complexity
Customer data laws have teeth. Non-compliance can mean fines and shutdown.
**Mitigation**: Explicit opt-in consent at registration. No third-party data sharing. Customer data deletion within 30 days of request. Hire compliance consultant for first audit.

### Risk 5 — Building 8 products is too ambitious for one designer
Even with Claude Code, designing + building + iterating 8 products in 8 months while running the store is risky.
**Mitigation**: Strict phasing. Phase 1 (POS + Agent) must work before Phase 2 starts. Each phase is independently valuable. Founder can defer Phase 3-4 if needed without breaking the business.

---

## 11. Open Questions

1. Will customers accept proactive WhatsApp messages or will they feel intrusive?
2. Can one agent realistically maintain 50-100 warm relationships? Or is 30 the real ceiling?
3. Will Beeramguda families pay a 5-10% premium for care, or is price still dominant?
4. Voice notes vs text for 55+ demographic — what's the right default?
5. Should we offer customer-facing WhatsApp commands ("status", "reorder") or keep all flow agent-mediated?

---

## 12. Build Sequence (Master Roadmap)

| Week | Milestone |
|---|---|
| 1-2 | WhatsApp agent experiment with 20-30 known contacts (no app needed) |
| 3-4 | 50 customer interviews + site scouting in Beeramguda |
| 4 | **Decision Gate 1** — does agent model work? |
| 5-6 | Supplier outreach + real unit economics model |
| 7 | **Decision Gate 2** — do unit economics work? |
| 7-8 | Lease signed + store fitout begins |
| 9-10 | Staff hiring + 2-day training |
| 11 | Soft launch — 50 beta customers |
| 12 | Grand opening |
| Month 1-2 | Build Phase 1 products (POS + Agent) |
| Month 3-4 | Build Phase 2 products (Inventory + Notify) |
| Month 5-6 | Build Phase 3 products (Loyalty + Ops) |
| Month 7-8 | Build Phase 4 products (Predict + HQ) |

---

## 13. Definition of Success (Master)

Consto succeeds when:

1. A first-time visitor in Beeramguda becomes a weekly customer within 30 days
2. That customer tells at least one neighbour about Consto unprompted
3. The store generates ₹8L+ monthly revenue with 25%+ margins by Month 9
4. Consto OS runs the entire operation with under 5 hours of human admin per week
5. At least one franchise partner signs up by Month 15 — validating the playbook

---

## 14. Related Documents

- `01-consto-agent-prd.md` — Phase 1 Product PRD
- `02-consto-pos-prd.md` — Phase 1 Product PRD
- (Future: 03-inventory, 04-notify, 05-loyalty, 06-ops, 07-predict, 08-hq)

---

*This document is the strategic source of truth for Consto. Individual product PRDs reference back to this for context, personas, and shared architecture decisions.*
