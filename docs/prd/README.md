# Consto OS — Product Requirements Documents

8 integrated AI-powered products that together form Consto OS. Same shared database, two AI model tiers (Haiku for speed, Sonnet for thinking), three deployment targets (Electron desktop for POS, Web PWA for staff/manager, Web for owner).

## How to use these PRDs

Each PRD is **self-contained**. Take any one PRD into a fresh build environment (Claude Code, Cursor, or any developer) and it should be possible to scaffold the app from it. Each PRD includes:

- What it is and who uses it
- Phase priority and dependencies
- Tech stack and platform
- Core capabilities by phase
- Data model
- AI integration (Haiku vs Sonnet split)
- Step-by-step build sequence
- Success metrics
- DPDP / brand voice notes

## Build order (strict)

| # | Product | Phase | Priority | Platform | Status |
|---|---|---|---|---|---|
| **01** | **Consto POS** | 1.1 | Critical | Electron desktop + web | Not started |
| **02** | **Consto Agent** | 1.2 | Critical | Embedded in POS + web admin | Not started |
| **03** | **Consto Inventory** | 2.1 | High | Web PWA (tablet-friendly) | Not started |
| **04** | **Consto Notify** | 2.2 | High | Web admin + SMS/WhatsApp APIs | Not started |
| **05** | **Consto Loyalty** | 2.3 | High | Web admin + embedded in POS | Not started |
| **06** | **Consto Ops** | 3.1 | Medium | Web PWA | Not started |
| **07** | **Consto Predict** | 3.2 | Medium | Web dashboard | Not started |
| **08** | **Consto HQ** | 3.3 | Critical for owner | Web dashboard | Not started |

**Phase 1 must validate before Phase 2 starts.** POS + Agent prove the core thesis. Without that proof, building Phase 2 is wasted capital.

## Platform decisions (locked in)

- **Single React + Tailwind codebase per app**
- **Consto POS specifically:** wrapped in Electron for store terminals. Same React code runs as a web fallback.
- **All other apps:** Web. PWA install for tablet-heavy use cases (Inventory, Ops).
- **No native iOS / Android apps in Phase 1-3.** A lightweight subscriber app (for Consto Subscribe add-ons) may come in Phase 2 as a separate decision.
- **WhatsApp deferred to Phase 2.** Phase 1 ships with SMS only for transactional comms. WhatsApp added once in-store relationship is validated.

## Tech stack (decided)

| Layer | Tool | Notes |
|---|---|---|
| Frontend | React + Tailwind | All 8 apps. PWA for tablet-friendly ones. |
| Desktop wrapper | Electron | POS only. |
| Database | Supabase | One shared project across all 8 apps. |
| AI | Claude API | Haiku for speed (POS, Loyalty, Ops), Sonnet for thinking (Agent, Inventory, Notify, Predict, HQ). |
| SMS | MSG91 or Twilio India | Phase 1.5 onwards. |
| WhatsApp | Meta Cloud API | Phase 2 onwards. |
| Payments | UPI via Razorpay + AEPS SDK | POS only. |
| Hosting | Vercel | All web apps auto-deploy from GitHub. |
| Desktop distribution | Electron auto-updater | POS only. |

## Shared infrastructure (do before any product)

1. Supabase project + service keys
2. Claude API key (Haiku + Sonnet access)
3. Vercel connected to repo (already done for portfolio site)
4. SMS provider account (MSG91 or Twilio India) — Phase 1.5
5. DPDP consent flag schema + data deletion function

## DPDP Act 2023 — non-negotiable rules (apply to every product)

- Explicit consent before any digital message (SMS or WhatsApp)
- Never message non-consented customers
- Customer data deletion within 30 days of request
- No third-party data selling
- Encrypt phone numbers at rest
- Maintain consent audit log

## Brand voice (apply to every AI-generated customer message)

- Always use customer's name
- Never ALL CAPS, never "Dear Customer"
- Telugu phrases where natural (`Baagunnara?`, `Dhanyavaadalu`)
- Sound like a caring person, never a system
- Under 40 words per message
- Human in the loop — AI drafts, humans review before any message sends

## Files in this folder

- `README.md` — this file
- `00-MASTER.md` — full Consto OS architecture, build phases, cross-app data flow
- `01-CONSTO-POS.md` — billing & checkout + embedded Agent panel
- `02-CONSTO-AGENT.md` — customer memory + intelligence layer
- `03-CONSTO-INVENTORY.md` — stock, expiry, reorder, waste prediction
- `04-CONSTO-NOTIFY.md` — customer communications (SMS Phase 1.5, WhatsApp Phase 2)
- `05-CONSTO-LOYALTY.md` — points, tiers, retention, churn prediction
- `06-CONSTO-OPS.md` — daily checklists, task management, photo proofs
- `07-CONSTO-PREDICT.md` — SKU-level demand forecasting
- `08-CONSTO-HQ.md` — owner dashboard + Wisdom layer + natural-language queries
