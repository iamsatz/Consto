# CLAUDE.md — Consto OS Project Context

This file is auto-read by Claude Code. It tells you everything you need to build Consto.

---

## What We're Building

**Consto** — India's first AI-powered Indianised convenience store. A physical store + 8 digital products (Consto OS) that give every customer a personal AI-backed agent who remembers them like family.

Read `00-master-prd.md` for the full concept before building anything.

---

## Tech Stack (Use These — Decided)

| Layer | Tool | Notes |
|-------|------|-------|
| Frontend | React + Tailwind | PWA, mobile-first, no separate mobile apps |
| Database | Supabase | One shared project across all 8 products |
| AI | Claude API | **Haiku** for speed tasks, **Sonnet** for thinking tasks |
| Messaging | WhatsApp Cloud API | Primary customer channel — zero adoption friction |
| Payments | UPI (Razorpay/direct) + AEPS SDK | |
| Hosting | Vercel | Auto-deploy from GitHub |
| Build | Claude Code | This tool |

**Do NOT use**: AWS, mobile native apps, localStorage in artifacts, any paid tool when a free tier works.

---

## The AI Rule (Critical)

- **Haiku** = speed (POS suggestions, stock math, points, checklist flags, quick lookups). Response under 1 sec.
- **Sonnet** = thinking (message drafting, demand forecasting, customer insight, NL queries). Response 2-3 sec.

Each PRD says which model each feature uses. Always use the specified one.

---

## Build Order (Strict — Don't Skip)

**Phase 1 (build now)**
1. **Consto POS** — `02-consto-pos-prd.md` — the data foundation, build first
2. **Consto Agent** — `01-consto-agent-prd.md` — the soul, build second

**Phase 2-4 (later, after Phase 1 validates)**
3. Inventory · 4. Notify · 5. Loyalty · 6. Ops · 7. Predict · 8. HQ
(PRDs for these not written yet — request when Phase 1 works)

Each product's PRD has a numbered **"Build Sequence for Claude Code"** section. Follow it step by step. Don't jump ahead.

---

## Shared Setup (Do Once, Before Any Product)

1. Create Supabase project, get keys
2. Get Claude API key (Haiku + Sonnet access)
3. Set up WhatsApp Cloud API (Meta Business account + number)
4. Connect Vercel to GitHub repo
5. Build the consent flag + data deletion function early (DPDP compliance)

---

## Design System

- Palette: saffron `#E8732A` (primary), sage `#6B8F71`, cream `#FAF5ED` (bg), deep `#1A1612` (text), gold `#D4A843`, terracotta `#C4583A`
- Tone: warm, not corporate. Even error messages are kind.
- Mobile-first. Large touch targets. Telugu + English support.
- (Detailed typography + components: founder will define in Figma)

---

## Privacy Rules (DPDP Act 2023 — Non-Negotiable)

- Explicit consent before any WhatsApp message
- Never message non-consented customers
- Customer data deletion within 30 days of request
- No third-party data selling
- Encrypt phone numbers

---

## Brand Voice (For Any AI-Generated Customer Messages)

- Always use the customer's name
- Never ALL CAPS, never "Dear Customer"
- Telugu phrases where natural ("Baagunnara?", "Dhanyavaadalu")
- Sound like a caring person, not a system
- Under 40 words per WhatsApp message

Good: "Lakshmi aunty, baagunnara? Ekadashi Thursday — kept tulsi bundle ₹80. Horlicks running low na?"
Bad: "Dear Customer, SPECIAL OFFER! 20% OFF! ORDER NOW!"

---

## Files in This Folder

- `00-master-prd.md` — full concept (read first)
- `01-consto-agent-prd.md` — WhatsApp CRM dashboard
- `02-consto-pos-prd.md` — billing & checkout
- `README.md` — how to use with Claude Code
- `CLAUDE.md` — this file

---

## How to Start a Build Session

Tell me (Claude Code):
> "Read CLAUDE.md and 02-consto-pos-prd.md. We're building Consto POS. Do the shared Supabase setup first, then start the POS Build Sequence at step 1."

Then go step by step. Review against Figma. Deploy when each product works.
