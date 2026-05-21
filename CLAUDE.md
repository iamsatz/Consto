# Consto

India's first AI-powered Indianised convenience store. A physical 600-800 sq ft neighbourhood store in Tier 2-3 suburbs (starting Beeramguda) paired with 8 digital products (Consto OS) — giving every customer a personal AI-backed agent who remembers them like family.

## Layout

- `Reference/` — all PRDs: `00-master-prd.md` (read first), `01-consto-agent-prd.md`, `02-consto-pos-prd.md`
- `BACKLOG.md` — task tracker

## Tech Stack

| Layer | Tool | Notes |
|---|---|---|
| Frontend | React + Tailwind | PWA, mobile-first, no native apps |
| Database | Supabase | One shared project across all 8 products |
| AI | Claude API | Haiku for speed tasks, Sonnet for thinking tasks |
| Messaging | WhatsApp Cloud API | Primary customer channel |
| Payments | UPI via Razorpay + AEPS SDK | |
| Hosting | Vercel | Auto-deploy from GitHub |

## Build Order (strict)

1. **Consto POS** — billing & checkout, data foundation → `Reference/02-consto-pos-prd.md`
2. **Consto Agent** — WhatsApp CRM dashboard → `Reference/01-consto-agent-prd.md`
3. Inventory · Notify · Loyalty · Ops · Predict · HQ (Phase 2–4, after Phase 1 validates)

Each product PRD has a numbered **Build Sequence for Claude Code** section. Follow it step by step.

## Rules

- **AI models:** Haiku = speed tasks (<1s). Sonnet = thinking tasks (2-3s). Each PRD specifies which. Never swap.
- **DPDP Act:** Explicit consent before WhatsApp. Encrypt phone numbers. Delete data within 30 days on request. Non-negotiable.
- **Brand voice:** Customer's name always. Under 40 words. Telugu phrases where natural. Sound like a person, never a system.
- **Do NOT use:** AWS, native apps, localStorage in artifacts, any paid tool when free tier works.

## Task tracking

`BACKLOG.md` is the single source of truth for work. Update it when tasks move.

## Other

- Workflow guide: `/Users/apple/Library/Mobile Documents/com~apple~CloudDocs/Arjun AI Gems/workflows/claude-code-workflow-guide.md`
- Memory: `/Users/apple/.claude/projects/-Users-apple-Documents-Claude-Apps-Cansto/memory/` (auto-loaded)
