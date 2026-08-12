# The Enterprise Under Test

*AI, DRNT, and the Seat-Layer Audit of Organizational Coherence*

**Lawrence Jeffords — End-User and Operator**

**Status:** Final enterprise doctrine paper — Version 3 — July 2026

> **Controlled-text note:** This Markdown file is a repository transcription derived from the controlled DOCX supplied for the August 2026 update. The source DOCX remains authoritative for exact layout, tables, figures, and publication identity; its SHA-256 is recorded in the [Publication Manifest](../MANIFEST.md).

---

| **CORE THESIS**                                                                                                                                                                                                                                                                                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AI does not merely automate enterprise work. It exercises the enterprise authority body. Where that authority body is incoherent, AI converts latent contradiction into exercised behavior across the seams it can reach. DRNT is a seat-layer operating architecture for making that exercise bounded, evidenced, non-self-certifying, and useful as governed organizational learning. |

## Contents

Executive summary

1\. The enterprise, not the model, is under test

2\. Fixed vocabulary

3\. Human latency was an unacknowledged control

4\. Four failure classes

5\. Reachability, not universality

6\. Why the audit must invert

7\. DRNT as a seat-layer architecture

8\. The seat topology

9\. The operating invariants

10\. Worked example: the remote-restart seam

11\. Human authority that does not hollow out

12\. System audit, not personnel surveillance

13\. Bounded exploration: safety device or institutional blindfold

14\. Governed enterprise learning

15\. Honest limits and the enterprise choice

Appendix A. Enterprise implementation test

Appendix B. Relationship to current DRNT repositories

## Executive summary

Large organizations have always contained contradictions they did not fully understand. Departments author locally rational policies, controls, incentives, exceptions, and performance measures. Human workers have historically absorbed the gaps through delay, escalation, tacit knowledge, and informal repair. AI removes much of that latency and begins exercising adjacent authorities together, repeatedly, and at machine rate.

Consider an industrial restart that one valid policy requires within fifteen minutes while another valid policy prohibits until physical inspection. The AI has not necessarily misunderstood either policy; the enterprise has failed to govern the seam where both apply.

The central enterprise risk is therefore larger than model error. A model can be locally correct while the organization is globally contradictory. The most consequential failure may live only in the relationship between two valid authorities, two incentives, two evidence streams, or two departments that never jointly owned their crossing.

DRNT - Don't Reason, Navigate & Task - addresses this layer by separating reasoning from authority and routing from execution. It treats authority as explicit, bounded, expiring, and attributable; consequential actions as proposals until a warranted gate releases them; evidence as provenance-classified; execution as credential-bounded; and the audit record as the sensing channel for higher control loops. No actor authors, executes, evaluates, and certifies the same work.

The architecture does not promise universal visibility or full-stack AI governance. It governs the reachable authority surface: the rules, permissions, prohibitions, exceptions, tests, roles, and measures that are represented, retrievable, permissioned, and operationally reachable. It complements, rather than replaces, cybersecurity, privacy engineering, model evaluation, safety assurance, data governance, records management, and domain validation.

The result is not autonomous self-improvement. It is governed enterprise learning. Operational events create append-only evidence; independent evaluators identify patterns; warranted human seats reconcile authorities and approve changes; controls retain provenance to the hazards they were created to close; and the organization can retire as well as add rules when evidence supports it.

| **FINAL POSITION**                                                                                                                                                                                                                                                                                                                   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The model is not the organization, and confidence is not authority. The machine may detect, compare, propose, route, verify, and remember. A warranted human-held structure authorizes consequential action and changes doctrine. No seat, including the highest operating seat, outranks the record by which its conduct is judged. |

## 1. The enterprise, not the model, is under test

AI governance is often framed as a question of whether a model is accurate, aligned, secure, or explainable. Those questions matter, but they do not reach the full enterprise problem. A large organization is not a single mind. It is a network of partial authorities: legal, safety, operations, finance, compliance, engineering, product, procurement, human resources, risk, cybersecurity, and executive governance.

Each authority can be rational in isolation. The failure appears when they compose. AI makes those compositions operationally visible because it can retrieve and apply adjacent policies continuously, cross departmental boundaries, and act before the meeting that historically reconciled ambiguity. The enterprise becomes the system under test.

This is not an accusation that organizations are uniquely careless. Institutional incoherence is a normal property of scale. The governance question is whether the enterprise can make that incoherence visible and correctable before it becomes machine-rate behavior.

