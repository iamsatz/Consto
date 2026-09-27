# Consto POS — Product Requirements Document

**Product**: Consto POS (Billing & Checkout)
**Phase**: 1 (Month 1-2)
**Status**: Draft v1.0
**Priority**: Foundation — every other product depends on this data
**Parent Doc**: `00-master-prd.md`

---

## 1. What This Product Is

Consto POS is the billing and checkout system used by staff at the counter. It handles every transaction — groceries, services, payments — and is the **data foundation** for the entire Consto OS. Every sale here feeds inventory, customer memory, predictions, and the owner dashboard.

It is not just a cash register. It's the moment where the customer relationship and the transaction meet — so it's designed to surface customer context at checkout, not just process payments.

---

## 2. The Problem This Product Solves

### Core Problem

Traditional kirana billing is either a paper book or a generic POS that knows nothing about the customer. This means:
- No clean transaction data (so inventory, forecasting, and CRM are impossible)
- No customer context at checkout (missed upsell and care moments)
- Slow, error-prone GST and service billing
- No digital receipts or purchase history

Consto POS fixes the foundation. Without clean transaction data flowing from here, none of the other 7 products can work.

### Who Has This Problem

**Primary user: Counter Staff**
- Bills 150-250 customers per day
- Needs speed above all — long queues kill the experience
- Handles mixed transactions (groceries + AEPS + bill payment in one go)
- Is not technical — needs a dead-simple interface

**Beneficiary: The Owner + All Other Products**
- Owner gets clean revenue data
- Inventory gets real-time stock deduction
- Consto Agent gets purchase history
- Consto Predict gets sales patterns

---

## 3. User Stories

### Staff Stories (Primary User)

- As counter staff, I want to scan a barcode and have the item appear instantly so billing is fast
- As counter staff, I want to search items by name when there's no barcode so I'm not stuck
- As counter staff, I want to add a service (AEPS, bill payment) to the same bill so the customer pays once
- As counter staff, I want to accept UPI, cash, or credit easily so any payment method works
- As counter staff, I want to link the bill to a customer so their history updates
- As counter staff, I want to send the receipt via WhatsApp so I don't waste paper
- As counter staff, I want to close the register at end of day with one tap so reconciliation is simple

### Owner Stories (Beneficiary)

- As the owner, I want every sale recorded cleanly so I can trust my numbers
- As the owner, I want to see today's revenue without asking anyone
- As the owner, I want the POS to flag pricing mistakes so I don't lose money

---

## 4. Core Features (MVP)

### Feature 1 — Item Entry

**What**: Adding products to a bill.

**Requirements**:
- Barcode scan (camera or USB scanner) → item appears instantly
- Manual search by name (for items without barcodes — loose vegetables, etc.)
- Quantity adjustment (tap +/- or type)
- Quick-add tiles for top 20 fast-moving items (milk, bread, eggs)
- Running total always visible
- Remove/edit line items easily

**AI involvement**: Haiku suggests frequently-bought-together items ("Customer buying milk + bread — usually buys eggs too?").

### Feature 2 — Service Billing

**What**: Adding non-product services to the same transaction.

**Requirements**:
- AEPS cash withdrawal (enter amount, process, add commission line)
- Bill payment (electricity, water, DTH, mobile)
- Mobile/DTH recharge
- Xerox/print charges
- Each service adds a line item with its commission tracked separately

**AI involvement**: None (these are fixed flows).

### Feature 3 — Payment Processing

**What**: Taking payment in any method.

