# HTML Style Guide

Use this reference when creating or substantially revising an HTML technical report.

## Visual Intent

The report should feel like an engineering decision document: quiet, precise, easy to scan, and comfortable for sustained reading. Avoid marketing composition, oversized decoration, nested cards, gradients, or large tinted backgrounds.

## Established Palette

Use this palette by default when no brand system is supplied:

| Role | Color | Usage |
|---|---|---|
| Deep navy | `#091540` | Body text, header |
| Primary blue | `#1055C9` | Headings, links, important labels |
| Accent blue | `#7692FF` | Section numbers, left rules, focused borders |
| Pale blue | `#ABD2FA` | Thin borders, separators, subtle shadows, dark-surface secondary text |
| White | `#FFFFFF` | Page, cards, callouts, table headers |

Do not use `#ABD2FA` as a large-area content background. It should remain a weak structural color.

## Semantic Accent Palette

Prose chrome stays in the blue system, but callouts and diagrams are not confined to it. When color carries meaning, use these semantic accents consistently:

| Meaning | Color | Usage |
|---|---|---|
| Problem / error / rejection | `#B03A2E` (strong), `#C0392B` (lines), `#E3A59B` (thin dense lines) | "before" side of comparisons, 4xx/5xx steps, failure transitions |
| Solution / success / credentials | `#1E7A46` (strong), `#2E9E62` (lines), `#BFE3CD` (secondary text on green) | "after" side of comparisons, token/code grants, healthy states |
| Warning / human decision point | `#D48806` (strong), `#c9862b` (lines), `#9A6200` (labels) | 注意 callouts, consent steps, manual approvals |
| Role distinction | `#6B5CA5` / `#7C5CD6` (violet), `#0F8B6C` (teal) | telling actors apart when blue alone is ambiguous (e.g. AS vs Server vs Client) |

Rules: one meaning per color across the whole report; colored SVG arrows need matching `marker` arrowheads (define one marker per line color); do not spread semantic colors into body prose or the TOC.

## Layout

- Main content is a constrained, centered column on white.
- The title band may use deep navy with white title text and pale-blue secondary text.
- On wide screens, reserve space for a fixed right-side TOC. Center it vertically and constrain height with internal scrolling.
- **Compute the fixed-TOC breakpoint, do not guess it.** With a centered column of `max-width: W` and a TOC at `position: fixed; right: R; width: T`, the content's right edge is `V/2 + W/2` and the TOC's left edge is `V - R - T`. Overlap disappears only when `V > W + 2R + 2T`. For `W=860, R=24, T=230` that is about 1400px — so a `min-width: 1280px` media query silently hides tables and code blocks under the TOC. Add margin and round up (1440px), or narrow the TOC.
- On narrow screens, return the TOC to document flow and collapse multi-column layouts to one column.
- Long API examples, event streams, and tool loops are vertical. Each provider receives a full-width row.
- Tables may overflow horizontally on small screens; do not crush columns until text becomes unreadable.

## Navigation

- Support primary and secondary TOC entries.
- The fixed right-side TOC uses a white background with dark text (deep navy for the label and all entries, primary and secondary alike — distinguish levels by indentation and font size only, not color); never fill it with deep navy. Apply these colors inside the wide-screen media query only — on narrow screens the TOC remains inside the dark header band and keeps its light-on-dark base colors (secondary entries there use the same light color as primary entries too).
- TOC labels must exactly match content headings, excluding optional chapter numbers only when the content uses a separate number element.
- Every destination has a stable, unique ID.
- Validate all fragment links before delivery.
- Give TOC entries a visible active state: highlight on click immediately, and follow scroll position with an `IntersectionObserver` over the sections (a `rootMargin` band near the viewport top works well). Active style: light blue background (`#E7EFFC`), deep-navy bold text, 3px primary-blue left bar. Add `scroll-behavior: smooth` and `scroll-margin-top` on anchored sections so jumps do not stick to the viewport edge.
- Observe **every anchored level**, not just `section[id]`. If secondary TOC entries point at `h3[id]`, observe `section[id], h3[id]` (filtered to ids that actually appear in the TOC) and apply `scroll-margin-top` to both. When multiple anchors are inside the band, activate the **lowest** one — selecting the highest lets a parent chapter permanently shadow its own subsections.

## Components

### Technical Figures

Use a full-width SVG figure for architecture, data-flow, workflow, sequence, or state diagrams. Generate the SVG with `fireworks-tech-graph` when that skill is available. Use a white background, thin pale-blue border, responsive image width, concise caption, and descriptive alt text. Read [diagram-guide.md](diagram-guide.md) before generating or embedding a technical figure.

### Cards

Use cards only for genuinely parallel alternatives or repeated items. White background, thin pale-blue border, radius no greater than 8px. Do not put cards inside cards.

### Tables

Use white headers with a stronger blue bottom rule. Avoid filled header bands unless the user requests them. Use tables for compact comparison, not long narrative prose.

### Callouts

Use semantic variants, not one white box for everything. Labels should state the callout's function: `判断边界`, `注意`, `生产落点`, or `一句话结论`.

