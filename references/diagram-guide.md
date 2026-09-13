# Technical Diagram Guide

Use diagrams to make a technical argument, not to decorate a report. A diagram is warranted when relationships, movement, ordering, state, or responsibility would take more prose to explain accurately.

## Tool Routing

1. If `fireworks-tech-graph` is available in the active skills list or at an explicit path supplied by the user, read its `SKILL.md` completely and use it as the preferred generator for architecture, data-flow, workflow, sequence, and state diagrams. Request SVG output.
2. Do not guess its commands, parameters, file formats, or installation location. If it is unavailable, continue with another diagram skill exposed in the session, or build a restrained diagram in native HTML/CSS when that is sufficient.
3. Use Mermaid only when the target artifact is guaranteed to load Mermaid or when rendering it to a stable image asset. Do not deliver an unrendered Mermaid block as the report's final visual.
4. Use SVG as the standard report format because it stays sharp at different viewport sizes, keeps technical labels readable, and is easy to embed in HTML. Keep any editable source produced by the diagram skill when useful, but embed the generated local `.svg` file in the report. Use PNG only when SVG generation is unavailable or the visual cannot be represented reliably as vector graphics.

## Choose The Diagram By The Reader's Question

### Architecture Diagram

Use when the reader asks:

- What components exist?
- Who owns each responsibility?
- Which boundaries are internal, external, trusted, or vendor-managed?
- Where do protocols or storage systems connect?

Show components, ownership boundaries, interfaces, stores, and external systems. Do not mix detailed runtime chronology into the same figure.

### Data-Flow Diagram

Use when the reader asks:

- Where does data originate and go?
- Where is it transformed, cached, stored, encrypted, or deleted?
- Which boundary changes the data's security or retention status?

Label arrows with the payload or event, not vague verbs such as “processes.” Distinguish control signals from business data when the difference matters.

### Workflow Or Sequence Diagram

Use when the reader asks:

- In what order do actions occur?
- Who initiates each step?
- Where do branches, retries, tool calls, approvals, or failures occur?

Prefer a sequence diagram for interactions across named actors. Prefer a workflow diagram for branching business logic. Do not use an architecture diagram to imply time order.

### State Diagram

Use when the reader asks:

- What lifecycle states exist?
- What event moves the system between states?
- Which transitions are invalid, terminal, retriable, or compensating?

Name states as stable conditions and transitions as events or commands. Include failure and cancellation states when they affect production behavior.

### Comparison Diagram

Use sparingly when alternatives share the same scenario but distribute responsibilities differently. Keep the same actors and visual order across alternatives so the difference is perceptible.

## Diagram Construction Rules

- Write the question the diagram must answer before generating it.
- Use the report's unified vocabulary. A component must not be called `Conversation Store` in prose and `History DB` in the figure unless the distinction is explained.
- Keep to one primary idea. Split architecture and runtime flow into separate figures when combining them makes either hard to read.
- Prefer 5–9 primary nodes. Group secondary details instead of shrinking text.
- Label every non-obvious arrow. Use arrow direction consistently.
- Mark assumptions, proposals, and unverified components visually or in the caption.
- Show trust, ownership, persistence, or provider boundaries only when they affect the decision.
- Do not imply exactly-once behavior, persistence, security, or causality unless the evidence supports it.
- Use the report palette for neutral structure: `#091540`, `#1055C9`, `#7692FF`, `#ABD2FA`, and white. Diagrams are NOT confined to it — when color carries meaning, use the semantic accent palette: red (`#B03A2E`/`#C0392B`/`#E3A59B`) for problems, errors, and the "before" side; green (`#1E7A46`/`#2E9E62`) for solutions, success, and credential issuance; amber (`#D48806`/`#c9862b`) for warnings and human decision points; violet (`#6B5CA5`/`#7C5CD6`) and teal (`#0F8B6C`) to distinguish actors when blue alone is ambiguous. Keep one meaning per color across the whole report and keep nodes mostly white so colored lines and titles carry the signal.
- Match every colored arrow with a same-colored `marker` arrowhead (define one marker per line color); a colored line ending in a blue arrowhead is a defect. Never write `marker-end="none"` on an edge the legend describes as directional.
- **Never draw a request edge and its response edge collinearly.** Two lines between the same pair of nodes in the same color coincide into a single stroke — the reader sees one line, and a legend claiming "solid = request, dashed = response" becomes unverifiable. Offset the pair by at least 12px perpendicular to their shared axis and give them distinct colors (e.g. primary blue solid outbound, accent blue dashed return).
- Stop edges short of the node border (4–6px) so the arrowhead sits outside the box rather than overlapping its stroke.
- Keep edge labels clear of the strokes they annotate: place them above the upper edge of a pair, not in the gap between two edges, and leave 6px or more between the text box and the nearest line.
- Avoid gradients, decorative shadows, tiny labels, and crossed connectors.

## Placement And Explanation

- Put the diagram immediately after the paragraph that introduces its question.
- Precede it with one sentence telling the reader what to look for.
- Follow it with two to four sentences interpreting the important relationships and trade-offs. Do not leave the reader to infer the conclusion from the picture alone.
- Give every figure a numbered caption when the report has more than one diagram.
- Add descriptive alt text that states the structure or flow, not merely “architecture diagram.”

## HTML Embedding

Prefer a local, user-facing SVG asset generated with `fireworks-tech-graph`:

```html
<figure class="tech-figure">
  <img src="assets/request-lifecycle.svg"
       alt="用户请求经过应用编排层、模型 API 和采购单工具后返回最终答复">
  <figcaption>图 1：工具调用把一次模型请求扩展为有身份关联的两阶段往返。</figcaption>
</figure>
```

Use responsive styling:

```css
.tech-figure {
  margin: 22px 0;
  padding: 16px;
  border: 1px solid #ABD2FA;
  border-radius: 7px;
  background: #FFFFFF;
}
.tech-figure img {
  display: block;
  width: 100%;
  height: auto;
}
.tech-figure figcaption {
  margin-top: 10px;
  color: #091540;
  font-size: 13px;
}
```

## Verification

Before delivery, verify:

- every diagram is referenced in prose and answers its stated question;
- labels are readable at desktop and mobile widths;
- no node, arrow, or label is clipped;
- arrow directions and labels agree with the written workflow;
- each legend claim is visually verifiable: if the caption distinguishes solid from dashed, or inbound from outbound, the reader can actually see two separate strokes;
- local assets resolve from the report's final location;
- the generated asset is SVG unless a documented fallback was necessary;
- the SVG has a valid `viewBox`, scales without distortion, and contains no clipped labels;
- alt text and captions are present;
- proposals and assumptions are not presented as confirmed current architecture.

Remove a diagram if it repeats a table or prose without adding structural understanding.
