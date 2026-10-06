# Persona review: Software Developer / Tech Lead

**Reviewer lens:** builds POS and WhatsApp systems for small Indian retailers (React + Tailwind PWA, Supabase, Claude API, WhatsApp Cloud API, Razorpay UPI, Vercel). Reads every store decision as a data requirement, a software requirement, or a request that software cannot fulfil.

**Inputs read:** `docs/research/Products.md` (all 12 sections and the 60 decisions), `02-consto-pos-prd.md`, `01-consto-agent-prd.md`, `CLAUDE.md`, research notes 03 (Tanpin Kanri), 04 (time-stamping, AI ordering), 06 (GST 2.0, MRP), 07 (labelling liability), 09 (hardware capex), and the September session archive.

**Three framing assumptions for everything below**

1. The store opens on a fixed date whether or not any Consto software is ready. Every line of day-1 operations must have a paper or off-the-shelf fallback.
2. Store 1 has roughly 150–250 bills a day, 1,500 items, 2 staff per shift, one owner who is also the product owner. There is no second developer and no ops engineer.
3. The founder cannot pilot, so the data captured in the first 90 days is the pilot. The software's first job is to capture clean data, not to be clever.

Date: 6 October 2026.

---

## Part 1. Where I disagree with the report or the PRDs

### 1. Build vs buy the POS for Store 1

> POS PRD: "Consto POS is the foundation. Build it first, build it solid. Every other product drinks from this well."
> Session archive: "don't build a POS from scratch for Store 1; build only the memory panel beside an existing POS. Keep AI out of billing."

The archive is right and the PRD is wrong for Store 1. A compliant Indian retail POS is not the 12-step build the PRD lists; it is GST B2C invoicing with the store GSTIN and FSSAI number in the header, HSN on request, MRP enforcement per batch, two-decimal prices after GST 2.0, returns, a cash drawer kick through an ESC/POS printer, a 1,500-item master with barcodes that someone has to type in, stock reports, an udhaar ledger, day-close, and offline billing. Every ₹500–2,000/month kirana POS on the market already does all of that; reaching parity in-house is 40–60 developer-days before the first customer-specific feature, and the store opens anyway. The "no paid tool when free tier works" rule does not apply: there is no free tier that does GST billing with a thermal printer, and two months of build is dearer than ₹2,000 a month.

My position: **buy a SaaS kirana POS for Store 1** on five must-haves (itemised bill export with timestamp and customer phone, daily CSV or an API, MRP-per-batch, udhaar ledger, offline billing), and **build only the Consto layer**: the Supabase warehouse the POS exports into, the memory panel on a second tablet, WhatsApp, pre-orders, waste log. Write Consto POS at Store 2–3, when the counter has told you what it needs. The PRD's build order ("POS first, Agent second") inverts: the Agent-lite ships first because it is the part nobody sells.

### 2. What the "agent" can actually do in month 1

> Agent PRD: "Agent sends 20+ proactive messages/week using the tool"; "Auto-flag customers with no visit in 7+ days"; Sonnet "infers dormancy reason from purchase patterns".

In month 1 there are no purchase patterns. Dormancy at 7 days needs a per-customer baseline of at least three visits; a profile worth summarising needs the same. The customer table on day 1 is empty, and the realistic fill rate is 20–30% of bills linked to a phone, not the PRD's 70%. The PRD also assumes the WhatsApp Cloud API is live on day 1; Meta business verification, a dedicated number (it cannot be the owner's personal WhatsApp) and template approvals take 2–6 weeks and can slip.

My position: month 1 the "agent" is a **WhatsApp number with five approved templates and a human reading replies**: (1) receipt, (2) opt-in ask, (3) pre-order confirmation, (4) 8pm discount window to opted-in customers, (5) khata balance. No Sonnet-drafted personal messages until a customer has ≥3 linked visits (roughly week 5–6 for the first regulars). If Meta verification is not through by opening day, the free WhatsApp Business app on a store phone does the same five jobs by hand. The "remembers you like family" promise in month 1 is a notes field and a name; the memory the software adds is real only from month 2.

### 3. Hot-case pull-by: POS block vs printed tag

> Report 8.7 / decision 7: "POS blocks sale after pull-by" — "My pick: 2 h, POS blocks sale after it."

A POS can only block what it can identify. Hot-case idli, puffs and samosa are loose, sold off a PLU tile, with no barcode and no batch ID at the point of sale. For the POS to block a tray, staff would have to scan a tray tag on every ₹30 sale during the 8am rush; they will not, and the software will be worked around in a week. Blocking only works for sealed vendor packs with a barcode that encodes a batch and expiry (GS1-128), which a local tiffin kitchen will not print.

My position: **the pull-by rule is a tag and a timer, not POS logic.** Every tray gets a printed or hand-written tag (load time, discard time); the counter tablet runs a tray-timer page (one tap "tray loaded" per delivery, 2–3 times a day) that turns red at pull-by and writes the load/discard times to the waste log. The POS does nothing except sell PLU 1101 "Idli 3 pc ₹30". The sealed chilled packs (curd rice, pulihora) carry the vendor's use-by; the 2pm and 8pm label passes handle them. Build cost of the tray timer: one day.

### 4. Odd-paise pricing after GST 2.0

> Decision 56: "exact on UPI, round down on cash, written on the counter."

