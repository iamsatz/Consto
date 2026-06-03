# Browser Workflow Tool · Image specs

Suggested visuals for the Browser Workflow Tool exploration page.

## 01 · Hero image (required)

**File:** `hero.jpg`
**Dimensions:** 1600×900 (16:9)
**Treatment:** Clean, slightly playful. Cream/warm-white background.

**Suggested content:**

- **Option A · Visual metaphor** — Stylised illustration of a maze of browser windows / tabs with a clear path through them. Shows the "complexity → clarity" idea.
- **Option B · Screenshot of the overlay UI** — If you have a working prototype, a clean screenshot of the hotkey overlay in action. Mac/Chrome chrome at the edges.
- **Option C · Time-lapse mockup** — A grid of 9 sequential browser screens showing the same workflow done manually vs done by the tool (compressed).

**Recommended:** Option B if you have a prototype to screenshot. Option A if not.

---

## 02 · Workflow capture UI mockup

**File:** `capture-flow.png`
**Dimensions:** 1400×900
**Treatment:** Clean Figma-style mockup. Browser chrome + overlay panel.

**Content:** Show the moment of "recording a workflow":
- Browser window showing a real page
- Overlay panel saying *"Recording — perform actions to capture"*
- A small timeline at the bottom showing 3-4 captured actions

This is the core UI moment of the product.

---

## 03 · Workflow replay UI

**File:** `replay-flow.png`
**Dimensions:** 1400×900
**Treatment:** Same style as capture flow.

**Content:** Show the moment of running a saved workflow:
- Browser window
- Small toast or pill showing *"Running: Open daily review tabs"*
- Subtle progress indicator

---

## 04 · Use-case diagram

**File:** `use-cases.svg`
**Dimensions:** 1400×800
**Treatment:** Clean diagram with icons.

**Content:** 4-6 example workflows the tool handles well:
- Multi-tab open (morning routine)
- Multi-step form fill
- Copy data between two browser apps
- Bulk navigation through a list
- Login + setup sequence

Shows the breadth of what's possible without overpromising.

---

## 05 · Architecture / privacy diagram

**File:** `architecture.svg`
**Dimensions:** 1200×800

**Content:** A simple diagram showing:
- Browser → Extension (local) → Local storage
- No server connection
- "All workflows local, never synced"

Reinforces the trust message.

---

## Image style notes

- Browser chrome should look like Chrome (most common target)
- Use real-looking sample data (not "lorem ipsum")
- Keep the overlay UI minimal — bold typography, subtle borders
- Avoid 3D effects, glassmorphism, gradients

## Minimum viable image set

1. Hero (one good screenshot or visual metaphor)
2. Capture-flow mockup
3. Use-cases diagram

Three good images > seven mediocre ones.
