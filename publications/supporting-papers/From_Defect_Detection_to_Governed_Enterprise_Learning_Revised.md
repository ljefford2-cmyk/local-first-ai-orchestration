# From Defect Detection to Governed Enterprise Learning

*Using the Sperry/Elmer Rail Example as a Narrow Illustration of DOT-Scale AI Governance*

**Lawrence Jeffords — End-User and Operator**

**Status:** Revised edition, incorporating peer review — July 2026

> **Controlled-text note:** This Markdown file is a repository transcription derived from the controlled DOCX supplied for the August 2026 update. The source DOCX remains authoritative for exact layout, tables, figures, and publication identity; its SHA-256 is recorded in the [Publication Manifest](../MANIFEST.md).

---

## Abstract

Sperry Rail Service’s AI-enabled rail inspection capabilities, including systems commonly associated with “Elmer” and Rail Health, should not be treated as a template for an enterprise AI architecture. They are better understood as a narrow operational example: one slice of one signal group within one specialized rail infrastructure function. Their value as an example is not that a government agency should copy the system, but that they make visible a larger enterprise problem: how to move time-sensitive, domain-specific evidence to the right human decision-maker at the right moment.

In a DOT-scale environment, the central challenge is not simply detecting anomalies. The challenge is governing how models, data, human judgment, audit records, and organizational learning interact. A defect is the immediate artifact requiring action. The knowledge lies in understanding how that defect emerged from interconnected conditions over time. The enterprise version of “Elmer” is therefore not a single model. It is the governed audit, routing, evaluation, and learning architecture that allows an organization to improve while keeping human authority—not the model—at the center of consequential decisions.

*This revised edition responds to peer review. It names the framework the original edition was using without knowing its name—system-theoretic safety analysis—and it closes the seam the review exposed: what keeps structural human authority from hollowing out into reflexive approval.*

## 1. Introduction

Artificial intelligence is often discussed as though the central question is whether a model can detect, classify, or recommend something. That framing is too narrow for a large government enterprise. In public-sector transportation environments, the more important question is: How does a detected signal become governed knowledge, routed action, human decision, and organizational learning?

Sperry Rail Service’s rail inspection ecosystem provides a useful example because it deals with a real-world safety domain: rail condition monitoring. Systems such as Elmer and Rail Health appear to support the identification, interpretation, and management of rail defects and related condition data. But this example must be framed carefully. It is not a proposed architecture for the Department of Transportation. It is not a pattern to copy wholesale. It is not an enterprise operating model.

It is a narrow example of a specialized capability in one department-like function: detecting and contextualizing rail anomalies. Its relevance is that it demonstrates the kind of data, signal, and decision-support flow that a larger enterprise architecture must be capable of governing.

The lesson is not “build another Elmer.” The lesson is: *build an enterprise environment where many narrow systems like Elmer can remain tightly controlled, produce useful evidence, route that evidence to the correct human authority, preserve the decision trail, and help the organization learn over time.*

## 2. The Sperry/Elmer Example as a Narrow Operational Slice

For purposes of this paper, Sperry/Elmer should be understood as an example of a domain-specific AI-enabled inspection capability. It represents one specialized use case: rail defect detection and rail health intelligence. In such a setting, sensors collect inspection data, analytical systems flag possible defects, and human experts review the evidence to determine whether action is required.

That is a valuable capability, but it is only a small slice of a much larger enterprise. It does not represent agency-wide data governance, model governance, audit architecture, cybersecurity, records management, procurement oversight, asset management integration, legal accountability, or cross-modal transportation intelligence.

A large government transportation agency may deal with rail infrastructure, aviation systems, highways, maritime systems, pipelines, grants, procurement, cybersecurity, civil rights compliance, environmental review, safety investigations, and operational continuity. Each of those domains may generate its own signals, anomalies, models, rules, authorities, and risks. The enterprise architecture must govern all of them without allowing any single model or vendor platform to become the decision authority.

