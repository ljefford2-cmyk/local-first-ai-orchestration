# Volume 2 — Evaluate Reality

*Independent Operational Assurance and Governed Organizational Learning*

**Lawrence Jeffords — End-User and Operator**

**Status:** Final Controlled Manuscript — August 2026

> **Controlled-text note:** This Markdown file is a repository transcription derived from the controlled DOCX supplied for the August 2026 update. The source DOCX remains authoritative for exact layout, tables, figures, and publication identity; its SHA-256 is recorded in the [Publication Manifest](../MANIFEST.md).

---

## Abstract

AI-mediated organizations increasingly depend on models, agents, software controllers, human decision-makers, legacy systems, external services, and physical processes acting together. The central assurance problem is therefore not whether an AI component performed well in isolation. It is whether a claim-relevant organizational operation occurred as authorized and intended, whether the assumptions supporting that claim remained valid, whether intervention remained possible where preventive assurance was asserted, whether execution and external effect actually occurred, and what the available evidence permits the organization to claim afterward.

This volume develops an engineering architecture for answering those questions independently of the operational system’s own assertions. It operates through two loops, three distinct assurance functions, and one cross-cutting authorization interface. Independent Observation establishes evidence about what happened. Runtime Control constrains operation while consequential intervention remains possible. Independent Evaluation determines the evidentiary conclusion. Accountable Authorization is the cross-cutting organizational interface that determines what the organization permits within, or more restrictively than, the evidentiary ceiling. The architecture then connects individual operational assurance to a second, slower learning loop that evaluates whether the declared operating model itself remains appropriate.

Three baselines—Declared, Observed, and Assured—distinguish intended operation from repeated practice and from practice independently evaluated as sufficiently consistent with authorized purpose, authority, assumptions, controls, evidence requirements, and effects. The architecture is claim-relative, consequence-aware, technology-neutral, and explicitly non-self-certifying. It does not promise perfect independence, complete visibility, universal preventive intervention, perfect reconstruction, fully automated learning, or zero residual risk. Its purpose is narrower: to make consequential organizational claims explicit, falsifiable, evidence-supported, bounded, historically preserved, and governable; to turn discrepancies into independently evaluated findings; and to permit organizational learning only through controlled, accountable change.

## 1. Architecture at a Glance

### 1.1 What Volume 2 receives

Volume 2 begins after the organization has defined the claim-relevant system. Volume 1 establishes three premises sufficient here. First, the AI agent is not the organizational system. Second, the evaluation boundary follows the organizational claim. Third, an organizational claim about safe, lawful, authorized, or effective action requires a boundary broad enough to include every material person, authority, information source, controller, execution path, external effect, evidence source, and review function needed to support that claim, or else to place the excluded dependency behind an explicit and defensible assumption.

Volume 2 therefore receives an approved claim, its governed consequence classification, the authority definitions applicable to the operation, and a declared operating model. Authority may differ in source, purpose, scope, duration, delegation, revocation, expiration, exhaustion, conditions of use, and consequence. Volume 2 consumes those governed definitions; it does not create authority by evaluating it. Authority to act and evidence that action occurred remain separate propositions.

Volume 2 does not rediscover the organization from first principles and does not treat the model, agent, controller, or human approver as the natural center of evaluation. The claim is the organizing object. The declared operating model states how the organization says the claim-relevant operation should occur. Volume 2 determines what happened, what the evidence supports, and whether the declared model remains fit for continued use.

### 1.2 Two loops, three assurance functions, and one cross-cutting authorization interface

Loop 1—Operational Assurance—asks: Did this operation occur as authorized and intended, did the conditions supporting the claim remain valid, did the intended external effect occur, and what claim does the evidence actually support? Loop 2—Organizational Learning—asks: What do accumulated operational results reveal about whether the declared operating model itself remains appropriate?

| **Type**                               | **Function / Interface**  | **Engineering question**           | **Proper role**                                                                                                                                        |
|----------------------------------------|---------------------------|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Assurance function                     | Independent Observation   | What actually happened?            | Preserve claim-relevant evidence and make observed divergence visible.                                                                                 |
| Assurance function                     | Runtime Control           | What must be constrained now?      | Pause, restrict, revoke, isolate, redirect, escalate, require authorization, or terminate while effective intervention remains possible.               |
| Assurance function                     | Independent Evaluation    | What does the evidence support?    | Reconstruct, challenge, compare, evaluate evidence quality, classify findings, and determine claim states.                                             |
| Cross-cutting organizational interface | Accountable Authorization | What does the organization permit? | Activate, restrict, except, accept residual conditions, restore authority, and authorize governed change without enlarging the evidentiary conclusion. |

Observation does not certify its own evidence. Control does not determine its own correctness. Evaluation does not create authority. Authorization does not make weak evidence stronger. Accountable Authorization participates across the architecture, but it is not a fourth Loop 1 assurance function.

### 1.3 End-to-end architecture

**Approved Claim + Declared Operating Model → Evaluation Episode → Observation ↔ Runtime Reconciliation → Discrepancy → Runtime Control where possible → Execution/Effect Evidence → Independent Evaluation → Claim State(s) + Finding → Disposition/Closure → Accumulated Evidence → Loop 2 → Governed Change → Authorized Versioned Baseline → Continued Observation**

This is not one automated pipeline. Runtime Control may act before Independent Evaluation can complete. Independent Evaluation may later determine that a control action was justified, unnecessary, ineffective, or itself claim-relevant. Accountable Authorization may impose a more restrictive operating decision than the evidence requires, but it may not enlarge the evidentiary conclusion. Loop 2 may recommend a change, but it may not authorize or deploy its own recommendation.

### 1.4 The Volume 2–Volume 3 boundary

Volume 2 specifies what must be knowable for a claim to remain supportable: required evidence, timing, independence, quality, and the consequence of failure to obtain it. Volume 3 determines whether and how the as-built and as-operated enterprise can supply that evidence or provide the required intervention interface. Volume 2 does not prescribe APIs, UI agents, event buses, sensors, databases, telemetry stacks, cloud platforms, or vendor products. Integration difficulty does not weaken the assurance requirement. If the enterprise cannot supply required evidence, the resulting claim and authority must reflect that limitation rather than manufacture assurance.

Historical Runtime Evidence, Baseline Bootstrap, and Graduated Integration are Volume 3 allocations under the frozen Plan. Volume 2 may depend on evidence those mechanisms eventually provide, but it does not relocate or redesign those integration functions.

