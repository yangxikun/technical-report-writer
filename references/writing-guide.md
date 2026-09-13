# Writing Guide

Use this reference to shape substantial technical reports. The goal is not to imitate a named author. Combine useful strengths: abstraction, memorable principles, system coverage, practical judgment, precise definitions, concise examples, a unified vocabulary, and scenario-driven explanation.

## 1. Start With The Decision Pressure

Open with a concrete situation in which the choice matters. Show the sequence that creates pressure on the current design.

Example shape:

```text
A request begins as image analysis
-> a threshold triggers a database tool
-> the user asks a follow-up
-> the application must preserve state and evidence
-> the API choice becomes an orchestration decision
```

This creates a reason to learn the abstractions that follow. Keep the scenario short; it is a spine, not a fictional story.

## 2. Build One Conceptual Model

Before listing parameters, name the unit each system organizes:

- the core object;
- its responsibility;
- what can exist independently;
- what must be nested inside it;
- how it crosses a turn or process boundary.

Use a small relationship diagram or a three-column comparison only when it sharpens this model. Avoid introducing terminology without showing how the terms relate.

## 3. Explain Evolution

For a successor design, use this chain:

```text
Original job
-> new workload pressure
-> abstraction strain
-> responsibility split or new primitive
-> benefit
-> new cost
```

Do not write “newer is better.” State which workloads expose the old design's limits and which workloads do not.

## 4. Cover The Decision Dimensions

For each material alternative, answer only the dimensions that affect the choice:

- `What`: the mechanism and core objects;
- `Why`: the problem it exists to solve;
- `How`: the protocol or workflow;
- `When`: conditions under which it is the better fit;
- `When Not`: conditions under which the simpler or different option wins;
- `Trade-off`: complexity, portability, cost, latency, control, operations, and lock-in;
- `Verification`: what metrics or tests prove the decision works.

## 5. Separate Epistemic Categories

Use precise language for:

- **Documented fact:** explicitly supported by an authoritative source.
- **Inference:** a conclusion derived from facts and architecture.
- **Recommendation:** a choice conditioned on stated priorities.
- **Example value:** illustrative runtime data, not a product guarantee.
- **Unknown:** information the source does not establish.

Avoid turning an internal benchmark, beta feature, or model-specific behavior into a universal claim.

## 6. Use Minimal Representative Examples

An example should reveal the contract, not showcase syntax volume.

For API comparisons:

- show raw JSON request bodies and response/event fragments by default;
- include system-level instructions in every request where they matter;
- split the first model request, model tool call, tool execution result, and second model request;
- show the correlation ID used between call and result;
- show streaming examples per API in separate vertical blocks;
- distinguish complete response JSON from SSE framing;
- annotate what the reader should notice immediately after the example.

Use SDK code only when the task is implementation-language-specific or the user asks for runnable code.

## 7. Use Diagrams For Relationships

When a report explains architecture, data movement, workflow, lifecycle, or responsibility boundaries, use an appropriate technical diagram before adding more prose. The figure should answer one explicit question and use the same vocabulary as the text.

- Architecture diagrams explain structure and ownership.
- Data-flow diagrams explain movement, transformation, storage, and trust boundaries.
- Workflow or sequence diagrams explain ordering, branches, retries, and actor interactions.
- State diagrams explain lifecycle and valid transitions.

Read [diagram-guide.md](diagram-guide.md) for tool routing, construction rules, and verification. Do not add a diagram when a compact table or three sentences communicate the same idea more clearly.

## 8. Make Trade-offs Concrete

Trade-offs should name who gains and who pays.

Weak:

> The new API is more powerful but more complex.

Better:

> The platform can preserve tool and reasoning context, reducing application-side transcript assembly. In exchange, the consumer must handle a typed output array and event state machine instead of assuming one assistant message.

## 9. End With Transferable Principles

Use two to four principles when they genuinely summarize the report. Good principles are conditional and operational.

Examples:

- Choose the protocol by task complexity, not endpoint familiarity.
- Unify business semantics, not every provider capability.
- Platform-managed state reduces orchestration work; it does not replace audit history.
- Treat tool calls as identity-bearing round trips.

## 10. Edit For Precision

During revision:

- delete repeated descriptions of the same capability;
- replace abstract claims with one mechanism or example;
- shorten headings without changing their meaning;
- keep terms stable across the report;
- ensure a table does comparison work, not narrative work;
- keep paragraphs focused on one claim;
- remove a section if the reader can still make the decision without it.

## 11. Avoid These Failure Modes

- Documentation transcription without synthesis.
- Feature matrices that omit responsibility and trade-offs.
- Many examples that repeat the same boundary.
- Principles stated as universal laws.
- Provider-specific objects leaked into the recommended business domain model.
- “Supports X” without explaining whether the platform or application executes X.
- Treating caching, storage, and conversation state as the same concept.
- Treating protocol design as proof of model quality.
- Decorative diagrams, unlabeled arrows, or figures that contradict the prose.