The Sperry/Elmer example is therefore best described as: *a concrete example of one specialized signal-processing and condition-monitoring capability within one operational domain, useful for illustrating the broader need to route evidence to human authorities under governed controls.*

## 3. The Defect Is the Artifact; the Knowledge Is the Relationship

The most important conceptual distinction is between a **defect** and **knowledge.**

A defect is the current artifact requiring attention. It is the visible problem: a rail flaw, geometry deviation, signal irregularity, structural crack, pavement distress indicator, inspection exception, or other anomaly. It is what appears in the workflow as something that may require action.

But the defect is not the full knowledge. The knowledge comes from understanding that anomalies do not develop in isolation. A rail defect, for example, may be connected to track geometry, cross-level changes, rail wear, ballast condition, drainage, subgrade stability, traffic loading, temperature, maintenance history, and prior inspection trends. A single inspection captures current state; repeated inspections reveal evolution; long-term history helps explain causality. The deeper knowledge lies in the relationships among conditions over time, not in the isolated defect alone.

This is the enterprise lesson. A model may identify an anomaly, but the organization must determine:

- Why did this anomaly appear here?

- What other signals were present before it emerged?

- What conditions are correlated with it?

- What maintenance, environmental, or operational factors contributed?

- Who needs to know now?

- What decision authority applies?

- What action was taken?

- Did the action resolve the risk, reduce it, transfer it, or reveal a broader pattern?

The model surfaces an artifact. The enterprise must produce understanding.

There is a formal name for this way of seeing, and this paper did not know it when the argument was first written. Nancy Leveson’s work at MIT—STAMP (System-Theoretic Accident Model and Processes) and its analysis method STPA—rejects the idea that failures live inside components. In her framing, hazards are emergent properties of the interactions among components that may each be behaving exactly as designed, and safety is enforced or lost through a hierarchical structure of control loops. That is the same claim this section makes in operational language: the defect is the part; the knowledge—and the hazard—lives in the relationships. A reviewer with deep expertise in system-theoretic safety analysis recognized the structure through this paper’s shop-floor vocabulary and supplied the field’s name for it. The revision adopts the vocabulary where it sharpens the argument, and holds the boundary stated in Section 7 about what this architecture is and is not.

## 4. The Purpose Is Routing Evidence, Not Delegating Authority

In a DOT-scale architecture, the primary purpose of AI-enabled systems should not be to replace human decision-makers. The purpose should be to identify, package, prioritize, and route evidence so the appropriate human can make a timely, informed, accountable decision.

This distinction matters. In safety-critical, regulated, or mission-sensitive environments, the customer does not want unexplained surprises. The customer wants repeatability, accountability, and proof that the system did what it was supposed to do. Regulated workflows, government services, clinical protocols, financial reconciliation, and safety-critical operations share a common requirement: the system must be able to show what happened and whether the correct process was followed.

That means the model’s role should be bounded. A model can help detect, classify, summarize, prioritize, compare, or recommend. But the model should not become the final authority over consequential action. Human-held authority remains essential.

In the Sperry/Elmer example, the important enterprise pattern is not “AI found a rail defect.” The important pattern is:

- A specialized system detects or flags a signal.

- The system packages relevant evidence.

- The evidence is routed to the correct role.

- A qualified human reviews the context.

- The human accepts, rejects, modifies, or escalates.

- The decision and action are recorded.

- The outcome becomes part of the organization’s learning record.

This is how AI remains useful without becoming ungoverned. In control-structure terms, this sequence is a single control loop: the specialized system is a sensor, the routing layer carries the signal, the human seat is the controller, and the recorded decision is both the control action and the feedback that the loop closed. Naming it that way matters, because it makes the failure modes analyzable instead of anecdotal: a loop can fail because the sensor missed, because the signal was routed to the wrong controller, because the controller’s picture of the situation had drifted from reality, or because feedback never returned. Section 7 uses exactly this lens on the failure mode that reviewers of the original edition pressed hardest.

