# 04 · Consto Notify

> Customer communications. SMS in Phase 1.5 (transactional only). WhatsApp opt-in in Phase 2 (emotional + relational). Every message reviewed by humans before sending. Hardcoded consent gate.

| | |
|---|---|
| **Phase** | 1.5 (SMS), 2.2 (WhatsApp) |
| **Priority** | High |
| **Platform** | Web admin app (central ops team) + backend integrations (SMS, WhatsApp) |
| **Primary user** | Central ops team reviewing drafts. Customers receiving messages. |
| **Build status** | Not started |
| **Dependencies** | Consto Agent generates drafts. Customer consent captured in POS. SMS provider account (Phase 1.5). Meta Business + WhatsApp Cloud API account (Phase 2). |
| **Depends on it** | Subscribe (delivery confirmations), Loyalty (tier upgrade messages), Predict (low-stock customer alerts) |

## What it is

A message review and send system. Agent drafts messages. Notify queues them for human review. Human approves. Notify sends via the chosen channel (SMS, then WhatsApp). Delivery logged. Consent enforced.

This is NOT a marketing platform. There is no "blast campaign" feature. Every message is per-customer, drafted by Agent, reviewed by a human.

## Who uses it

| User | What they do |
|---|---|
| **Central ops reviewer** | Approves / edits / rejects drafted messages all day |
| **Manager** | Reviews delivery rates, opt-out rates, customer responses |
| **Owner** | Sees aggregate engagement reporting in HQ |
| **Customer** | Receives the message via SMS or WhatsApp |

## Why it exists

Customers need to hear from Consto sometimes (delivery confirmed, festival items kept aside, fresh paneer just in). But:

- They must explicitly consent first
- The message must sound like a person, not a system
- A human must review before send
- They must be able to opt out instantly

Notify is the controlled delivery layer that makes all of that possible.

## Tech stack

| Layer | Tool |
|---|---|
| Frontend | React 18 + Tailwind CSS, deployed to Vercel |
| DB | Supabase (shared `consent_log`, `agent_drafts`, plus Notify-specific tables) |
| SMS | MSG91 (recommended for India) or Twilio India |
| WhatsApp (Phase 2) | Meta WhatsApp Cloud API |
| AI | Sonnet (already used by Agent to draft; Notify just sends) |

## Core capabilities

### MVP Phase 1.5 (SMS only)

| | |
|---|---|
| **Drafts queue** | All drafts from Agent in one view. Filter by urgency, customer, channel. |
| **Review UI** | Per-draft: full context (why this draft now), draft text, edit, approve, reject. |
| **SMS send** | After approval, send via MSG91 / Twilio. Log delivery status. |
| **Consent gate** | Hardcoded check: no draft can be sent to a customer without active `consent_sms`. |
| **Reply handling** | Inbound SMS replies parsed for STOP keyword, opt-out auto-applied. |
| **Delivery tracker** | Per-message: sent, delivered, failed, opt-out. |
| **Template library** | Pre-approved transactional templates (delivery confirmed, recharge done, credit reminder). Agent uses these as starting points. |
| **Quiet hours** | Hardcoded: no sends before 7am or after 9pm. Drafts queue for next morning. |

### Phase 2 additions (WhatsApp)

- WhatsApp Cloud API integration
- Template message library pre-approved by Meta
- Per-customer channel preference (SMS, WhatsApp, both, neither)
- Rich content (images for festival items, location for store)
- Two-way conversations (customer can ask questions, Agent drafts replies for human review)
- 24-hour conversation window awareness

### Phase 3 additions

- Bulk operations (with caution): approve all of a category at once
- A/B testing of message variants
- Sentiment analysis on customer responses
- Voice-call drafts (Agent suggests a phone-call script for high-priority customers)

## Key screens

