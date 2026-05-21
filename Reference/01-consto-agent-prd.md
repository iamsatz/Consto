# Consto Agent — Product Requirements Document

**Product**: Consto Agent (WhatsApp CRM Dashboard)
**Phase**: 1 (Month 1-2)
**Status**: Draft v1.0
**Priority**: Core Product — the heart of Consto
**Parent Doc**: `00-master-prd.md`

---

## 1. What This Product Is

Consto Agent is a web dashboard that store staff use to manage customer relationships. It is **not** WhatsApp itself — it's the intelligence layer that sits behind WhatsApp.

**Simple analogy**:
- WhatsApp = the pipe (how messages reach customers)
- Consto Agent = the brain (what to say, to whom, when, and why)

A store agent opens Consto Agent on a tablet or phone. They see every customer's full profile, get AI-drafted personalised messages, spot who's drifting away, and deliver concierge-level care to 50-100 customers — something that normally requires a luxury hotel staff.

---

## 2. The Problem This Product Solves

### Core Problem

A ₹15,000/month store worker cannot remember the preferences, family details, festivals, allergies, and buying patterns of 100 customers. Human memory doesn't scale. So either:
- The store stays tiny (one owner who knows everyone), OR
- The store scales but loses the personal touch (becomes a supermarket)

Consto Agent breaks this trade-off. It gives a low-skill worker the memory of a butler and the warmth of family — at scale.

### Who Has This Problem

**Primary user: The Store Agent** (the employee using the tool)
- Needs to feel confident and capable, not overwhelmed
- Is WhatsApp-native but not enterprise-software-trained
- Manages 50-100 customers simultaneously
- Has limited time between in-store customers

**Beneficiary: The Customer** (Priya, Lakshmi, Raju, Arjun)
- Wants to feel remembered and cared for
- Doesn't want to repeat preferences
- Wants proactive help, not reactive scrambling

---

## 3. User Stories

### Agent Stories (Primary User)

- As an agent, I want to see all my customers in one list so I know who I'm responsible for
- As an agent, I want to instantly see a customer's preferences when they walk in so I don't have to ask "what do you usually buy?"
- As an agent, I want AI to draft a personalised message so I don't stare at a blank screen wondering what to say
- As an agent, I want to know which customers haven't visited recently so I can check in before I lose them
- As an agent, I want to log a new preference in 5 seconds so customer memory stays current
- As an agent, I want to see if my messages are working (replies, visits) so I know I'm doing a good job

### Customer Stories (Beneficiary)

- As Priya, I want my agent to remember my family so I never repeat myself
- As Lakshmi Aunty, I want voice notes instead of text because I struggle to read small screens
- As Raju, I want a quick "your size L Jockey restocked" message so I don't waste a trip
- As Arjun, I want recommendations that match my keto diet without me explaining it again

---

## 4. Core Features (MVP)

### Feature 1 — Customer List

**What**: A scrollable list of all customers assigned to this agent.

**Requirements**:
- Show name, photo (optional), last visit date, total spend, loyalty tier
- Visual indicators: red dot (dormant 7+ days), gold star (top spender), green dot (visited today)
- Search by name or phone number
- Filter: All / Dormant / Top spenders / New (joined this week)
- Sort: Recent visit / Highest spend / Alphabetical
- Tap any customer → opens their profile

**AI involvement**: None (this is a data view). Uses Haiku only if natural-language search is added later.

### Feature 2 — Customer Profile

**What**: The full memory of a single customer.

**Requirements**:
- **Header**: Name, phone, photo, loyalty tier, total spend, member since
- **Preferences**: Diet (veg/non-veg/keto), allergies, preferred brands, regular items
- **Family**: Family size, members' names, important dates (birthdays, anniversaries)
- **Festivals**: Which festivals they celebrate (drives festival bundling)
- **Communication**: Preferred language, text vs voice note preference, best time to message
- **Purchase history**: Last 10 transactions with date, items, amount
- **Notes**: Free-text field for agent observations ("prefers morning visits", "son studies abroad")
- **Credit ledger**: If applicable — amount owed, last payment (with dignity — never shown publicly)

**Editing**: Agent can update any field in under 5 seconds. Changes save instantly.