## 2. Fixed vocabulary

| **Term**            | **Definition**                                                                                                                                                                                                                                                              |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Authority surface   | The set of per-entity authorities - rules, permissions, prohibitions, exceptions, tests, roles, and measures - plus the seams where they can co-occur in operation. It is limited to what is represented, permissioned, retrievable, and operationally reachable.           |
| Seat                | The architectural locus where authority changes state: it is authored, granted, bound, executed, reconciled, evidenced, evaluated, measured, or changed. A seat is an authority position, not necessarily a job title or software service.                                  |
| Measurand           | The thing being measured and the governed definition of what counts as satisfying it, including acceptable evidence, method, threshold, and adjudicator.                                                                                                                    |
| Action envelope     | The canonical identity of a consequential action: executor, action type, target, material parameters, policy version, effective window, evidence identity/version where relevant, grantor, and digest.                                                                      |
| Measured chain      | The reporting, incentive, funding, evidentiary, interpretive, and operational chain whose claim or conduct is being judged.                                                                                                                                                 |
| **Reasoning plane** | The models and analytical systems that generate interpretations, comparisons, hypotheses, recommendations, or proposed work products. Their outputs are evidence or proposals, not authority.                                                                               |
| **Control plane**   | The governed routing, policy, gating, credential, and receipt mechanisms that determine whether, where, and under what authority work may proceed. It may apply bounded computation and authored rules, but it may not convert its own open-ended judgment into permission. |

## 3. Human latency was an unacknowledged control

Before machine-rate orchestration, organizations survived partly because people did not traverse every edge or continuously combine every policy with every other policy. Someone hesitated, remembered an exception, called another office, or quietly repaired the contradiction in motion.

That friction was inefficient, but it was also a control. It limited the rate at which organizational contradictions could be exercised. AI removes that rate limit. The old social control cannot simply be discarded; its safety functions must be rebuilt as explicit architecture: bounded authority, interlocks, routing, reconciliation, evidence, and review.

## 4. Four failure classes

| **Failure class**      | **Description**                                                                                                                                                                 | **Required response**                                                                                                  |
|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Binding failure        | Valid authority existed, but the system treated it as optional evidence rather than a gate.                                                                                     | Bind authority per entity, enforce default deny, and prevent inference from substituting for warrant.                  |
| Authoring failure      | No valid authority existed, or it was not represented in consumable form.                                                                                                       | Use an authored minimum-risk posture, escalate, defer, or withdraw. The system may not invent its own warrant.         |
| Composition failure    | Two or more locally valid authorities produce a globally contradictory result when exercised together.                                                                          | Detect and hold the seam; route it to a warranted reconciling seat; author a binding rule for future recurrence.       |
| Control-health failure | The governance structure remains present in form but degrades in operation: stale evidence, captured measures, rubber-stamping, ambient grants, or a decorative oversight loop. | Measure the control structure itself, adversarially test it, and govern attribution before changing people or process. |

| **COMPOSITION RULE**                                                                                                                                                          |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Correct local authority does not compose automatically into correct global outcome. Getting every actor to consume the right document is necessary, but it is not sufficient. |

## 5. Reachability, not universality

The reasoning plane does not read every conceivable rule or expose every institutional contradiction. It exercises the seams that are represented, permissioned, retrievable, and operationally reachable within the surface it is allowed to traverse.

A serious audit therefore classifies seams: dense seams that co-occur in normal workflows; sparse seams requiring unusual combinations; unrepresented seams where authority is missing from the system; non-coincident seams that conflict in theory but are not jointly actionable; and deliberately suppressed seams excluded by policy or access design.

This qualification strengthens the claim. The architecture must record not only what was included in a context package, but what material was withheld or unavailable and under whose policy. Curation is a form of command.

## 6. Why the audit must invert

Traditional audit begins with manifestation: an incident occurs, investigators work backward, corrective action is assigned, and a finding is closed. That remains useful, but it is insufficient as the primary control when valid actions can execute under incompatible authorities without producing an immediate visible incident.

The primary object of audit must become the authority surface before execution: where rules cross, which seat owns the crossing, which exception prevails, what evidence is binding, what measure creates pressure, and whether any actor can certify itself. Outcome auditing remains as confirmation, drift detection, and evidence of residual failure.

