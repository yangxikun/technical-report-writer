# Review Checklist

This file is the contract for the review pass. It is read by a **reviewer subagent**, not by the author.

The reviewer is read-only by design: it inspects and reports, it does not edit. The author keeps ownership of every change, because the reviewer lacks the conversation history and cannot know which choices were explicitly requested by the user.

## Reviewer Brief Template

Spawn a read-only subagent (`Explore` type is sufficient and cannot accidentally write). The reviewer has not seen the conversation, so the brief must be self-contained:

```
File: <absolute path to the HTML report>
Standards: /Users/<user>/.workbuddy/skills/technical-report-writer/references/review-checklist.md
           (plus html-style-guide.md and diagram-guide.md in the same directory)

Context: <what the report is about, who reads it, what decision it supports>
Changed in this round: <concrete list — sections added/removed, CSS touched, payloads reformatted>
Established choices — do NOT flag these as problems: <e.g. semantic diagram colors are
  intentional; chapter N was deliberately deleted; TOC is deep-navy-on-white by request>

Run the mechanical checks in the standards file, then read the changed regions closely.
Report in under 300 words as a flat list: SEVERITY | location (line or section) | what is
wrong | why it matters. Use BLOCKER / SHOULD-FIX / NITPICK. If a category is clean, say so
in one line. Do not rewrite the report and do not propose full replacement markup.
```

Do not ask the reviewer to "check everything and fix what it finds" — that pushes judgment onto an agent without context and produces generic, low-signal output.

## What The Reviewer Checks

### 1. Mechanical (run the validation script from html-style-guide.md)

- Tag balance for `section`, `div`, `pre`, `nav`, `svg`, `table`, `figure`, `code`.
- Every `href="#id"` resolves to an existing `id`.
- Every `language-json` block parses with a real JSON parser.
- No `<pre><code>` block starts or ends with a newline.
- Every `marker-end="url(#x)"` has a matching `<marker id="x">`.
- No leftover `{{TOKEN}}` template markers.

### 2. Consistency After Edits

This is where regressions actually live. Check specifically:

- **Chapter numbering**: `secno` spans, heading text, TOC labels, and section `id`s all agree after any insertion or deletion. A deleted chapter leaves stale numbers in three places.
- **Cross-references**: prose that says "见第 7 章" or "上一节" still points at the right target.
- **Orphaned paragraphs**: an edit that replaced a block may have duplicated or stranded the paragraph that followed it. Grep for repeated opening phrases.
- **TOC ↔ heading drift**: labels must match destination headings exactly.
- **Style rules applied uniformly**: if one callout became semantic, all of them did; if one payload was pretty-printed, all of them were.

### 3. Substance

- Facts are attributed and separated from analysis and recommendation.
- Field names, method names, header names, and error codes match the cited specification version exactly — including the `...Error` suffix when the spec names a schema type.
- Examples are internally consistent: **one request `id` must not produce two different results**. A `complete` response and an `input_required` response cannot share an id; a retry after supplying input uses a new id, while the `input_required` response itself keeps the original id.
- Every tool, endpoint, or entity invoked in an example was introduced earlier, or its absence is explained (e.g. paginated list with a non-empty `nextCursor`).
- One scenario carried through: a weather example should not produce an airline booking error message.
- External links resolve. Watch for malformed URLs (stray `userinfo@` from an email paste, `http://` where the site is HTTPS-only) and opaque shortlinks — resolve shorteners to their canonical target so the reader can judge the source.
- Recommendations are conditional ("when X holds, choose Y"), not universal law.
- Cross-cutting claims agree: if section 2 says a capability is deprecated, the migration table cannot tell the reader to port it.
- Every diagram answers a named question and is explained in surrounding prose.
- No section merely transcribes documentation.

### 4. Rendered Appearance

Source-level checks miss rendered defects. The reviewer should look at the actual page for:

- blank first/last rows inside code blocks;
- arrowheads whose color does not match their line, or lines declared in the legend but drawn without arrowheads;
- **collinear overlapping edges**: a request line and its response line drawn between the same two nodes in the same color coincide into one stroke, silently falsifying a "solid = request, dashed = response" legend. Offset the pair and give them distinct colors.
- clipped SVG labels or connectors at desktop and mobile widths, and labels sitting on top of connectors;
- **fixed-TOC overlap**: compute it rather than eyeball it. With a `max-width: W` centered column and a `position: fixed; right: R; width: T` TOC, the breakpoint must satisfy `V/2 + W/2 < V - R - T`, i.e. `V > W + 2R + 2T`. A 860px column with `right: 24px` and a 230px TOC needs roughly 1400px, so a 1280px breakpoint overlaps.
- text overlapping or hidden under the fixed TOC;
- callouts that are visually indistinguishable from each other;
- tables crushed past readability at narrow widths.

### 5. Interactive Behavior

When the report ships JS:

- Anchor highlighting covers **every** TOC level. Observing only `section[id]` leaves secondary entries dead — observe `section[id], h3[id]` and filter to ids that exist in the TOC.
- When several anchors sit in the observer band, pick the **lowest** one; picking the highest makes a parent chapter permanently shadow its subsections.
- `scroll-margin-top` must apply to every anchored element, not just `section[id]`.
- Guard lookups so a heading without a TOC entry cannot throw.
- Check for duplicate declarations of the same property after several rounds of edits (e.g. `scroll-behavior` set twice).

## Acting On The Report

The author triages, then fixes:

- **BLOCKER** — factual error, broken structure, invalid JSON, dangling anchor, stale chapter number. Fix before delivery, no exceptions.
- **SHOULD-FIX** — real defect with a clear cheap fix. Fix unless it contradicts an explicit user instruction.
- **NITPICK** — taste. Apply only if it costs nothing and does not churn established style.

When a finding conflicts with something the user explicitly asked for, keep the user's choice and say so in the delivery message. Do not silently revert a requested change because a reviewer without context disliked it.

If the reviewer reports a BLOCKER, fix it and re-run the mechanical checks. A second full review pass is only warranted when the fix was structural (chapter insertion/deletion, layout rework, renumbered examples, redrawn diagram), not for a typo.

When you do run a second pass, brief it as *verification*: list each fix you applied and ask the reviewer to confirm it landed correctly and introduced no regression. This reliably catches half-fixes — a diagram whose `marker-end="none"` was removed but whose two edges still coincide into one stroke, or a renamed field that survives in one of five places. Do not assume a fix worked because you intended it to.

## When To Skip The Review Pass

The pass is required for a new report, a major rewrite, and any change that adds or removes a chapter, restructures layout, or rewrites CSS or payloads in bulk.

It is not required for a single-word fix, a one-line copy edit, or a change the user is watching in real time and will judge themselves. Run the mechanical script regardless — it costs seconds.