Two problems. First, "round down" is a silent 0–99 paise gift on every cash bill; on 100 cash bills a day it is small money but it is also not what any Indian bill does. The standard, which every bought POS already prints, is a **round-off line to the nearest rupee on cash** (net zero over a day) and exact paise on UPI. Second, and more important for the data model: after GST 2.0 the same barcode sits on the shelf with two MRPs (old stock at ₹5, new stock at ₹4.45). **Barcode → MRP is no longer one-to-one.** The item master needs MRP per batch or at least current MRP plus previous MRP with an effective date, and a hard validation that the selling price never exceeds the MRP of the unit being sold. Prices must be stored as integer paise, never floats.

My position: nearest-rupee round-off on cash with the line printed, exact on UPI, MRP-per-batch in the master, price ≤ MRP as a blocking rule, and the static UPI QR is per-amount (dynamic QR from the POS) so ₹4.45 is collected as ₹4.45.

### 5. Weighing and loose items

> POS PRD open question: "How to handle loose item weighing (vegetables) — integrate a weighing scale?"
> Session decision: "nothing weighed or cooked, barcode + UPI checkout." Report 11.3: "Test loose eggs by the piece."

The two documents contradict each other and the scale is the wrong answer to both. Selling by weight needs a Legal Metrology-stamped scale, annual re-verification, and a scale-to-POS integration that is the flakiest part of any kirana POS. Bagging onions to 500 g in-store makes the store the packer under the Packaged Commodities Rules (declarations: name, net quantity, MRP, date, packer). Neither fits a 60-second checkout with two staff.

My position: **no scale at the counter, no store-packed weights.** Everything loose is sold by count or bunch at a fixed price as a PLU: eggs by the piece, lemons 4 for ₹20, curry leaves per bunch, bananas per half-dozen, onion and tomato as vendor-packed, vendor-labelled fixed-weight bags or as a "₹30 heap" by count. One stamped 30 kg scale lives in the back room for receiving vendor trays, if at all. Revisit only if loose produce passes 5% of sales at day 60.

### 6. Khata (credit ledger)

> Report 11.3: "Add a digital khata in the Agent, capped per customer, WhatsApp reminders." POS PRD: "Credit (link to customer's credit ledger — for trusted regulars)."

The ledger belongs in the POS, not the Agent. A credit sale is still a GST invoice issued at the time of sale with payment mode "credit"; the balance is an accounting fact that must reconcile with day-close cash. Every bought kirana POS has an udhaar ledger for exactly this reason. The Agent should read balances, never own them. Two things the report does not say: a khata needs a phone number and consent before any WhatsApp reminder can be sent, so credit is the first service that naturally forces customer linking; and the "dignity" rule means the balance never prints on the receipt handed over at a crowded counter, only in the WhatsApp message.

My position: udhaar ledger in the POS, cap per customer set in the POS (₹2,000 default), 30-day settle, balance visible in the Agent profile, reminder by approved utility template on day 25. Paper khata book as the fallback for anyone without a consented phone.

### 7. Offline mode

> POS PRD: "POS must work without internet ... Queue transactions locally, sync to Supabase when connection returns. Never block a sale because the internet is down."

Offline-first with bidirectional sync is the single most expensive line in the PRD: conflict resolution on stock, customers created offline on two devices with the same phone, duplicate bill numbers, partial syncs. Thirty developer-days done properly, and still the first thing to break. What "never block a sale" actually needs at Store 1 is cheaper and mostly not software: a bought POS that bills offline by design, a ₹300/month 4G hotspot behind the broadband, a UPS, a static UPI QR plus soundbox (the customer's phone has data even when the store's does not), and a paper bill book for the hour everything fails. The Consto layer (receipts, memory, pre-orders) is allowed to be down for an hour; nobody loses a sale because the memory panel is offline.

My position: offline is a hardware and vendor-selection problem for Store 1. When Consto POS is written for Store 2–3, make it local-first with an append-only transaction log that syncs one way (POS → Supabase), and never allow offline customer edits.

### 8. DPDP consent at the counter

> Agent PRD: "Consent flag per customer (opted into messaging: yes/no)." CLAUDE.md: "Explicit consent before any WhatsApp message."

A flag that staff tick on the customer's behalf during a 60-second checkout is not evidence of consent and would not survive a complaint. Two distinct purposes are being collapsed into one checkbox. Giving a number to receive a bill is the customer voluntarily providing data for a specified purpose (a transactional use); marketing and reminders are a second purpose that needs its own consent, in English or Telugu, with a record of what was agreed and when.

My position: **the first WhatsApp message is the consent mechanism.** Staff enter the number only to send the receipt; the receipt template carries a one-tap button "Yes, message me about my orders and offers" (Telugu and English). The customer's tap, with Meta's message ID and timestamp, is the consent record (`consent_source`, `consent_ts`, `consent_text_version`). Every marketing message carries "Reply STOP". Nothing beyond receipts, pre-order confirmations and khata balances goes to a number that has not tapped. This costs nothing extra to build and it is the only version a compliance consultant will sign off. Have counsel confirm the transactional/consent split; I am stating my reading, not legal advice.

### 9. Children's data

> Agent PRD: "Family: Family size, members' names, important dates (birthdays, anniversaries)." Flow 1: "son's birthday June 15".

Under the DPDP Act a child is anyone under 18, processing a child's data needs verifiable parental consent, and tracking or targeted advertising directed at children is prohibited. A `family` table with a child's name and date of birth is a child's personal data held by the store, collected without any of that. The session archive flagged this and the PRD was not changed.

