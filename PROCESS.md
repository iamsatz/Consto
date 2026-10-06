# Consto · How I work (design process, learnings and gated plan)

> Written 6 Oct 2026 with Claude Code after a review of this repo, its sessions and commands.
> Purpose: the single page that explains how I think, what I got wrong, what I changed, and the order of work.
> Rule: a step is not started until the previous step's "Done when" is ticked.

---

## 1. Seven learnings (what I missed)

| # | Learning | Where I missed it | What it cost |
|---|---|---|---|
| L1 | **Brief before tools.** Every session opens with deliverable, user, constraints, done-when. | Sessions opened with topics ("idea discussion", "questions and doubts"). | The /start → /ship loop never ran once in five months. |
| L2 | **The design chain is person → story → flow → screen → component → code.** No link may be skipped. | June PRDs dropped the User Stories and User Flows sections the May PRDs had. | Screens and tech exist with no flow behind them. Every agent guesses. |
| L3 | **One truth.** One PRD set, one CLAUDE.md, and the entry file points at the current version. | Two PRD folders that contradict (store size, WhatsApp timing, Electron vs PWA). CLAUDE.md points at the older one. | Every session starts on stale facts. |
| L4 | **Makers before critics.** Orchestration needs agents that produce, then agents that attack. Critics need a rubric and shared data. | Six persona critics, zero makers, none looked at the digital product. | A pile of opinions, no designed artifact. |
| L5 | **Close every session.** Think session ends with /capture + commit. Build session ends with /ship. | 300 KB of research left uncommitted. Commits on seven days in five months. | Work at risk, no history a recruiter can read. |
| L6 | **Tools follow the phase.** A code loop cannot run a research project. | Borrowed a developer's shipping loop for a pre-code design phase. | The system looked complete but never fired. |
| L7 | **Interview data is the asset.** 50 interviews must become stories and flows, by me, not by an agent. | Interviews informed the concept but were never converted into design artifacts. | Agents and docs argue in the abstract. |

## 2. Instead of X, do Y

| What I did | What to do instead |
|---|---|
| Opened a session to think, with no exit. | Open with a 4-line brief. End with /capture and a commit, or /ship. |
| Named sessions by topic. | Name by deliverable: "Consto - POS checkout flow". |
| Asked Claude "what am I missing?" | Ask "here is the flow, attack it from the staff's view at 7:45 am". |
| Rewrote PRDs as feature and screen lists. | Keep stories and flows as sections 3 and 5. Screens come after flows. |
| Ran six critics by hand, once. | Save a workflow: makers pipeline, then critics with rubric, then skeptics, then CEO ruling. |
| Kept two PRD sets and two CLAUDE.md files. | Pick one (June PRDs), archive the other, fix CLAUDE.md. |
| Mixed portfolio, concept site and product in one repo. | Product repo = docs/, site/, apps/, .claude/. Portfolio content moves to the portfolio repo. |
| Built the whole command system first. | Build the artifact chain first. Add machinery when a step repeats three times. |

## 3. Does anything need rework?

**Keep as is (good work):** concept site, 8 PRDs' content, Products.md research, Products-Decisions.md, rulebook, category master, session archives, BACKLOG, memory.

**Rework (small, deliberate):**
1. Single truth: June `PRDs/` wins. Reconcile CLAUDE.md to it. Move May PRDs to an archive folder.
2. Restore User Stories and User Flows into the POS and Agent PRDs, written from the interviews.
3. Repo tidy: `docs/`, `site/`, `apps/`, `.claude/`. Portfolio material out.
4. Persona review becomes a saved workflow with makers added and the customer + compliance critics added.

Nothing is thrown away. The rework is re-ordering and reconnecting, about two sessions of work.

## 4. The gated plan

Each step: goal, who does what, deliverable, done-when, and the one sentence I say in an interview about it.