**AI involvement**: Sonnet can summarise the profile into a one-line briefing ("Regular Tuesday shopper, family of 4, prefers Akshayakalpa milk, son's birthday June 15").

### Feature 3 — Message Composer

**What**: Where the agent sends WhatsApp messages, with AI assistance.

**Requirements**:
- Select a customer (or customer segment for broadcasts)
- AI pre-drafts a contextual message based on the customer's profile and situation
- Agent can edit, regenerate, or write from scratch
- Tone presets: Proactive restock / Festival reminder / Win-back / Thank you / Custom
- Telugu / English / mixed toggle
- Voice note option (for customers who prefer it)
- Preview before send
- One-tap send via WhatsApp Cloud API
- Never sends without agent approval (human-in-the-loop)

**AI involvement**: Sonnet drafts the message. Must use the customer's name, match their language preference, reference their actual preferences, and sound human — never "Dear Customer" or ALL CAPS.

**Example AI draft**:
> "Lakshmi aunty, baagunnara? 🙏 Ekadashi Thursday — I've kept tulsi and wick bundle ₹80. Also your Horlicks is running low na? Shall I keep one?"

### Feature 4 — Dormancy Alerts

**What**: A list of customers who've stopped visiting, with AI-suggested reasons and re-engagement messages.

**Requirements**:
- Auto-flag customers with no visit in 7+ days (configurable threshold)
- Sort by: days since last visit / spend value (prioritise high-value dormant)
- AI suggests likely reason ("Lakshmi usually visits Monday — missed 2 weeks. Pension delay or travelling?")
- AI drafts a care-first re-engagement message (not salesy)
- Agent reviews, edits, sends
- Track: did the dormant customer return after the message?

**AI involvement**: Sonnet infers dormancy reason from purchase patterns and drafts re-engagement message.

### Feature 5 — Agent Dashboard (Daily View)

**What**: The agent's daily performance and to-do view.

**Requirements**:
- Today's stats: messages sent, replies received, orders placed via WhatsApp, dormant customers re-engaged
- Today's suggested actions: "5 customers to message", "3 dormant to check in", "2 birthdays this week"
- Agent performance score (gentle, motivating — not punitive)
- Quick access to most-active customers

**AI involvement**: Sonnet generates the daily action list by analysing customer states.

---

## 5. User Flows

### Flow 1 — Customer Walks Into Store

1. Agent sees customer enter, recognises or asks name
2. Agent searches name in Customer List → taps profile
3. Profile shows: "Priya — family of 4, usual: Akshayakalpa milk 2L, son's birthday June 15"
4. Agent greets warmly: "Priya ji! Milk's here. Saw your son's birthday is coming — want to see some gift options?"
5. After transaction, agent adds any new note (e.g. "asked about organic vegetables")

### Flow 2 — Proactive Morning Message Round

1. Agent opens Dashboard → sees "8 customers to message today"
2. Taps first customer → Message Composer opens with AI draft pre-loaded
3. Agent reviews draft, tweaks one word, taps Send
4. Repeats for each → 8 messages sent in ~10 minutes
5. Dashboard updates: "8 sent, 0 replies yet"

### Flow 3 — Re-engaging a Dormant Customer

1. Agent opens Dormancy Alerts → sees "Lakshmi Aunty, 14 days, usually weekly"
2. AI note: "Likely pension delay or travel. Suggest gentle check-in."
3. AI draft: "Lakshmi aunty, haven't seen you in a while — everything okay? 🙏 Kept your usual Horlicks aside just in case."
4. Agent sends as voice note (Lakshmi prefers voice)
5. System tracks: Lakshmi visits 2 days later → marked re-engaged

---

## 6. Design Requirements

### Visual
- Warm, approachable — not corporate CRM coldness
- Consto palette: saffron primary, sage accents, cream background
- Large touch targets (used on tablets/phones, sometimes by older staff)
- Customer photos prominent (faces build empathy)

### Interaction
- Everything reachable in 2 taps maximum
- Message composer must feel fast — agents send 20+ messages/week
- Editing a preference: tap field → type → auto-save (no save button friction)
- Mobile-first (primary device is a phone or tablet)

