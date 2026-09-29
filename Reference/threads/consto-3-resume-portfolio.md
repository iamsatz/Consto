# Consto thread 3: Resume & portfolio copy

Saved 2026-09-29. Handover record of the "Consto overview for resume / LinkedIn / portfolio" thread.

> **Read this first:** this thread ended **before any final copy was drafted**. I asked which role to target and whether the 50+ interviews really happened, and the thread closed before either question was answered. Sections 1 and 2 are therefore empty on purpose. Nothing below was invented to fill them.

---

## 1. FINAL resume text

**Not written yet.** No resume text was drafted or approved in this thread. Your existing resume was never shared either, so there is nothing to copy here word for word.

What was agreed as the *format* (not the copy):
- Put it under "Selected Projects", labelled **Concept**
- 1 line + 3 bullets

## 2. FINAL portfolio copy and structure

**Not written yet.** No portfolio card copy (about 80 words) or LinkedIn project entry was drafted or approved.

Proposed structure (a recommendation you haven't approved yet):

| # | Section | Shows |
|---|---|---|
| 1 | Problem framing: kirana owners remember customers, chains don't | Insight + personal story (15 yrs in the family kirana, Gadwal) |
| 2 | Service blueprint: 3 pillars (Memory, Services, Community), 4 store formats | Service design, systems thinking |
| 3 | Consto OS map: 8 products and how data flows between them | Product strategy, architecture |
| 4 | One PRD excerpt (Consto Agent) | Specs engineers can build from |
| 5 | `experience.html` walkthrough (GIF or video) | Designs and builds, with AI-assisted development |
| 6 | Guardrails: DPDP consent, "AI drafts, a human sends" | Mature AI product judgement |
| 7 | Validation plan: 60-day ₹0 pilot + success bar | Validates before building |

Format per channel (proposed):
- **Resume:** 1 line + 3 bullets, labelled Concept
- **LinkedIn:** Projects section + live Vercel link
- **Portfolio card:** hero visual of the walkthrough + about 80 words

## 3. Review: strengths, gaps, problems

Scope: I reviewed the live site copy in this repo (`index.html`, `portfolio.html`). I never saw your resume, so there's no review of it here.

### Strengths
- End-to-end ownership: research → strategy → 8 PRDs → a working interactive prototype, all built by you
- An authentic founder insight: 15 years behind your father's counter in Gadwal
- Mature AI framing: human-in-the-loop sending, DPDP consent, a Haiku/Sonnet split per task
- Validate-first discipline (60-day ₹0 pilot in `BACKLOG.md`), which is rare in a portfolio

### Problems (credibility risks, live on the site now)
| Claim | Where | Issue |
|---|---|---|
| "interviewed 50+ families in Beeramguda" / "50+ in-person interviews" / "What 50+ conversations revealed" | `index.html` L7, L584, L708; `portfolio.html` L7, L487, L490, L498 | Not verified. `BACKLOG.md` L9 says to confirm it or remove it. A hiring manager will ask for the notes. |
| "Zero organized chains. Japan has 80,000+." | `portfolio.html` L436 | False on both counts. India has SuperK, Apna Mart, 7-Eleven India, Twenty Four Seven; Japan has about 56K. |
| "India has zero organized convenience stores." | `portfolio.html` L545 | False (same reason) |
| "India has zero organized convenience chains" | `portfolio.html` L1153 | False (same reason) |
| "15 years" next to "18+ years of retail observation" | `index.html` L567/L575; `portfolio.html` L510–511 | Reads as inflated or confusing. Pick one clear timeline. |

### Gaps
- No clear label saying "concept, not launched". The copy must not suggest users, revenue or an open store.
- No target role chosen, so the story isn't pointed at any one type of hiring manager
- The 8 full PRDs risk overwhelming reviewers. Feature one excerpt and link the rest.
- Filler framing ("18+ years watching retail evolve") weakens the stronger personal story

## 4. How Consto is positioned

**Not decided.** You hadn't picked a target role. These options were offered:
1. Product Designer (UX/UI)
2. Service / Strategic Designer
3. AI Product Designer / Design Technologist

| Channel | Role | Title | One-line description |
|---|---|---|---|
| Resume | Not decided | Not decided | Not written |
| LinkedIn | Not decided | Not decided | Not written |
| Portfolio | Not decided | Not decided | Not written |

What doesn't depend on the role: frame it as a **self-initiated product + service design case study (concept)**, not as a startup.

## 5. Decisions made and rejected

**Your decisions:** none recorded. The thread closed before you answered.

**Guidance from the thread setup (constraints, not new choices):**
- Don't imply launched results, users or revenue
- Don't claim "zero organised convenience stores"
- Don't use "50+ interviews" until you've confirmed it
- Agree the target role before drafting

**Cuts I recommended (you haven't confirmed them):**
- Anything suggesting the store is live
- The "zero" claims
- Featuring all 8 PRDs in full
- Filler lines like "18+ years watching retail evolve"

## 6. Changes still to do (priority order)

1. **Answer: did the 50+ interviews happen?** If not, change every mention to what's true (e.g. "informal conversations with neighbours and shopkeepers"). Files: `index.html`, `portfolio.html`.
2. **Remove the "zero" claims and fix Japan to ~56K** on `portfolio.html` L436, L545, L1153
3. **Pick the target role** (1, 2 or 3 above)
4. Draft the resume (1 line + 3 bullets), LinkedIn project entry and portfolio card (about 80 words) for that role
5. Settle the timeline wording ("15 years inside" vs "18+ years")
6. Add a visible "Concept, not launched" label to the case study
7. Record a GIF or video of `experience.html` for the portfolio hero
8. Pull one Consto Agent PRD excerpt to feature. Link the other seven.
