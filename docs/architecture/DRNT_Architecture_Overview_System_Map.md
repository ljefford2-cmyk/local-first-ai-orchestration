## DRNT Architecture Overview — System Map

Status of this document: Architecture overview for the committed DRNT corpus. This is the document the Authority Map is deliberately not: where the map governs how a claim is classified, this map draws how the layers form a single operating whole. Read the Authority Map ( `00_DRNT_Authority_Map_final` ) first — it is the control surface, and where anything here conflicts with it, the map wins.

What this document is. A layer-by-layer picture of DRNT as one system: the seat that holds authority, the gateway that routes rather than reasons, the bounded layer that performs the work, the immutable record that work writes into, and the downstream chain that turns that record into organizational learning without any actor grading its own work. It carries the honest split — built / specified / gated / pending — into the architecture picture itself, not only into the claims about it.

What this document is not. It is not a specification of any single component, not an implementation report, and not a claim that the system runs end to end. The built portion is a clean spine inside a larger specified architecture, and this map says so at every layer rather than letting the drawing imply more than the testbed has proven.

## 0. The one altitude this map is drawn at

DRNT sits above the professional instruments, not inside one department’s tool. The problem it addresses is that an organization operates inside one integrated system but observes it through fragmented professional lenses — research, engineering, maintenance, operations, management, executive — each correct and each incomplete. The failure that produces is not a shortage of intelligence; it is a shortage of shared understanding, and a slow drift between the reported state of the organization and its actual state, carried by the people who have learned to keep the reported state acceptable.

The architecture’s job is to protect the situational awareness of the person responsible for an outcome: to help them know what is true, what has changed, what does not fit, and what needs attention while there is still time to act. Every layer below exists to serve that one thing, or it does not belong in the map.

This is execution-class work, not exploration-class. An exploration system optimizes for discovery and treats controlled non-determinism as a feature — the same input may produce a different decomposition by design, because the system is searching a space. An execution system does a defined thing consistently and auditably: given the same input, policy version, and capability registry, it produces the same routing decision on Tuesday that it produced on Monday. DRNT is the second kind. The defining question is not “how clever was the answer” but “did the system do exactly what it was supposed to do, and can we prove it?” The boringness is the value.

The name states the discipline. DRNT — Don’t Reason, Navigate & Task: the system must navigate to the right bounded task before reasoning, and must never use reasoning as a substitute for authority, policy, containment, or verification. Reasoning is permitted as a tool inside a bounded task; it is never permitted to stand in for the seat of authority, the governing policy, the containment boundary, or the independent check. The name is the rule, not a brand.

## 1. How to read the map: the seat frames it, the operating sequence runs through it

DRNT can be read two ways, and the map uses both deliberately.

By authority, top down. The human authority seat is the frame. It is the layer the whole system exists to protect, and the layer that no software component is permitted to occupy. Everything below it assists, proposes, routes, evaluates, monitors, and records — none of it becomes the seat.

By operating sequence, front to back. Work enters, is routed, is gated, is performed under containment, is recorded, and is then carried through a downstream chain that returns to the seat. This is the data path the source material walks, and it is the order the layers are numbered below.

The two views meet at the same place. The seat opens the map as the authority frame, the operating sequence runs through the layers as the data path, and the downstream chain returns the result to the seat. The seat bookends the architecture.

A single-line statement of the whole, which the rest of the document expands:

