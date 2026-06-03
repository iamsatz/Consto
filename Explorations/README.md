# Portfolio · Explorations

> Self-contained content for the three "Explorations" cards on the portfolio site. Take any of these to a Claude Code thread (or any other build environment) and the page can be implemented from the markdown alone.

## What is in this folder

Three exploration cases, each in its own subfolder with markdown content plus an `images/` folder with image specifications.

```
Explorations/
├── README.md                                      ← this file
├── 01-consto/
│   ├── consto.md                                  ← full exploration content
│   └── images/
│       └── README.md                              ← image specs (what to make, dimensions)
├── 02-browser-workflow-tool/
│   ├── browser-workflow-tool.md                   ← template with [YOUR INPUT] markers
│   └── images/
│       └── README.md
└── 03-ai-tuition-for-students/
    ├── ai-tuition.md                              ← template with [YOUR INPUT] markers
    └── images/
        └── README.md
```

## How to use this folder

### When you want to implement an exploration page

Hand the relevant `<name>.md` file to your Claude Code thread (or any developer / LLM) along with the brief: *"Build a detail page for this exploration that matches the style of the rest of the portfolio."*

### When you want to fill in your own thinking

Two files (`browser-workflow-tool.md` and `ai-tuition.md`) have `[YOUR INPUT NEEDED]` markers. Replace those sections with your own writing. The structure and prose around them are already drafted.

The Consto file (`consto.md`) is complete because we built the full thinking for it in the previous sessions.

## Tone guide (applies to all three)

Match the rest of the portfolio:

- First person. Personal voice. *"I built this because..."*
- Italics for emphasis (rendered in accent color on the live site)
- Generous spacing, single-column flow, max ~65ch text width
- Short sentences. Period-driven, not comma-driven.
- No marketing speak. No "leveraged synergies." No "innovative solutions."
- Show the thinking, not just the conclusions. These are *explorations*, not finished case studies.
- A single status line near the top: `Concept` / `Building` / `Live`

## Image conventions

- **Hero image** at the top: 16:9 ratio, 1600×900px minimum. Keep it warm and editorial — never stock-photo-glossy.
- **In-content images**: any ratio. Keep total image weight under ~1.5MB per page.
- **Diagrams / mockups**: SVG when possible, PNG fallback.
- **Style**: cream/warm-white background to match the portfolio palette. Avoid pure white screens of UI.

Each subfolder's `images/README.md` has specific image needs for that exploration.

## Brand palette reference

Same as the portfolio site:

| Token | Hex | Use |
|---|---|---|
| Background | `#F8F5F0` | Main canvas |
| Surface | `#FFFFFF` | Cards |
| Primary text | `#1A1612` | Headlines, body |
| Secondary text | `#5A5651` | Captions, supporting |
| Accent | `#C76737` | Italic emphasis, links, highlights |
| Border | `#E5E1D9` | Subtle dividers |

Font: **Inclusive Sans** throughout, weight 400 for body, 500 for headlines.

## Status of each exploration

| # | Exploration | Status | Content state |
|---|---|---|---|
| 01 | **Consto · Convenience Store for India** | Concept (deep), prototypes in progress | **Complete** — full content drafted |
| 02 | **Browser Workflow Tool** | Concept (early) | **Template** — needs your input on specifics |
| 03 | **AI Tuition for Students** | Concept (early) | **Template** — needs your input on specifics |