### Step 0 · Single truth and the brief habit (half a day)
- **Goal:** one map, one entry file, one way to start a session.
- **Me:** confirm June PRDs are the truth. Confirm Explorations go to the portfolio repo.
- **Claude:** commit today's research; merge the two CLAUDE.md; fix paths; move May PRDs to docs/archive; add the brief template to CLAUDE.md.
- **Deliverable:** clean main branch, one CLAUDE.md, PROCESS.md linked from it.
- **Done when:** `git status` is clean and a fresh session summarises the correct store size and WhatsApp phase without being told.
- **Interview line:** "Before designing I made sure the team, including the AI, read from one source of truth."

### Step 1 · People and stories (3 to 4 days, mostly me)
- **Goal:** turn 50 interviews into the people and the jobs the product serves.
- **Me:** write 3 personas (till staff, morning customer, owner) and 10 to 12 user stories in "as a… I want… so that…" form, each tagged with the interview it came from.
- **Claude:** structure, challenge weak stories, check each story maps to a PRD capability or flags a gap.
- **Deliverable:** `docs/design/01-people-and-stories.md`.
- **Done when:** every story cites evidence, and the staff persona can be read aloud to a shopkeeper without them laughing.
- **Interview line:** "I grounded every story in a real interview, not in what I wished customers wanted."

### Step 2 · Flows (2 to 3 days)
- **Goal:** the three POS flows the May PRD named, drawn end to end, plus the one Agent flow (dormant customer gets a message).
- **Me:** sketch on paper first. Decide the happy path and the two most common failures (offline, customer not found).
- **Claude:** render flows (Mermaid or FigJam), count steps and taps, flag any step with no data source.
- **Deliverable:** `docs/design/02-flows.md` with one diagram per flow and a tap count.
- **Done when:** standard checkout is under the PRD's speed target on paper, and each flow has an offline branch.
- **Interview line:** "I measured the flow in taps and seconds before a single screen was drawn."

### Step 3 · Tokens and screens (1 week)
- **Goal:** design system tokens and the six POS screens from the PRD, each traceable to a flow step.
- **Me:** approve tokens (saffron, sage, cream, deep, gold, terracotta), decide type scale, approve each screen.
- **Claude:** token file, Figma frames via the Figma connector, component list, annotations per screen.
- **Deliverable:** Figma file + `docs/design/03-screens.md` with screen → flow step → story mapping.
- **Done when:** every screen element traces to a flow step, and a content pass applies the brand voice rule to every string.
- **Interview line:** "Every pixel traces back to a story. I can show the chain for any element on screen."

### Step 4 · Orchestrated review (2 days)
- **Goal:** the saved persona-review workflow, run on steps 1 to 3 output.
- **Me:** write the rubric per critic and pick the data each critic reads.
- **Claude:** `.claude/workflows/persona-review.js`: makers hand forward, critics (customer, staff, investor, DPDP, skeptic) attack with rubric, three skeptics verify each finding, CEO agent rules.
- **Deliverable:** rulings file; design updated for the survivors.
- **Done when:** the workflow runs from one sentence and produces the same structure twice.
- **Interview line:** "I used AI agents as a review panel with evidence and rubrics, with adversarial checks so weak opinions die before reaching me."

### Step 5 · Build POS (Ship loop, 2 to 3 weeks)
- **Goal:** the MVP build sequence in the POS PRD, one feature per session.
- **Me:** brief each session, review each Vercel preview against the Figma screen.
- **Claude:** /start, plan mode, atomic commits, /check, /ship, /sync. Worktree only when two builds run at once.
- **Deliverable:** working POS on a preview URL.
- **Done when:** the three flows from Step 2 can be walked on the preview in the measured tap counts.
- **Interview line:** "Design to code with a branch, a PR and a preview per feature, reviewed against the design."

### Step 6 · Validate and tell the story (1 week)
- **Goal:** test with real staff and customers, then the case study.
- **Me:** 5 sessions at a real counter. Note where the flow broke.
- **Claude:** compile findings, update flows, draft the case study for the portfolio repo.
- **Done when:** the case study shows the chain: interview → story → flow → screen → code → test → change.

## 5. The 60-second answer when someone asks "how do you work?"