```
                          HUMAN AUTHORITY SEAT
                  (the seat the architecture protects;
                   no software component may occupy it)
                                  │
        ┌─────────────────────────┴─────────────────────────┐
        │                 operating sequence                 │
        │                                                    │
   intent in
        │
   ┌────▼─────────────────────────────────────────────────┐
   │  L1 GATEWAY / ROUTER                                  │  ── Tier 1: built
   │  routes · gates · dispatches · records                │
   │  (route, don't reason)                                │
   │  capability registry · WAL gate · context boundary    │  (Tier 1: built)
   └────┬─────────────────────────────────────────────────┘
        │
   ┌────▼─────────────────────────────────────────────────┐
   │  BOUNDED EXECUTION LAYER                              │
   │  performs the task L1 dispatches                      │
   │   ├─ built state:  sandboxed tool-leases              │  ── Tier 1: built
   │   │                (Docker + seccomp)                 │
   │   └─ gated state:  roaming L3 agents                  │  ── Tier 2: specified,
   │                    (held pending stronger isolation)  │     deliberately not built
   └────┬─────────────────────────────────────────────────┘
        │   writes the receipt
   ┌────▼─────────────────────────────────────────────────┐
   │  AUDIT / RECEIPT LAYER                                │  ── Tier 1: built
   │  append-only · SHA-256 hash-chained · replayable      │
   │  (the immutable record execution writes into;         │
   │   the producer can append but never amend)            │
   └────┬─────────────────────────────────────────────────┘
        │
        │   ════════ the built portion ends at the receipt ════════
        │   everything downstream is specified or pending, not built
        │
   ┌────▼──────────────────┐
   │ ① INDEPENDENT          │  ── Tier 2: specified, NOT built
   │    EVALUATION          │     (separate reader of the record;
   │                        │      barred from being its producer)
   └────┬──────────────────┘
        │
   ┌────▼──────────────────┐   ┐
   │ ② AUTHORITY-DOCUMENT   │   │
   │    RESOLUTION /        │   │  ── Tier 2: specified (doctrine)
   │    MEANING             │   │
   └────┬──────────────────┘   │  grouped under
        │                      ├─ "human follow-up,"
   ┌────▼──────────────────┐   │  but distinct stops —
   │ ③ HELD HUMAN SEAT /    │   │  not one fused seat
   │    ACTION              │   │  ── Tier 2: specified (doctrine)
   └────┬──────────────────┘   ┘
        │
   ┌────▼──────────────────┐
   │ ④ ORGANIZATIONAL       │  ── Tier 2: specified
   │    LEARNING            │     (the loop's end; intent, not built)
   └────┬──────────────────┘
        │
        └────────────► returns to the HUMAN AUTHORITY SEAT
                       (the cycle repeats)
```

SHEPUF — the bounded job-briefing structure (Sequence of events, Hazards, Employee assignments, Procedures, Understanding, Follow-up) — is not drawn as a box because it is not a layer. It is the doctrine that spans the two ends of this loop: its front half (S–U) establishes provenance before a bounded job begins, and its back half (F) is the human follow-up that stations ② and ③ structure. It is defined in operating doctrine but has no committed home in this corpus yet; it is named here, not relied on as implemented, and its canonical placement is a later follow-up/evaluation doctrine document. (See §9.)

## 2. The human authority seat — the layer software instruments but never occupies

The seat is the function that turns prose policy, ambiguous input, or contested meaning into a binding decision — and it is held by a human, or it is not held. This is the architecture’s load-bearing layer, and the one most often misdrawn, so the map states its boundary without softness.

Software may instrument the seat. It may not occupy it. To instrument means: route the request to the seat, gate on the seat’s output, record what the seat decided, version the corpus the seat decided against, and replay later decisions against the snapshot that was live at the time. To occupy means: perform the derivation itself — read the prose, decide what it requires, weigh completeness, capture intent — and have that derivation trusted as if a held seat had made it. The first is the system’s job. The second is the precise failure the architecture exists to forbid.

The reason this cannot be automated is not a present limitation to be patched later. The act of determining what policy prose actually requires is not deterministically extractable from the text — the obligations are not sitting in the sentence waiting to be pulled out, so there is no function from text to rule. A model placed in that seat does not derive the answer from the authority document; it continues from the nearest plausible text, fluently and confidently, and wraps the result in a record that looks clean in every direction. The seat stays empty no matter which model sits in it. A human holding the output against the authority document is the only element that catches it.

