# Backlog — Consto

## High Priority

| Task | Platform | Model | Status |
|---|---|---|---|
| **Shared infrastructure setup** — Supabase project + keys, Claude API key, WhatsApp Cloud API (Meta Business account + number), Vercel → GitHub, DPDP consent flag + data deletion function | Desktop | Sonnet | Open |
| **Consto POS** — full build per `Reference/02-consto-pos-prd.md` Build Sequence: scaffold → billing flow → customer linking → payment methods → WhatsApp receipt → day-end reconciliation | Desktop | Sonnet | Open |
| **Consto Agent** — full build per `Reference/01-consto-agent-prd.md` Build Sequence: customer list → profile view → AI message drafting → dormancy detection → message tracking | Desktop | Sonnet | Open |

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