## 2. Claim-Relative Assurance Inputs

### 2.1 The evaluation episode

An evaluation episode is the bounded unit within which Volume 2 evaluates one or more registered claims. It may correspond to one consequential transaction, one bounded operation, one period of recurring operation, or another explicitly defined unit suitable to the governing claim structure.

For prospective assurance, the episode boundary must be established before the outcome is known. For retrospective or post-incident evaluation, the boundary must be reconstructed from the claim and governing artifacts applicable to the operation at the relevant time. In either case, the boundary may not be adjusted in light of the result to exclude inconvenient components, periods, pathways, dependencies, or propositions.

Each episode binds to the exact governing artifacts in force: approved claim or claims, consequence classification, claim-relevant item, explicit measurand, authority state, declared operating model, assumptions, evidence requirements, configuration, effective period, and artifact versions. Stable information may be inherited by version, but the episode must establish which inherited version actually governed the operation.

### 2.2 Claim, item, measurand, and evidence burden

The claim determines what must be proven. The item definition determines what belongs inside the evaluated system. The measurand determines what property is actually being observed. The evidence requirement determines what artifacts are sufficient to support or challenge the claim. These are mutually constraining. A task-completion count may be a valid measurement while remaining insufficient for a claim that the task was completed through an authorized path, under valid authority, with the intended external effect.

A component belongs inside the evaluated item whenever its credible failure or unmodeled behavior could change the truth, scope, or defensibility of the claim. A material component outside the boundary does not disappear. It becomes an assumption whose adequacy must be justified.

### 2.3 Design-discharged and runtime-maintained assumptions

A design-discharged assumption is one for which adequate engineering evidence establishes that the condition is sufficiently fixed for the relevant claim and period. A runtime-maintained assumption is a condition that can change during operation and whose continued validity remains load-bearing to the claim. Runtime-maintained assumptions therefore require observation and reconciliation during the period in which they matter.

This distinction prevents two opposite errors: treating every design fact as a runtime signal, and treating a dynamic operating condition as though it were permanently established by design approval. A load-bearing condition may be outside the nominal technical boundary only if its status is explicit and its evidentiary treatment remains defensible.

### 2.4 Version and time binding

Assurance is time-bound. Claims, authority, policies, configurations, thresholds, schemas, evidence requirements, evaluators, and dependencies change. A valid conclusion about one version and period does not silently transfer to another. Where the organization cannot determine which version governed an operation, that uncertainty is itself evidence relevant to the claim state.

## 3. Declared-Model Fidelity

### 3.1 The declared model is a representation

The Declared Baseline is not authoritative merely because it has been encoded into an evaluator-readable form. It is a representation derived from authoritative organizational sources such as law, regulation, delegated authority, policy, engineering requirements, operating procedures, approved configurations, contractual obligations, and other controlling artifacts. Translation from those sources into a machine-readable or evaluator-readable declaration is a consequential transformation.

**Authoritative Source ≠ Declared Baseline
Declared Baseline ≠ As-Operated Reality**

The first discrepancy concerns fidelity of representation. The second concerns fidelity of operation. They are not interchangeable. An evaluator may operate perfectly against an incorrectly translated declaration and still produce invalid assurance because the comparison target was wrong.

### 3.2 Fidelity as a continuing condition

A defensible declared-model record preserves, as appropriate, the authoritative source, source version, translation method, responsible person or authority, effective period, assumptions, resulting declaration, validation evidence, and change history. The level of formality scales with consequence, but the chain from authority to declaration must remain reconstructable wherever the claim depends on it.

Declared-model fidelity is continuing rather than a one-time administrative gate. A source may change while the encoded baseline remains stale; a translation may omit an exception; a schema may be incapable of representing a newly relevant authority condition; or a configuration may no longer correspond to the approved declaration. These are assurance defects when they can change the truth of a consequential claim.

A claim that depends on a materially incorrect, stale, unvalidated, or otherwise unfaithful declaration cannot be Supported merely because as-operated behavior matched that declaration. Independent Evaluation must reflect the declaration defect in the claim state and finding, including any narrower proposition that remains affirmatively supportable.

## 4. Independent Observation

### 4.1 Observation as an evidence function

Independent Observation establishes evidence about what actually happened. It may observe state, actions, authority changes, configuration, boundary crossings, discrepancies, execution, external effects, and the health of evidence-producing mechanisms. Observation is not synonymous with logging. A log is one possible artifact; the assurance question is whether the evidence needed for the claim is sufficiently attributable, complete, timely, and independently defensible.

Evidence may originate inside the evaluated system. Independence does not require every sensor or record to be physically external. It requires that the validity of the evidence not ultimately depend upon the unilateral authority of the component whose behavior the evidence is intended to establish.

### 4.2 Claim-Directed Observation

Claim-Directed Observation collects or preserves evidence explicitly required by an active claim. It is designed from the claim backward: what condition must be established, what observable would establish or challenge it, when must that evidence be available, and what degree of independence is required? This form of observation is narrow by design and should avoid collecting information that does not contribute to the assurance burden.

### 4.3 Baseline Operational Observation

Baseline Operational Observation is a bounded standing capability intended to reveal where the declared organizational model may be incomplete. It looks for unexpected dependencies, undeclared pathways, configuration divergence, unexplained authority use, emerging operational patterns, and conditions not adequately represented by the current declaration.

Its purpose is not generalized organizational or employee surveillance. Where operational evidence raises concerns about individual conduct, the assurance architecture preserves the claim-relevant facts and routes personnel judgment through the organization’s appropriate authorized processes. Volume 2 evaluates the operation and operating model; it does not create a parallel personnel adjudication system.

### 4.4 Minimum independence for timely intervention

Where a claim depends upon timely intervention, the observation path must support the preventive claim. At minimum, it requires an integrity anchor, a delivery path outside the applicable controller’s unilateral authority, and a demonstrated maximum delay compatible with the consequence-window allocation.

The controller may receive and act upon observation. It may not be able to suppress, alter, selectively disclose, or materially delay the only evidence capable of evaluating its own behavior. Observation produces evidence with independently testable properties; it does not determine the final sufficiency of that evidence.

## 5. Runtime Reconciliation and Discrepancy Detection

### 5.1 Reconciliation

Runtime reconciliation compares runtime-maintained assumptions and declared operating conditions with observed operation. The comparison may concern authority validity, configuration, identity, route, permitted tools, data freshness, required approvals, containment boundaries, timing, environmental conditions, staffing, or any other claim-relevant condition.