The seat therefore appears in the map twice: as the authority frame that opens it, and as station ③ in the downstream chain, where it decides action on what the evaluator surfaces.

Both are the same principle — authority over irreversible action stays with a human — and neither is a software component.

Tier: specified (doctrine). The seat is not a built component and cannot be one; what is built is the instrumentation around it. (Authority Map §5; seat doctrine.)

## 3. L1 gateway / router — route, don’t reason

L1 is the entry plane and the discipline the architecture is named for. It coordinates execution without becoming the primary reasoning engine. Given a request, it does four things and no more at this layer: it routes to the right bounded capability, gates on policy and trust, dispatches the authorized work, and records the decision path by writing a receipt into the audit layer.

The questions L1 answers are routing questions, not reasoning questions: what type of work is this, which capability should handle it, what context is allowed to cross which boundary, what trust level applies, and whether human approval is required before anything consequential happens. Given the same input, policy version, and capability registry, L1 returns the same routing decision every time. It does not reason about decomposition; a system that reasoned about which interpretation was meant would have to pick one, and picking wrong is the failure the architecture is built to prevent. L1 routes each instance against its governing authority and lets the seat rule where ruling is required.

Three controls sit at this layer and are part of the built routing discipline:

- Capability registry — the unit of permission. Capabilities exist at design time with known scope. They do not inherit trust from a lead agent; each holds its own permission for its own job. Default deny, explicit allow.

- WAL gate (Workflow Autonomy Levels, WAL-0 through WAL-3) — the unit of authority. Trust-tier gating on what an action is permitted to do, from propose-only through bounded execution. Trust is earned per capability and can be demoted as well as promoted; a change to a model, prompt, tool schema, or policy resets it.

- Context boundary — the unit of shareability. Sensitivity and shareability handling before data crosses a boundary: the agent never automatically receives unrestricted organizational context. (The map names this layer only at the level the read sources support — as the gate that enforces shareability and sensitivity before data moves. Its de-identification and named-entity rules are governed by a source not yet read into this corpus and are deliberately not drawn here.)

Boundary with the audit layer (stated exactly). L1 _writes the receipt_ — recording is an act L1 performs. The audit layer _is the immutable record the receipt is written into_ . L1 records into it and can append to it; it cannot amend it. “Records” at L1 means the act of writing; it does not mean L1 owns or can rewrite the record.

Tier: built. Route-don’t-reason routing, the capability registry, WAL gates, and the context boundary were found implemented on the testbed. “Built” here means proven runnable on a single end user’s personal-desktop testbed — a proof that the governance and routing primitives are real and runnable, never a claim of a production or scaled system. (Authority Map §3.)

## 4. Bounded execution layer — performs the task, in one of two trust states

This is the layer that actually performs the work L1 dispatches. It is drawn as one layer with two trust states, not as two sibling layers, because the source material treats sandbox dispatch, runtime isolation, and agent execution as a single containment-and-execution region governed by one dial: how strong the containment is determines what is permitted to run inside it.

Built state — sandboxed tool-leases. What runs today is bounded execution under Docker + seccomp: each task receives a bounded execution envelope — bounded context, bounded tools, bounded authority, bounded network access, bounded credentials, bounded time. There is no roaming agent authority; the layer performs the specific task it was leased, inside the envelope it was given, and returns observations, drafts, proposals, or structured results. Agents propose; they do not self-authorize.

Gated state — roaming L3 agents. The architecture specifies a layer where agents perform broader bounded work, and it is deliberately not built. It is gated on containment strength: the agent layer is not stood up while the implemented sandbox is Docker + seccomp rather than the stronger isolation the spec calls for (Firecracker / microVM / rootless Podman / network namespaces). This is the architecture enforcing its own containment-before-capability rule on itself — not an unfinished gap, but a hold the design imposes deliberately. Related named design choices at this layer (OpenShell / NemoClaw sandbox-supervisor integration) exist as specification with no implementing code and are carried as intended architecture, never as built.