**Requirements**:
- UPI (generate QR or enter UPI ref)
- Cash (enter amount tendered, calculate change)
- Credit (link to customer's credit ledger — for trusted regulars)
- Split payment (part UPI, part cash)
- Payment confirmation before bill closes

**AI involvement**: None.

### Feature 4 — Customer Linking

**What**: Connecting the bill to a known customer.

**Requirements**:
- Search customer by phone/name (or "walk-in" for anonymous)
- Once linked: their purchase history updates, loyalty points accrue
- Show a one-line customer briefing at checkout ("Priya — Gold tier, usually buys organic")
- Quick-add new customer (name + phone) in under 10 seconds

**AI involvement**: Haiku surfaces the customer briefing instantly.

### Feature 5 — Digital Receipt

**What**: Sending the receipt to the customer.

**Requirements**:
- WhatsApp receipt (default — links to their number)
- Print receipt (thermal printer — for those who want paper)
- Receipt includes itemised list, total, payment method, loyalty points earned
- Store branding on receipt

**AI involvement**: None.

### Feature 6 — Daily Register Close

**What**: End-of-day reconciliation.

**Requirements**:
- One-tap "Close Register"
- Shows: total sales, cash sales, UPI sales, credit extended, service commissions
- Expected cash vs counted cash (flag discrepancies)
- Auto-saves daily summary to owner dashboard
- Generates next-day starting state

**AI involvement**: Haiku flags anomalies ("Cash short by ₹340 — review").

---

## 5. User Flows

### Flow 1 — Standard Grocery Checkout

1. Customer brings items to counter
2. Staff scans each barcode (or searches loose items)
3. POS shows running total; Haiku suggests "add eggs?" based on basket
4. Staff searches customer phone → links bill → sees "Priya, Gold tier"
5. Customer pays via UPI (scans QR)
6. Receipt sent to Priya's WhatsApp automatically
7. Inventory auto-deducts, Priya's history updates, points added

### Flow 2 — Mixed Transaction (Groceries + Service)

1. Raju wants ₹2000 cash (AEPS) + an energy drink + phone charger
2. Staff adds energy drink + charger (scan)
3. Staff adds AEPS withdrawal ₹2000 → processes biometric → adds commission line
4. Total = products + AEPS commission
5. Raju pays, gets cash, receipt to WhatsApp
6. Done in under 2 minutes

### Flow 3 — End of Day

1. Staff taps "Close Register"
2. POS shows: ₹18,400 total | ₹6,200 cash | ₹11,100 UPI | ₹1,100 credit
3. Staff counts physical cash → enters ₹6,150
4. POS flags "₹50 short" → staff notes reason
5. Daily summary saves to Consto HQ automatically

---

## 6. Design Requirements

### Visual
- Large, high-contrast buttons (speed + accuracy under pressure)
- Running total always visible, large font
- Consto palette but functional — clarity over decoration
- Minimal cognitive load during peak hours

### Interaction
- Barcode scan → item appears in under 0.5 seconds
- Most-used actions (scan, pay, close) are biggest and easiest to reach
- Forgiving — easy to undo a mis-scan
- Works one-handed where possible (other hand handling products)

### Speed Targets
- Standard grocery checkout: under 60 seconds
- AEPS withdrawal: under 2 minutes
- Customer linking: under 5 seconds

---

## 7. Technical Requirements

### Stack
- **Frontend**: React + Tailwind (PWA, works offline)
- **Database**: Supabase (transactions, items, inventory tables — shared with other products)
- **AI**: Claude Haiku (basket suggestions, anomaly flagging — speed-critical)
- **Payments**: UPI integration (Razorpay or direct), AEPS device SDK
- **Printing**: Thermal printer API
- **Messaging**: WhatsApp Cloud API (receipts)
- **Hosting**: Vercel
- **Build tool**: Claude Code

### Key Data Model (Supabase)
- `items` (id, name, barcode, price, gst_rate, category, stock_qty)
- `transactions` (id, customer_id, date, total, payment_method, status)
- `transaction_items` (transaction_id, item_id, qty, price)
- `services` (transaction_id, service_type, amount, commission)
- `register_close` (date, total_sales, cash, upi, credit, discrepancy)

### Critical: Offline Capability
- POS must work without internet (store connections are unreliable)
- Queue transactions locally, sync to Supabase when connection returns
- Never block a sale because the internet is down

---

## 8. Success Metrics (This Product)

- Standard checkout completed in under 60 seconds (90% of transactions)
- Zero lost sales due to system downtime (offline mode works)
- 100% of transactions produce clean data for other products
- 80%+ of customers opt for WhatsApp receipt (paper reduction)
- Daily register reconciliation under 5 minutes
- Customer linking on 70%+ of transactions (vs anonymous walk-ins)

---

## 9. MVP Scope

### In Scope (Phase 1 MVP)
- Barcode scan + manual item search
- Quick-add tiles for top items
- Basic service billing (AEPS, bill pay, recharge)
- UPI + cash + credit payment
- Customer linking + briefing
- WhatsApp + print receipt
- Daily register close

### Out of Scope (Later)
- Advanced promotions/discounts engine
- Multi-store price sync
- Detailed analytics (→ Consto HQ)
- Supplier purchase orders (→ Consto Inventory)
- Returns/refunds workflow (add in v1.1)

---

## 10. Build Sequence for Claude Code

1. Set up Supabase tables (items, transactions, transaction_items, services)
2. Build item entry screen (barcode scan + search + quick-add tiles)
3. Add running total + line item edit/remove
4. Build payment screen (UPI + cash + change calc + credit)
5. Add customer linking (search + briefing via Haiku)
6. Integrate WhatsApp receipt + thermal print
7. Build service billing flows (AEPS, bills, recharge)
8. Build daily register close + reconciliation
9. Add offline mode (local queue + sync)
10. Add Haiku basket suggestions + anomaly flagging
11. Test with real barcodes + dummy transactions
12. Deploy to Vercel

---

## 11. Dependencies

- **Feeds data to**: Consto Agent (purchase history), Consto Inventory (stock deduction), Consto Predict (sales patterns), Consto HQ (revenue), Consto Loyalty (points)
- **Depends on**: Supabase setup, WhatsApp Cloud API, UPI/AEPS provider accounts

---

## 12. Open Questions (This Product)

1. Which AEPS provider has the best small-merchant terms in Telangana?
2. UPI: build our own QR or use a provider like Razorpay/PhonePe for business?
3. How to handle loose item weighing (vegetables) — integrate a weighing scale?
4. Returns/refunds — MVP or v1.1?

---

*Consto POS is the foundation. Build it first, build it solid. Every other product drinks from this well.*
