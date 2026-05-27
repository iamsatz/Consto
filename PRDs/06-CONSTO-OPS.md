# 06 · Consto Ops

> Daily operations management. Opening / closing checklists, task assignments, photo proofs, manager review, missed-task escalation. Tablet-friendly PWA.

| | |
|---|---|
| **Phase** | 3.1 |
| **Priority** | Medium (Phase 3 polish, but huge daily value once live) |
| **Platform** | Web PWA (used on tablets and phones on the shop floor) |
| **Primary user** | Store staff (executing tasks), store manager (assigning + reviewing) |
| **Build status** | Not started |
| **Dependencies** | Consto POS + Consto Inventory live. Need staff records in `staff` table. |
| **Depends on it** | HQ (reads ops health into owner dashboard), Predict (uses ops incidents to refine forecasts) |

## What it is

The system that runs the store's daily rhythm: morning open checklist, fresh-produce intake, mid-day reset, evening close. Plus ad-hoc tasks. Plus incident logging. Plus a manager review queue so nothing slips.

Without Ops, the store relies on tribal knowledge. With Ops, every day looks the same regardless of who's running the floor.

## Who uses it

| User | What they do |
|---|---|
| **Cashier / floor staff** | Tap through daily checklists. Submit photo proofs. Report incidents. |
| **Store manager** | Assigns tasks, reviews completed checklists, addresses missed items. |
| **Owner** | Sees aggregate ops health in HQ. |
| **AI (Haiku)** | Flags missed tasks. Suggests escalations. |

## Why it exists

In Indian retail, the difference between a well-run store and a messy one is operational discipline:
- Was the fresh produce sorted by 7 am?
- Did the dairy chiller hit -2°C overnight?
- Did the cashier balance the till at close?
- Did anyone notice the broken light on aisle 2?

Ops makes these checkable, accountable, and audit-able without becoming overhead.

## Tech stack

| Layer | Tool |
|---|---|
| Frontend | React 18 + Tailwind, PWA-enabled |
| Camera | Native browser MediaDevices API (no native app needed) |
| DB | Supabase |
| AI | Haiku for task flagging + ETA estimation |
| Storage | Supabase Storage for photo proofs |

## Core capabilities

### MVP

| | |
|---|---|
| **Daily checklists** | Per-store, per-shift checklists generated daily. Each item: tap to complete, optional photo proof. |
| **Templates** | Reusable templates for: morning open, mid-day reset, evening close, weekly deep clean, festival prep. |
| **Photo proofs** | Camera capture for items needing visual verification (chiller temp, fresh-produce arrangement). |
| **Missed-task escalation** | If item not done by deadline → flag to manager. If still missed by EOD → flag to owner. |
| **Ad-hoc task assignment** | Manager can assign one-off tasks to staff with deadline + priority. |
| **Incident reporting** | Staff can report incidents: broken equipment, spillage, customer complaint, theft. Photo + notes. |
| **Shift summary** | At end of each shift, manager gets a summary: tasks done, tasks missed, incidents, anomalies. |

### Phase 3.5 additions

- Multi-store ops rollup
- Auto-generated tasks from other apps (Inventory says "ragi flour expires today" → Ops creates "Route to Waste-to-Wealth" task)
- Recurring tasks with smart scheduling
- Staff performance summary (manager-only)

### Future

- Voice-input task completion ("Hey Consto, fresh produce sorted")
- Computer vision verification of photo proofs (was the chiller actually at -2°C in the photo?)
- Predictive ops (busy period coming, prep extra)

## Key screens

