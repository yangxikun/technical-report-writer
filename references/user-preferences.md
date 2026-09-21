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
- Pretty-print every payload: 2-space indentation, one key per line, literal Unicode (no `\uXXXX`), no blank first line inside `<pre><code>`. Minified single-line JSON is rejected.
- Include the system prompt or top-level instructions in request examples.
- For tool calling, split the first request, model tool call, and second request into separate blocks.
- Show the correlation field explicitly: `tool_call_id`, `call_id`, or `tool_use_id`.
- Put each API/provider on its own vertical row for long examples.
- Show streaming output as actual SSE event examples, one API per row.
- Clearly label illustrative values and protocol fragments; do not present them as observed model results.
- When the report covers a protocol, expect a full end-to-end packet walkthrough (discovery → list → call → mid-flight input → error layers) over one consistent scenario, with real header names and required `_meta` keys.

## Information Architecture

- Content column width is 75% of the viewport (`.shell { width: 75% }`), not a fixed pixel max-width; do not add `max-width` caps to `.subtitle` / `.lead` or other prose blocks, as they would silently defeat the 75% directive.
- On wide screens (≥1280px) keep the fixed right-side TOC (240px wide, at `right: 24px`, vertically centered via `top: 50%` + `translateY(-50%)`) and left-align the content column (`margin-left: clamp(28px, 3vw, 96px)`) instead of centering it — a centered 75% column leaves only 12.5vw per side, which collides with the TOC below ~2100px viewport width. Clearance invariant: content right edge = margin-left + 75vw must stay at least ~10px left of the TOC left edge (100vw − 264px).
- On narrow screens (≤850px) fall back to full width (`width: auto`), never 75%.
- Support secondary TOC entries for meaningful subsections.
- TOC text must match destination headings exactly.
- Keep the document focused. Remove chapters that do not help the requested decision.
- Proactively include architecture, data-flow, workflow, sequence, or state diagrams when they explain relationships better than prose. Prefer the `fireworks-tech-graph` skill and use it to generate SVG diagrams when it is available; do not block delivery when it is not.

## Visual Style

- Use a white reading surface and a restrained blue system.
- Default palette: `#091540`, `#1055C9`, `#7692FF`, `#ABD2FA`, and `#FFFFFF`.
- Use pale blue mainly for borders, separators, subtle shadows, and secondary text on dark surfaces, not large background fills.
- The fixed right-side TOC must use a white (light) background with dark text — never a dark navy fill. Reverse TOC text to deep navy inside the wide-screen media query only; secondary entries use the same color as primary entries (deep navy), with hierarchy shown by indentation and font size, not color. The TOC still sits inside the dark header band on narrow screens, so keep its base colors light, and keep secondary entries the same light color as primary there as well.
- Prefer white cards and table headers with blue border accents.
- Callouts must be semantic: default info uses a light blue tint; `注意`/warning uses the amber family (`#FFF7E4` background, `#D48806` left rule, `#9A6200` label) — a warning must be visually distinct without reading the label.
- Diagrams are not confined to the blue palette: use semantic colors (red = problem/error, green = solution/success/credentials, amber = warning/human decision, violet/teal = actor distinction), one meaning per color, with arrowheads matching their line color.
- TOC entries need an active state: highlight on click and follow scroll via IntersectionObserver; use smooth scrolling and `scroll-margin-top` on anchored sections.
- Use Highlight.js `github` light theme for code blocks, including its GitHub-like light background.
- Keep long examples full-width; avoid squeezing three code samples into one row.
- Keep technical SVG diagrams full-width, readable, and captioned.

## Interaction Pattern

- Expect iterative refinement of headings, sections, layout, and color. Make requested changes narrowly and preserve established choices elsewhere.
- After each revision, validate anchors and HTML structure. For visual changes, render and inspect rather than relying only on CSS text.
- This user reads the rendered page carefully and reports visual defects precisely (blank rows in code blocks, arrowheads whose color does not match the line). Run the review pass before delivery rather than waiting for the report.
- When the report lives in a 资料库 page node, re-import to the same `node-block-id` after every revision so the link stays stable and history stays traceable.