My position: **no child records.** Store family events as free text on the parent's profile, entered by the parent themselves or dictated by them ("son's birthday 15 June", no name required, never a birth year), never message anyone but the account holder, never build a child profile or recommend to a child. Drop the `family` table in favour of an `events` table (customer_id, label, month, day) with no identity fields. Deleting the parent deletes the events. This keeps the "remembers the birthday" promise and removes the exposure.

### 10. AI in the billing loop

> POS PRD: "Haiku suggests frequently-bought-together items"; "Haiku surfaces the customer briefing instantly"; "Haiku flags anomalies ('Cash short by ₹340 — review')".

None of these is a model call. "Frequently bought together" is a nightly SQL query over bills; the customer briefing is a cached one-liner regenerated overnight, not generated while a queue waits; "cash short by ₹340" is subtraction. Putting a 1-second network call into a sub-60-second checkout adds latency, a failure mode, and a distraction staff will ignore at peak. The archive's "keep AI out of billing" is correct.

My position: zero model calls in the billing path. Haiku runs in nightly batches (profile one-liners, order suggestions once there is data). Sonnet runs on tap (draft a message for this customer) and nowhere else in Phase 1. At 100 customers and 30 drafts a day the Claude bill is well under ₹1,000 a month either way; the objection is latency and attention, not cost.

### 11. "Services on day one" as integration, and the phone-number claim

> Report 12.7: "AEPS, BBPS, recharge, parcel holding and pre-orders should be live on day one ... they are also the only way the Agent learns a phone number without asking."

Operationally day one, yes. As software, no. AEPS needs an aggregator onboarding with KYC (1–2 weeks), a registered biometric device, and settlement on the aggregator's schedule; BBPS and recharge run inside the aggregator's agent app. None of that is integrated into a POS in month 1; the commission is a service line typed into the POS (or a daily sheet) after the aggregator app completes the transaction. And AEPS does not give the Agent a phone number: it gives an Aadhaar-authenticated transaction that the store must never store (no Aadhaar numbers, no biometrics, ever). The store gets a phone number only if the customer gives one, which they will for a khata, a delivery, a pre-order or a parcel, not for a cash withdrawal.

My position: services live on day one on the aggregator's own app and a separate phone; integration into Consto is a Store 2 item. The phone-number engine is khata, delivery, pre-orders and membership, not AEPS.

### 12. "Customer linking on 70%+ of transactions" and "delete all customer records"

> POS PRD success metric: "Customer linking on 70%+ of transactions (vs anonymous walk-ins)." Agent PRD: "Data deletion function (removes all customer records on request)."

Seventy percent is a Japanese loyalty-card number, not a kirana reality; asking 200 people a day for a phone number at a 60-second checkout gets 20–30% in month 1. Design for bills without identity as the normal case and identity arriving through the services that need it (see point 11). Target 25% linked in month 1, 40% by day 90.

Deletion: "removes all customer records" collides with CGST Section 36, which requires invoices to be kept for 72 months from the annual return due date. The deletion function must **pseudonymise**, not delete: strip name, phone, notes, events and messages; keep the invoice with `customer_id = null`. Phone numbers stored encrypted at rest with a separate keyed hash for lookup, so deletion of the key material is also provable.

---

## Part 2. Data the store must capture from day 1

