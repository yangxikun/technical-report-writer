# HTML Style Guide

Use this reference when creating or substantially revising an HTML technical report.

## Visual Intent

The report should feel like an engineering decision document: quiet, precise, easy to scan, and comfortable for sustained reading. Avoid marketing composition, oversized decoration, nested cards, gradients, or large tinted backgrounds.

## Established Palette

Use this palette by default when no brand system is supplied:

| Role | Color | Usage |
|---|---|---|
| Deep navy | `#091540` | Body text, header, fixed TOC |
| Primary blue | `#1055C9` | Headings, links, important labels |
| Accent blue | `#7692FF` | Section numbers, left rules, focused borders |
| Pale blue | `#ABD2FA` | Thin borders, separators, subtle shadows, dark-surface secondary text |
| White | `#FFFFFF` | Page, cards, callouts, table headers |

Do not use `#ABD2FA` as a large-area content background. It should remain a weak structural color.

## Layout

- Main content is a constrained, centered column on white.
- The title band may use deep navy with white title text and pale-blue secondary text.
- On wide screens, reserve space for a fixed right-side TOC. Center it vertically and constrain height with internal scrolling.
- On narrow screens, return the TOC to document flow and collapse multi-column layouts to one column.
- Long API examples, event streams, and tool loops are vertical. Each provider receives a full-width row.
- Tables may overflow horizontally on small screens; do not crush columns until text becomes unreadable.

## Navigation

- Support primary and secondary TOC entries.
- TOC labels must exactly match content headings, excluding optional chapter numbers only when the content uses a separate number element.
- Every destination has a stable, unique ID.
- Validate all fragment links before delivery.

## Components

### Technical Figures

Use a full-width SVG figure for architecture, data-flow, workflow, sequence, or state diagrams. Generate the SVG with `fireworks-tech-graph` when that skill is available. Use a white background, thin pale-blue border, responsive image width, concise caption, and descriptive alt text. Read [diagram-guide.md](diagram-guide.md) before generating or embedding a technical figure.

### Cards

Use cards only for genuinely parallel alternatives or repeated items. White background, thin pale-blue border, radius no greater than 8px. Do not put cards inside cards.

### Tables

Use white headers with a stronger blue bottom rule. Avoid filled header bands unless the user requests them. Use tables for compact comparison, not long narrative prose.

### Callouts

Use white backgrounds with a blue left rule and subtle border. Labels should state the callout's function: `判断边界`, `注意`, `生产落点`, or `一句话结论`.

### Code Blocks

Default to Highlight.js `github` light theme loaded from cdnjs:

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.11.1/styles/github.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.11.1/highlight.min.js"></script>
<script>hljs.highlightAll();</script>
```

Use GitHub-like fallback styling: background `#f6f8fa`, text `#24292f`, border `#d0d7de`, radius `6px`. Mark blocks with `language-json` or `language-plaintext` as appropriate. Keep an offline fallback: syntax colors may disappear, but content and contrast must remain readable.

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
- confirm external theme URLs and source links;
- render the page and visually inspect the first viewport plus at least one dense example section.
- inspect every SVG diagram at desktop and mobile widths, confirm its asset path resolves, and verify its `viewBox`, labels, and connectors are not clipped.