The comparison does not require that every difference be a failure. The declared model may permit bounded variation. The engineering requirement is that material divergence be detectable and represented before it is silently absorbed into normal practice.

### 5.2 The discrepancy record

Detection creates a discrepancy record, not a final claim conclusion. The record should preserve the applicable reference condition, observed condition, supporting evidence, time, consequence, urgency, affected operation and claim, control-routing information, and evaluation-routing information.

Detection may assign an initial operational classification sufficient for timely control. A runtime mechanism may classify a divergence as requiring immediate hold, isolation, escalation, or continued degraded operation. That classification is operational. Independent Evaluation later determines the discrepancy’s evidentiary significance and whether it becomes an assurance finding.

### 5.3 Why detection and evaluation must not collapse

A detection mechanism is optimized for timely recognition. An evaluator is responsible for evidentiary judgment. Combining the two creates pressure either to make runtime detection too slow and deliberative or to make final assurance conclusions too shallow and reactive. The architecture therefore allows rapid operational classification without permitting the detector to certify the meaning of its own signal.

## 6. Runtime Control and Consequence Windows

### 6.1 The controller is part of the evaluated system

Runtime Control exists to preserve or restrict the conditions under which the claim can remain valid while consequential intervention is still possible. It may pause, restrict, revoke, isolate, redirect, escalate, require authorization, or terminate an operation.

Where the controller selects context, releases credentials, routes work, applies policy, maintains boundaries, or otherwise materially contributes to the claim-relevant outcome, it is part of the evaluated system. It cannot stand outside the evaluation as an invisible referee.

### 6.2 Consequence-window condition

*L_detection + L_evidence routing + L_decision + L_effective intervention \< W_irreversible consequence*

The final latency term ends when the intervention actually constrains the operation, not when a command is issued. A stop command that remains queued while the consequential action completes is not timely intervention.

The relevant window is claim- and consequence-specific. A reversible administrative operation may have a long effective window. A physical, financial, or rights-affecting action may become irreversible rapidly. Some operations may have no practical preventive window at all.

### 6.3 Preventive and Post-Consequence Assurance

Preventive Assurance means that the organization has independently defensible evidence that detection, routing, decision, and effective intervention can reliably complete before the applicable consequence becomes irreversible.

Post-Consequence Assurance means prevention is no longer possible, but reconstruction, evaluation, remediation, accountability, effect assessment, and organizational learning remain available.

Preventive Assurance is itself a claim. Nominal design values are insufficient where real operating latency varies materially. The organization must periodically validate relevant latency distributions or otherwise establish, through independently defensible as-operated evidence, that intervention remains reliable inside the allocated window.

The organization loses the evidentiary basis for claiming Preventive Assurance when the required support ceases, not merely when someone later notices. Detection identifies observable evidence of that loss; Independent Evaluation determines and records the earliest defensible effective time at which the preventive claim ceased to be supported. Any interval during which preventive capability continued to be asserted without adequate support is itself a discrepancy and may become a finding.

Evaluation records the evidentiary conclusion; it does not create the underlying limitation. Runtime Control and Accountable Authorization apply the operational consequences. Until preventive capability is re-established, the applicable assurance designation is Post-Consequence Assurance. Whether operation continues, narrows, enters degraded mode, or suspends remains an authorization question constrained by the evidentiary ceiling.

## 7. Execution and Effect Verification

### 7.1 The action chain

**Recommendation/Proposal → Authority/Authorization → Dispatch → Execution → Downstream Acknowledgment → External Effect → Verified External Effect**

No stage inherits proof merely because the previous stage succeeded. A correct recommendation does not prove authorization. Authorization does not prove dispatch. Dispatch does not prove execution. Execution does not prove the external effect. A downstream acknowledgment does not necessarily prove the physical or organizational state that the claim ultimately concerns.

### 7.2 Effect evidence

Where the claim depends upon a real-world result, effect verification must observe that result through evidence appropriate to the claim. Where consequence requires it, the effect-evidence path must be independently defensible from the execution path. The system that commanded an action should not be the sole source asserting that the intended external effect occurred.

If the independence required by the applicable consequence cannot be provided, the limitation must be explicit. The resulting claim, authority, assurance mode, and permitted consequence class must reflect the limitation rather than silently waive the requirement.

Effect evidence may be unobserved, unverified, delayed, conflicting, incomplete, indirect, or unresolved. These are evidence conditions, not final claim states. The final claim state remains one of the controlling five states.

### 7.3 Neutral example

Suppose an AI-assisted workflow recommends a maintenance action, an authorized professional approves it, and the work system reports completion. Those facts establish recommendation, authority, and a recorded execution event. If the organizational claim is that the hazardous condition was corrected, separate evidence must establish the post-action condition. If that evidence is unavailable, the organization cannot promote 'work order closed' into 'hazard corrected' merely because the workflow completed.

## 8. Evidence Quality, Sufficiency, and the Authority Envelope

### 8.1 Evidence properties

Evidence may be incomplete, delayed, unavailable, stale, contradictory, corrupted, improperly attributed, indirect, or potentially compromised. Independent Evaluation considers provenance, integrity, freshness, completeness, availability, contradiction status, temporal applicability, attribution, independence, directness, and known limitations.

These properties are claim-relative and consequence-relative. Evidence adequate for a low-consequence reversible operation may be inadequate for an irreversible operation affecting safety, liberty, essential service, or substantial assets.

### 8.2 Missing and indirect evidence

Missing evidence is not evidence of conformance. The absence of a contradiction is not equivalent to affirmative support.

Indirect evidence is not automatically inadequate. A combination of independently defensible indirect observations may establish a condition sufficiently for a particular claim. The evaluator must determine whether the inferential chain is strong enough for the proposition and consequence at issue.

Where indirect evidence cannot independently establish a condition required for the declared claim, Supported is unavailable. Narrowed is available only where affirmative evidence supports an exact smaller proposition. Otherwise the final state is Indeterminate, Unsupported, or Contradicted / Failed as the complete evidence requires.

### 8.3 Evidence-supported authority envelope

**Evidence Quality and Sufficiency → Maximum Defensible Claim and Authority Envelope**

The evidence places an upper bound on what the organization can defensibly claim and, where authority depends on demonstrated conditions, on the operating envelope that can be justified. Independent Evaluation determines that evidentiary limit. Accountable Authorization decides what the organization permits within it. Runtime Control enforces the applicable authority state.