## 5. Exploration Versus Execution: Why the Distinction Matters

The Sperry/Elmer example also helps illustrate the difference between exploration-oriented AI and execution-oriented AI.

Exploration systems are useful when the goal is discovery. They help users investigate open-ended questions, generate hypotheses, synthesize incomplete information, or explore possible explanations. Their strength is flexibility.

Execution systems are different. They are built for repeatability, controlled scope, and auditability. In execution-oriented environments, the system should not freely decide what process to follow. It should operate within defined routes, permissions, policies, thresholds, and human approval gates. The distinction is central: exploration systems use reasoning as the control plane, while execution systems use routing, rules, policy, and bounded authority as the control plane.

Rail defect detection, safety inspection, and infrastructure maintenance workflows belong closer to the execution side. They may contain analytical and exploratory components, but the operational decision path must remain bounded and auditable. A system that is excellent at finding interesting answers may still be wrong for a workflow that requires the same process, the same authority structure, and the same evidence chain every time.

For a government enterprise, this means AI systems should be classified by function:

|                     |                                                  |                                                           |
|---------------------|--------------------------------------------------|-----------------------------------------------------------|
| **Work Type**       | **Appropriate AI Role**                          | **Governance Need**                                       |
| Open-ended research | Exploration support                              | Source review, uncertainty labeling, human interpretation |
| Signal detection    | Bounded model assistance                         | Validation, thresholds, versioning, audit                 |
| Safety action       | Decision support only unless formally authorized | Human approval, escalation, records                       |
| Compliance workflow | Execution support                                | Repeatability, traceability, policy adherence             |
| Enterprise learning | Governed synthesis                               | Independent evaluation and authority-approved change      |

The Sperry/Elmer example is valuable because it shows how a narrow model can assist with signal detection, but it also shows why the enterprise must control what happens after detection.

## 6. The Enterprise Version of “Elmer” Is Not a Model

The enterprise version of Elmer is not one rail inspection AI. It is not a single model, chatbot, dashboard, or vendor system. The enterprise version is the governed layer that allows many such systems to operate safely across the organization.

That layer must answer questions such as:

- What model produced this signal?

- What data did it use?

- What version was running?

- What confidence or uncertainty was attached?

- What rules determined routing?

- Who received the alert?

- What context was included or withheld?

- What human decision was made?

- What override occurred?

- What action followed?

- What was the result?

- Should procedures, thresholds, or routing rules change?

This is where audit and governance become central. In mature AI operations, the audit log is not merely a compliance accessory. It becomes the backbone of organizational learning. Every meaningful event—routing decision, context-packaging action, model response, human decision, override, anomaly, failure, and system event—should become part of an append-only record. That record turns AI operations into a sequence of bounded, reviewable facts.

The system should not learn by silently rewriting itself. Instead, it should improve through a governed path: the audit stack accumulates evidence, a separate evaluator reviews that evidence, a held authority reviews the recommendation, a configuration change is approved, and only then does the system operate under new rules. This preserves adaptability while preventing autonomous self-governance.

Named plainly, that governed path is a control loop nested around the operational loops of Section 4. The inner loops handle individual signals in real time: detect, route, decide, act, record. The outer loop operates on a slower cycle and controls the inner loops themselves: the audit stack is its sensing channel, the independent evaluator is its instrument, and the held authority is its controller—the only actor permitted to change the rules the inner loops run under. Loops nested within loops, with the human authority holding the outermost one. That nesting is not an implementation detail. It is the property that lets the enterprise adapt without ever placing a model in charge of its own constraints.

## 7. Human Authority Must Be Structural, Not Decorative—and What Keeps It That Way

A common failure in AI governance is treating human review as a superficial approval step. In a serious enterprise architecture, human authority must be structural. It must be built into the operating model. The human should not merely rubber-stamp a model output. The human should occupy a defined authority seat with responsibility for accepting, rejecting, modifying, escalating, or deferring action, and the system should preserve that decision as a durable event.