### Tone
- Even error messages should be warm ("Couldn't send — let's try again" not "Error 403")
- Performance score is motivating, never shaming

---

## 7. Technical Requirements

### Stack
- **Frontend**: React + Tailwind (PWA)
- **Database**: Supabase (customers, preferences, messages, transactions tables)
- **AI**: Claude Sonnet (message drafting, dormancy inference, profile summaries)
- **Messaging**: WhatsApp Cloud API (send/receive, delivery status)
- **Hosting**: Vercel
- **Build tool**: Claude Code

### Key Data Model (Supabase tables)
- `customers` (id, name, phone, photo, tier, total_spend, joined_date, agent_id)
- `preferences` (customer_id, diet, allergies, brands, language, message_pref, notes)
- `family` (customer_id, member_name, relation, important_date)
- `festivals` (customer_id, festival_name)
- `transactions` (id, customer_id, date, items, amount) — synced from POS
- `messages` (id, customer_id, content, sent_at, status, replied)

### Integrations
- WhatsApp Cloud API (outbound + delivery webhooks)
- Reads transaction data from Consto POS (shared Supabase)

### Privacy (DPDP Act)
- Consent flag per customer (opted into messaging: yes/no)
- No message sends to non-consented customers
- Data deletion function (removes all customer records on request)

---

## 8. AI Prompt Design (For Claude Code to implement)

### Message Drafting Prompt (Sonnet)

System context to provide:
- Customer's name, language preference, preferences, recent purchases, situation (restock/festival/dormant/thankyou)
- Brand voice rules: warm, use name, never ALL CAPS, never "Dear Customer", Telugu phrases where appropriate, sound like a caring person not a system

Expected output: One short WhatsApp-ready message under 40 words.

### Dormancy Inference Prompt (Sonnet)

System context to provide:
- Customer's usual visit pattern, last visit date, purchase history, any notes

Expected output: One-line likely reason + one re-engagement message draft.

### Profile Summary Prompt (Haiku — speed)

System context: customer record
Expected output: One-line briefing for agent to read at a glance.

---

## 9. Success Metrics (This Product)

- Agent sends 20+ proactive messages/week using the tool
- 30%+ of AI-drafted messages sent with minor or no edits (proves draft quality)
- 40%+ reply rate on proactive messages
- 50%+ of dormant customers re-engaged within 30 days of a check-in message
- Agent can pull up any customer profile in under 5 seconds
- New preference logged in under 5 seconds

---

## 10. MVP Scope (What to Build First)

### In Scope (Phase 1 MVP)
- Customer List (with search, basic filters)
- Customer Profile (view + edit all fields)
- Message Composer (AI draft + manual edit + WhatsApp send)
- Dormancy Alerts (auto-flag + AI suggestion + send)
- Basic Daily Dashboard (today's stats + action list)

### Out of Scope (Later)
- Customer segmentation for bulk campaigns (→ Consto Notify, Phase 2)
- A/B testing messages (→ Consto Notify)
- Voice note transcription
- Multi-agent management view (→ when 2nd agent hired)
- Automated message scheduling (keep human-in-loop for MVP)

---

## 11. Build Sequence for Claude Code

1. Set up Supabase tables (customers, preferences, family, festivals, messages)
2. Build Customer List screen (read from Supabase, search, filter)
3. Build Customer Profile screen (read + edit, auto-save)
4. Integrate WhatsApp Cloud API (send message, receive delivery status)
5. Build Message Composer (Sonnet draft + edit + send)
6. Add Dormancy logic (query customers by last_visit, Sonnet inference)
7. Build Daily Dashboard (aggregate stats + action list)
8. Add DPDP consent flag + data deletion function
9. Test with real WhatsApp number + 5 dummy customers
10. Deploy to Vercel

---

## 12. Open Questions (This Product)

1. Should customers be able to message the store directly and have it appear in the agent's view? (inbound flow)
2. How do we handle one customer shared across two agents (e.g. shift change)?
3. Should the AI draft auto-translate to Telugu, or should the agent toggle manually?
4. What's the right dormancy threshold — 7 days, 10 days, or per-customer based on their usual frequency?

---

*This product is the soul of Consto. If only one product gets built well, it's this one.*
