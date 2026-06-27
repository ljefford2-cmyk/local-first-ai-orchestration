# DRNT Architecture Corpus — Authority Map

**Status of this document:** Control surface for the committed architecture corpus. Read this before relying on any other file in the folder.

**What this document is.** This is the anti-drift map for the DRNT architecture corpus. It is not an overview, a summary of what DRNT does, or an argument for it. It answers one question, for every claim made anywhere in this folder: *what kind of truth is this, and which source is authoritative for it.* Every later document in this corpus is to be read against this map. Where a later document asserts something, this map governs whether that assertion is implemented fact, specified intent, or draft thinking — and which repository or file settles it.

**Why it exists.** DRNT is an architecture under development, explored from the perspective of the end user who has to live with the outcome. It is not a product and it is not a finished system. Some of it is built and running on a personal testbed; more of it is specified but not yet built; the newest thinking is ahead of both. A corpus that blurs those three things would do exactly what the architecture exists to prevent: present interpretation in the costume of fact. This map keeps them separated. It is the first file because it is the precondition for trusting the rest.

**What this map does not do.** This document does not describe the full DRNT architecture. It governs how architecture claims are *classified*. The system architecture itself — how the layers form a single operating whole — is described in the architecture overview and the supporting control-plane documents, not here. This file answers only one prior question: whether a given claim is built, specified, staged, or pending verification, before any later document relies on it. It is the rule for reading the corpus, not the corpus's account of the system.

---

## 1. The three tiers of truth

Every claim in this corpus has a controlling tier. A document may contain claims from more than one tier — a single essay can hold a Tier 3 proposal, a Tier 2 doctrine statement, and a Tier 1 implementation claim at once — but each claim must be classified by the source that would make it true or false. The tiers are not a ranking of importance; they are a statement of *what kind of thing a claim is* and therefore what would settle it.

**Tier 1 — Implementation truth.** What is actually built and testable. The authoritative source is the implementation repository, `local-first-ai-gateway`. A Tier 1 claim is settled by reading the code, running the tests, or inspecting the running testbed — not by reading a paper that describes the code.

**Tier 2 — Architecture / specification truth.** What the system *is*, what it requires, what rules govern it, and what remains specified but not yet built. The authoritative source is the specification and governance repository, `local-first-ai-orchestration` — including this corpus once it is committed there. A Tier 2 claim is settled by the governing spec or doctrine, and a Tier 2 claim about a component does *not* assert that the component is implemented.

**Tier 3 — Drafting / staging truth.** Working papers, essays, redline reviews, and pre-commit material. The staging location is the OneDrive `_Incoming` folder. A Tier 3 source is current architectural thinking, pending review and implementation. It is not authority. It is where authority is drafted before it is committed.

The correct label for material that is ahead of the code is **current architectural thinking, pending implementation** — not "stale," and not "built." Tier 3 essays may be the most recent and the most advanced statement of the architecture, and still describe things that neither Tier 1 nor Tier 2 has yet.

---

## 2. Which repository is authoritative for which kind of claim

| Kind of claim | Authoritative source | Settled by |
| --- | --- | --- |
| What is built, runnable, and tested | `local-first-ai-gateway` (implementation / personal testbed) | Reading the code, tests, and operational behavior |
| What the system is, requires, and is governed by | `local-first-ai-orchestration` (spec / governance; this corpus once committed) | The governing spec, doctrine, or control-plane mapping |
| What is being drafted but not yet committed | OneDrive `_Incoming` (staging) | Nothing — staging is not authority |

A claim is only as strong as the tier it comes from. A statement that a capability *should* behave a certain way (Tier 2) is not a statement that it *does* (Tier 1). A statement in an essay (Tier 3) is not a statement that the architecture requires it (Tier 2) or implements it (Tier 1) until it has been moved into the appropriate repository.

---

## 3. Operating truth table — built, specified, staged

The following records the state of the architecture as established by the June 2026 repository reconnaissance of the two repositories, read against the corpus. **Status lines describe what that reconnaissance found on the personal testbed and in the spec repo; they are not timeless claims.** Where the reconnaissance did not establish a component's state, this table says so rather than guessing.

### Built (Tier 1 — found implemented on the `local-first-ai-gateway` testbed)

The implementation repository is a personal-desktop testbed: the best the architecture can be exercised on a single end user's setup. What is built there is a proof that the governance and routing primitives are real and runnable — not a claim of a production or scaled system.

- **Route-don't-reason routing (the L1 / gateway discipline).** Classifier-driven routing that dispatches rather than reasons about decomposition. This is the discipline DRNT names: *Don't Reason, Navigate & Task.*
- **Capability registry / capability-scoped trust.** Capabilities as the unit of permission.
- **Workflow Autonomy Level gates (WAL-0 through WAL-3).** Trust-tier gating on what an action is permitted to do.
- **Context packager / context boundary.** Sensitivity and shareability handling before data crosses a boundary.
- **Append-only, hash-chained audit record.** SHA-256 chained receipts — the immutable record layer.
- **Sandbox dispatch via Docker + seccomp.** The containment that is actually implemented today.

