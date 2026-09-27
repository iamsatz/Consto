# Backlog — Consto

## Now — Validate First (₹0, no code)

| Task | Platform | Model | Status |
|---|---|---|---|
| **60-day memory pilot** — 30–50 known households near Beeramguda. Google Sheet as the "memory" (name, family, usuals, festivals, last visit). Claude drafts SMS/WhatsApp nudges; a human sends each one. Only message people who opted in (DPDP). Track: repeat-visit rate, basket size, reply rate, "felt remembered" feedback vs a no-message control group | Either | Sonnet | Open |
| **Pilot success bar** — write down before day 1 what result means "build" vs "stop" (e.g. ≥20% lift in repeat visits vs control) | Either | Sonnet | Open |
| **Confirm customer interviews** — check whether the "50+ Beeramguda interviews" claim happened; if not, run them and remove the claim from copy until it's true | Either | Sonnet | Open |
| **Store 1 break-even** — pressure-test `docs/store-1-break-even.md` with real Beeramguda rent, wage and supplier-margin quotes | Either | Sonnet | Open |

## High Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Repo privacy** — `iamsatz/Consto` is public and holds `Reference/strategy-product-company.md` (marked internal). Make repo private, or move the file out; it stays in git history either way | Either | — | Open — owner decision |
| **POS: buy vs build** — compare off-the-shelf GST-compliant POS (e.g. Petpooja, GoFrugal, Marg, Vyapar) against `PRDs/01-CONSTO-POS.md`. Default: buy the POS, build only the memory layer on top | Either | Sonnet | Open |
| **Shared infrastructure setup** — Supabase project + keys, Claude API key, SMS provider (MSG91 / Twilio India), Vercel → GitHub, DPDP consent flags + data deletion function. *After pilot validates* | Desktop | Sonnet | Blocked on pilot |
| **Consto POS** — per `PRDs/01-CONSTO-POS.md` build sequence (or integration spec if "buy" wins). *After pilot + buy-vs-build* | Desktop | Sonnet | Blocked on pilot |
| **Consto Agent** — memory panel per `PRDs/02-CONSTO-AGENT.md` build sequence. *After pilot validates* | Desktop | Sonnet | Blocked on pilot |

## Medium Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **DPDP: children's data** — DPDP Act §9 needs verifiable parental consent for under-18s and bans tracking/targeted ads at them. Add an age flag; never store or message minors (school-kid snack buyers, tuition users) without it | Desktop | Sonnet | Open |
| **Telugu language toggle** — i18n across POS + Agent; needs library decision (react-i18next?) before building | Desktop | Sonnet | Open |
| **Offline POS mode** — sync strategy for internet drops during store hours; needs architecture decision (IndexedDB + service worker?) | Desktop | Opus | Open |
| **AEPS SDK integration** — vendor selection + sandbox access needed first | Desktop | Sonnet | Open |
| **Fix public site stats** — "zero organised convenience stores" and "Japan 80,000+" in `index.html`, `concept.html`, `concept-share/index.html`, `portfolio.html`. Needs owner approval (public copy) | Either | Sonnet | Open — owner decision |

## Low Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Competitor teardown** — SuperK, Apna Mart, 7-Eleven India, Twenty Four Seven: format, pricing, store economics, why they win/lose in suburbs | Either | Sonnet | Open |
| **Positioning** — decide: Tier-1 suburb (Beeramguda, quick-commerce reachable) vs true Tier-2 town. Changes format, pricing and pitch | Either | Sonnet | Open |

## Quick Wins

| Task | Platform | Model | Status |
|---|---|---|---|
| ESLint + Prettier setup with project rules | Desktop | Sonnet | Open |
| Supabase row-level security policies for customer data | Desktop | Sonnet | Open |
| Shared design token file (saffron `#E8732A`, sage `#6B8F71`, cream `#FAF5ED`, deep `#1A1612`, gold `#D4A843`, terracotta `#C4583A`) | Desktop | Sonnet | Open |
| Brand voice prompt template for SMS/WhatsApp (reusable across Agent + Notify) | Either | Sonnet | Open |

## Done

| Task | Status |
|---|---|
| Archive v1 PRDs to `Reference/archive-v1/`; `PRDs/` is canonical | Done |
| Rewrite `CLAUDE.md` to match `PRDs/`; add `AGENTS.md` pointer | Done |