The two states are the same box at two settings of the trust dial. The map shows the dial rather than implying a built execution layer with a separate finished agent layer above it.

Tier: the built state is Tier 1 (sandboxed tool-leases under Docker + seccomp, found implemented). The gated state is Tier 2 — specified, deliberately not built, pending stronger isolation. Stronger isolation itself is Tier 2: present in spec prose as the intended containment tier, not implemented. (Authority Map §3.)

## 5. Audit / receipt layer — the immutable record the work writes into

Every meaningful event enters a single append-only stream: routing decisions, WAL state changes, context-packaging actions, model responses, human decisions, overrides, anomalies, and failures. This turns the operation into a sequence of bounded, reviewable facts — not a transcript of what was said, but a record of what happened, preserving the chain between request, context, model, result, human judgment, and follow-up.

One property of this layer is load-bearing for everything downstream of it: because the stream is append-only, the system that performs the work can add to the record but can never amend it. This is not a storage convenience. It is the physical form of the evaluator’s independence (§6): a separate evaluator can read the record as evidence rather than as the work-performing system’s preferred account of itself. A record the producer could quietly rewrite would be a record that certifies its own producer — the precise failure the architecture exists to forbid. An immutable record is the precondition for independent evaluation.

This layer carries a discipline the corpus states repeatedly and the map preserves: a receipt is not the event, a summary is not the source, an access log is not the decision record, and behavioral acceptance is not correctness. The record proves that an act occurred and what its inputs and outputs were. It is structurally incapable, on its own, of guaranteeing the act was sound — that judgment belongs to the stations downstream, and to the seat. The audit layer produces a trustworthy record; it does not certify the work the record describes.

The built portion of the architecture ends here. The receipt is where what-is-built stops. Everything in §§6–8 is specified or pending placement, and the map draws it as exactly that.

Tier: built. Append-only, SHA-256 hash-chained, replayable receipts were found implemented on the testbed. (Authority Map §3.)

## 6. Station ① — Independent evaluation (specified, not built)

Past the receipt, the architecture’s intended shape is not `task → record → done` . It is a chain of distinct stations, and the first is an independent evaluator. This station is specified and not built, and the map holds that line exactly, because it is the place the corpus is most at risk of claiming more than exists.

What it is specified to be. A separate evaluator — small, tightly scoped, built for evaluation and nothing else — that reads the immutable record independently from the system that produced it, and is barred from being the actor that produced the record. Its job is discrepancy detection, follow-up analysis, and escalation proposals. Its verdict rests on reproducible comparison against recorded evidence, not on a fresh generative pass: it asks whether the interpretation matches what the record shows, returning the same verdict against the same evidence every time. A separate model that merely offered a fresh opinion would reintroduce the non-reproducibility the design rejects.

Why it must be separate, stated as the rule it enforces. No actor — human or model — may serve as the independent certifier of its own prior action. There are no exceptions. This bars self-verification, not authority: an actor may hold authority and decide, but may not then stand as the independent check on that same action. Applied to the record, the consequence is precise — the system that produces the record cannot be the system that judges it. A model grading its own output is not evaluating; it is the first perspective running a second time, because the same judgment that elevated one signal and suppressed another during the work will elevate and suppress the same way during self-review. Two independent reasons make this structural rather than advisory: a system cannot catch its own omission (the thing that would flag the gap is the thing that omitted it, and fluency conceals the gap), and a second generative pass is not reproducible (so “the system checked its work” is a claim that cannot be reproduced, which means it cannot be audited, which means it cannot be the authority).

What exists today. The audit-record foundation this evaluator would read is built (§5). A build-time multi-model review practice exists as doctrine. A separate running evaluator component does not. Until that component exists as a separate running process reading the immutable record independently from its producer, evaluator behavior is architectural intent, not implemented runtime behavior. No document in this corpus — including this map — is to describe a live runtime evaluator until that component exists. Four statements are true at once and the map holds all four: the record is built, the separation principle is absolute, the independent evaluator is specified, and the implementation is not yet complete.