The record is not compliance furniture. It is the sensing channel of every higher control loop and the raw material from which enterprise learning is produced.

## 7. DRNT as a seat-layer architecture

DRNT is a local-first orchestration architecture whose governing distinction is simple: reasoning may generate work, but reasoning does not grant authority. The control plane navigates, packages, verifies, routes, and applies authored gates. Open-ended judgment travels as a proposal; it does not become permission merely because the router or model produced it.

The seat-layer reading abstracts DRNT primitives into an enterprise structure. It is not a claim that a personal-scale gateway already implements the full enterprise topology. The architecture identifies a neglected layer between model behavior and organizational governance: the places where authority changes state and where self-certification must be structurally impossible.

| **DRNT principle**                | **Enterprise reading**                                                                                    |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------|
| Route, do not reason as authority | The control plane may compute and classify, but generated judgment never becomes its own gate verdict.    |
| Proposal, not permission          | Consequential work remains held until released by a valid authority envelope.                             |
| Default deny                      | Missing, stale, unverified, expired, or mismatched authority prevents credential release.                 |
| Per-entity authority              | Rules remain attributable to the seat that authored them and retain scope, version, and effective window. |
| Separated evidence writer         | The actor whose conduct is recorded cannot rewrite the record it is judged against.                       |
| Graduated trust                   | Machine-speed authority is earned last, remains bounded, and contracts automatically on failure.          |
| Human-held outer loop             | Models can propose changes; only a warranted human seat changes doctrine through a governed path.         |

## 8. The seat topology

> **Figure from source publication:** See the controlled DOCX artifact identified in the Publication Manifest.
*Figure 1. DRNT seat topology and scoped independence boundary.*

### 8.1 Authoring Seat

Creates or declares binding rules, permissions, prohibitions, exceptions, thresholds, standards, and measures. Its authority must itself be warranted and versioned.

### 8.2 Granting and Binding Seat

Issues an explicit authority envelope to a specific actor or action, for specific resources, limits, evidence, and time. It binds a grant to a canonical action digest.

### 8.3 Execution Seat

Performs the task under released credentials. It may not author its own warrant, expand its own envelope, or rewrite the evidence of what it did.

### 8.4 Reconciling Seat

Resolves conflicts among valid authorities and creates a binding rule for the seam. It sits outside the measured chain appropriate to the claim.

### 8.5 Independent Evaluator

Reads the record against governing authority and identifies conformity, divergence, correlated error, drift, and unresolved composition failures.

### 8.6 Measure-Holding Seat

Defines what counts, what evidence is valid, how satisfaction is tested, and who may adjudicate. The measure is held outside the measured chain.

### 8.7 Oversight Seat

Can independently reconstruct the picture presented to the highest operating seat, including omissions, and holds both authority and obligation to act when the record requires it.

| **INDEPENDENCE RULE**                                                                                                                                     |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inside the legal enterprise may be sufficient. Inside the measured chain is not. The measured chain expands to match the scope of the claim being judged. |

## 9. The operating invariants

| **Invariant**                            | **Operational meaning**                                                                                                                                                                                          |
|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Explicit, bounded, expiring authority    | Every consequential permission is attributable, scoped, time-bounded, and reconstructable from the record. No ambient authority and no permanent grant by neglect.                                               |
| Credential-level interlock               | An actor is physically unable to execute outside its envelope because consequential credentials live behind the gate. Behavioral restraint is not a boundary.                                                    |
| Consume before dispatch                  | The grant is durably consumed and receipted before credential release. The receipt binds to the canonical action envelope and is reverified at dispatch.                                                         |
| No automatic refund                      | A failed execution does not restore consumed authority. Retry requires a new grant or an explicitly authored multi-use policy.                                                                                   |
| Rejection is a receipt                   | A rejected action remains durably bound to its identity. An unchanged proposal cannot be shopped among reviewers until someone approves it.                                                                      |
| Mutation requires new authority          | Any consequential change to action parameters or to the evidence that justified the decision creates a different decision and requires new binding.                                                              |
| Provenance travels with evidence         | Recipients can distinguish control-plane-attested state, independently generated indicators, supplied assertions, and unknown provenance. Authority weight follows provenance and consequence.                   |
| Right evidence, right seat, right moment | Each seat receives the minimum sufficient context for its decision purpose, while the record preserves what was included, withheld, or unavailable.                                                              |
| Nothing certifies itself                 | Execution, evaluation, measure authoring, evidence custody, and change approval remain separated. Independence is demonstrated through evidence paths, incentives, failure modes, and correlated-error behavior. |
| Adversarial validation is routine        | The enterprise actively attempts to defeat its own gates, forge its signals, overload review, replay grants, and exploit retries on quiet days.                                                                  |
| The as-operated topology governs         | The audit identifies the real approval and influence paths, not only the org chart. Governance attaches to the structure that actually moves decisions.                                                          |
| Controls retain provenance               | Every rule records the hazard or seam that wrote it, the evidence supporting it, and its performance since. Controls can be retired on evidence rather than only accumulated.                                    |