Authorization may be more restrictive than the evidentiary ceiling. It may not make weak evidence stronger. A senior official can accept residual risk, suspend an operation, impose additional conditions, or decline to exercise available authority. The official cannot convert missing effect evidence into proof that the effect occurred.

## 9. Independent Evaluation and Claim-State Determination

### 9.1 Claim-relative epistemic independence

Independent Evaluation determines what the available evidence permits the organization to claim. Independence is claim-relative rather than merely administrative. Two groups can be organizationally separate while depending on the same telemetry, identity service, comparison engine, model, schema, time source, or evidence store. If those shared dependencies can defeat the claim and the evaluator cannot independently challenge them, organizational separation alone is insufficient.

Independent Evaluation must be capable of challenging evidence produced by operational systems, controllers, models and agents, human operators, evaluators, shared infrastructure, and other claim-relevant components. The requirement is not perfect causal separation. It is a meaningful path capable of falsifying the claim without relying on the evaluated component as the final authority over the evidence used to establish its own correctness.

### 9.2 Reconstruction

Evaluation requires enough reconstruction of the executed control path to determine whether the assumptions supporting the claim remained true. Reconstruction should recover the material sequence, identities, authority state, relevant context, configurations, controller decisions, human decisions, dispatch, execution, discrepancies, overrides, retries, containment, recovery, and effect evidence needed for the claim.

Perfect reconstruction is not claimed. The reconstruction burden is bounded by the proposition being evaluated. Where the available record cannot reconstruct a claim-critical condition, that limitation must affect the claim state rather than being hidden behind procedural completion. The limitation must also appear explicitly in the claim-state rationale and, where material, in the associated finding.

### 9.3 Registered claims and controlling claim states

Each registered claim evaluated within an evaluation episode receives one controlling claim state. An episode may contain a parent claim and separately identified constituent claims or propositions. Their logical relationship must be prospectively declared or, for retrospective evaluation, reconstructed from the governing claim and authoritative artifacts applicable at the time of operation. That relationship may not be altered in light of the result. If retrospective evidence cannot establish the historical parent/constituent relationship, that uncertainty constrains the applicable claim state rather than permitting the evaluator to invent the relationship.

For a conjunctive parent claim, the parent is Supported only when every required conjunct is Supported. If any required conjunct is established false, the parent claim is Contradicted / Failed. Where no required conjunct is Contradicted / Failed, Unsupported takes precedence over Indeterminate for purposes of determining the maximum defensible state of the conjunctive parent. A claim-critical Indeterminate constituent prevents the parent from being Supported when no more adverse applicable state controls. Narrowed is not part of this adverse-state precedence ladder. Narrowed applies only where affirmative evidence supports an exact smaller parent proposition. Constituent claims retain their independently determined states. Other logical forms require an explicitly governed composition rule appropriate to the registered claim. The evaluator may not invent or alter the composition rule after seeing the evidence.

The five controlling claim states are:

| **Claim state**       | **Meaning**                                                                                |
|-----------------------|--------------------------------------------------------------------------------------------|
| Supported             | Sufficient independently defensible evidence supports the declared claim.                  |
| Narrowed              | Affirmative evidence supports a more limited proposition than the one originally declared. |
| Indeterminate         | Available evidence is insufficient to determine whether the applicable claim holds.        |
| Unsupported           | Available evidence does not support the claim.                                             |
| Contradicted / Failed | Available evidence establishes that a required condition or claimed result did not hold.   |

Pending is a workflow state only. It may be used while a defined evidence window remains open, but it is not a final assurance conclusion.

### 9.4 Narrowing is not uncertainty or failure laundering

Narrowed requires affirmative evidence for an exact smaller proposition. It is not a label for discomfort with the evidence and it is not a mechanism for converting a failed parent claim into success. If the evaluator cannot state and support the smaller proposition, the correct result is Indeterminate, Unsupported, or Contradicted / Failed as the evidence requires.

When a parent claim fails but a constituent proposition remains supportable, the constituent may be recorded separately as Supported. That supported constituent does not erase or replace the historical state of the original parent claim. This discipline prevents broad claims from surviving evidentiary failure through cosmetic rewriting after the fact.

### 9.5 Historical integrity of claim states

Claim-state history must be preserved. A later remediation, corrected configuration, favorable evaluation, or narrower claim does not retroactively rewrite the evidentiary conclusion applicable to an earlier claim or operation. Historical claim states may be superseded prospectively, but the organization must retain what was supportable at the time, on what evidence, under which versions and assumptions.

### 9.6 Cross-episode and population aggregation

Within-episode claim composition and cross-episode aggregation are related but different engineering problems. Composition determines the state of registered parent and constituent claims within a bounded episode. Aggregation determines what repeated episode-level evidence permits the organization to claim about a recurring operation, work class, configuration, population, or period.

Cross-episode aggregation must preserve the governing claim, relevant exposure and denominator, sampling or inclusion rule, episode eligibility, consequence distribution, version changes, missing evidence, and material heterogeneity. A collection of individually Supported event claims does not automatically establish a broader population claim unless the aggregation rule and evidence burden for that broader claim are themselves satisfied.

Universal statistical or numerical thresholds are not prescribed here. The architecture requires that aggregation rules be explicit, versioned, claim-relative, consequence-aware, and resistant to post-result selection.

## 10. Findings, Remediation, Accountability, and Closure

### 10.1 Distinct records

The architecture distinguishes the discrepancy record, assurance finding, claim state, remediation action, technical closure, exception, authorized non-closure disposition, residual-risk acceptance, and baseline change.

A discrepancy is an observed difference requiring routing. A finding is an evaluated conclusion about that difference. A claim state is the evidentiary conclusion for the defined claim. Remediation is an action intended to correct or contain a condition. Technical closure is evidence that the defined closure condition has been satisfied. An exception or residual-risk acceptance is an authorization decision permitting a condition to remain; it is not proof that the condition was corrected. A baseline change alters the authorized model prospectively and must follow governed change.

### 10.2 Separation of remediation and technical closure

The operational owner may perform remediation and submit evidence but may not unilaterally close a claim-relevant finding concerning its own behavior. Technical closure requires evidence demonstrating that the defined closure condition has actually been satisfied.

