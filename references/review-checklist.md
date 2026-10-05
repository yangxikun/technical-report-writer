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

## Per-Diagram Reviewer (mandatory, one subagent per SVG)

Every generated SVG diagram gets its own forked `Explore`-type subagent, scoped to exactly that one diagram. Run all diagram reviewers in parallel, after the mechanical script passes. Never batch two diagrams into one reviewer and never fold diagram review into the whole-report pass — a reviewer reading prose skims geometry.

### Per-Diagram Brief Template

```
File: <absolute path to the report HTML>
Scope: ONLY the SVG inside the <figure> at lines <A>–<B> — the diagram answering
  "<named question>". Do not review the rest of the report.
Standards: /Users/<user>/.workbuddy/skills/technical-report-writer/references/review-checklist.md
           ("What The Diagram Reviewer Checks" below) and diagram-guide.md
Legend and color semantics: <e.g. green = write path, blue = read path, amber = dynamic
  content, red = invalidation; one meaning per color>
Established choices — do NOT flag these: <e.g. two-line labels at font-size 11 in the
  left label column are intentional>

Run the checks in "What The Diagram Reviewer Checks". Report in under 200 words as a
flat list: SEVERITY | element/coordinates | what is wrong | why it matters. Use
BLOCKER / SHOULD-FIX / NITPICK. Read-only: do not edit any file, do not propose
replacement markup.
```

### What The Diagram Reviewer Checks

- **Text–shape overlap, computed not eyeballed**: estimate every `<text>` width as `CJK chars × font-size + latin chars × 0.55 × font-size`; from its `x` and `text-anchor` derive the rendered span; check the span against the bounds of every `rect`, `line`, and neighboring `text` at the same vertical band. A side-column label that runs under a content box is the recurring defect — check every line of a multi-line label individually.
- **viewBox bounds**: no text span, connector, or marker extends past the viewBox edge or is clipped by it.
- **Marker pairing**: every line with `marker-end` references a defined marker id, and the marker's fill matches its line's stroke color. Lines declared in the legend as arrows must actually draw arrowheads.
- **Legend ↔ drawing consistency**: every swatch in the legend appears in the drawing with the same meaning; no color carries two meanings; semantic colors match the report's palette rules.
- **Prose consistency**: diagram labels use the report's terminology, numbers in the diagram (prices, TTLs, limits) match the surrounding prose and tables, and the caption states what the diagram actually shows.
- **Position semantics**: when an element's *position* encodes mechanism semantics (a breakpoint line at a block boundary, an order of layers, a before/after split), verify the position against the prose's mechanism description element by element — not just colors and labels. Recurring defect: a marker drawn at the "stable-looking" boundary while the prose says the mechanism places it elsewhere (e.g. an implicit breakpoint drawn before the dynamic block it actually follows). Read the relevant prose sections and check every positioned marker against them.

## Readability Reviewer (novice reader, one subagent for the whole report)

The expert reviewers above verify correctness. This reviewer verifies **comprehensibility** by deliberately lacking expert context. Run it after the whole-report review fixes land, so the text is stable.

Why a separate pass: the author and the expert reviewer share background knowledge and unconsciously fill gaps ("obviously X means Y"). A reader with only undergraduate fundamentals cannot, and that is where undefined terms, skipped steps, and unexplained examples surface.

### Independence Requirements

This reviewer must be a **standalone subagent**, not the main agent and not a fork:

- Launch it as a fresh Task invocation (`Explore` type, read-only) whose only inputs are the brief below. It must not inherit the conversation, the author's drafts, the research notes, or any prior reviewer's findings.
- One novice reviewer per review round. Never reuse an expert, per-diagram, or earlier novice subagent: their context already contains the answers. A verification round gets a **new** subagent.
- The main agent must not pre-answer, hint at, or summarize the report's content in the brief, and must not "simulate" the novice itself if the subagent is unavailable. In that case record the readability review as **not performed** in the delivery message.
- Do not run it in parallel with fixes that are still editing the report; the subagent must read the final stable text.

### Readability Brief Template

Keep the brief minimal. Do **not** include the conversation, the author's intent, the decision's backstory, or the other reviewers' findings — any of these leak the context the persona must not have.

