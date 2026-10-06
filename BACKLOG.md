# Backlog — Consto

## Products research (added 6 Oct 2026)

| Task | Platform | Model | Status |
|---|---|---|---|
| **Products-Decisions.md** — rebuild the "Decisions for Sateesh" list as a separate, much deeper file (target 200+ questions). Each item: plain question, why it matters, options, "My pick" as a firm assumption (no "test it" answers), blank for Sateesh. Themes: store space & layout (zone by zone, sq ft, shelf feet), food sourcing (vendor tie-up vs own dark kitchen vs hybrid, item by item), hot case / chiller / bakery / drinks menus with prices, organic-natural-healthy range, category shelf shares, brands per need, own brand, pricing & offers, services counter, hours & staffing, medicines, waste, equipment buy/rent, tech, licences, customer policies, launch plan. Done 6 Oct 2026 → `docs/research/Products-Decisions.md` (364 questions). | Desktop | Sonnet | Done |
| **Persona review of Products** — spawn parallel agents as Product Designer, Investor, Entrepreneur, Developer (and Store Operator, Customer); each critiques `docs/research/Products.md` + the decisions file; then a CEO agent reconciles the views and writes the final recommendation into the decisions file. Done 6 Oct 2026 (6 persona files + CEO rulings in `Products-Decisions.md`). | Desktop | Opus | Done |
| **Fold answered decisions into docs** — once Sateesh fills in his answers, update `consto-category-master.md` and `consto-rulebook.md`. | Desktop | Sonnet | Open |
| Commit Products.md + research notes. Done 6 Oct 2026 on branch `chore/single-truth`. | Desktop | — | Done |

## High Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Shared infrastructure setup** — Supabase project + keys, Claude API key, WhatsApp Cloud API (Meta Business account + number), Vercel → GitHub, DPDP consent flag + data deletion function | Desktop | Sonnet | Open |
| **Consto POS** — full build per `docs/prd/01-CONSTO-POS.md` Build Sequence: scaffold → billing flow → customer linking → payment methods → SMS receipt (WhatsApp Phase 2) → day-end reconciliation | Desktop | Sonnet | Open |
| **Consto Agent** — full build per `docs/prd/02-CONSTO-AGENT.md` Build Sequence: customer list → profile view → AI message drafting → dormancy detection → message tracking | Desktop | Sonnet | Open |

## Medium Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Telugu language toggle** — i18n across POS + Agent; needs library decision (react-i18next?) before building | Desktop | Sonnet | Open |
| **Offline POS mode** — sync strategy for internet drops during store hours; needs architecture decision (IndexedDB + service worker?) | Desktop | Opus | Open |
| **AEPS SDK integration** — vendor selection + sandbox access needed first | Desktop | Sonnet | Open |

## Low Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Phase 2 PRDs** — write Inventory + Notify PRDs once Phase 1 is live and validated | Either | Sonnet | Open |

## Quick Wins

| Task | Platform | Model | Status |
|---|---|---|---|
| ESLint + Prettier setup with project rules | Desktop | Sonnet | Open |
| Supabase row-level security policies for customer data | Desktop | Sonnet | Open |
| Shared design token file (saffron `#E8732A`, sage `#6B8F71`, cream `#FAF5ED`, deep `#1A1612`, gold `#D4A843`, terracotta `#C4583A`) | Desktop | Sonnet | Open |
| WhatsApp brand voice prompt template (reusable across Agent + Notify) | Either | Sonnet | Open |