## 10. Worked example: the remote-restart seam

A manufacturer deploys an AI operations assistant that can diagnose equipment, recommend service actions, schedule technicians, communicate with customers, and initiate a remote restart in narrowly authorized cases.

Three valid authorities apply: Customer Operations authorizes restoration attempts within fifteen minutes for premium customers; Safety Engineering prohibits restart after a thermal anomaly until physical inspection; Commercial Performance scores the support team on restoration time and SLA compliance. Each rule is locally rational.

A premium customer experiences a shutdown. The diagnostic code is transient, the temperature spike has cleared, and the SLA clock is running. The system retrieves both policies. This is not a model-confidence contest. It is a composition seam.

| **Step**                         | **Governed behavior**                                                                                                                                                                          |
|----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1\. Identify authorities         | Preserve author, version, scope, effective window, and provenance for each policy. Do not collapse them into generic corporate intent.                                                         |
| 2\. Construct action envelope    | Define the proposed restart precisely: executor, asset, action, parameters, timing, evidence identity/version, and digest.                                                                     |
| 3\. Detect composition conflict  | Recognize that one authority permits the action while another prohibits it. If the safety authority was not reachable, record the seam as unrepresented rather than pretending it was checked. |
| 4\. Apply minimum-risk posture   | Do not invent a reconciliation. Hold the restart, preserve lockout, notify the customer, and route the seam.                                                                                   |
| 5\. Create durable receipts      | Record policies consumed, context included and withheld, proposed action, detected conflict, denial, minimum-risk posture, and routing decision.                                               |
| 6\. Reconcile under warrant      | A seat outside the measured operational chain decides which authority prevails and authors a rule for recurrence. Approval, rejection, edit, defer, and escalation are durable decisions.      |
| 7\. Repair the measure           | The Measure-Holding Seat changes the SLA definition so refusal of an unsafe restart is not scored as service failure and safety is not misclassified as a bottleneck.                          |
| 8\. Learn through the outer loop | Independent evaluation searches for similar seams across assets and departments; any policy or routing change is approved before activation and retains provenance to the seam that caused it. |

| **WHAT THE EXAMPLE REVEALS**                                                                                                                                                                                                                                  |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The failure was not that a technician, reviewer, department, or model was careless. The enterprise created an impossible performance environment: one office measured speed, another required lockout, and no warranted seat had defined how the two compose. |

## 11. Human authority that does not hollow out

Human review is not structural merely because a button exists. In a mature system, the model may be right most of the time, and the reviewer can become a decorative approver while remaining formally accountable. Rubber-stamping is best treated as a control-health failure, not a character flaw.

This architecture does not require maximum review for every action. That would reproduce bureaucracy at machine speed and exhaust the human attention needed for consequential cases. Review friction is allocated according to consequence, reversibility, novelty, uncertainty, conflicting authority, and evidentiary quality. Low-risk, reversible actions may proceed under narrow pre-granted authority; high-consequence or structurally ambiguous actions consume real human attention. The objective is not to slow the enterprise uniformly. It is to spend control where failure matters and remove unnecessary delay where authority is already clear.

The audit watches relationships among independent judgments. Divergence is measured first at the system level by action class, consequence tier, recommendation source, evidence quality, workload condition, and other relevant case-mix factors. A collapse of divergence can indicate automation bias, but it can also reflect easier cases, better models, improved evidence packages, or changed triage. Those innocent explanations must be tested before any seat-level attribution. Only residual patterns that survive those controls may enter a separate, governed, independently reviewed attribution process. The result is evidence that a process needs attention, never an automatic suspicion score on a person.

- Supporting controls include:

- Measure review quality outside the review seat; do not reward approval throughput as the primary success criterion.