The original edition of this paper stopped there, and a reviewer was right to push on it. Saying authority must be structural does not answer the harder question: what keeps the seat from hollowing out while still sitting on the org chart? If the model is right most of the time—and in a mature system it will be—the human in the loop is at risk of becoming a decorative approver: still accountable on paper, no longer actually deciding. And the one time the machine is wrong is precisely the time the human was supposed to catch it. This section closes that seam.

The first move is to stop treating rubber-stamping as a character flaw. In system-theoretic terms, an approval issued after degraded review is an unsafe control action; reflexive approval is the structural pattern that makes that unsafe control action likely—a controller whose picture of the process has gone stale, acting on a degraded signal, under incentives that reward throughput. The causes are almost always structural—too many alerts, evidence presented badly or with false confidence, time pressure, stale training, and above all a scoreboard that counts cases cleared per hour. A system measured that way will tend to produce rubber-stamping no matter who is sitting in the chair. If the cause is structural, the fix must be structural too. Exhortation is not an engineering control.

The architecture answers with two seats and a signal.

**The measure-holding seat.** The first structural fix concerns who defines success for the review function. The measure-holding seat is the authority that decides what counts as a good review, what evidence demonstrates it, and what incentives attach to it—and it must be held separately from the operation whose throughput it might otherwise be tempted to flatter. This is the same rule that runs through the rest of the architecture: nothing certifies itself. If the review seat is measured by the speed of its own approvals, the failure has been built in by hand. The measure-holding seat must reward genuine independent judgment, including the judgment that disagrees with the model.

**The divergence signal.** The second fix acknowledges a hard limit: you cannot govern a human’s attention by watching the human. There is no sensor that reads whether someone is really thinking. What can be watched is the relationship among independent judgments. When several independent reviewers—or independent models—examine the same class of signal, real judgment produces a measurable amount of honest disagreement in nontrivial cases. Independence here means measured independence of judgment behavior—not merely separate model names, vendors, or reviewer identities—because actors trained on the same data, tuned by the same team, or shaped by the same benchmark culture can share error modes while appearing distinct on paper. A seat that has quietly decayed into rubber-stamping produces the opposite pattern: its judgments stop carrying independent variance and begin agreeing with the machine a little too perfectly. That collapse of disagreement is a system-level signal, and it lives in the audit trail the architecture already keeps. It is evidence that the seat needs attention—not proof by itself—and it is read by watching relationships among valid actors, never by surveilling the person in the seat. The signal is a process-health signal, not a personal suspicion score; escalation is governed by role, consequence, and pattern, not by ad hoc scrutiny of an individual. The hollowed-out seat is caught the same way every other condition in this architecture is caught: in the relationships, not inside any one component.

**Consequence-based allocation of review.** The reviewer’s second point belongs here, because attention is the scarce resource this whole section is protecting. If every trivial, reversible, low-stakes call demands a full human review, the enterprise burns the attention it needs for the calls that matter—and that exhaustion is itself a structural cause of rubber-stamping. Human review should therefore be assigned where consequence, uncertainty, novelty, conflicting authority, or the weight of evidence actually demands it, and a machine earns a longer leash only where stakes are low, actions reverse cleanly, and confidence is demonstrated. That is not a loosening of authority. It is spending human attention where it does real work, so the review that remains is real.

In practical terms, the enterprise architecture must support:

- Human review before consequential actions, allocated by consequence rather than uniformly.

- Clear role-based authority, with the measure for each review seat authored outside that seat.

- Ability to cancel, redirect, modify, or escalate.

- Audit records of both model output and human decision, sufficient to support divergence analysis across independent judgments.

- Fallback processes when AI systems are unavailable or suspended.

- Restrictions on what models can do without approval.

