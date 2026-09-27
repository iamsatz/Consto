# Consto

An AI-backed Indian neighbourhood convenience store. A 1,000-1,500 sqft physical store ("Home" format) starting in Beeramguda, Hyderabad, paired with 8 digital products (Consto OS) that remember every customer like family.

## Layout

- `PRDs/` — **canonical PRDs.** Read `00-MASTER.md` first, then `01-CONSTO-POS.md`, `02-CONSTO-AGENT.md`, etc. `PRDs/README.md` has the stack and build order.
- `Reference/archive-v1/` — superseded first-draft PRDs. Do not build from these.
- `Reference/reference for portfolio/` — portfolio / pitch source copy.
- `Explorations/` — concept write-ups for the portfolio cards.
- `docs/` — business models (e.g. `store-1-break-even.md`).
- `*.html` — public portfolio site (Vercel auto-deploys `main`).
- `BACKLOG.md` — task tracker.

## Validate first (rule)

No software gets built until the **60-day ₹0 pilot** (Google Sheet + manual SMS/WhatsApp, known contacts) shows the memory idea changes repeat visits. See the top of `BACKLOG.md`. If a task isn't the pilot or needed for it, it waits.

## Tech Stack

| Layer | Tool | Notes |
|---|---|---|
| Frontend | React + Tailwind | Web / PWA. No native apps. |
| Desktop wrapper | Electron | POS only. Same React code runs as web fallback. |
| Database | Supabase | One shared project across all 8 products |
| AI | Claude API | Haiku for speed tasks, Sonnet for thinking tasks |
| Messaging | SMS (MSG91 / Twilio India) | Phase 1. WhatsApp Cloud API arrives in Phase 2. |
| Payments | UPI via Razorpay + AEPS SDK | |
| Hosting | Vercel | Auto-deploy from GitHub |

Buy-vs-build for POS is still open — see `BACKLOG.md`. Don't scaffold a POS before that decision.

## Build Order (strict, after the pilot)

1. **Consto POS** — billing & checkout, data foundation → `PRDs/01-CONSTO-POS.md`
2. **Consto Agent** — customer memory panel embedded in POS → `PRDs/02-CONSTO-AGENT.md`
3. Inventory · Notify · Loyalty · Ops · Predict · HQ (`PRDs/03`–`08`, only after Phase 1 validates)

Each PRD has a numbered build sequence. Follow it step by step.

## Rules

- **AI models:** Haiku = speed tasks (<1s). Sonnet = thinking tasks (2-3s). Each PRD specifies which. Never swap.
- **DPDP Act 2023:** Explicit consent per channel (SMS, WhatsApp) before any message. Encrypt phone numbers. Delete data within 30 days on request. No data on under-18s without verifiable parental consent. Non-negotiable.
- **Brand voice:** Customer's name always. Under 40 words. Telugu phrases where natural. Sound like a person, never a system. AI drafts, a human sends.
- **Facts in public copy:** India does have organised convenience chains (7-Eleven India, Twenty Four Seven, SuperK, Apna Mart). Don't claim "zero". Japan has ~56K convenience stores, not 80K+.
- **Do NOT use:** AWS, native apps, localStorage in artifacts, any paid tool when free tier works.
- **Git:** Don't push straight to `main` (it auto-deploys). Work on a branch.

## Task tracking

`BACKLOG.md` is the single source of truth for work. Update it when tasks move.
