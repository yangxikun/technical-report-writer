# Established User Preferences

These preferences were confirmed through iterative edits. Apply them when producing reports for this user, while yielding to explicit instructions in the current request.

## Deliverable

- Prefer a polished Chinese HTML report for substantial technical research and API comparisons.
- Make the artifact immediately usable; do not stop at an outline or paste a long report only into chat.
- Include authoritative source links and state the research date.

## Writing

- Lead with the real problem, then establish the conceptual model, then show mechanisms and examples.
- Explain why a successor exists, what limitation it addresses, when it is appropriate, and what it costs.
- Favor decision guidance over feature transcription.
- Extract a small number of memorable engineering principles, but do not present context-dependent advice as universal law.
- Keep prose rigorous, direct, and practical. Avoid showing off terminology.

## API Examples

- Show raw JSON objects rather than SDK code unless a programming language is requested.
- Include the system prompt or top-level instructions in request examples.
- For tool calling, split the first request, model tool call, and second request into separate blocks.
- Show the correlation field explicitly: `tool_call_id`, `call_id`, or `tool_use_id`.
- Put each API/provider on its own vertical row for long examples.
- Show streaming output as actual SSE event examples, one API per row.
- Clearly label illustrative values and protocol fragments; do not present them as observed model results.

## Information Architecture

- Use a fixed right-side TOC, vertically centered on wide screens.
- Support secondary TOC entries for meaningful subsections.
- TOC text must match destination headings exactly.
- Keep the document focused. Remove chapters that do not help the requested decision.
- Proactively include architecture, data-flow, workflow, sequence, or state diagrams when they explain relationships better than prose. Prefer the `fireworks-tech-graph` skill and use it to generate SVG diagrams when it is available; do not block delivery when it is not.

## Visual Style

- Use a white reading surface and a restrained blue system.
- Default palette: `#091540`, `#1055C9`, `#7692FF`, `#ABD2FA`, and `#FFFFFF`.
- Use pale blue mainly for borders, separators, subtle shadows, and secondary text on dark surfaces, not large background fills.
- Prefer white cards, callouts, and table headers with blue border accents.
- Use Highlight.js `github` light theme for code blocks, including its GitHub-like light background.
- Keep long examples full-width; avoid squeezing three code samples into one row.
- Keep technical SVG diagrams full-width, readable, captioned, and consistent with the report's blue palette.

## Interaction Pattern

- Expect iterative refinement of headings, sections, layout, and color. Make requested changes narrowly and preserve established choices elsewhere.
- After each revision, validate anchors and HTML structure. For visual changes, render and inspect rather than relying only on CSS text.