### Specified but not yet built (Tier 2 — doctrine and design intent; no running component)

These are load-bearing parts of the architecture that exist as specification, doctrine, or build-time practice, but not as running code on the testbed. They must not be described anywhere in this corpus as implemented runtime behavior.

- **The independent runtime evaluator (the separate L2 evaluator).** Specified as an independent reader of the immutable record — a separate model built solely to analyze the audit-trail data, barred from being the actor that produced the record, responsible for discrepancy detection, follow-up analysis, and escalation proposals. What exists today is the audit-record foundation it would read, plus a build-time multi-model review practice. **A separate running evaluator component does not yet exist.** See §5 for the exact framing this requires.
- **The L3 agent execution layer.** Specified in the control-plane mapping as where agents perform bounded work. **Deliberately not built**, gated on containment strength: the agent layer is not stood up while the implemented sandbox is Docker + seccomp rather than the stronger isolation the spec calls for. This is the architecture enforcing its own *containment-before-capability* rule on itself, not an unfinished gap.
- **Stronger runtime isolation (Firecracker / microVM / rootless Podman / network namespaces).** Present in spec prose as the intended containment tier. The implemented sandbox today is Docker + seccomp. The agent layer's gating (above) depends on closing this distance.
- **OpenShell / NemoClaw sandbox-supervisor integration.** Present in governance and spec material (including a WAL integration profile) as a named design choice. **No implementing code.** May be carried as intended architecture, never as built.

### Staged / draft (Tier 3 — `_Incoming`, pending review and assembly)

- The architecture essays and papers that make up this corpus, in their pre-commit form, along with older versions and AI redline reviews. These are the drafting tier. Once reviewed and assembled, the corpus becomes the committed spec/governance material in `local-first-ai-orchestration`; `_Incoming` remains staging.

### Pending — flagged, not asserted

Two different kinds of "pending" live here, and they must not be conflated. One is *pending verification*: the source exists but has not been read into the corpus. The other is *pending placement*: the concept is defined in the user's operating doctrine but has no committed home in the corpus yet. The first is a gap in reading; the second is a gap in assembly.

**Pending verification (source located, not yet read):**

- **De-identification / context-boundary posture.** The authoritative source has been *located* — `DRNT_Spec3_Context_Boundary.md` §4 in the spec repo, with `config/sensitivity.json` as the rule data and a "default-deny; over-redacting is recoverable, under-redacting is not" principle — but its full text has not yet been read into this corpus. **No de-identification or named-entity handling is to be relied on until §4 is read in full.** This row is a flag, not a summary.

**Pending placement (defined in doctrine, not yet committed to the corpus):**

- **SHEPUF.** Defined in the operating doctrine as the bounded job-briefing structure: **S**equence of events, **H**azards, **E**mployee assignments, **P**rocedures, **U**nderstanding, **F**ollow-up. It is not merely a checklist; it is the bridge between field execution and audit-driven learning. The front half (S–U) establishes provenance before a bounded job begins — named owners, explicit intent, referenced procedures, confirmed understanding — and the back half (F, follow-up) is where the system learns across many actions without the doer grading its own work. In DRNT terms it sits at the evaluation/follow-up boundary: the L1/gateway side accepts the brief, routes and gates the bounded task, dispatches authorized execution, and writes receipts; the specified L2/evaluator side independently replays the receipts against the brief and routes to a human when thresholds trip. SHEPUF is **defined and sourced to the operating doctrine, but not yet placed in a committed governing document.** It should not be cited as implemented; its canonical home is a follow-up/evaluation doctrine document still to be assembled. (Note: unlike the de-identification row, the gap here is placement, not reading — its definition is known.)

- **Lightweight Evaluation Loop.** Referenced in adjacent material as a related evaluation/follow-up concept, but **not yet defined by any source read into this corpus.** It is held out entirely until its source is located or it is defined directly. It must not be used as a category, equated with SHEPUF, or written into any document until then. This is a stricter hold than SHEPUF: SHEPUF is defined-but-unplaced; this is undefined.

---

## 4. Naming

The canonical name of the architecture is **DRNT — Don't Reason, Navigate & Task.** DRNT is not a module bolted onto the architecture; it names the discipline itself: route before reasoning, govern before action, task within bounded authority.

Stated as the governing invariant rather than an acronym: **DRNT means the system must navigate to the right bounded task before reasoning, and must never use reasoning as a substitute for authority, policy, containment, or verification.** The name is the rule. Reasoning is permitted as a tool inside a bounded task; it is never permitted to stand in for the seat of authority, the governing policy, the containment boundary, or the independent check. Anywhere a later document uses "DRNT," it is invoking that invariant, not a brand.