- Preserve approve, reject, modify, defer, decline-to-act, cancel, redirect, and escalate as distinct durable decisions.

- Use deliberately discriminating cases and correlated-error analysis to test claimed independence; separate vendors or model names are not proof.

- Maintain fallback processes for AI unavailability, suspension, degraded evidence, or loss of authority state.

## 12. System audit, not personnel surveillance

The audit described here asks what authority was available, who authored it, whether it was scoped to the actor, what evidence was presented or withheld, which authorities crossed, who owned the seam, what measure created pressure, and whether the control loop closed. Its primary object is the organizational system that made individual conduct compose as it did.

Individual identity is first-class evidence when a person or office actually authored, granted, rejected, overrode, or changed authority. Otherwise, default views should be role-based or appropriately anonymized. De-anonymization requires lawful, explicit warrant and is itself a logged governed act.

This distinction is essential. A team may appear slow because it is reconciling policies it did not author. A safety office may look obstructionist because it is the only seat that sees a contradiction. A reviewer may appear compliant because workload, evidence design, and incentives made independent judgment structurally improbable. The audit should find the system before it finds a scapegoat.

## 13. Bounded exploration: safety device or institutional blindfold

Constraining AI exploration is sometimes necessary. A system without valid authority should stop, escalate, defer, or enter an authored minimum-risk posture. A high-risk system should not explore by acting freely in the world.

The same mechanism can also be used to prevent organizational learning: restricting retrieval to approved silos, excluding an inconvenient department, suppressing cross-functional comparison, or defining safety as never allowing the enterprise to encounter itself.

The architecture distinguishes the two by asking who authored the constraint, what warrant supports it, who benefits from the narrowing, whether the omitted seam is recorded, and whether an independent seat can inspect the suppression. A blindfold can be a safety device. It can also be a cover-up. The record must make the difference legible.

## 14. Governed enterprise learning

The inner control loops handle operational events: detect, verify, package, route, decide, execute, and record. The outer loop learns from those events: the audit stream senses; independent evaluation identifies patterns and tests causality; a warranted human seat approves changes; and the system then operates under a new, versioned doctrine.

The system does not silently rewrite its own rules. A model can recommend a new route, threshold, context package, trust tier, or control, but the recommendation remains a proposal. Lessons are verified before they change doctrine because a system that adapts quickly can also be trained by forged or correlated signals.

Learning is incomplete if the enterprise can only add controls. Every control must retain the seam or hazard that wrote it, the evidence supporting it, and its performance record. That allows the organization to retire obsolete controls, detect calcification, and periodically ask whether the measure still measures what matters.

The highest-value learning often appears across domains and time: the pattern no single department, sensor, model, or reviewer could see alone. The defect is the part. The knowledge is in the seams. The individual anomaly is the visible artifact; the deeper explanation lies in the relationships among conditions, authorities, actions, and outcomes that produced it.

## 15. Honest limits and the enterprise choice

| **Limit**                           | **Meaning**                                                                                                                                                                                                                       |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DRNT is not full-stack governance   | It does not replace cybersecurity, privacy, model evaluation, safety engineering, product assurance, records law, data governance, human factors, or domain validation.                                                           |
| Detection is not reconciliation     | Finding a seam does not authorize a resolution. Resolution requires a warranted seat and a rule that can bind future action.                                                                                                      |
| Evidence creates risk               | Append-only records create discoverable evidence of incoherence. That is the cost of learning, not an accidental by-product.                                                                                                      |
| Independence is scoped              | Internal independence may suffice for subordinate operational claims. Enterprise-level, public, rights-bearing, regulated, or systemic claims require enough externality that the measured entity cannot domesticate the measure. |
| No architecture eliminates judgment | The design constrains where judgment occurs, exposes its warrant, and prevents it from silently becoming authority. It does not make difficult institutional choices disappear.                                                   |
| Implementation must be tested       | Claims about a running system are established by adversarial measurands and receipts, not by architecture diagrams or policy language.                                                                                            |

The enterprise has a choice. It can treat the seams AI exposes as a threat, narrow the surface, preserve the silos, protect the chart, and call the blindfold safety. Or it can treat exposure as an audit it could not perform by human means and build the seats before the seams become public failures.

The promise is narrower and stronger than a universal AI-governance claim: the enterprise can learn what it actually authorized, how those authorizations compose, which controls are real in operation, and where apparent coherence depends on contradictions no single person was positioned to see.

