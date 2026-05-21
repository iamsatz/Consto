# Consto PRD — Read Me First

This folder contains the Product Requirements Documents for Consto. Use these with Claude Code to build the products.

---

## Folder Structure

```
consto-prd/
├── README.md                  ← You are here
├── 00-master-prd.md           ← Entire Consto concept (read first)
├── 01-consto-agent-prd.md     ← Product 1: WhatsApp CRM dashboard
└── 02-consto-pos-prd.md       ← Product 2: Billing & checkout
```

Future PRDs to write (Phase 2-4):
- `03-consto-inventory-prd.md`
- `04-consto-notify-prd.md`
- `05-consto-loyalty-prd.md`
- `06-consto-ops-prd.md`
- `07-consto-predict-prd.md`
- `08-consto-hq-prd.md`

---

## How to Read These

1. **Start with `00-master-prd.md`** — it has the vision, problem, personas, architecture, and risks. Everything else references it.
2. **Then read the product PRD** you want to build (`01` or `02`).
3. Each product PRD has a **"Build Sequence for Claude Code"** section — that's your step-by-step.

---

## How to Use With Claude Code

### Step 1 — Set Up Your Project

```
mkdir consto-os
cd consto-os
claude
```

### Step 2 — Give Claude Code Context

Start your Claude Code session by sharing the relevant PRD:

> "Read this PRD. We're building Consto POS. Follow the Build Sequence section. Start with step 1 — set up the Supabase tables."

Paste or reference the PRD file.

### Step 3 — Build Phase by Phase

Don't build all 8 products at once. Follow the master roadmap:

- **Month 1-2**: Build POS + Consto Agent (Phase 1)
- **Month 3-4**: Inventory + Notify (Phase 2)
- **Month 5-6**: Loyalty + Ops (Phase 3)
- **Month 7-8**: Predict + HQ (Phase 4)

### Step 4 — Build One Feature at a Time

Within each product, follow the numbered Build Sequence. Tell Claude Code:

> "Now do step 2 — build the item entry screen with barcode scan and search."

Review each step against your Figma designs before moving on.

---

## Shared Setup (Do Once, Before Any Product)

All products share this foundation:

| Layer | Tool | Setup |
|-------|------|-------|
| Database | Supabase | Create project, get API keys |
| AI | Claude API | Get API key — Haiku + Sonnet |
| Messaging | WhatsApp Cloud API | Meta Business account + number |
| Hosting | Vercel | Connect GitHub repo |
| Design | Figma | Your screens |

Tell Claude Code to set up the shared Supabase project first, then build products on top of it.

---

## The AI Rule (Important)

- **Haiku** = speed tasks (POS suggestions, stock math, quick lookups)
- **Sonnet** = thinking tasks (message drafting, forecasting, customer insight)

Each PRD specifies which model each feature uses. Tell Claude Code to use the right one.

---

## Privacy (Don't Skip)

Every product must follow DPDP Act 2023:
- Explicit consent before WhatsApp messaging
- Customer data deletion on request
- No third-party data selling
- Encrypted phone numbers

Build the consent flag and deletion function early, not as an afterthought.

---

## Your Workflow Going Forward

1. **Design** the screens in Figma
2. **Open** Claude Code in your project folder
3. **Reference** the relevant PRD
4. **Build** following the Build Sequence, one step at a time
5. **Review** against Figma, iterate
6. **Deploy** to Vercel
7. **Move** to the next product

---

## Build Order (Recommended)

1. **Consto POS first** — it's the data foundation. Nothing works without clean transaction data.
2. **Consto Agent second** — it's the soul. The reason Consto exists.
3. Then Phase 2-4 as you validate the model.

---

*Keep these PRDs updated as you learn. A PRD is a living document, not a contract. When reality teaches you something, update the doc.*
