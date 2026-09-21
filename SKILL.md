---
name: technical-report-writer
description: Research, write, and revise decision-oriented Chinese technical reports, especially HTML reports comparing APIs, architectures, platforms, or engineering approaches. Use when the user needs a polished report artifact with evidence, technical diagrams, examples, trade-offs, and production guidance; skip ordinary short explanations and routine code changes.
metadata:
  short-description: Create rigorous, readable Chinese technical reports
---

# Technical Report Writer

Create a report that helps the reader make and implement a technical decision. Do not treat the deliverable as a catalog of facts.

## Route The Work

- Read [references/writing-guide.md](references/writing-guide.md) for every substantial report or major rewrite.
- Read [references/html-style-guide.md](references/html-style-guide.md) when producing or revising HTML.
- Read [references/diagram-guide.md](references/diagram-guide.md) when the subject includes system structure, data movement, multi-step execution, lifecycle, deployment, or another relationship that a diagram would explain better than prose.
- Read [references/user-preferences.md](references/user-preferences.md) when the report is for this user or when matching the established house style matters.
- Read [references/review-checklist.md](references/review-checklist.md) before the review pass — for a new report, a major rewrite, or any revision that adds or removes a chapter, reworks layout, or rewrites payloads in bulk.
- Use [assets/technical-report-template.html](assets/technical-report-template.html) as a visual starting point when no stronger existing template or design system is supplied. Replace every `{{TOKEN}}`, remove unused sections, and adapt the hierarchy to the actual decision; never deliver template markers.

## Working Method

1. Infer the audience, decision, scope, and deliverable from the request. Ask only when a missing answer would materially change the report.
2. Research factual claims from authoritative, current sources. Prefer first-party documentation for products and APIs. Separate sourced facts from analysis, recommendation, and unverified assumptions.
3. Establish one core conceptual model before comparing details. Examples: Message vs Item vs Content Block; control plane vs data plane; synchronous vs event-driven.
4. Decide which relationships require diagrams before drafting the detailed prose. For substantial architecture or workflow reports, normally include at least one diagram when it materially reduces explanation cost. Use `fireworks-tech-graph` as the preferred diagram skill when it is available in the active session; first read and follow that skill's own `SKILL.md`, and ask it to produce an SVG diagram. If it is unavailable, use another available diagram capability that can produce SVG, or a restrained native HTML/CSS diagram without blocking the report.
5. Organize the report around the reader's problem and decision:
   - real problem or scenario;
   - current approach and its friction;
   - new approach or alternatives;
   - why each works;
   - evidence and minimal representative examples;
   - trade-offs, boundaries, and failure modes;
   - production practices and a concise decision rule.
6. Prefer one scenario carried through the report over unrelated examples. Use a second example only when it reveals a genuinely different boundary.
7. End important sections with a reusable principle, decision rule, or warning. Avoid slogans unsupported by the section.
8. Self-validate: run the mechanical validation script from [references/html-style-guide.md](references/html-style-guide.md) (tag balance, anchor resolution, JSON parse, code-block whitespace, SVG marker references) and fix what it catches.
9. Run the review pass described below — including **one forked reviewer subagent per SVG diagram** — triage the findings, and fix what matters.
10. Deliver: link the artifact and state what changed. If the report lives in a remote page node, re-import to the same node so the link stays stable.

## Review Pass

Self-review is unreliable on your own output — you read what you intended to write, not what is on the page. Delegate the check to **read-only reviewer subagents** before delivery.

### Per-Diagram Review (mandatory, one subagent per SVG)

Every generated SVG diagram gets its own forked review subagent. Diagram defects are geometric (text overlapping shapes, labels clipped at the viewBox edge, arrowheads in the wrong color) and a whole-report reviewer skimming prose reliably misses them — a dedicated reviewer with a narrow scope does not.

- Fork one `Explore`-type subagent **per diagram**, all in parallel, after the diagram is embedded and the mechanical script passes. Do not batch multiple diagrams into one reviewer, and do not fold diagram review into the whole-report pass.
- Scope each reviewer to exactly one SVG: give the file path, the line range of the `<figure>`, the named question the diagram answers, the legend and color semantics, and which choices were explicitly requested. Use the per-diagram brief template in [references/review-checklist.md](references/review-checklist.md).
- The reviewer must compute geometry, not eyeball it: estimate every `<text>` width (CJK chars × font-size + latin chars × 0.55 × font-size), map each span against the bounds of every nearby `rect` / `line` / `text`, and check nothing exceeds the viewBox.
- This applies equally when a later revision touches a diagram — re-review that diagram with a fresh subagent, even if the rest of the report needs no review pass.