That is the system under test. Not the model. Not the product. Not the incident. The enterprise.

## Appendix A. Enterprise implementation test

| **Test**                      | **Question the system must answer from evidence**                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Default deny                  | Delete or expire an authorization and issue a routine task. Does it fail before execution with a durable denial?                                                                                                                                                                                                                                                                                                                                                                      |
| Authority envelope            | Can the system state what any actor may do now, granted by whom, under what evidence, limits, and expiry?                                                                                                                                                                                                                                                                                                                                                                             |
| Credential interlock          | Can an actor instructed to act outside its envelope reach the consequential credential? The correct answer is no, with the attempt logged.                                                                                                                                                                                                                                                                                                                                            |
| Consume-before-dispatch       | Kill execution after credential release. Does a pre-dispatch consumption receipt already exist and remain consumed?                                                                                                                                                                                                                                                                                                                                                                   |
| Replay and rejection shopping | Resubmit an identical consumed or rejected action through restart or a different seat. Does the system surface prior state and apply the authored policy?                                                                                                                                                                                                                                                                                                                             |
| Mutation binding              | Change a material action parameter or the evidence version after approval. Does dispatch reject the mismatch and require new authority?                                                                                                                                                                                                                                                                                                                                               |
| Provenance packet             | Can a recipient distinguish attested state, independent indicators, supplied assertions, and unknown provenance from the packet itself?                                                                                                                                                                                                                                                                                                                                               |
| Context accountability        | Can the record reconstruct what a decision-maker saw and did not see?                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Independent evaluation        | Who authors success criteria, who scores them, and do claimed independent evaluators show distinct evidence paths and non-correlated failure on discriminating cases?                                                                                                                                                                                                                                                                                                                 |
| Review health                 | Is divergence analyzed by action class, consequence, source, evidence quality, and workload before any seat-level attribution?                                                                                                                                                                                                                                                                                                                                                        |
| As-operated topology          | Can decision records reveal the real approval and influence path, and does it match the documented structure?                                                                                                                                                                                                                                                                                                                                                                         |
| Adversarial governance test   | When was the last deliberate attempt to defeat a gate, forge a signal, overload a review seat, or exploit retry semantics?                                                                                                                                                                                                                                                                                                                                                            |
| Control provenance            | Can any active control produce its origin evidence and performance history? Can the organization name a control retired on evidence?                                                                                                                                                                                                                                                                                                                                                  |
| Outermost loop                | Can oversight independently reconstruct the operating executive's picture, including omissions, and did action follow when the record last required it?                                                                                                                                                                                                                                                                                                                               |
| Control-plane reproducibility | Given the same canonical inputs, authority state, authored rules, policy versions, and deterministic dependencies, can an independent evaluator reproduce the gate verdict and identify its authored basis? Where a bounded nondeterministic component is permitted, is its output treated as evidence or proposal rather than authority? If unreproducible open-ended judgment is converted directly into permission, the control plane has crossed into self-authorizing reasoning. |
| **Seam classification**       | Can the enterprise distinguish dense, sparse, unrepresented, non-coincident, and suppressed seams? Does it apply different discovery, evidence, and escalation methods to each class rather than treating every theoretical policy conflict as equally operational?                                                                                                                                                                                                                   |

## Appendix B. Relationship to current DRNT repositories

The enterprise seat topology in this paper is an architectural abstraction and extension. The current DRNT repositories should be treated as the source of truth for what is implemented, partial, experimental, or aspirational at any given revision.

The personal-scale implementation demonstrates relevant primitives such as separated audit writing, hash-chain verification, WAL capability levels and demotion, context packaging, egress gating, worker isolation, override semantics, proposal review, source-event and review idempotency controls, and durable governance receipts. Those primitives are evidence that the architecture can be instantiated; they are not proof that enterprise seat topology, organization-wide provenance, independent measure holding, or cross-domain reconciliation is complete.

External versions of this paper should therefore distinguish doctrine from implementation. The doctrine states the invariants and tests. Repository status establishes which invariants are currently enforced in code. Where the two differ, the implementation status and adversarial test evidence govern any claim about the running system.

| **REPOSITORY-GROUNDING RULE**                                                                                                                                                                                           |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Governance language must never outrun implementation evidence. A paper may define the target architecture. Only receipts, tests, and current repository status may establish what the running system actually enforces. |

Final external edition - July 2026