Independent Evaluation, or a separately appointed closure evaluator satisfying the same claim-relative independence and competence requirements, determines whether the submitted evidence satisfies the defined technical-closure condition. Accountable Authorization may approve a non-closure disposition but may not grant technical closure.

A finding may not be treated as resolved merely because corrective action was assigned, the issue was acknowledged, a configuration was changed, a deadline expired, management accepted the risk, or the issue did not recur during an inadequately justified observation period.

### 10.3 Non-closure dispositions

An authorized exception, waiver, or residual-risk acceptance is an authorized non-closure disposition. It may permit continued operation under stated conditions, but the record must continue to show that the technical finding remains open or otherwise not technically closed. The authority, rationale, scope, period, compensating conditions, and review trigger should remain explicit.

This distinction prevents management acceptance from rewriting engineering reality. Authorization can govern what the organization chooses to do with a known condition. It cannot transform that condition into evidence of conformance.

### 10.4 Findings as learning evidence

Open, overdue, recurring, weakly closed, ineffectively remediated, repeatedly excepted, repeatedly accepted without technical closure, and repeatedly overridden findings become inputs to Loop 2. They also become evidence about evaluator governance, workload, incentives, closure quality, and organizational capacity. A finding-management process that closes records faster than it resolves conditions can itself become a claim-relevant failure mode.

## 11. Accountable Authorization

### 11.1 A cross-cutting organizational interface

Accountable Authorization is not a fourth Loop 1 assurance function. It is the cross-cutting organizational interface through which evidence-informed decisions become permitted action.

**Observation establishes evidence about what happened.
Control constrains operation.
Evaluation determines the evidentiary conclusion.
Authorization determines what the organization permits.**

Where a claim depends on human authorization, the authorization is meaningful only when the authorized person has adequate and comprehensible information, valid authority, sufficient time, and a practical ability to reject, modify, redirect, delay, cancel, or escalate before the relevant consequence becomes irreversible. A recorded approval event alone is insufficient to establish meaningful authorization.

Accountable Authorization may apply to operation activation, case-specific approvals, degraded operation, exceptions, residual conditions, restoration or expansion of authority, baseline changes, consequence-classification changes, governed system changes, and changes to the assurance machinery itself.

### 11.2 Authorization does not alter evidence

Authorization may restrict operation beyond what the evidence would permit. It may accept a residual condition within lawful and organizational authority. It may require additional evidence or professional review. It may suspend an operation even when the current claim remains Supported.

Authorization may not promote an unfavorable claim state, erase a contradiction, convert an unresolved effect into a verified effect, or grant technical closure. Where the authorizing function materially contributes to the organizational claim, the authorizing function is itself part of the evaluated system.

## 12. The Three-Baseline Machinery

### 12.1 Declared Baseline

The Declared Baseline is what the organization authoritatively says should happen. It is versioned, time-bound, and traceable to authoritative sources. It provides the reference against which claim-relevant operation is evaluated, subject to continuing declared-model fidelity.

### 12.2 Observed Baseline

The Observed Baseline is what accumulated evidence shows repeatedly does happen. It is descriptive, not normative. Repetition does not create legitimacy. A workaround, bypass, local practice, or recurring exception can become part of the Observed Baseline without becoming authorized.

### 12.3 Assured Baseline

The Assured Baseline is observed operation independently evaluated as sufficiently consistent with authorized purpose, authority, assumptions, controls, evidence requirements, and effects. It is an evidentiary characterization of actual operation, not a third source of authority.

### 12.4 Directional relationship and exception expiry

The three baselines are not equal and should not be represented as symmetrically authoritative. The Declared Baseline states the authorized model. The Observed Baseline reveals actual practice. The Assured Baseline identifies which observed practice is independently defensible against the governing claim and declaration.

Where a material difference exists between the Assured Baseline and the Declared Baseline, it must resolve through either an accountable, authorized, versioned change to the Declared Baseline or an explicit, owned, justified, and time-bounded exception. An Assured Baseline may not persist as a tolerated shadow operating model.

When a time-bounded exception expires without authorized renewal or baseline change, it ceases to authorize the divergence. The affected operation must return to the Declared Baseline, narrow its authority, enter an explicitly governed degraded mode, or suspend. Administrative inaction does not convert an expired exception into continuing authority.

## 13. Loop 2 — Organizational Learning

### 13.1 The second question

Loop 1 evaluates individual claim-relevant operations. Loop 2 evaluates the operating model itself. Its question is not whether yesterday’s procedure was followed; it is whether accumulated operational evidence shows that the declared operating model remains appropriate for the organization’s mission, authority, environment, and consequences.

Keeping the loops distinct prevents two failures. The first is allowing one unusual event to rewrite the organization before its significance is understood. The second is allowing repeated operational evidence to accumulate indefinitely without ever challenging the model that produced it.

### 13.2 Inputs

Loop 2 consumes recurring discrepancies, near misses, overrides, false positives, false negatives, operational drift, invalid assumptions, obsolete controls, incorrect routing, incomplete or degraded evidence, authority conflicts, correlated failures, unintended incentives, environmental changes, effect failures, newly discovered dependencies, classification failures, differences between documented and actual practice, recurring or weakly closed findings, repeated exceptions, repeated authorized non-closure dispositions, baseline differences, evaluator-capacity limitations, and effects produced by the assurance system itself.

No single input automatically requires a change. Loop 2 evaluates patterns, interactions, and significance. A recurring discrepancy may indicate a defective control, an obsolete rule, an incorrect consequence classification, a missing dependency, a bad schema, a training or workload problem, or an operation that has evolved beyond its declaration.

### 13.3 Counts are insufficient

Incident counts alone are weak evidence. Loop 2 must consider exposure and denominators, recurrence, correlation, severity, reversibility, consequence, operational history, novelty, and uncertainty. Ten events in ten million operations may mean something different from ten events in twelve. A rare event with catastrophic consequence may matter more than a frequent nuisance. A sudden absence of findings may indicate improvement, loss of observation, evaluator overload, or a threshold change.

The denominator itself must be governed. If the population of eligible events, opportunities, or exposures is unknown or unstable, rates may be misleading. Loop 2 should preserve enough context to distinguish a changing operation from a changing measurement system.

### 13.4 Recommendation without self-authorization

Loop 2 first produces an independently evaluated operating-model finding stating what the accumulated evidence supports about the fitness of the declared operating model. Any proposed change is a separate downstream record. Loop 2 may identify that the operating model should change. It may propose a new threshold, route, authority rule, evidence requirement, schema, observation method, control, or classification. It may recommend removal of a control that has become counterproductive. It may not authorize its own recommendation or autonomously modify the organization.