### Screen 1 — Today's checklist (default for staff)

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO OPS · Beeramguda · Ravi · Morning shift          │
├──────────────────────────────────────────────────────────┤
│  Today, 06:30 AM   ●●●●○○○○ 4 of 8 done                  │
├──────────────────────────────────────────────────────────┤
│                                                            │
│  ☑ Unlock store, security check (6:25 AM)                 │
│  ☑ Open POS, verify till opening cash                     │
│  ☑ Fresh produce intake — count, sort                     │
│       📷 Photo logged 06:42 AM                            │
│  ☑ Dairy chiller temp check                                │
│       📷 -2°C, OK                                          │
│                                                            │
│  ☐ Idli batter intake from kitchen supplier               │
│       Due: 7:00 AM   ⚠ 12 min                             │
│       [Mark done] [Photo]                                  │
│                                                            │
│  ☐ Display board updated (today's specials)                │
│       Due: 7:30 AM                                         │
│       [Mark done] [Photo]                                  │
│                                                            │
│  ☐ Festival corner refresh (Sankranti coming up)          │
│       Due: 8:00 AM                                         │
│       [Mark done] [Photo]                                  │
│                                                            │
│  ☐ Stock front shelf (high-demand items)                  │
│       Due: 8:30 AM                                         │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 2 — Item detail / photo capture

Tap a task → opens detail with task description, photo capture button, notes field, "Mark done" button.

### Screen 3 — Incident report

Quick-access from main screen. Categories: equipment, spillage, complaint, theft, other. Photo + notes + auto-tagged to current shift.

### Screen 4 — Manager dashboard

```
┌──────────────────────────────────────────────────────────┐
│  CONSTO OPS · Manager view · Beeramguda                  │
├──────────────────────────────────────────────────────────┤
│  Today                                                     │
│  Morning shift: ●●●●●●●● 8 of 8 ✓                        │
│  Afternoon shift: ●●●○○○○○ 3 of 8 (in progress)          │
│                                                            │
│  Active incidents (1)                                      │
│  • Aisle 2 light broken (Ravi, 09:14) → assigned          │
│                                                            │
│  Missed yesterday (review)                                 │
│  • Dairy chiller temp check missed 8 PM                   │
│    [Mark resolved] [Flag for retraining]                  │
│                                                            │
└──────────────────────────────────────────────────────────┘
```

### Screen 5 — Templates editor (manager only)

Manager edits the morning / afternoon / evening checklists. Adds new items, reorders, sets deadlines.

### Screen 6 — Shift summary report

End of shift, auto-generated. PDF-downloadable. What got done, what didn't, incidents, photos.

## Data model

```sql
-- Checklist templates
checklist_templates (
  id uuid primary key,
  store_id uuid references stores,
  name text, -- 'morning_open', 'evening_close', 'festival_prep_ugadi'
  shift_type text,
  items jsonb, -- [{name, description, deadline_offset_minutes, requires_photo}]
  is_active boolean default true,
  created_at timestamp default now()
)

-- Daily generated checklists
daily_checklists (
  id uuid primary key,
  store_id uuid references stores,
  template_id uuid references checklist_templates,
  shift_date date,
  shift_type text,
  assigned_to uuid references staff,
  status text, -- 'pending', 'in_progress', 'complete', 'partial'
  started_at timestamp,
  completed_at timestamp
)

-- Individual task items
checklist_items (
  id uuid primary key,
  daily_checklist_id uuid references daily_checklists,
  task_name text,
  task_description text,
  deadline timestamp,
  requires_photo boolean default false,
  status text, -- 'pending', 'done', 'missed', 'skipped'
  completed_at timestamp,
  completed_by uuid references staff,
  photo_url text,
  notes text,
  skip_reason text
)

-- Ad-hoc tasks
adhoc_tasks (
  id uuid primary key,
  store_id uuid references stores,
  assigned_to uuid references staff,
  assigned_by uuid references staff,
  title text,
  description text,
  priority text, -- 'low', 'medium', 'high', 'urgent'
  deadline timestamp,
  status text, -- 'pending', 'in_progress', 'done', 'cancelled'
  completed_at timestamp,
  created_at timestamp default now()
)

-- Incidents
incidents (
  id uuid primary key,
  store_id uuid references stores,
  reported_by uuid references staff,
  category text, -- 'equipment', 'spillage', 'complaint', 'theft', 'safety', 'other'
  severity text, -- 'low', 'medium', 'high', 'critical'
  description text,
  photo_url text,
  status text, -- 'open', 'in_progress', 'resolved', 'closed'
  resolved_by uuid references staff,
  resolved_at timestamp,
  resolution_notes text,
  created_at timestamp default now()
)

-- Shift summaries
shift_summaries (
  id uuid primary key,
  store_id uuid references stores,
  shift_date date,
  shift_type text,
  staff_id uuid references staff,
  tasks_completed int,
  tasks_missed int,
  incidents_count int,
  notes text,
  summary_text text, -- AI-generated narrative
  generated_at timestamp default now()
)
```

## AI integration

| Operation | Model | Why |
|---|---|---|
| Task flag-by (overdue detection) | Haiku | Simple time comparison, runs every 5 min. |
| Suggested ETA for assigned tasks | Haiku | Based on historical similar-task durations. |
| Shift summary narrative | Haiku | Quick prose summary of the shift, not deep analysis. |
| Incident severity classification | Haiku | Categorise reported incidents into severity tiers. |
| Pattern detection (Phase 3.5) | Sonnet | "Dairy chiller missed 3 times this month — investigate." |

## Build sequence for Claude Code

1. **Scaffold PWA + Supabase wiring + camera permissions**
2. **Build checklist templates editor** (manager-only)
3. **Build daily checklist generation** — every morning, create that day's checklists from templates
4. **Build staff task UI** — list view, tap to complete, photo capture
5. **Build photo upload to Supabase Storage**
6. **Build missed-task detection** — Haiku job every 5 min, flags overdue items
7. **Build manager dashboard** — today's status, missed-task queue
8. **Build incident report flow** — category, severity, photo, notes
9. **Build ad-hoc task assignment** — manager → staff
10. **Build shift summary auto-generation** — end of shift, Haiku composes summary
11. **Build templates for: morning open, evening close, weekly deep clean, festival prep**
12. **PWA install + offline support** — checklist works offline, syncs when online
13. **Test on tablet** for a full week with real staff

## Success metrics

| Metric | Target |
|---|---|
| Task completion rate | 95%+ of daily tasks completed on time |
| Photo proof completion | 90%+ of photo-required tasks have a photo |
| Incident response time | Median under 30 min for high-severity |
| Repeat incidents | Decrease month-over-month |
| Staff adoption | All staff use the app daily within 2 weeks of launch |
| Manager review time | Under 10 min per shift summary |

## DPDP compliance

- Staff data covered under staff agreements
- Customer-related incident reports (complaints) reference customer ID; full audit logged
- Photo proofs of areas with customer faces: blurred or excluded by policy

## Brand voice

Internal tool. Tone is direct and practical. No customer-facing voice needed.

## What Ops does NOT do

- Track staff for surveillance (it's tools for accountability, not monitoring)
- Replace manager judgment (it flags missed items, manager decides why and what to do)
- Generate punitive reports (focus is on patterns and improvement, not blame)

## Reading list before building

- React PWA service worker + IndexedDB patterns
- Supabase Storage RLS for staff-uploaded photos
- Indian labour law on workplace monitoring (light reading; we are not surveilling)
