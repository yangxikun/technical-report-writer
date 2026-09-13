# Technical Report Writer

A skill for AI coding agents that researches, writes, and revises **decision-oriented Chinese technical reports** — delivered as polished, self-contained HTML with evidence, diagrams, and production guidance.

Most reports fail because they transcribe documentation instead of helping a reader decide. This skill enforces the opposite: every section must reduce the cost of a real technical decision.

> If the report only restates documentation or lists features, revise it before delivery.

## Why

A good technical report answers a question like *"should we adopt API A or API B, and what has to change in production if we do?"* — not *"here are the features of A and B."*

This skill encodes a working method for that outcome:

- start from the decision pressure, not the product page;
- build one conceptual model before comparing details;
- separate documented fact from inference, recommendation, and unknown;
- show minimal but contract-complete examples;
- end with conditional, operational principles.

## Features

- **Decision-first structure** — problem → friction → alternatives → evidence → trade-offs → production practice → decision rule.
- **Explicit evolution reasoning** — what workload pressure exposed the old abstraction's limit, and what responsibility the new one moves or separates.
- **Epistemic discipline** — labels claims as documented fact, inference, recommendation, example value, or unknown. No "supports X" without saying *who* executes X.
- **Representative API examples** — raw JSON over SDK code, system instructions included, tool-call round trips split into separate blocks, correlation IDs (`tool_call_id` / `call_id` / `tool_use_id`) shown, streaming shown as real SSE events.
- **Technical diagrams** — architecture, data-flow, workflow/sequence, state, and comparison diagrams, each required to answer one named question.
- **A restrained HTML design system** — white reading surface, fixed two-level right-side TOC, responsive layout, and a validated anchor structure.
- **A verification checklist** — tag balance, anchor resolution, TOC-to-heading matching, diagram readability, and responsive behavior at four widths.

## Installation

Clone into your agent's skills directory.

**Codex**

```bash
git clone https://github.com/yangxikun/technical-report-writer.git \
  ~/.codex/skills/technical-report-writer
```

**Claude Code**

```bash
git clone https://github.com/yangxikun/technical-report-writer.git \
  ~/.claude/skills/technical-report-writer
```

The skill is self-contained — no build step, no dependencies to install.

## Usage

Ask for a report artifact, not a chat answer:

```text
Use $technical-report-writer to compare the Responses API and the Chat Completions API
for a multi-step tool-calling assistant, and produce a Chinese HTML report.
```

```text
用 technical-report-writer 调研这三套消息队列方案，产出一份可落地的中文 HTML 技术报告，
包含架构图和数据流图。
```

Typical triggers:

- comparing APIs, architectures, platforms, or engineering approaches;
- choosing between two designs with real production consequences;
- producing a written artifact that a team will read and act on.

**Not** for short explanations, quick answers, or routine code changes.

## Repository Layout

```text
SKILL.md                              # entry point: routing, working method, standards, quality bar
agents/openai.yaml                    # agent interface metadata (display name, brand color, default prompt)
assets/technical-report-template.html # visual starting point; replace every {{TOKEN}}
references/
  writing-guide.md                    # how to shape the argument and the prose
  html-style-guide.md                 # palette, layout, components, responsive checks
  diagram-guide.md                    # diagram routing, construction rules, embedding, verification
  user-preferences.md                 # confirmed house style for this user
```

## How It Works

### 1. Route the work

The agent loads only the references the task needs — writing guide for any substantial report, HTML guide when producing HTML, diagram guide when the subject has structure or flow, user preferences when matching the house style.

### 2. Build the argument

Establish the scenario and the pressure it creates, name the conceptual model, then compare alternatives across only the dimensions that affect the choice:

`What` · `Why` · `How` · `When` · `When Not` · `Trade-off` · `Verification`

### 3. Deliver HTML

The report *is* the first screen — not a landing page. Long examples stack vertically, one alternative per row. Tables do comparison work; prose does causality and trade-offs.

### 4. Verify before delivery

Anchors resolve, TOC labels match headings exactly, tags balance, diagrams are readable at desktop and mobile widths, and no node or connector is clipped.

## Design System

Default palette when no brand system is supplied:

| Role | Color | Usage |
|---|---|---|
| Deep navy | `#091540` | Body text, header, fixed TOC |
| Primary blue | `#1055C9` | Headings, links, important labels |
| Accent blue | `#7692FF` | Section numbers, left rules, focused borders |
| Pale blue | `#ABD2FA` | Thin borders, separators, subtle shadows |
| White | `#FFFFFF` | Page, cards, callouts, table headers |

Code blocks use Highlight.js `github` light theme from cdnjs, with GitHub-like fallback styling so content stays readable offline.

## Diagrams

Diagrams make an argument, not decoration. The guide routes each figure to the reader's actual question:

| Reader asks | Diagram |
|---|---|
| What components exist, who owns what? | Architecture |
| Where does data move, transform, or cross a trust boundary? | Data-flow |
| In what order do actions occur, and who initiates? | Workflow / sequence |
| What lifecycle states exist and what moves between them? | State |
| How do alternatives distribute the same responsibilities? | Comparison |

Output is **SVG**, embedded as a local asset with a numbered caption and descriptive alt text. When the `fireworks-tech-graph` skill is available, it is the preferred generator; otherwise the skill falls back to another available diagram capability or a restrained native HTML/CSS figure rather than blocking delivery.

## Quality Bar

A report is complete only when a reader can answer:

1. What problem is being solved?
2. What is the conceptual difference between the options?
3. Which facts are documented, and which conclusions are analysis?
4. What should be chosen under which conditions?
5. What must change in production code, state, testing, operations, and governance?
6. What can fail, and how will the team verify the migration?
7. Which diagram shows the critical structure or flow — and can the reader explain it without guessing what an arrow means?

## Requirements

- An agent that supports skills (Codex or Claude Code).
- Network access to the Highlight.js CDN for syntax highlighting. The report degrades gracefully without it.
- Optional: a diagram skill such as `fireworks-tech-graph` for SVG generation. Without it, the skill falls back to native HTML/CSS figures.

## License

No license file is currently included. Add one before distributing or accepting contributions.