### Screen 1 — Drafts queue (default landing)

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO NOTIFY · DRAFTS                          47 pending│
├──────────────────────────────────────────────────────────┤
│  [Urgent] [Today] [This week] [All] · Channel: [All ▼]   │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  🔴 URGENT — Lakshmi Aunty (Telugu)                       │
│  Ekadashi tomorrow. Reminder + tulsi bundle reserved.     │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ "Lakshmi gaaru, baagunnara? 🙏 Ekadashi reapay,    │ │
│  │  tulsi bundle ready Rs 40. Take your time."         │ │
│  └─────────────────────────────────────────────────────┘ │
│  Trigger: Festival calendar + her pattern (always buys)   │
│  Channel: SMS                                              │
│  [Approve & send] [Edit] [Reject]                         │
│                                                            │
│  ─────────────────────────────────────────────────────── │
│                                                            │
│  Priya (English)                                           │
│  Fresh paneer arrival, her usual rhythm                    │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ "Priya, fresh paneer in. Your usual Rs 85/250g.    │ │
│  │  Want me to keep for tomorrow's milk delivery?"     │ │
│  └─────────────────────────────────────────────────────┘ │
│  ...                                                       │
└──────────────────────────────────────────────────────────┘
```

### Screen 2 — Draft edit modal

Full context shown above the editable text. Reviewer can edit before sending. Original Agent-generated version is preserved for learning.

### Screen 3 — Sent / Delivery log

Per-customer history. Last 30 days of sends. Delivery status. Replies received.

### Screen 4 — Opt-out manager

All customers who opted out, when, via what mechanism (STOP reply, in-store request, deletion request). Read-only.

### Screen 5 — Template library

Pre-approved transactional templates. Per-template: status, last used, success rate. Phase 2 also shows Meta approval status for WhatsApp templates.

### Screen 6 — Channel preferences

Per-customer: SMS / WhatsApp / both / none. Source of truth for which channel a draft routes to.

## Data model (Notify-specific tables)

```sql
-- Already in shared schema: consent_log, customers
-- Already in Agent: agent_drafts

-- Sent message log
sent_messages (
  id uuid primary key,
  draft_id uuid references agent_drafts,
  customer_id uuid references customers,
  channel text, -- sms, whatsapp
  provider text, -- 'msg91', 'twilio', 'meta'
  provider_msg_id text,
  to_phone_encrypted text,
  body text,
  status text, -- queued, sent, delivered, failed, undelivered
  status_updated_at timestamp,
  sent_at timestamp default now(),
  failure_reason text,
  cost_paise int -- per-message cost tracking
)

-- Inbound replies
inbound_messages (
  id uuid primary key,
  customer_id uuid references customers,
  channel text,
  from_phone_encrypted text,
  body text,
  received_at timestamp default now(),
  parsed_intent text, -- 'opt_out', 'query', 'confirm', 'unknown'
  reply_to_draft_id uuid references agent_drafts, -- if matched to recent send
  processed boolean default false
)

-- Customer channel preferences
customer_channel_prefs (
  customer_id uuid primary key references customers,
  prefers_sms boolean default false,
  prefers_whatsapp boolean default false,
  preferred_send_window text default '08:00-20:00',
  language_preference text default 'te',
  updated_at timestamp default now()
)

-- Templates (transactional, pre-approved)
templates (
  id uuid primary key,
  name text,
  channel text,
  body text,
  variables jsonb, -- {customer_name, item, amount}
  meta_template_id text, -- for WhatsApp approval ref
  status text, -- 'draft', 'approved', 'rejected', 'paused'
  approved_at timestamp,
  created_at timestamp default now()
)