### Whole-Report Review

- Spawn an `Explore`-type subagent. Read-only is deliberate: the reviewer cannot accidentally edit, so you keep ownership of every change.
- Give it a self-contained brief — it has not seen the conversation. State the file path, the standards files to apply, what changed this round, and which choices were explicitly requested by the user and must not be flagged. Use the brief template in [references/review-checklist.md](references/review-checklist.md).
- Require a flat, prioritized report: `BLOCKER / SHOULD-FIX / NITPICK | location | what is wrong | why it matters`, under 300 words. Do not let the reviewer propose replacement markup.
- Triage before acting. Fix every BLOCKER; fix SHOULD-FIX unless it contradicts an explicit user instruction; apply NITPICK only when free. When a finding conflicts with something the user asked for, keep the user's choice and say so at delivery.
- Re-run the mechanical script after fixing. A second review pass is warranted only for structural fixes, not typos.

Skip the whole-report subagent for a single-word fix or a change the user is watching in real time; still run the mechanical script, which costs seconds. The per-diagram subagent is skippable only when the change provably does not touch any SVG.

Revisions deserve the same scrutiny as first drafts. Most defects in a mature report are regressions from editing — stale chapter numbers after a deletion, a stranded duplicate paragraph after a block replacement, a style rule applied to one instance out of nine, a formatter run twice over the same block.

## Content Standards

- Answer `What`, `Why`, `How`, `When`, `When Not`, and `Trade-off` for consequential choices.
- Explain evolution explicitly: what pressure exposed the old abstraction's limit, and what responsibility the new abstraction moves or separates.
- State recommendations conditionally. Prefer “when X holds, choose Y” over “always use Y.”
- Name responsibilities precisely: who stores state, executes tools, retries, authorizes side effects, validates schemas, and records audit history.
- Distinguish protocol capability from model quality. Do not claim an API shape automatically improves answer quality unless official evidence supports that claim.
- Include common mistakes only when they lead to real implementation failures.
- Keep examples minimal but complete enough to expose the contract. For API reports, show raw JSON request/response objects unless the user asks for SDK code.
- Pretty-print every payload: 2-space indentation, one key per line, literal Unicode, no blank first line inside the code block. A minified payload is not an example, it is a puzzle. See the JSON formatting rules in [references/html-style-guide.md](references/html-style-guide.md).
- Use exact field names, event names, IDs, and stop reasons. Do not invent compatibility where provider semantics differ.
- Every diagram must answer a named question, use the report's terminology, and be explained in the surrounding prose. Do not add decorative diagrams or invent undocumented topology.

## HTML Deliverables

- Produce the actual report as the first screen, not a marketing landing page.
- Keep the reading surface white and restrained. Use color for hierarchy, borders, links, and small emphasis rather than large decorative areas.
- Use a right-side, vertically centered, fixed two-level TOC on wide screens; collapse it into normal document flow on narrow screens.
- TOC labels must match destination headings exactly. Add stable IDs to every linked heading and validate every anchor.
- Long examples for multiple alternatives should usually be stacked vertically, one alternative per row. Do not compress large JSON or event streams into three narrow columns.
- Code blocks carry payloads the reader parses field by field: pretty-print, mark the language, and open `<pre><code>` directly onto the first content character — `<pre>` renders a leading newline as an empty row.
- Use tables for compact comparisons; use prose and scenarios for causality and trade-offs. Avoid nested cards.
- Embed generated SVG diagrams as local report assets with a descriptive caption and useful alt text. Keep architecture, data-flow, workflow, and state diagrams full-width when labels would become cramped in columns.
- For syntax highlighting, use Highlight.js with a theme appropriate to the requested tone. Preserve readable fallback styling if the CDN is unavailable.
- Put user-facing deliverables in the task output directory and link the final artifact.

## Final Quality Bar

The report is complete when a reader can answer:

- What problem is being solved?
- What is the conceptual difference between the options?
- Which facts are documented, and which conclusions are analysis?
- What should be chosen under which conditions?
- What must change in production code, state, testing, operations, and governance?
- What can fail, and how will the team verify the migration or implementation?
- Which diagram shows the critical structure or flow, and can the reader explain it without guessing what an arrow means?

And mechanically:

- Does the validation script pass — tags balanced, anchors resolving, every JSON block parsing, no stray whitespace rows in code blocks, every arrowhead marker defined?
- Did a reviewer subagent see the changed regions, was every BLOCKER resolved — and did each SVG diagram get its own forked review subagent with computed (not eyeballed) geometry?

If the report only restates documentation or lists features, revise it before delivery.