Human authority is not a user-interface feature. It is a governance requirement—and now, a monitored one.

One boundary should be stated plainly, because it protects both this paper and the framework it now cites. STAMP and STPA are methods for finding how a control structure can fail; they produce the catalog of unsafe control actions and the scenarios behind them. What this paper describes is the runtime answer for what happens once those failures are found—how a signal is held, routed, evidenced, denied, escalated, or turned into something the organization learns from. STPA-derived hazards and constraints are inputs to this architecture. The architecture is not an instance of STPA. And the convergence is itself worth recording: Leveson reached this picture from control engineering; this paper reached it from operational railroad infrastructure, where governing truth is indexed to each individual operator and nothing is allowed to certify itself. Two unrelated starting points arriving at the same structure is not a coincidence to be explained away. It is evidence that the structure is real.

## 8. Right Data, Right Person, Right Moment

The real operational value is timely routing of evidence. A rail defect, bridge anomaly, aviation safety signal, cybersecurity alert, or grant compliance issue only creates value if it reaches the right decision-maker in a usable form at the right moment.

A mature enterprise orchestration environment must therefore manage not only model outputs but context packages. Different users need different forms of evidence. A field inspector may need a short checklist or mobile alert. A supervisor may need an exception summary and approval menu. A researcher may need the long evidence package. An executive may need trend-level risk and decision options. Enterprise work occurs across different devices, environments, and cognitive loads, so the interface should be appropriate to the user, role, situation, and trust level.

This is directly relevant to the Sperry/Elmer example. A detected rail condition may need to be routed differently depending on severity, location, confidence, operational impact, and authority:

- Immediate field alert for urgent safety risk.

- Supervisor review for maintenance prioritization.

- Engineering analysis for recurring condition patterns.

- Asset management integration for lifecycle planning.

- Audit review for model performance and decision quality.

- Executive reporting for systemic risk trends.

The same defect signal may therefore produce different information products for different users. The enterprise architecture must control those routes.

## 9. Governance Prevents the Model from Becoming the Organization

Without governance, a model can become an informal authority. Users may defer to it because it is fast, technical, confident, or embedded in workflow. Over time, the organization may forget that the model is only one instrument within a larger decision system.

A governed architecture prevents that drift by separating functions:

|                         |                                  |
|-------------------------|----------------------------------|
| **Function**            | **Responsible Actor or System**  |
| Signal generation       | Sensor, model, or source system  |
| Context packaging       | Orchestration layer              |
| Routing                 | Policy-controlled enterprise hub |
| Decision                | Authorized human                 |
| Audit                   | Immutable event stream           |
| Evaluation              | Independent evaluator            |
| Change approval         | Held authority seat              |
| Organizational learning | Governance process               |

This separation matters because no actor should be the final authority over its own correctness. The system that performs the work should not be the sole judge of that work, and it should not be able to rewrite the record it is judged against. A trustworthy operating model requires immutable evidence, independent evaluation, authority documents, and distinct human-held seats for decision and action.

Readers familiar with system-theoretic safety analysis will recognize what this table actually is: a hierarchical control structure. Each row is a position in the structure; the rows above and below each actor define what constrains it and what it constrains; the audit stream is the feedback channel that lets higher loops observe lower ones. The original edition drew this structure without knowing its formal name. The revision names it, because naming it makes it analyzable: the ways such a structure fails—a missing feedback path, a controller acting on a stale picture of the process, a constraint no loop enforces—are exactly the failure classes STPA enumerates, and each row of this table is a place where one of them can be asked about directly.

That is the true enterprise lesson. The goal is not model autonomy. The goal is controlled assistance, accountable decision-making, and governed improvement.

## 10. Why This Matters for a Massive Government Agency

A massive government agency does not have one clean data source, one model, one workflow, one user type, or one operational environment. It has modern systems, legacy platforms, spreadsheets, shared drives, internal portals, vendor applications, field devices, databases, and human-facing workflows. Enterprise AI must operate across this mixed environment without losing control of data boundaries, permissions, auditability, or human authority.