-- Delivery webhook log
delivery_webhooks (
  id uuid primary key,
  provider text,
  payload jsonb,
  received_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Draft generation | Sonnet (in Agent product) | Notify doesn't generate drafts — it sends them. Agent drafts; Notify routes. |
| Inbound reply intent parsing | Haiku | Quick classification: STOP, query, confirm, unknown. |
| Suggested reply for inbound query | Sonnet (drafted in Agent) | Reply drafts go back into the drafts queue for human review. |

## Build sequence for Claude Code

### Phase 1.5 (SMS only)

1. **Scaffold web app + Supabase wiring**
2. **Sign up for MSG91 / Twilio India + test send**
3. **Build drafts queue UI** — pull `agent_drafts` where status='pending'
4. **Build review modal** — show draft + context + edit + approve buttons
5. **Build SMS send pipeline** — provider API, log to `sent_messages`, update draft status
6. **Build hardcoded consent gate** — block any send to non-consented customer
7. **Build inbound webhook** — receive provider callbacks, log delivery status
8. **Build STOP parsing** — inbound SMS with "STOP" or "STOP CONSTO" → revoke consent + log
9. **Build quiet hours enforcement** — drafts created at night queue for 7am
10. **Build template library** — transactional templates for delivery / recharge / credit
11. **Build sent log + opt-out manager** — debug + audit surfaces
12. **Test end-to-end** with a real customer phone for 1 week

### Phase 2 (WhatsApp added)

13. **Set up Meta Business + WhatsApp Cloud API** — verification, number, webhook
14. **Submit transactional templates for Meta approval**
15. **Build WhatsApp send pipeline** — provider API + 24-hour window awareness
16. **Build channel preference UI** — per-customer SMS / WhatsApp / both
17. **Build rich content support** — image, location, list, button messages
18. **Build conversation-thread view** — group inbound + outbound by customer
19. **Build two-way reply drafting** — inbound query triggers Agent draft for review

## Success metrics

| Metric | Target |
|---|---|
| Draft approval rate | 70%+ approved without major edit (signals brand voice accuracy) |
| Send delivery rate | 95%+ (SMS), 98%+ (WhatsApp) |
| Opt-out rate | Under 2% in any 30-day period |
| Inbound reply engagement | 25%+ of sent messages get a reply (a strong signal) |
| Cost per active customer per month | Under ₹30 (SMS-only), under ₹80 (with WhatsApp) |
| Time to review per draft | Under 30 seconds average |
| Quiet-hours violations | Zero |

## DPDP compliance (critical for this product)

- **Hardcoded consent gate.** Every send checks `consent_log` for active consent of the right channel for that customer at that moment. No bypass.
- **STOP keyword universal.** Customer texts STOP → consent revoked immediately, logged, no further messages.
- **Deletion handler.** When customer requests deletion: stop all queued drafts, archive sent_messages with PII purged, encryption keys for old messages destroyed within 30 days.
- **Audit log.** Every send + every consent change logged in `consent_log` with timestamp + reason + (for staff-initiated) staff witness.
- **No third-party sharing.** Phone numbers shared only with the SMS/WhatsApp provider for the immediate send. Not to analytics tools.
- **PII encryption.** `to_phone_encrypted` column. Decryption only at the moment of the API call. Encrypted at rest.

## Brand voice enforcement

Notify does not generate drafts (Agent does). But Notify can FLAG drafts that violate brand voice rules:

- Detects ALL CAPS phrases → flags
- Detects "Dear Customer" or generic openings → flags
- Detects > 40 words → flags for shortening
- Detects sends outside 7am-9pm → blocks and queues for morning

Flagged drafts still go to human review but with the violation surfaced.

## Cost model

### Phase 1.5 (SMS only)

- MSG91 transactional SMS: ~₹0.15 per message
- Estimated volume: 500 customers × 3 messages/week × 4 weeks = 6,000 SMS/month
- Monthly cost: ~₹900

### Phase 2 (with WhatsApp)

- WhatsApp Cloud API: ₹0.30-1.20 per conversation
- Estimated volume: 200 customers (opt-in) × 4 conversations/week × 4 weeks = 3,200 conversations/month
- Monthly cost: ~₹3,000-₹6,000
- Plus residual SMS for non-WhatsApp customers: ~₹500

**Total estimated comms cost at 500-customer scale:** ₹4,000-₹7,000/month per store.

## What Notify does NOT do

- Send any unconsented message (hardcoded block)
- Generate message drafts (Agent does this)
- Decide what to say (it's a delivery rail, not a copy generator)
- Send marketing blasts (no campaign builder)
- Send outside quiet hours
- Operate without a human in the review loop in Phase 1

## Reading list before building

- MSG91 transactional SMS API docs
- WhatsApp Cloud API documentation (Meta for Developers)
- DPDP Act 2023 — Section 7 (consent) and Section 12 (right to erasure)
- TRAI DLT regulations for SMS (DND, headers, templates)
- Meta WhatsApp Business Policy