```
File: <absolute path to the HTML report>

Role: You are a recent undergraduate graduate (computer-related major). You know course-level
  programming, data structures, networking, databases, HTTP and JSON. You have NO industry
  experience, NO prior knowledge of the products, protocols, frameworks, or internal jargon
  this report discusses, and nobody to ask. Your manager asked you to read this report and
  then explain its recommendation to the team.

Task: Read the ENTIRE report once, top to bottom, in page order — body text, tables, captions,
  code blocks and their comments, and text labels inside SVG diagrams. Do not skip sections
  and do not use web search or outside knowledge to fill gaps. If you can only follow
  something by guessing, treat it as not understood.

Output (under 400 words, at most 15 questions, most important first), in your own voice as
  the novice. One line each:
  <location: section heading or line> | <your question, phrased as a real question> | <gap type>
  Gap types: UNDEFINED-TERM, MISSING-STEP, UNCLEAR-REFERENT, UNEXPLAINED-WHY,
  UNEXPLAINED-EXAMPLE, UNSUPPORTED-LEAP, NO-BIG-PICTURE.
  Then add two lines:
  - Where you first felt lost: <location + one sentence>
  - Confidence you could explain the recommendation to a teammate: <0-100%> and the main reason.
  Read-only: do not edit any file, do not propose rewritten text, do not comment on visual
  design or factual accuracy.
```

### What Counts As A Question

- **UNDEFINED-TERM**: a term, acronym, field name, or product name used before (or without) being explained. Check first use only; later uses are fine.
- **MISSING-STEP**: the text jumps from A to C; a procedure, derivation, or data transformation skips the middle.
- **UNCLEAR-REFERENT**: "it", "this", "该方案", "上述机制" where more than one candidate exists.
- **UNEXPLAINED-WHY**: a design choice, default, or recommendation stated without a reason the novice can follow.
- **UNEXPLAINED-EXAMPLE**: a JSON payload, event stream, code block, or diagram shown without saying what to look at and what it demonstrates.
- **UNSUPPORTED-LEAP**: a conclusion or trade-off asserted before the evidence appears, or without visible reasoning.
- **NO-BIG-PICTURE**: the reader loses track of why the current section exists or how it connects to the decision.

### Author Triage Of Questions

| Situation | Action |
|---|---|
| A target reader would plausibly ask it | Answer **in the report**: one-sentence definition at first use (or a short glossary when five or more terms need it), insert the missing step, replace the vague pronoun with its noun, add a "because ..." sentence, annotate the example field by field, add a one-line roadmap at the section start |
| Same question appears several times | Fix the root cause once (usually a missing early definition or concept section), not each symptom |
| Below the report's declared audience, or off-topic | Skip; list it with a reason in the delivery message |
| Answer would require removing precision | Keep the precision and add the explanation beside it; do not turn the report into a tutorial |

After fixing, re-run the mechanical script (inserted text can break anchors, TOC, or tag balance). Run a verification pass with a **fresh** novice subagent when several `MISSING-STEP`, `NO-BIG-PICTURE`, or `UNSUPPORTED-LEAP` questions were fixed: pass it the earlier question list and the changed regions, and ask whether each question is now answerable from the page and whether the fixes created new confusion. A single added definition does not need a second pass.

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
- **SVG text overlapping shapes**: compute it, do not eyeball it. Estimate each `<text>` width as `CJK chars × font-size + latin chars × 0.55 × font-size`, add the text's `x` (or subtract for `text-anchor="middle"/"end"`), and check the resulting span against the bounds of every `rect`, `line`, and neighboring `text` it could touch. Multi-line labels in a side column must each fit the column — one long line that slips under a content box is the recurring defect.
- **fixed-TOC overlap**: compute it rather than eyeball it. Established layout is a 75%-of-viewport content column, left-aligned on wide screens with `margin-left: M` and a `position: fixed; right: R; width: T` TOC: clearance requires `M + 0.75V + gap < V - R - T`. With `M = clamp(28px, 3vw, 96px)`, `R = 24`, `T = 240`, gap ≈ 10px, the breakpoint resolves to `min-width: 1280px`. Verify the arithmetic at the breakpoint and at 1440/1920/2560.
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

The pass is required for a new report, a major rewrite, and any change that adds or removes a chapter, restructures layout, or rewrites CSS or payloads in bulk. Independently of that, the per-diagram reviewer is required whenever any SVG was created or modified this round — even on an otherwise minor revision.

The readability (novice reader) pass is required for a new report, a major rewrite, or any chapter that is added or substantially rewritten.

It is not required for a single-word fix, a one-line copy edit, or a change the user is watching in real time and will judge themselves — provided no SVG was touched. Run the mechanical script regardless — it costs seconds.