That is why a narrow example like Sperry/Elmer is useful but insufficient. It shows what a specialized signal system can do. It does not solve the enterprise problem of governing many such systems across many departments, missions, and authorities.

At enterprise scale, the architecture must be able to handle:

- Multiple specialized models.

- Multiple data sensitivity levels.

- Multiple operational domains.

- Multiple human authorities.

- Multiple device types.

- Multiple approval paths.

- Multiple audit and records requirements.

- Multiple failure modes.

- Multiple vendor and internal systems.

The orchestration layer must classify intent, package context, apply sensitivity policy, enforce capability gates, coordinate specialized agents, record audit events, and deliver role-appropriate results. It must also support internal systems, legacy UI-only programs, databases, files, vendor sites, local models, retrieval agents, workflow agents, and document-analysis agents.

The enterprise is not one Elmer. It is an environment where many Elmer-like capabilities must be governed together.

## 11. From Current Artifact to Organizational Learning

The defect is where the workflow begins, not where knowledge ends.

A rail flaw, infrastructure anomaly, or safety signal creates a moment of action. But after action, the organization should be able to learn:

- Was the model correct?

- Did the human receive enough context?

- Was the alert routed to the right person?

- Was the action timely?

- Did the repair or mitigation work?

- Were related anomalies present elsewhere?

- Did policy or procedure need adjustment?

- Did the model threshold need review?

- Did the data pipeline omit relevant context?

- Did the organization detect a systemic pattern?

This is the feedback loop that converts individual events into institutional knowledge. A mature architecture should not merely process cases. It should help the organization understand what its cases reveal.

The audit stack makes this possible because it captures not only final outputs but the chain of evidence, decision, action, override, and result. A separate evaluator can then examine the record and surface patterns. A human authority can approve changes to routing, thresholds, procedures, or model use. The system improves, but only through a governed path.

That is the enterprise version of learning. It is not a model silently updating itself. It is an organization improving through disciplined evidence. In the vocabulary this revision has adopted, it is the outermost control loop doing its job: sensing through the audit record, evaluating through an independent instrument, and acting only through an accountable authority.

## 12. Conclusion

Sperry Rail Service’s Elmer and Rail Health systems should be used as a narrow illustration, not as an architecture to copy. They represent one specialized slice of one signal group within one operational department. Their value is that they make visible a broader enterprise requirement: specialized AI can help surface defects and anomalies, but the organization must govern how those signals become human decisions and institutional knowledge.

The defect is the current artifact needing action. The knowledge comes from understanding that anomalies do not develop in isolation. They emerge from interconnected technical, environmental, operational, historical, and organizational conditions. A single model may help identify the artifact. A governed enterprise architecture must understand the relationships.

The goal is therefore not to make the model the decider. The goal is to keep models tightly controlled, route the right evidence to the right person at the right moment, preserve the full audit trail, and allow the organization to learn through governed evaluation and human-approved change. And because a seat can hollow out while still sitting on the org chart, the architecture now watches for that too—not by surveilling the person, but by watching whether independent judgments still disagree the way independent minds should.

In that sense, the enterprise version of “Elmer” is not a defect-detection model. It is the audit-and-governance architecture—a hierarchical structure of control loops with human authority holding the outermost one—that enables a massive government agency to act, prove, and improve, without surrendering authority to the machine.

**Acknowledgment**

This revision owes its two central improvements—the closing of the rubber-stamping seam in Section 7 and the control-structure naming throughout—to a reviewer with deep expertise in system-theoretic safety analysis, who recognized the structure of the original argument through its operational vocabulary and generously supplied the field’s formal frame, including Nancy Leveson’s STAMP and STPA. The convergence of that frame with an argument derived independently from railroad infrastructure practice is treated here as evidence for the structure itself. The remaining faults are the author’s.