**MissionLayer is retired.** It was a prior or internal working label. It is not to be used as the architecture name, a repository label, or a control-plane term. Where it appears in source material — notably the control-plane mapping document — it must be transposed to DRNT before that material becomes part of the committed corpus, unless it is explicitly marked as a prior/internal name preserved on purpose. Other informal names for the architecture exist; none is canonical except DRNT.

---

## 5. The evaluator: the one framing that must not drift

This is stated separately because it is the place the corpus is most at risk of claiming more than is built, and because the rule behind it is the architecture's load-bearing principle.

**No actor — human or model — may serve as the independent certifier of its own prior action. There are no exceptions.** This bars self-verification, not authority: a human may hold authority and make a binding decision, but may not then stand as the independent check on that same action; a model may produce work, but may not certify its own work as correct. Applied to the audit record, this has a precise consequence: the system that produces the record cannot be the system that judges it. The judgment must come from a separate evaluator that reads the immutable record independently from the producer of that record.

The honest split, which every document in this corpus must preserve:

> DRNT currently implements the governed execution and audit-record foundation: append-only receipts, hash-chain integrity, durable review records, WAL trust gates, capability scoping, and sandbox enforcement. That foundation is built. The separate evaluator is a *specified next layer* — an independent reader of the immutable record, barred from being the actor that produced the record, responsible for discrepancy detection, follow-up analysis, and escalation proposals. Until that component exists as a separate running process, evaluator behavior is **architectural intent, not implemented runtime behavior.**

No document in this corpus is to claim that DRNT has a live runtime evaluator until a separate running component, reading the immutable record independently from its producer, actually exists. The record is built. The separation principle is absolute. The independent evaluator is specified. The implementation is not yet complete. All four of those statements are true at once, and the corpus must hold all four.

The chain does not end at the evaluator. The architecture's intended shape is not *task → audit record → evaluator* but *task → receipt → independent evaluation → human follow-up → organizational learning.* The stages past the audit record — independent evaluation, the human follow-up that SHEPUF's **F** is meant to structure, and the organizational learning that follow-up feeds — are **specified or pending placement, not built** (see the SHEPUF and evaluator entries above). This longer chain is noted here only so the corpus does not mistake the built audit record for the whole loop. The record is where the built portion ends; everything downstream of it is architectural intent until each stage is built and committed.

---

## 6. How conflicts are resolved

When two sources disagree, the conflict is resolved by *what kind of claim is in dispute*, not by which document is newer or better written.

1. **Disputes about what is built** are settled by Tier 1 — the implementation repository. If a paper says a component works and the code does not implement it, the code is authoritative for the claim "it works," and the paper has made a Tier 2 (specification) claim wearing Tier 1 (implementation) language. The fix is to relabel the claim, not to believe the paper.

2. **Disputes about what the system is or requires** are settled by Tier 2 — the spec/governance repository and this corpus once committed. Doctrine and control-plane mappings govern here, not essays.

3. **Disputes between a recent essay and the committed material** are resolved by recognizing the essay as Tier 3: current architectural thinking, pending implementation. It is neither dismissed as stale nor treated as built. If its thinking is to become authoritative, it is moved into Tier 2 (specified) or Tier 1 (built) by the appropriate step — not by being cited as if it already had.

4. **Prose-versus-code disagreements** are expected, because the thinking runs ahead of what a single end user can implement on a personal setup. The gap is a resource boundary, not a credibility gap. The corpus names both honestly: what the architecture is for (Tier 2/3) and what has been proven runnable so far (Tier 1).

The general rule: **a claim may never be promoted to a stronger tier by assertion.** Specified does not become built because a document says so confidently. Draft does not become specified because it is recent. Promotion across tiers happens by the work — building the component, committing the spec — and until that work is done, the claim keeps the tier it actually has.

---

## 7. Standing constraints on this corpus

- **Nothing is committed automatically.** Assembling this corpus into the committed spec/governance material in `local-first-ai-orchestration` is an explicit, directed step. No file in this folder is to be treated as committed, or as authorized for commit, until that direction is given.
- **The drafting tier is not authority.** `_Incoming` remains staging. Material there informs the corpus; it does not settle it.
- **Located is not read.** A source whose location is known but whose text has not been read into the corpus (see the de-identification row in §3) is flagged as pending, never summarized from inference.
- **Unsourced specifics do not enter.** Any term, name, figure, or claim that cannot be traced to the committed repositories, the governing spec, or an explicitly provided source is drift and is held out and flagged, not written in. This includes terms that appear in adjacent material but are not yet defined by an authoritative source.