- Default (info/principle): light blue tint `#F5F9FF` background, `#D6E4F8` border, primary-blue left rule and label.
- `注意` (warning): amber family — `#FFF7E4` background, `#EFD9A7` border, `#D48806` left rule, `#9A6200` label. A warning must be recognizable at a glance without reading the label.

```css
.callout { background:#F5F9FF; border:1px solid #D6E4F8; border-left:4px solid #1055C9; border-radius:6px; padding:14px 18px; margin:20px 0; }
.callout .label { font-size:12px; font-weight:700; letter-spacing:1px; color:#1055C9; margin-bottom:6px; }
.callout.warn { background:#FFF7E4; border-color:#EFD9A7; border-left-color:#D48806; }
.callout.warn .label { color:#9A6200; }
```

### Code Blocks

Default to Highlight.js `github` light theme loaded from cdnjs:

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.11.1/styles/github.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.11.1/highlight.min.js"></script>
<script>hljs.highlightAll();</script>
```

Use GitHub-like fallback styling: background `#f6f8fa`, text `#24292f`, border `#d0d7de`, radius `6px`. Mark blocks with `language-json`, `language-http`, or `language-plaintext` as appropriate. Keep an offline fallback: syntax colors may disappear, but content and contrast must remain readable.

#### JSON And Payload Formatting

A protocol payload is evidence the reader has to parse field by field. Never ship minified or hand-wrapped JSON.

- **Pretty-print every JSON payload**: 2-space indentation, one key per line, expanded nested objects and arrays. Do not compress a request into two or three long lines to "save space" — vertical length is cheap, comprehension is not.
- **Do not escape non-ASCII**: keep Chinese and other Unicode values literal (`ensure_ascii=False` when generating with Python). Escaped `\uXXXX` sequences are unreadable.
- **No leading or trailing blank lines inside `<pre><code>`**. `<pre>` preserves whitespace literally, so a newline right after `<code>` renders as an empty first row. Open the tag directly onto the first content character and close it directly after the last one.
- **Mixed HTTP + JSON blocks**: keep the request line and headers as plain lines, then exactly one blank line, then the pretty-printed JSON body. Mark these `language-http`.
- **Keep key order semantic**, not alphabetical: `jsonrpc` / `method` / `id` first, then `params`, then nested `_meta`. Readers scan for the envelope before the payload.
- **Elide, do not truncate**: shorten long opaque values with an explicit `"..."` suffix (e.g. a JWT) instead of cutting a line mid-token.

If payloads are generated or reformatted by script, run the transform **once** over the whole file. Re-running a formatter over already-formatted blocks is the common source of injected blank lines — a bug that is invisible in the source diff but obvious in the rendered page.

### Inline Code

Use white background with a pale-blue border and deep-navy text. Do not give inline code a large tinted pill appearance.

## Typography

- Do not scale font size with viewport width.
- Body text should remain dark navy, not saturated link blue.
- Use primary blue for headings and links, accent blue for small emphasis.
- Keep letter spacing at zero except compact uppercase labels.
- Use concise headings; match heading scale to the section, not to the page title.

## Responsive Checks

Verify at minimum:

- wide desktop with fixed TOC;
- narrow desktop/tablet where the TOC returns to flow;
- mobile width where cards stack and code scrolls horizontally;
- no text overlaps, clipped headings, or content hidden under the TOC;
- long TOC labels wrap cleanly and remain clickable.
- diagram labels remain readable and no connectors or nodes are clipped.

## Structural Validation

Before delivery:

- count opening and closing `section`, `article`, `div`, `pre`, and `nav` tags;
- check every `href="#id"` resolves;
- verify each linked heading matches its TOC label;
- parse every `language-json` block with a real JSON parser, and assert no `<pre><code>` block starts or ends with a newline;
- confirm external theme URLs and source links;
- render the page and visually inspect the first viewport plus at least one dense example section.
- inspect every SVG diagram at desktop and mobile widths, confirm its asset path resolves, and verify its `viewBox`, labels, and connectors are not clipped.
- confirm every colored SVG line has a same-colored arrowhead marker and that every referenced `marker` id exists.

A single script can cover the mechanical half of this list:

```python
import re, json
src = open(path, encoding='utf-8').read()
for t in ['section', 'div', 'pre', 'nav', 'svg', 'table', 'figure', 'code']:
    o, c = len(re.findall(rf'<{t}[ >]', src)), len(re.findall(rf'</{t}>', src))
    assert o == c, (t, o, c)
ids = set(re.findall(r'id="([^"]+)"', src))
assert not [h for h in re.findall(r'href="#([^"]+)"', src) if h not in ids]
for m in re.finditer(r'<pre><code[^>]*>([\s\S]*?)</code></pre>', src):
    body = m.group(1)
    assert not body.startswith('\n') and not body.endswith('\n')
    i = body.find('{')
    if i >= 0:
        json.loads(body[i:])           # HTTP headers may precede the JSON body
markers = set(re.findall(r'<marker id="([^"]+)"', src))
assert not set(re.findall(r'marker-end="url\(#([^)]+)\)"', src)) - markers
```