Tier: specified, NOT built. (Authority Map §3 and §5 — the one framing that must not drift.)

## 7. Stations ② and ③ — Human follow-up: meaning, then action, as two distinct seats

The chain does not pass from evaluation straight to action. Two distinct stations sit between them, and the map draws them as distinct stops — they may be visually grouped under “human follow-up,” but they are not one seat. Collapsing them is the failure the corpus is trying to prevent.

Station ② — Authority-document resolution / meaning. When terminology or interpretation is contested, resolution comes from the governing authority document attached to the relevant entity — not from statistical frequency, historical usage, or similarity to prior work. The evaluator may identify ambiguity and surface candidate interpretations; it is structurally prohibited from deciding among them. Enough accumulated evidence never becomes authority, no matter how much accumulates. This station determines which interpretation is authoritative, against the authority document, and where required, a licensed professional rules on the interpretation by reference to that authority document.

Station ③ — Held human seat / action. A held seat, governed by its own authority document, decides whether and how to change operational behavior in response to the surfaced pattern. Evidence may inform this decision. Evidence may not replace the decision-maker. This is the same seat principle as the authority frame in §2, now acting on what the chain has surfaced.

Why two stations and not one. The seat that holds authority over _meaning_ — which interpretation is correct against the authority document — is not necessarily the seat that holds authority over _action_ — whether to change routing or capability scope in response to a pattern. Where one actor holds both, that actor can rule that an interpretation is correct and then act on its own ruling with no separate vantage point: a super-seat certifying itself across two layers. The discipline that governs the model governs the seats. Meaning-authority and action-authority are distinct seats with distinct authority documents, or the architecture has quietly built the very thing it forbids. The two stops may be grouped under “human follow-up” in the picture; they are never fused into one box.

This is also where SHEPUF’s back half lives: the follow-up (F) that determines whether the intended outcome actually occurred and whether the assumptions that drove the action were correct — performed without the doer grading its own work.

Tier: specified (doctrine). Both stations are doctrine, not built components. (Authority Map §3; Audit Stack doctrine on distinct meaning and action seats.)

## 8. Station ④ — Organizational learning (specified)

The last station closes the loop. With independent evaluation and distinct human-follow-up seats in place, the record becomes a durable learning surface: the organization can see patterns across departments, tasks, contacts, failures, corrections, and outcomes that no single vantage point could see alone — surfaced not by the system grading itself, but by the independent evaluator reading the immutable record, and acted on only by a held seat.

The map states the constraint that keeps this from becoming self-deception, because it is the same rule applied one level out. Behavior is mutable through a governed path and never autonomously: evidence accumulates in the immutable record, a separate evaluator surfaces patterns against it, a held seat reviews the surfaced evidence, a configuration change is approved, and only then does the system operate under the new configuration. Every mutable surface that can change the system’s behavior — routing tables, retrieval indices, packaging rules, authority-state transitions, evaluator thresholds, and model parameters alike — is treated as configuration and obeys the identical evidence → independent evaluation → authority review → signed change path. There is no surface whose mutation is privileged to skip the path; a change that bypasses the evaluator and the seat on the grounds that it is “the model itself” rather than “the configuration” is the autonomous self-modification the architecture forbids, wearing the costume of a technical detail. These surfaces are named here as specified governance surfaces — the set the governed-mutation path is designed to cover — not as a claim that each surface, or the path that governs it, is implemented on the testbed today; per §5 and §10, the built portion ends at the receipt, and this chain is Tier 2 doctrine.

Two boundaries this station holds, because they are the domains where confusing evidence with verdict does real harm. Operational association is evidence for routing and context; it is never, on its own, a resolution of authority — patterns identify where a term or contact tends to point, not which meaning is authoritative for the job in front of the system. And a contact pattern is evidence for routing and context only; it is not a personnel judgment, a character judgment, or a disciplinary conclusion, and the architecture is constructed so that it cannot be rendered as one.

