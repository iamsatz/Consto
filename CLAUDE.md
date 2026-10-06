# Consto

India's first AI-backed neighbourhood store. A 1,000-1,500 sq ft physical store in Beeramguda, Hyderabad (Consto Home format, Store #01) plus 8 digital products (Consto OS) that give every customer a personal AI-backed agent who remembers them like family.

Owner: Sateesh (founder, designer, product). No code exists yet. Current phase: design (see `PROCESS.md`, Step 0 done, Step 1 next).

## Single source of truth

- `docs/prd/` — the 8 product PRDs (June 2026). `00-MASTER.md` first, then `01-CONSTO-POS.md`, `02-CONSTO-AGENT.md`. **These win over anything older.**
- `docs/research/` — Products.md, Products-Decisions.md (364 questions + CEO rulings), research notes, six persona reviews
- `docs/decisions/` — convenience-store rulebook, category master, product-company strategy
- `docs/archive/` — May 2026 PRDs (superseded) and session archives. Read for history only.
- `PROCESS.md` — how we work, the gated plan, Claude Code vocabulary
- `BACKLOG.md` — the task list. Update it when tasks move.
- Root `*.html` + `concept-share/` — the public concept site (Vercel, static). Not the product.

## Facts that changed in June 2026 (do not use the old values)

| Topic | Current | Old (archived) |
|---|---|---|
| Store size | 1,000-1,500 sq ft (Home format) | 600-800 sq ft |
| Customer messaging | SMS in Phase 1; WhatsApp opt-in added in Phase 2 | WhatsApp from day one |
| POS platform | React in Electron for the terminal, same code as web fallback | PWA only |
| Products | 8 PRDs written | 3 PRDs |

## Tech stack (decided)

| Layer | Tool | Notes |
|---|---|---|
| Frontend | React + Tailwind | POS wrapped in Electron; everything else web / PWA. No native apps in Phase 1-3 |
| Database | Supabase | One shared project across all 8 products |
| AI | Claude API | Haiku for speed tasks (<1 s), Sonnet for thinking tasks (2-3 s). Each PRD says which. Never swap |
| Messaging | SMS (Phase 1), WhatsApp Cloud API (Phase 2) | |
| Payments | UPI via Razorpay + AEPS SDK | |
| Hosting | Vercel | Auto-deploy from GitHub |

Do NOT use: AWS, native apps, localStorage in artifacts, any paid tool when a free tier works.

## Build order (strict)

1. Consto POS → 2. Consto Agent (Phase 1, must validate) → 3. Inventory · 4. Notify · 5. Loyalty (Phase 2) → 6. Ops · 7. Predict · 8. HQ (Phase 3). Each PRD has a numbered Build Sequence. Follow it.

## How to start any session (the brief)

Every session opens with four lines before any work:

```
Deliverable: one named file, screen or feature
User / audience: who it is for and the moment of use
Constraints: PRD section, tokens, must-not-do list
Done when: what Sateesh will look at to say "finished"
```

If it is a thinking session, say so: "Thinking session, no edits, end with a decision list." Think sessions end with `/capture` and a commit. Build sessions end with `/ship`. Name sessions by deliverable: "Consto - POS checkout flow".

Design chain, no link skipped: person → user story → flow → screen → component → code. Stories and flows live in `docs/design/` (Step 1 and 2 of `PROCESS.md`).

## Working with Sateesh

- Give options and a recommendation before saying an idea is right or wrong.
- Ask before changing documents based on a decision he has not confirmed.
- Code work: feature branch → PR → Vercel preview → merge. Atomic commits. Never push broken code to main.

## Non-negotiables

**DPDP Act 2023:** explicit consent before any marketing message. Encrypt phone numbers. Delete customer data within 30 days of request. No third-party data selling.

**Brand voice (any AI-generated customer message):** customer's name always. Under 40 words. Telugu phrases where natural ("Baagunnara?", "Dhanyavaadalu"). Never ALL CAPS, never "Dear Customer". Sound like a caring person, never a system.

**Design tokens:** saffron `#E8732A` (primary), sage `#6B8F71`, cream `#FAF5ED` (bg), deep `#1A1612` (text), gold `#D4A843`, terracotta `#C4583A`. Warm, not corporate. Mobile-first, large touch targets, Telugu + English.

## Memory

`/Users/apple/.claude/projects/-Users-apple-Documents-Claude-Apps-Cansto/memory/` (auto-loaded).