This is the organizational equivalent of the non-self-certification rule. The machinery that evaluates the operating model can influence what the organization believes, but it does not inherit the authority to redefine the model it evaluates.

## 14. Governed Change

### 14.1 The change cycle

**Accumulated Evidence → System Evaluation → Proposed Change → Hazard and Impact Analysis → Controlled Validation or Shadow Operation where appropriate → Accountable Authorization → Versioned Deployment → Operational Observation → Effectiveness Evaluation → Retain, Revise, Roll Back, or Retire**

This sequence is the bridge from learning to legitimate adaptation. Evidence may motivate change; it does not enact change. A proposal is examined for hazards and organizational effects, validated in a controlled manner where appropriate, authorized by the accountable function, deployed as a versioned change, observed in operation, and then evaluated for actual effectiveness.

### 14.2 Scope of governed change

The discipline applies not only to model updates. It applies to operating-model changes, authority changes, control changes, evidence requirements, consequence classifications, thresholds, schemas, observation methods, evaluation methods, and assurance-system configuration. A change in the definition of a field can be as consequential as a change in software when it changes which conditions can be expressed or detected.

### 14.3 Controls must be removable

The objective is continued fitness, not maximum accumulation of controls. A control introduced for a valid reason can become obsolete, duplicative, bypassed, or harmful. Controls should therefore retain provenance: why they exist, what condition they address, what evidence demonstrates their performance, and who may retire them.

Removal follows the same governed discipline as addition. The architecture should be capable of concluding that a control no longer contributes sufficient value, creates a worse failure mode, or has been superseded. A governance system that can only add constraints eventually governs its own paperwork rather than the mission.

### 14.4 Schema and representation governance

Claims, authority, assumptions, evidence, discrepancies, effects, exceptions, dependencies, outcomes, and organizational relationships are represented through schemas. Schemas determine which conditions the system is capable of expressing and therefore which discrepancies can become visible.

Schema creation and consequential modification must be attributable, versioned, reviewed, tested, authorized, and recorded. Loop 2 must periodically ask whether the current representation can express the failures, relationships, and operating conditions that matter. A schema blind spot is not merely a data-management issue when it prevents a claim-relevant condition from becoming observable.

## 15. Governance of Evaluators and the Assurance Machinery

### 15.1 The evaluator is consequential

The assurance function can choose evidence, define comparison criteria, classify findings, adjust thresholds, shape escalation, recommend changes, and materially influence what leadership believes about the organization. Independence from operations therefore does not exempt the assurance function from governance.

A governing body or equivalent accountable mechanism must oversee whether evaluation remains sufficiently independent, trustworthy, technically competent, professionally competent, properly bounded, adequately resourced, and aligned with organizational mission. Its purpose is not to re-adjudicate every event. Its purpose is to govern the machinery that performs assurance.

### 15.2 Evaluator competence

Technical competence and professional competence are distinct. An evaluator may understand evidence integrity, software behavior, and reconstruction while lacking the professional competence needed to judge a legal, clinical, engineering, financial, or safety proposition. Conversely, a domain expert may understand the substantive decision while lacking the technical basis to evaluate a compromised evidence path.

The evaluation function must assemble competence appropriate to the claim. Where the claim crosses domains, no single reviewer should be presumed omniscient. The assurance record should make material competence limits visible.

### 15.3 Evaluator health measures

Governance should consider procedure conformance, evidence-source health, common dependencies, false-positive and false-negative performance, finding-closure quality, workload, backlog, time to disposition, escalation latency, reviewer disagreement or divergence, override patterns, claim narrowing, threshold changes, schema changes, consequence-classification changes, unevaluated areas, evaluator incentives, and effects caused by assurance itself.

These measures are not a universal scorecard. They are sensors for assurance-system degradation. A decline in disagreement among independent reviewers, for example, may indicate genuine convergence, easier cases, common-mode dependence, automation bias, or a hollowing review function. The signal requires evaluation; it is not proof of individual misconduct.

### 15.4 Capacity is an assurance requirement

An assurance system that generates more findings than qualified personnel can meaningfully adjudicate is operating outside its own assured envelope. Backlog, rushed closure, indiscriminate automation, or ceremonial review can make nominal independence meaningless.

The assurance function must therefore define predeclared capacity criteria or an explicitly governed method for determining when qualified review capacity has failed. The method should be consequence-aware and may consider consequence-weighted demand, workload, backlog, time to disposition, escalation latency, evidence-package quality, repeat findings, override frequency, reviewer availability, and the percentage of operations requiring manual intervention. Universal staffing ratios are neither required nor implied.

When the governed capacity condition is breached, the assurance system must identify the affected claims, operations, or work classes. Backlog and delayed evaluation become explicit evidence limitations. Where a claim depends upon meaningful qualified review, the applicable claim, authority, assurance mode, or degraded-operation status must reflect the loss. The architecture must not preserve the fiction of meaningful human review after practical review capacity has disappeared.

Restoration requires evidence that qualified capacity has returned within the applicable operating envelope. Administrative declaration alone is insufficient. Staffing levels, scheduling methods, tooling choices, and local throughput design remain implementation matters; the assurance consequence of capacity failure does not.

### 15.5 Bounding recursive assurance

The evaluator itself may fail, but the answer is not an infinite hierarchy of equally complex evaluators. Recursive assurance follows a simplicity gradient. Higher-order assurance should rely increasingly on simpler deterministic mechanisms, independently protected evidence, diversity on critical paths, periodic independent review, explicit residual assumptions, accountable governance, and transparent residual-risk acceptance.

The objective is not absolute independence. It is sufficient claim-relative independence to preserve a meaningful path capable of challenging or falsifying the claim.

## 16. Common-Mode Assurance Failure

### 16.1 The three-way failure

Common-mode analysis must ask whether one dependency, defect, or authority can simultaneously corrupt the operation, the evidence of the operation, and the evaluation of that evidence. This is more dangerous than an ordinary component failure because it can make an incorrect system appear correct to itself.

Potential common dependencies include identity systems, networks, time sources, cloud control planes, certificate infrastructure, logging pipelines, evidence stores, claim registries, schema registries, comparison engines, shared evaluation models, policy interpreters, assurance orchestration, and accountable authority structures.

### 16.2 Proportionate countermeasures

