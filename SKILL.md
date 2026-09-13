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
8. Verify the artifact: factual consistency, valid links, matching TOC anchors and headings, responsive layout, diagram readability, balanced visual hierarchy, and valid HTML structure.

## Content Standards

- Answer `What`, `Why`, `How`, `When`, `When Not`, and `Trade-off` for consequential choices.
- Explain evolution explicitly: what pressure exposed the old abstraction's limit, and what responsibility the new abstraction moves or separates.
- State recommendations conditionally. Prefer “when X holds, choose Y” over “always use Y.”
- Name responsibilities precisely: who stores state, executes tools, retries, authorizes side effects, validates schemas, and records audit history.
- Distinguish protocol capability from model quality. Do not claim an API shape automatically improves answer quality unless official evidence supports that claim.
- Include common mistakes only when they lead to real implementation failures.
- Keep examples minimal but complete enough to expose the contract. For API reports, show raw JSON request/response objects unless the user asks for SDK code.
- Use exact field names, event names, IDs, and stop reasons. Do not invent compatibility where provider semantics differ.
- Every diagram must answer a named question, use the report's terminology, and be explained in the surrounding prose. Do not add decorative diagrams or invent undocumented topology.

## HTML Deliverables

- Produce the actual report as the first screen, not a marketing landing page.
- Keep the reading surface white and restrained. Use color for hierarchy, borders, links, and small emphasis rather than large decorative areas.
- Use a right-side, vertically centered, fixed two-level TOC on wide screens; collapse it into normal document flow on narrow screens.
- TOC labels must match destination headings exactly. Add stable IDs to every linked heading and validate every anchor.
- Long examples for multiple alternatives should usually be stacked vertically, one alternative per row. Do not compress large JSON or event streams into three narrow columns.
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

If the report only restates documentation or lists features, revise it before delivery.