The recursion terminates, and the map says where. Changes to the evaluator are themselves events in the immutable stream, surfaced by a distinct verifier and signed by a distinct authority seat against the document that governs evaluation scope. The chain does not terminate in a stronger model or a meta-evaluator that has learned to improve its own judge; it terminates at human-held authority documents — versioned, readable, reviewable instruments that define who may change what, under what evidence, by what process. That is the bottom of the stack. Anything below it is not governance; it is self-certification moved one layer higher.

Tier: specified. The learning surface’s mechanism is built insofar as the immutable record is built (§5); the independent evaluation that makes anything read from it trustworthy, and the governed-mutation chain that acts on it, are specified or pending — intent, not implemented runtime behavior. (Authority Map §3 and §5.)

## 9. SHEPUF and what is deliberately not in this map

SHEPUF (Sequence of events, Hazards, Employee assignments, Procedures, Understanding, Follow-up) is the bounded job-briefing structure that spans the two ends of the loop. Its front half (S–U) establishes provenance before a bounded job begins — named owners, explicit intent, referenced procedures, confirmed understanding — and its back half (F) is the human follow-up structured at stations ② and ③. It is the bridge between field execution and audit-driven learning, not merely a checklist. It is defined in operating doctrine and sourced to it, but not yet placed in a committed governing document in this corpus; it is named in this map and not relied on as implemented, and its canonical home is a later follow-up/evaluation doctrine document still to be assembled. (Authority Map §3, pending placement. The “S” expansion follows the Authority Map: “Sequence of events.”)

The following are deliberately not drawn in this map, and their absence is intentional:

- The Lightweight Evaluation Loop is held out entirely. It is referenced in adjacent material but not defined by any source read into this corpus. It is not used as a category, not equated with SHEPUF, and not written into this map. (Authority Map §3 — a stricter hold than SHEPUF: SHEPUF is defined-but-unplaced; this is undefined.)

- De-identification, redaction rules, and named-entity handling at the context boundary are named only at the level of “a gate that enforces shareability and sensitivity.” Their governing source is located but not yet read into the corpus, so no specific rule is drawn. (Authority Map §3, pending verification.)

- The MissionLayer control-plane vocabulary has been transposed to DRNT throughout. Where the source control-plane material describes the L2 plane in the present tense as a live “reasoning auditor” that validates and audits the work, that present tense has been transposed to specified-not-built per §6 above. MissionLayer is retired; it is not used as a name, a label, or a control-plane term. (Authority Map §4.)

## 10. The honest split, carried into the picture

The map’s organizing fact, stated plainly so the drawing cannot imply more than the testbed has proven:

The built portion is a clean spine. Route (L1) → scope and gate (capability registry, WAL, context boundary) → perform under containment (sandboxed tool-leases, Docker + seccomp) → write the receipt (append-only, hash-chained, replayable). That spine is proven runnable on a single end user’s personal-desktop testbed. It is real, and it is not production or scaled.

Everything the architecture is _for_ past the receipt is specified or pending. Independent evaluation, the meaning seat, the action seat, organizational learning, the roaming L3 agent state, stronger isolation, and SHEPUF’s committed placement are doctrine, design intent, or pending placement — not implemented runtime behavior. This is not a credibility gap. The thinking runs ahead of what one end user can implement on a personal setup; that gap is a resource boundary, and the corpus names both honestly — what the architecture is for, and what has been proven runnable so far.

A claim is never promoted to a stronger tier by assertion. Specified does not become built because this map draws it. Promotion happens by the work — building the component, committing the spec — and until that work is done, each layer keeps the tier it actually has. That is the discipline the architecture enforces on itself, and the reason the picture above marks every layer with the tier it holds rather than letting the single operating whole imply a single built whole.

_This document is part of the DRNT architecture corpus. It is produced for review and is not committed to_ _`local-first-ai-orchestration` until explicitly directed. Read against_ _`00_DRNT_Authority_Map_final` , which governs how every claim above is classified._