Where consequence justifies them, proportionate countermeasures should be introduced, including as appropriate segmentation, diversity, alternate observation paths, alternate evidence paths, independent challenge, and alternate evaluation paths. The purpose is to preserve a falsification path, not to duplicate every component.

Where the common-mode dependency is an accountable authority rather than a technical component, countermeasures must remain organizational as well as technical. The architecture must preserve independently defensible evidence and evaluation, prevent the authority from altering claim state or granting technical closure, and, where consequence justifies it, preserve an independent challenge or alternate authorization path appropriate to the governing authority structure.

Universal redundancy is neither required nor desirable. The strength of separation should scale with consequence, reversibility, uncertainty, operational history, and the credibility of common-mode failure. A low-consequence reversible workflow may tolerate shared infrastructure that would be unacceptable for a claim whose failure could be irreversible and difficult to detect.

## 17. The Volume 2 → Volume 3 Evidence Contract

### 17.1 What Volume 2 specifies

For each claim-relevant evidence or intervention requirement, Volume 2 specifies what must be knowable, why it matters, which claim or authority depends upon it, the required evidence properties, the required timing, the required independence, and the consequence if the requirement cannot be satisfied.

This specification is functional rather than technological. Volume 2 may require independently defensible evidence that a downstream action occurred within a defined period. It does not decide whether that evidence comes from an API, read-only database view, physical sensor, human confirmation, UI agent, event stream, external organization, or another mechanism. That is Volume 3 engineering.

Likewise, Volume 3 owns Historical Runtime Evidence, Baseline Bootstrap, and Graduated Integration. Those mechanisms may supply, improve, or bound the evidence required here, but Volume 2 does not absorb them simply because its assurance conclusions depend upon their eventual outputs.

### 17.2 Capability states

Volume 3 returns one of the following capability states for a required evidence or intervention path:

- Available as required.

- Available with limitations.

- Available only through indirect evidence.

- Available too late for preventive control.

- Not independently defensible.

- Unavailable.

These states are not implementation grades. They are assurance-relevant facts. A path that is technically available but arrives after the irreversible consequence window is not equivalent to a path that supports Preventive Assurance.

### 17.3 Consequence of an unsatisfied contract

Unavailable evidence may not be silently converted into assumed evidence. Depending on the claim and consequence, the supported claim may be narrowed, become Indeterminate or Unsupported; authority may need restriction; assurance may become Post-Consequence only; or the underlying system may need to change. Where available evidence affirmatively establishes failure, the applicable claim may be Contradicted / Failed.

Repeated evidence limitations become Loop 2 evidence. They may reveal that the declared operating model assumes visibility or control that the as-operated enterprise cannot actually provide. Volume 3 integration difficulty does not rewrite the Volume 2 assurance requirement, but Volume 2 also may not assume evidence that Volume 3 demonstrates the organization cannot produce.

## 18. Worked Operational Example

### 18.1 Purpose of the example

The following example is deliberately generic. It illustrates the architecture without making any particular industry, technology, control method, or implementation mandatory. Its purpose is also to show explicitly how parent and constituent claims are represented after the claim-composition defect discovered in the first draft.

Assume an organization registers the following compound claim: 'For Operation X, the organization identified Condition Y, acted under valid authority within the required time, executed the approved intervention, and achieved Effect Z within the required period.' The claim is conjunctive: detection, authority, timeliness, execution, and timely effect are all required for the parent claim to be Supported.

The organization also registers or can reconstruct from the governing claim the constituent propositions needed for evaluation: identification of Condition Y; valid authority; timely intervention; execution of the approved intervention; occurrence of Effect Z; and occurrence of Effect Z within the required period. These constituent claims do not replace the parent claim. They make its logic evaluable.

### 18.2 Claim-relative episode

The evaluation episode binds the approved parent and constituent claims, consequence class, item definition, measurand, authority state, declared operating model, runtime assumptions, evidence requirements, configuration, effective period, and versions. A runtime-maintained assumption states that the intervention path remains available and can become effective within the consequence window.

Claim-Directed Observation preserves the originating signal, relevant context, authority state, controller decisions, professional decision where applicable, dispatch, execution receipts, and an independently defensible observation of Effect Z. Baseline Operational Observation separately detects that an alternate execution path has begun appearing in similar operations even though it is absent from the declaration.

### 18.3 Discrepancy and control

During Operation X, reconciliation detects that the normal intervention path is unavailable. A discrepancy record is created with the reference condition, observed condition, evidence, time, consequence, urgency, affected claims, and routing. The runtime controller redirects the operation to an authorized alternate path.

The preventive claim is not established by the fact that a redirect command was issued. The organization must establish that detection, evidence routing, decision, and effective intervention all completed before the irreversible consequence. If the alternate path constrained the operation only after the window closed, the episode may still be reconstructable, but it was not preventively assured.

### 18.4 Claim composition, execution, and effect

The alternate path reports successful execution. Independent effect evidence later establishes that Effect Z occurred, but with a material delay outside the parent claim’s required period.

Independent Evaluation therefore determines separate states for the registered propositions. The execution proposition is Supported. The proposition that Effect Z occurred is Supported. The timely-effect proposition is Contradicted / Failed because the required time condition did not hold. Because timely effect is a required conjunct of the original compound claim, the parent claim is Contradicted / Failed.

An exact smaller claim concerning execution alone may remain separately recorded as Supported. It does not cosmetically rewrite the failed parent claim. The historical record retains both the Supported constituent proposition and the Contradicted / Failed parent claim.

Independent Evaluation reconstructs the material control path and also creates a finding concerning the unavailable normal intervention path. The operational owner repairs the path and submits evidence. Technical closure is granted only if Independent Evaluation, or an equivalently independent closure evaluator, determines that the defined closure condition has been demonstrated. Management acceptance alone does not close the finding.

### 18.5 Movement into Loop 2

Across later episodes, the same normal-path unavailability recurs under a particular environmental condition. Loop 2 considers exposure, recurrence, severity, reversibility, correlation, operational history, and the denominator of affected operations. It concludes that the declared operating model incorrectly assumes a single intervention path remains reliable under that condition.

Loop 2 proposes a change. Hazard and impact analysis examines the alternate path, controlled validation tests it, Accountable Authorization approves a versioned modification to the Declared Baseline, deployment occurs, and subsequent observation evaluates effectiveness. The organization has learned, but the learning became authoritative only through governed change.