| # | Field / record | Why it matters | Captured by whom, how | Used later for |
|---|---|---|---|---|
| 1 | Bill header: bill no., timestamp to the minute, staff ID, payment mode (cash/UPI/credit/split), total, round-off, customer_id (nullable) | Every other number derives from this | POS, automatic | Hourly sales (hours decision), staffing, UPI share, Store 2 sizing |
| 2 | Bill lines: barcode or PLU, item_id, qty, unit price, MRP of the unit sold, GST rate, discount flag, is_hot_case | Item-level sell-through by hour is the Tanpin Kanri input | POS, automatic | Ordering, hero product, 4-week zero delist, KVI parity, waste % |
| 3 | Item master: barcode/PLU, name_en, name_te, dept/category (category master code), brand slot (best/cheaper/premium), MRP current + previous + effective date, sell price, last cost, GST rate, HSN, primary vendor, is_KVI, is_own_brand, hypothesis line, listed_date, delisted_date | The catalogue Sateesh owns; the 1–3 brands and "one line of why" rules live here | Sateesh's sheet, imported to POS and Supabase | Delisting, brand count per need, own-brand floor, Store 2 catalogue |
| 4 | Hot-case tray batch: item, vendor, qty loaded, load time, planned discard time, hot-case temperature at load | The pull-by clock and waste maths need a batch, not a day | Tray-timer page on counter tablet (one tap) + probe reading | Waste by batch, load-size tuning (8:00/12:30/18:30), vendor spec |
| 5 | Waste log: item, qty, reason (pull-by / use-by / damaged / discounted-unsold / donated / returned-to-vendor), time, value at cost | Report: "zero waste means the case was empty"; this is the ordering input | Waste form on the tablet; paper in week 1, typed at close | Waste budget (5% then 3%), order quantity, vendor sale-or-return claims |
| 6 | Vendor delivery sheet: date, vendor, item, qty delivered, unit cost, invoice no., batch/use-by printed on pack, temperature at delivery, late (y/n) | Margin, FSSAI traceability, the vendor review | Manual sheet at receiving, photo of the invoice | Gross margin by category, two-vendor comparison, late-truck evidence |
| 7 | Vendor returns: date, vendor, item, qty returned, credit % agreed, credit received (₹) | Sale-or-return only works if written down | Same sheet, filled at pick-up | Vendor contracts, true waste cost |
| 8 | Temperature log: hot case and chiller, twice a shift, who | FSSAI Schedule 4 evidence; vendor dispute defence | Paper or a 3-field form | Licence inspection, complaint defence |
| 9 | Customer: phone (encrypted + lookup hash), name, language (te/en), diet (veg/egg/non-veg), preference tags (organic-natural, healthy, diabetic, baby, pet), address landmark for delivery, notes | The memory | Agent panel, entered by staff or from WhatsApp replies | Messages, organic basket, delivery route, Store 2 personas |
| 10 | Consent events: phone, event (receipt-only / opt-in / opt-out), channel, Meta message ID, timestamp, notice version | DPDP audit trail | WhatsApp button tap, webhook | Who may be messaged; compliance proof |
| 11 | Events (not family members): customer_id, label, month, day | Birthday and festival reminders without child data | Agent panel, parent dictates | Pre-order prompts for cakes and kits |
| 12 | Services: type (AEPS/BBPS/recharge/xerox/parcel/print), amount, commission, time, customer_id if given | Services share of gross profit; the 7-Eleven India lesson | Aggregator app export + a service line in POS | Services mix KPI, whether the counter pays |
| 13 | Pre-orders: customer, item (cake/festival kit/organic basket/rice), qty, due date-time, advance paid, vendor, status | The Agent's first real job | Form in the Agent panel; WhatsApp message as source | Cake and kit demand, vendor lead times, Store 2 festival calendar |
| 14 | Delivery orders: order id, customer, slot (am/pm), items, fee, dispatched time, delivered time, who | Whether 2 km batched delivery pays | Sheet in month 1, form at day 30 | Route, slot timing, delivery P&L |
| 15 | Khata ledger: customer, debit/credit, amount, bill no., balance, reminder sent date | Credit is the kirana expectation | POS udhaar module | Bad-debt rate, cap tuning, reminder timing |
| 16 | Membership: customer, start, end, fee, cashback accrued, redeemed | SuperK's 35–40% uptake is the benchmark | POS item "Membership ₹300" + customer flag | Uptake, spend lift, Store 2 pricing |
| 17 | Message log: customer, template, sent, delivered, read, replied, visit within 7 days (derived) | The 40% reply and 50% re-engagement metrics | Cloud API webhooks | Agent economics, what to send, dormancy threshold |
| 18 | Discount window: item, qty tagged at 8pm, qty sold at discount, qty left | Tunes the window and the markdown % | POS discount flag + waste log | Window timing, points vs price |
| 19 | Weekly stock count: item, counted qty, system qty | Shrinkage and the delist rule need truth | Staff on the tablet, Sunday 10pm | Shrinkage %, catalogue pruning |
| 20 | Outage log: power/internet start, end | Decides generator and offline investment | Staff note | Day-60 backup-power decision |
| 21 | Deletion requests: phone hash, requested, completed | DPDP 30-day proof | Agent panel button | Compliance |
| 22 | Footfall proxy: bills per hour (derived from 1), plus seated-not-buying count at 2pm and 8pm (manual tick) | Hours decision (9–11pm > 8%?), the rest-corner question | Derived + a tick sheet | 24h decision at day 90, seating |

Three data disciplines that cost nothing and decide whether any of this is usable: **every hot item is its own PLU, never "Misc ₹30"**; **every bill has a timestamp to the minute**; **every reason code is from a fixed list, not free text**.

---

## Part 3. Build / buy / skip for Store 1

Costs are per month unless marked one-off; effort is developer-days for the version named in the recommendation. Assumption: one developer (Sateesh with Claude Code), Vercel and Supabase free tiers for the first 90 days (Supabase Pro at roughly ₹2,100/month once row counts or auth needs force it).