"I start from people, not features. Fifty interviews became three personas and twelve stories. Stories became four flows, measured in taps and seconds before any screen. Screens were drawn to trace back to flow steps. I then used AI as a structured review panel: maker agents produce, critic agents attack with a rubric and real data, skeptics verify, and only surviving findings change the design. Build happens one feature per branch with a preview I review against the design. Then I test at a real counter and the case study shows the full chain. The tooling matters less than the chain. When the chain was broken, the tooling sat unused, and fixing that was my biggest lesson on this project."

---

## 6. Claude Code vocabulary, with Consto examples

| Term | What it is | Consto example | How I trigger it |
|---|---|---|---|
| Session | One conversation in one folder, resumable. One meeting, one agenda. | "Consto - POS checkout flow" | Open a new session, start with the 4-line brief. |
| Main agent | The Claude I chat with. Reads CLAUDE.md and memory, edits files. | This conversation. | Always on. |
| Subagent | A helper the main agent spawns. Fresh context, one task, returns a report. Does not see my chat. | "Spawn six persona critics on Products.md." | Ask for it in plain words. |
| Custom agent | A subagent saved as a markdown file in `.claude/agents/` with name, tools, model and standing instructions. Same behaviour every time. | `till-staff-critic.md`: "judge every screen by taps and seconds at 7:45 am." | Create the file once, call by name after. |
| Orchestration | The main agent as manager: split, hand out, wait, merge. Five shapes: orchestrator and workers, pipeline, generator and critic, adversarial verify, planner and executor. | UX agent drafts flow, UI agent draws screens, critics attack, CEO rules. | "Spawn X to do A, then Y to attack it, then merge." |
| Workflow (lowercase) | My human routine plus slash commands. | start, plan, build, check, ship, sync | Habit. |
| Dynamic workflow (Workflow tool) | Orchestration as a saved JavaScript script in `.claude/workflows/`. Decides who runs, order, parallelism, when to stop. Runs in the background. | `persona-review.js` | Say "use a workflow" or "ultracode", or a slash command that calls it. Never fires otherwise. |
| Slash command / skill | A saved prompt run with a slash. Commands live in my home folder (global, invisible to a clone). Skills live in `.claude/skills/` in the repo and travel with it. | `/capture`, `/to-do`; future `/brief` | Type the slash, or Claude auto-picks a matching skill. |
| Plan mode | Claude may read and propose, not edit. I approve, then exit the mode. | Agree the six POS screens before Figma work. | Shift+Tab in the app. |
| Worktree | A second copy of the repo on another branch so two sessions edit without colliding. | Only when POS and Agent are both in code. | Session option, or EnterWorktree. |
| Hook | A shell command that runs on an event. No AI judgment. | Block commits that edit the archived May PRDs. | settings.json; not yet. |
| MCP connector | A plug into an outside tool. | Figma for Step 3; Slack, Gmail, Calendar, Drive attached. | Already connected; Claude loads them on demand. |
| CLAUDE.md | The brief I write for every session. Loaded at start. | Tech stack, build order, brand voice, the 4-line brief rule. | Edit the file. |
| Memory | The notebook Claude writes for itself across sessions. | "Sateesh wants options before verdicts." | Claude writes it; I can ask it to remember or forget. |
| Permission mode | How much Claude may do unasked: plan, ask each time, accept edits, auto, bypass. | Plan for design decisions; accept edits for doc tidy. | App setting per session. |

**Choosing the level, day to day**
- Level 1, one Claude: default. "Draft the checkout flow from these stories."
- Level 2, orchestration by hand: more than one independent viewpoint or more than one file to produce. "Spawn a UX designer subagent to draft, three critics to attack, then merge."
- Level 3, saved workflow: a level 2 pattern that has repeated three times. "Use the persona-review workflow on docs/design/02-flows.md."

**Decisions taken 6 Oct 2026**
- June `PRDs/` are the truth. May PRDs archived.
- Explorations and portfolio references move to the portfolio repo.
- Rework happens on a branch through the Ship loop, so the history shows the method.