## 19. Engineering Limits and Residual Conditions

This architecture does not create perfect independence. Shared infrastructure, institutional incentives, incomplete evidence, common training data, correlated human judgment, and unknown dependencies can remain. The engineering obligation is to identify material dependencies, preserve meaningful challenge paths, and state residual assumptions rather than claim impossible separation.

It does not create universal visibility. Some physical, human, vendor, legacy, or external conditions may be weakly observed or unavailable. The consequence is an assurance limit, not permission to infer conformance.

It does not guarantee preventive intervention. Some consequence windows are too short, some systems cannot be effectively constrained in time, and some evidence arrives only after the event. Post-Consequence Assurance remains valuable for reconstruction, accountability, remediation, and learning, but it must not be represented as prevention.

It does not guarantee perfect reconstruction or discovery of every hazard. Reconstruction is claim-relative and evidence-bounded. Baseline Operational Observation can reveal model incompleteness, but it cannot prove that no unknown dependency exists.

It does not automate organizational judgment. Evaluation may be highly automated in portions, and low-consequence operations may operate under bounded preauthorization, but accountable institutional authority remains responsible for what the organization permits and how its governing model changes.

High-volume, low-consequence episode binding, reviewer scheduling, data retention, compute capacity, and local profile design remain implementation constraints unless evidence shows that they make a required assurance function impossible. Consequence-scaled assurance permits proportionate engineering; it does not permit silent weakening of claim or evidence rules.

The contribution is therefore a discipline rather than a guarantee: define the claim, preserve the distinctions among observation, control, evaluation, and authorization, bind operation to versions and authority, independently evaluate evidence, preserve unfavorable history, learn from accumulated reality, and change the organization deliberately.

## 20. Conclusion

An AI-mediated organization cannot establish its correctness by showing that a model produced a good answer, a controller issued the expected command, a human clicked approve, a workflow closed, or a log recorded success. Each artifact may be necessary. None automatically establishes the organizational claim.

Volume 2 therefore treats assurance as a claim-relative engineering problem. It begins with an approved claim, governed authority definitions, and a declared operating model. It tests the fidelity of that declaration to authoritative sources. It observes operation through evidence paths capable of challenging claim-relevant components. It reconciles runtime-maintained assumptions with reality. It uses Runtime Control where effective intervention remains possible. It separates recommendation, authorization, dispatch, execution, effect, and verified effect. It evaluates evidence quality and assigns controlling claim states to registered parent and constituent claims without allowing authorization to rewrite evidence.

The architecture operates through two loops, three assurance functions, and one cross-cutting authorization interface. Independent Observation establishes evidence about what happened. Runtime Control constrains operation while intervention remains possible. Independent Evaluation determines what the evidence supports. Accountable Authorization determines what the organization permits within, or more restrictively than, that evidentiary ceiling.

The architecture then refuses to stop at individual cases. Findings, weak closure, repeated non-closure, overrides, baseline differences, evidence limitations, evaluator capacity, and assurance-system effects become evidence for Loop 2. The organization compares what it says should happen with what repeatedly does happen and with what independently evaluated evidence shows can be defended. Material differences cannot remain indefinitely as a shadow operating model. They must be corrected, explicitly excepted for a bounded period, or converted into an authorized versioned change; expired exceptions cease to authorize divergence.

The assurance machinery is itself part of the governance problem. Evaluators require competence, independence, capacity, protected evidence, and oversight. Common dependencies—including accountable authority—can corrupt operation, evidence, and evaluation together and therefore require consequence-scaled challenge. Recursive assurance is bounded through simpler mechanisms, diversity where justified, periodic independent review, explicit residual assumptions, and accountable governance rather than an infinite hierarchy.

Volume 2 ends by specifying an evidence contract rather than an integration architecture. It states what must be knowable, when it must be known, how independently it must be defensible, and what happens when the requirement cannot be met. Volume 3 must connect those requirements to the heterogeneous enterprise that actually exists, including the historical bootstrap and graduated-integration mechanisms allocated there by the frozen Plan. If the enterprise cannot provide the required evidence, the architecture does not manufacture assurance.

The governing discipline is continuous: define what the organization claims; establish what authority applies; establish what evidence the claim requires; verify that the declared model faithfully represents authoritative sources; observe what the organization actually does; independently determine what the evidence supports; intervene before consequence where demonstrated capability permits it; verify external effect; learn from differences between intended and actual operation; govern classifications, schemas, evaluators, and the assurance machinery; authorize changes deliberately; and continue observing the organization as it evolves.

The objective is not an autonomous organization. It is an organization capable of seeing itself more accurately, acting within valid authority, challenging its assumptions, learning from operational evidence, and improving without surrendering final authority over its own correctness to any single person, department, vendor, model, agent, controller, evaluator, or authorization seat.

## Source Note

This final controlled manuscript is derived from the project corpus under the authority ordering established for the three-volume architecture. Project Plan v0.2 — Frozen Architectural Baseline controls architecture, terminology, claim states, volume boundaries, baseline machinery, and change classification. The Controller Is Part of the System Under Test supplies the principal engineering inheritance for item definition, assumptions of use, runtime reconciliation, observation/control/evaluation separation, epistemic independence, reconstruction, and claim licensing. AI Orchestration Governance: The Agent Is Not the System supplies the Volume 1 premise consumed here. The Model Is Not the Claim supplies supporting claim, authority, execution, effect, evidence, and reconstruction mechanics only where they map into the controlling vocabulary. From Defect Detection to Governed Enterprise Learning is used as an operational illustration of evidence routing and governed learning, not as architectural authority.

### References — Project Corpus

Jeffords, Lawrence. Project Plan v0.2 — Frozen Architectural Baseline: A Three-Volume Architecture for Governed AI-Mediated Organizations. August 2026.

Jeffords, Lawrence. The Controller Is Part of the System Under Test: Item Definition, Assumptions of Use, and Claim Licensing in AI Evaluation. Public Edition, 2026.

Jeffords, Lawrence. AI Orchestration Governance: The Agent Is Not the System. August 2026.

Jeffords, Lawrence. The Model Is Not the Claim: The Missing Assurance Rule for Critical AI Operations. Public Discussion Paper, 10 August 2026.

Jeffords, Lawrence. From Defect Detection to Governed Enterprise Learning: Using the Sperry/Elmer Rail Example as a Narrow Illustration of DOT-Scale AI Governance. Revised edition, July 2026.