| Piece | Store 1 call | Cost (₹/month) | Effort (days) | Recommendation and reason |
|---|---|---|---|---|
| POS (billing, GST, MRP, print, day-close) | **Buy** | 500–2,000 | 3–5 (item master load, printer, templates) | SaaS kirana POS chosen on: itemised export with timestamp + phone, CSV or API, MRP-per-batch, udhaar, offline billing, FSSAI/GSTIN on bill. Build Consto POS at Store 2–3 (40–60 days). |
| POS → Supabase pipeline | **Build** | 0 | 3–5 | Nightly CSV import (or API poll) into `pos_bills_raw`, then views. This is the real "data foundation", not the POS. Day 1–14. |
| Inventory (stock, reorder) | **Buy** (POS module) + sheet | 0 | 2 | POS stock report + Google Sheet for vendor orders. Consto Inventory product: skip until Store 2. |
| Hot-food batch tags / tray timer | **Build (tiny)** + stationery | 0 (label printer ₹4–8k one-off, optional) | 1 | Tray-timer page on the counter tablet; printed or hand-written tray tags; 3 label passes a day on a checklist. No POS blocking. |
| Waste log | **Build (tiny)** | 0 | 1–2 | One form: item, qty, reason code, time. Paper for week 1, typed at close. |
| Vendor delivery and sale-or-return | **Skip software** (sheet) | 0 | 0, then 2 at day 60 | Google Sheet with photo of invoice; move to a Supabase form at day 60 when vendors are stable. |
| WhatsApp receipts | **Buy** (POS's own bill share) → **Build** at day 30 | 0, then per-message | 2 | Month 1: POS's WhatsApp bill share from the store phone. Day 30: Cloud API utility template from the Consto number with the opt-in button. |
| WhatsApp Cloud API setup + inbox | **Build** | Per-message (India utility a fraction of a rupee, marketing under ₹1; verify Meta's current rate card) | 8–12 | Meta verification started 6 weeks before opening. Webhook → `messages` table → simple inbox in the Agent panel. If verification slips, WhatsApp Business app on a store phone. |
| WhatsApp ordering (inbound) | **Build-lite** | 0 extra | 3 | Inbound messages land in the inbox; staff create a pre-order or delivery row by hand. No NLP parsing in Phase 1. |
| Pre-orders (cakes, festival kits, organic basket, rice) | **Build (small)** | 0 | 2–3 | Form + list in the Agent panel; status, advance, vendor, due time. Google Form fallback for week 1. |
| Credit ledger (khata) | **Buy** (POS udhaar) | 0 | 1 (reminder template) | Balances read into Agent profile from the export; reminder by utility template on day 25 of a balance. |
| Loyalty / membership | **Skip points; build membership-lite** at day 60 | 0 | 3 | Membership = POS item "₹300/6 months" + customer flag + nightly cashback calc into a wallet balance. No tiers, no points engine. |
| AEPS / BBPS / recharge | **Buy** (aggregator app) | 0 (biometric device ₹2,500–4,000 one-off; aggregator KYC) | 1 + waiting | Runs on the aggregator's app on a separate phone; commission typed as a service line. No integration until Store 2. Never store Aadhaar. |
| UPI collection | **Buy** | 0 (soundbox may carry a small rental) | 0.5 | Static QR + soundbox for resilience; dynamic per-amount QR from the POS for odd paise. Razorpay only when online pre-order payments are needed (day 60+). |
| Delivery dispatch | **Skip software** (sheet + WhatsApp group) → **Build-lite** at day 90 | 0 | 3 | Two slots a day, a printed list from the pre-order/delivery table. Route optimisation never. |
| Agent panel (customers, notes, consent, events, profile, composer) | **Build** | 0 (Claude API well under ₹1,000) | 15–20 | The thing nobody sells. Ships day 1 as customers + notes + consent + pre-orders; composer with Sonnet drafts at day 30. |
| Dormancy list | **Build (SQL)** | 0 | 1 | A view: customers with ≥3 visits whose gap exceeds 2× their median gap. Live only when the data exists (week 5+). |
| Dashboards | **Build (one page)** or Looker Studio on the export | 0 | 1–3 | One page: daily sales split hot/chilled/packaged/services, bills per hour, waste ₹, linked %, members, deliveries. Day 30. |
| Telugu in the UI | **Build-lite** | 0 | 2 | `name_te` on items and a Telugu/English toggle in the composer. Staff UI stays English. Thermal receipts stay English/transliterated (ESC/POS printers do not render Telugu text without image-mode printing). |
| AI ordering / Predict | **Skip** until day 90 | 0 | 0 | A weekday-by-hour spreadsheet beats a model until there are 8 weeks of data. Note 04 says the same. |
| Offline sync engine | **Skip** (buy via POS; hotspot + UPS) | 300 (4G hotspot) | 0 | Store 2–3 item when Consto POS is written. |
| Consto Notify / Loyalty / Ops / HQ products | **Skip** | 0 | 0 | Phase 2–4 as CLAUDE.md says; nothing in the report changes that. |

Total build for opening day: roughly 25–30 developer-days (pipeline, Agent panel v0, tray timer, waste form, pre-orders). Total recurring software cost in month 1: under ₹5,000 including the POS, the hotspot and WhatsApp messages.

---

## Part 4. Decision questions from the developer lens

Format: question, why, options, my pick with the assumption behind it. No "test it" answers.

### POS and billing

1. **Buy a SaaS POS or build Consto POS for Store 1?** Why: 40–60 days of parity work vs ₹2,000 a month. Options: [buy SaaS / build thin in-house / build full PRD]. My pick: buy (assumption: the chosen POS exports itemised bills with timestamp and phone daily; if none does, build the thin version in 15 days with no AEPS, credit or AI).
2. **Which five features are non-negotiable in the bought POS?** Why: they decide whether the data pipeline exists. Options: [export + MRP-per-batch + udhaar + offline + FSSAI/GSTIN on bill / fewer]. My pick: all five, plus dynamic UPI QR (assumption: at least two products in the ₹500–2,000 band meet all six).
3. **Windows mini-PC, Android tablet, or iPad at the counter?** Why: thermal printer and cash-drawer kick need ESC/POS access; a PWA on iPad cannot kick a drawer. Options: [Windows PC / Android tablet / iPad]. My pick: Windows mini-PC with the bought POS, Android tablet for the Agent panel (assumption: ₹25–40k all-in; the archive's "Electron POS" idea only matters if we build).
4. **Barcode or PLU for hot-case and loose items?** Why: the hero decision needs per-item counts per hour. Options: [PLU tiles per item / one "hot food" tile with amount / barcode stickers on trays]. My pick: PLU tiles, one per item and size, never a miscellaneous tile (assumption: ≤25 hot/loose PLUs fit on one screen).
5. **Who may edit prices and MRPs?** Why: a price above MRP is a ₹25,000 fine. Options: [owner only / shift lead / any staff]. My pick: owner only, with the POS set to block any sell price above the batch MRP (assumption: the bought POS has role-based permissions).
6. **How are two MRPs for one barcode handled after GST 2.0?** Why: old stock at ₹5 and new at ₹4.45 coexist for weeks. Options: [MRP per batch in POS / sticker the old stock and sell at the new MRP / sell both at the lower]. My pick: MRP per batch (assumption: the POS supports it; if not, sell old stock at the lower new MRP and take the paise loss).
7. **Cash rounding rule?** Why: customers notice 55 paise and the bill must show it. Options: [nearest rupee with a round-off line / always round down / exact with coins]. My pick: nearest rupee with the line printed; exact on UPI (assumption: net round-off over a day is within ±₹20).
8. **Returns and refunds on day 1?** Why: the PRD defers them to v1.1, but a bought POS has them. Options: [POS returns module from day 1 / no returns for 30 days]. My pick: day 1, refund to the same mode, manager PIN (assumption: under 1% of bills).
9. **Does the FSSAI number and veg/non-veg board live in software?** Why: FSSAI number on every bill since 2021; logo and allergen symbols on the board for loose food. Options: [POS header + printed board / only board]. My pick: both; the board is laminated paper, not a screen (assumption: hot-case menu changes weekly at most).

### Hot case, waste, vendors

10. **How is a hot-case batch logged?** Why: pull-by and waste need a batch record the counter will actually create. Options: [tray-timer tap on the tablet / paper tag only / scan a tray barcode in POS]. My pick: tray-timer tap plus a hand-written tag (assumption: 2–3 loads a day, 10 seconds each).
11. **Does the POS block sales after pull-by?** Why: report decision 7 says yes. Options: [block in POS / tag + timer + human / no rule]. My pick: tag + timer + human; no POS logic (assumption: staff will not scan a tray tag per sale).
12. **How is waste logged?** Why: it is the ordering input and the vendor-return evidence. Options: [form per item per reason at discard / tally at close / weekly]. My pick: form at discard with a fixed reason list; paper week 1 (assumption: ≤15 waste lines a day).
13. **How are vendor sale-or-return quantities recorded?** Why: credits are not received unless claimed with numbers. Options: [sheet at pick-up / POS purchase-return / nothing]. My pick: sheet at pick-up with the credit % column, photo of the signed note (assumption: 3–4 vendors, one pick-up each per day).
14. **Where does the "one line of why" for a new item live?** Why: a forced POS field gets filled with "x". Options: [POS mandatory field / catalogue sheet owned by Sateesh / nowhere]. My pick: catalogue sheet, reviewed every Sunday; the POS never blocks item creation (assumption: ~20 items reviewed a week).
15. **How is sell-through by hour produced?** Why: it is the Tanpin Kanri verify step. Options: [POS report / Supabase view on the import / spreadsheet]. My pick: Supabase view, weekday × hour × item (assumption: bill timestamps are to the minute in the export).
16. **When does order-quantity suggestion become software?** Why: Japan's AI ordering started on ambient goods after years of data. Options: [day 1 / day 90 spreadsheet / month 6 model]. My pick: day 90 spreadsheet by weekday and weather note; a Haiku batch job only after 8 clean weeks (assumption: 1,500 items × 90 days is enough for a median-by-weekday rule).
17. **Temperature log on paper or in software?** Why: the inspector wants a record; a form costs a day. Options: [paper / 3-field form / probe with Bluetooth]. My pick: 3-field form on the tablet from day 1 (assumption: 4 readings a day).

### WhatsApp and the Agent

18. **Cloud API or WhatsApp Business app on opening day?** Why: Meta verification takes 2–6 weeks and can slip. Options: [Cloud API only / Business app only / Cloud API with the app as fallback]. My pick: Cloud API, verification started 6 weeks before opening, Business app as the fallback (assumption: a dedicated SIM bought in the company name).
19. **Which number?** Why: the Cloud API number cannot also run the WhatsApp app, and the owner's personal number must never be the store's. Options: [new company SIM / owner's number]. My pick: new company SIM, displayed on the board and the receipt (assumption: number portability to the API later is not needed).
20. **What is captured at first contact to count as consent?** Why: a staff-ticked flag is not consent. Options: [staff checkbox / customer's button tap on the receipt / paper form]. My pick: button tap on the receipt template, recorded with Meta message ID and notice version (assumption: Meta approves a utility template with a quick-reply button, which is standard).
21. **What does the Agent send in month 1?** Why: there is no memory yet. Options: [receipts only / receipts + pre-order + khata + 8pm window / full proactive rounds]. My pick: the five templates: receipt, opt-in, pre-order confirmation, 8pm window, khata balance (assumption: ≤60 outbound a day, mostly utility).
22. **When does Sonnet start drafting personal messages?** Why: drafts need facts. Options: [day 1 / when a customer has ≥3 linked visits / day 60]. My pick: ≥3 linked visits per customer, which lands around week 5 for regulars (assumption: regulars visit every 2–3 days).
23. **Inbound messages: who reads them and where?** Why: the PRD's open question 1. Options: [store phone, by hand / inbox in the Agent panel / auto-reply bot]. My pick: inbox in the Agent panel from day 30, store phone until then, no bot (assumption: ≤40 inbound a day).
24. **Dormancy threshold: fixed days or per-customer?** Why: PRD open question 4. Options: [7 days / 10 days / 2× the customer's median gap]. My pick: 2× median gap with a 10-day floor, computed in SQL (assumption: needs ≥3 visits, so nothing fires before week 5).
25. **Which model does what in Phase 1?** Why: CLAUDE.md fixes Haiku for speed and Sonnet for thinking; the PRDs put Haiku in the billing path. Options: [as PRDs / Sonnet on tap for drafts, Haiku nightly batches, nothing in billing]. My pick: Sonnet on tap for drafts and dormancy text; Haiku nightly for profile one-liners and, later, order suggestions; zero calls at checkout (assumption: the model rule is about task type, not about putting a model in every screen).
26. **Telugu: auto-translate or toggle?** Why: PRD open question 3. Options: [auto / toggle per customer language field / always mixed]. My pick: toggle driven by the customer's language field, default Telugu-mixed as in the brand examples; agent edits before send (assumption: 70% of customers prefer Telugu-mixed).
27. **Voice notes in month 1?** Why: Lakshmi aunty's story. Options: [staff records on the store phone / skip / TTS]. My pick: staff records a 10-second voice note by hand for the ≤10 customers who need it; no software (assumption: under 10% of customers).
28. **How is "organic / natural / healthy" preference tagged?** Why: the organic basket pre-order and note 08's 15–20% active buyers. Options: [free-text notes / fixed tag list / inferred from purchases]. My pick: fixed tags (organic-natural, healthy, diabetic, baby, pet, pooja-regular) set by staff from what the customer says, plus a nightly rule that adds the tag after 3 purchases from the tagged categories (assumption: ≤8 tags, each a boolean).
29. **Do customers see what is remembered?** Why: the archive suggested it; DPDP gives a right to access and correction. Options: [send "what we know" on request / self-service page / never]. My pick: on request, a WhatsApp message listing tags, events and notes, with "reply to correct" (assumption: ≤5 requests a month).

### Privacy and data handling

30. **Phone number storage?** Why: CLAUDE.md says encrypt. Options: [plain / encrypted at rest + keyed hash for lookup / tokenised by a provider]. My pick: encrypted column plus HMAC lookup hash in Supabase, RLS on every customer table (assumption: one Supabase project, service-role key never in the browser).
31. **What does "delete within 30 days" delete?** Why: invoices must be kept 72 months under GST. Options: [hard delete everything / pseudonymise and keep invoices / keep everything, mark deleted]. My pick: pseudonymise: strip name, phone, notes, events, messages; keep bill rows with customer_id null; log the request (assumption: counsel confirms invoice retention outranks erasure for the invoice itself).
32. **Retention of messages and notes for active customers?** Why: a notes field grows into a dossier. Options: [forever / 24 months rolling / 12 months]. My pick: 24 months rolling for messages, notes kept while active, purged 12 months after last visit (assumption: nobody needs a 2023 message in 2026).
33. **Children's data?** Why: DPDP Section 9. Options: [family table with names and DOB / events only, no names, no year / nothing]. My pick: events only (assumption: "son's birthday 15 June" is enough to prompt a cake pre-order).
34. **Who can see the khata balance and where?** Why: dignity rule and the counter queue. Options: [printed on receipt / WhatsApp only / Agent screen only]. My pick: Agent screen and WhatsApp reminder; never on the paper receipt (assumption: the bought POS lets the ledger line be hidden from the bill print).
35. **Aadhaar and AEPS data?** Why: the aggregator handles authentication; the store must hold nothing. Options: [store nothing / store last 4 / store the transaction ID only]. My pick: store the aggregator transaction ID and amount; no Aadhaar fragment, no biometric, ever (assumption: the aggregator's report is the audit trail).
36. **Staff access and audit?** Why: five staff, one login each, price edits traceable. Options: [shared login / per-staff PIN / per-staff accounts with roles]. My pick: per-staff PIN in the POS and per-staff login in the Agent, every edit stamped (assumption: both products support it).

### Hardware, offline, printing

37. **Offline strategy?** Why: "never block a sale" is cheaper as hardware than as sync code. Options: [build offline-first sync / bought POS offline + hotspot + UPS + paper / nothing]. My pick: bought POS offline + 4G hotspot + 2 kVA UPS + paper bill book (assumption: outages are minutes, as note 09 says).
38. **Printer?** Why: PWA printing to thermal is the usual day-1 failure. Options: [80 mm USB ESC/POS on the Windows POS / Bluetooth to a tablet / no paper]. My pick: 80 mm USB thermal on the POS PC, cash drawer kicked by the printer; WhatsApp receipt as the default and paper on request (assumption: 40–60% still want paper in month 1).
39. **Hardware list for opening day?** Why: note 09 prices it. Options: [minimal / standard]. My pick: POS mini-PC + 2D scanner + 80 mm printer + cash drawer (₹30–45k), Android tablet for the Agent (₹15–20k), store phone with the Consto SIM (₹10–12k), separate phone for the aggregator app, biometric device (₹3–4k), UPI soundbox, 4G hotspot, 2 kVA UPS (₹25k), 6-camera CCTV (₹25–35k), label printer optional (₹5–8k), probe thermometer (₹1–2k) (assumption: total under ₹1.5 lakh excluding CCTV and UPS which are in the capex note already).
40. **Scale at the counter?** Why: Legal Metrology stamping and a flaky integration. Options: [stamped scale integrated / stamped scale in the back for receiving / none]. My pick: one in the back for receiving only; nothing sold by weight (assumption: produce is by count or vendor-packed).
41. **Internet?** Why: UPI confirmations and WhatsApp need it even if billing does not. Options: [broadband only / broadband + 4G hotspot / two broadbands]. My pick: broadband + 4G hotspot with auto-failover on the router (assumption: ₹1,000 + ₹300 a month).

### Pricing, membership, delivery

42. **Membership mechanics?** Why: dual price lists break on MRP goods and in bought POS. Options: [member price list / flat cashback credited nightly / points]. My pick: ₹300 item in the POS, customer flag, nightly cashback into a wallet balance redeemable on the next bill (assumption: the POS supports a customer wallet or an "advance" ledger; else a manual coupon for 60 days).
43. **Combos and the price ladder in software?** Why: chai + biscuit ₹15 must be one tap. Options: [combo PLUs / discount rule / manual]. My pick: combo PLUs (assumption: ≤6 combos).
44. **8pm discount: how applied?** Why: the window needs a count of what was tagged and sold. Options: [discount PLU per item / % discount button / re-sticker]. My pick: a "-35%" button scoped to hot and chilled PLUs after 20:00, logged as a discount flag on the line (assumption: the POS supports time-boxed discounts; if not, separate "evening" PLUs).
45. **Delivery fee and slots in software?** Why: the delivery P&L needs a fee line. Options: [free / ₹10–20 flat PLU / free above ₹300]. My pick: ₹15 PLU, waived above ₹300, two slots (assumption: 10–20 deliveries a day by day 60).
46. **Pre-order advance payment?** Why: cakes and festival kits need commitment. Options: [none / 50% at the counter / UPI link via Razorpay]. My pick: 50% at the counter or by UPI to the static QR, logged on the pre-order row; Razorpay links only from day 60 (assumption: most pre-orders are placed in person or by WhatsApp from regulars).
47. **Dashboards: who looks, how often?** Why: one KPI on the wall. Options: [daily page / weekly email / monthly]. My pick: one page refreshed nightly; the wall number is average daily sales split hot/chilled/packaged/services (assumption: Sateesh reads it every morning).
48. **Supabase project structure?** Why: one shared project is the rule; a bought POS changes the shape. Options: [POS-centric schema per PRD / raw landing tables + views + Consto tables]. My pick: `pos_bills_raw`, `pos_items_raw` landing tables from the import, clean views, and the Consto tables (customers, consent_events, events, preorders, waste, trays, messages) owned by the Agent (assumption: the POS vendor's export format is stable for 6 months).
49. **What proves the "memory" thesis by day 90?** Why: the founder cannot pilot. Options: [reply rate / repeat rate of linked vs unlinked / pre-orders per 100 customers]. My pick: repeat rate (≥4 visits/month) of consented customers vs the unlinked baseline, plus pre-orders per 100 consented customers (assumption: ≥300 consented customers by day 90).

---

## Part 5. Minimum tech for opening day, then days 30, 60, 90

**Opening day (10 lines)**

1. Bought SaaS POS on a Windows mini-PC: 2D scanner, 80 mm thermal printer, cash drawer; 1,500-item master loaded from Sateesh's catalogue sheet with MRP, GST, dept code, PLU tiles for every hot and loose item.
2. Static UPI QR + soundbox at the counter; dynamic QR from the POS for odd-paise amounts.
3. Aggregator app for AEPS/BBPS/recharge on a separate phone with a registered biometric device; commission typed as a POS service line.
4. Store WhatsApp number (company SIM): Cloud API with five approved templates if Meta verification is through; otherwise WhatsApp Business app on the store phone doing the same five jobs by hand.
5. Agent panel v0 on an Android tablet (Vercel + Supabase): customers, consent record, tags, events, notes, pre-order form, waste form, tray timer, temperature form.
6. Nightly POS export → Supabase import, with one view: sales by item by hour.
7. Udhaar ledger in the POS, cap ₹2,000, paper khata book as fallback.
8. Broadband + 4G hotspot failover, 2 kVA UPS on POS/router/CCTV, paper bill book in the drawer.
9. Google Sheets: vendor delivery and returns, delivery orders, outage log.
10. Laminated boards: FSSAI number, veg/non-veg and allergen symbols for the hot case, the consent notice in Telugu and English, the cash rounding rule.

**Day 30 adds:** Cloud API receipts from the Consto number with the opt-in button (if not already live); inbox in the Agent panel; message composer with Sonnet drafts enabled only for customers with ≥3 linked visits; one-page dashboard (daily sales split, bills per hour, waste ₹, linked %); nightly Haiku one-liners for profiles; 8pm window broadcast to consented customers.

**Day 60 adds:** membership (₹300 item + flag + nightly cashback); vendor delivery and returns moved from sheet to a Supabase form; pre-order advance by Razorpay link; dormancy view live (2× median gap, 10-day floor); "what we remember" message on request; deletion function (pseudonymise) tested end to end with one real request.

**Day 90 adds:** weekday × hour order-quantity spreadsheet from 12 weeks of sell-through, with a Haiku batch job only if the spreadsheet proves stable; delivery dispatch list from the orders table; decision data for hours (9–11pm share), rest corner, Store 2 catalogue (items with zero sales in 4 weeks delisted); first draft of the Consto POS requirements written from 90 days of counter observation, for Store 2–3, not Store 1.
