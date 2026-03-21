# The Case for the Departmental Orchestrator

## Why Agency AI Agents Should Route, Not Reason

*A design philosophy for privacy-first, compliance-native agent architecture serving regulatory field personnel.*

> **Note:** This document describes a reference architecture for a class of federal regulatory environment. It does not represent the position of any specific federal agency.

---

# **Abstract**

Most AI agent frameworks deployed in federal government environments today share a fundamental assumption: the agent should be as intelligent as possible, executing complex reasoning locally or routing all cognitive work to a single cloud provider. This paper argues for a different architecture tailored to the operational realities of the a federal regulatory agency—one where a modest departmental model serves exclusively as an orchestration layer between the agency personnel (inspectors, analysts, and support staff) and multiple FedRAMP-authorized frontier AI services.

The departmental orchestrator handles routing, context curation, compliance enforcement, and quality control while deliberately refusing to perform tasks beyond its reliable capability. It does not replace the judgment of inspectors conducting bridge safety evaluations or analysts reviewing pipeline incident data. It ensures that when those professionals need AI assistance, every interaction is routed to the right model, packaged with the right context, validated against the right schema, and logged for the right audit trail.

This proposal is designed for the agency’s operational structure, where “local” does not mean field laptops—it means departmental compute infrastructure serving teams of inspectors and support staff within operational divisions such as the agency's operational divisions. The orchestrator runs within the agency’s existing enclave boundaries, on department-controlled hardware, subject to the agency’s Authority to Operate process.

# **1. The Problem with Current Federal AI Architecture**

The current generation of AI deployments across federal civilian agencies shares default assumptions that produce predictable failure modes. Understanding these defaults is necessary before proposing an alternative for the agency.

## **1.1 The Intelligence Maximalism Trap**

Most frameworks assume the agent itself should be maximally capable. They optimize for putting the smartest possible model at the center of every operation. In a the agency context, this creates a dangerous false binary: either the AI can handle the inspector’s task, or the inspector gets no AI assistance at all.

The problem is that intelligence and reliability are not the same thing. A large language model can produce impressive regulatory analysis, but it will also confidently fabricate CFR citations, misparse inspection data, and hallucinate incident statistics. When an Division D inspector relies on an AI system that is both the reasoner and the executor, there is no independent check on the quality of its reasoning. The system trusts itself to be right, and when it is wrong, the consequences propagate into safety-critical inspection reports, enforcement actions, and compliance determinations.

## **1.2 The Context Dumping Anti-Pattern**

When a departmental AI tool escalates to a cloud model, the default behavior in most frameworks is to send as much context as possible. In the agency’s environment, this creates three compounding problems: cost scales linearly with context size across hundreds of concurrent users; cloud models are distracted by irrelevant information exactly as human analysts are; and sensitive data—including Sensitive Security Information (SSI), pre-decisional enforcement data, and personally identifiable information from regulated entities—is transmitted to external endpoints without curation.

Context curation—selecting exactly what a cloud model needs and nothing more—is not a convenience feature for the agency. It is a FISMA compliance requirement and an operational necessity for protecting safety-sensitive information.

## **1.3 The Single-Model Dependency**

Most federal AI deployments are designed around a single cloud provider. This leaves the agency unable to leverage the genuine capability differences between providers, vulnerable to single-provider outages and rate limits during surge operations (post-incident response, rulemaking comment periods), and locked into pricing structures that cannot be optimized across task types. The model broker pattern—routing different task types to different FedRAMP-authorized models based on measured performance—is architecturally simple but rarely implemented in federal environments.

## **1.4 The Trust Vacuum**

The most consequential gap in current federal AI frameworks is the absence of a principled trust model aligned with the agency’s operational authority structure. Most systems offer a binary choice: fully autonomous execution or fully manual approval. There is no mechanism for an inspection workflow to earn autonomous execution privileges through demonstrated reliability, no mechanism to automatically revoke those privileges when anomalies are detected, and no alignment between automation authority and the GS-level approval chains that the agency’s operational divisions already operate.

# **2. The Departmental Orchestrator Pattern**

The departmental orchestrator pattern inverts the default assumption. Instead of asking the agent to be as intelligent as possible, it asks the agent to be as reliable as possible—and to know exactly where its reliability ends.

## **2.1 Core Principle: Route, Don’t Reason**

A departmental model in the 7–13 billion parameter range, running on the agency-controlled GPU infrastructure within the department’s enclave boundary, can reliably perform a specific set of tasks: parsing user intent into structured commands, selecting which FedRAMP-authorized cloud model to route a request to, assembling curated context packages compliant with the agency data classification rules, validating structured responses against schemas, and dispatching deterministic operations like database queries, template fills, and document formatting.

What it cannot reliably do is generate regulatory analysis, perform nuanced safety assessments, synthesize information across multi-year inspection histories, or make judgment calls where the difference between a good answer and a mediocre one has safety implications. The departmental orchestrator draws a hard line between these categories. The departmental model handles the first set. Everything in the second set is escalated to a FedRAMP-authorized frontier cloud model.

*The departmental orchestrator’s intelligence is not measured by what it can answer. It is measured by what it can ask, how well it selects the right model to ask, and how effectively it synthesizes multiple model responses into a single coherent result for the inspector or analyst.*

## **2.2 Task Classification Tiers**

The orchestrator classifies every incoming request into one of three tiers. The classifier is deliberately biased toward escalation—when in doubt, the task goes to a cloud model. This reflects the agency’s quality-first, safety-first design philosophy: the cost of one unnecessary cloud call is trivially small compared to the cost of a bad local result propagating into an inspection report or enforcement recommendation.

| **Tier** | **Scope** | **Verification** |
| --- | --- | --- |
| **Tier 1: Departmental Deterministic** | Database queries, report template fills, scheduling lookups, CFR reference retrieval, inspection form pre-population. The departmental model maps intent to a structured function call under strict JSON schema. | Output is deterministic. If the schema validates and the data source is authoritative, the result is correct by construction. |
| **Tier 2: Departmental + Verify** | Tasks where departmental model output is structurally verifiable. Extracting inspection dates and violation counts from structured reports, parsing known document formats, cross-referencing carrier data. | Both structure and semantics are checked. A date must be in range, a violation count must match the source. Verification failure triggers automatic escalation to cloud. |
| **Tier 3: Cloud Escalation** | Regulatory analysis, safety assessments, policy impact drafting, multi-year trend synthesis, enforcement recommendation drafting. The departmental agent packages the request; it does not perform the reasoning. | Cloud responses validated against expected schemas. Malformed or incomplete responses trigger retry logic or human review. All outputs queued for inspector/analyst approval. |

## **2.3 Multi-Model Orchestration**

For complex requests that benefit from multiple perspectives or specialized capabilities—such as a bridge safety assessment requiring both structural analysis and regulatory compliance review—the orchestrator decomposes the request into subtasks, dispatches them to different FedRAMP-authorized cloud models in parallel, and synthesizes the results.

The critical insight is that decomposition is the highest-leverage operation the departmental agent performs. A bad decomposition produces subtask results that do not fit together regardless of how capable each individual model is. A good decomposition produces focused, independent subtasks whose results merge cleanly. This is prompt engineering at the architectural level.

When model responses conflict—and they will, particularly on questions involving regulatory interpretation—the system does not silently choose one. It surfaces the disagreement explicitly, presenting all perspectives so the inspector or analyst can make the judgment call with full information. This embodies the agency’s core operating principle: AI handles assembly, the qualified professional makes the decision.

## **2.4 Decomposition Reliability**

Decomposition is both the system’s highest-leverage capability and its primary failure mode. A 7–13B model can reliably decompose structured, familiar workflow types using template-based patterns (e.g., “inspection report generation” follows a known decomposition). For novel or ambiguous requests, the system escalates the decomposition itself to a cloud model rather than attempting an unreliable local decomposition.

The cost of one additional cloud call for planning is far less than the cost of reassembling incoherent results from a bad decomposition—particularly when the output feeds into safety-critical documents.

# **3. Context Curation as a Compliance Discipline**

In the agency’s environment, context curation is not merely an engineering optimization—it is the primary mechanism for enforcing data classification, protecting SSI, and ensuring FISMA compliance on every cloud interaction.

## **3.1 The Context Package**

When the orchestrator escalates to a cloud model, it assembles a structured payload containing: the user’s parsed intent with role-appropriate output format specifications; curated context selected from the user’s partition of persistent memory (not the full memory store, and never another user’s data); relevant regulatory text and inspection data (redacted of SSI, PII, and pre-decisional markings per the agency classification rules); and an embedded execution plan defining what the orchestrator will do with the response. The departmental model generates concise context summaries from longer files, keeping token costs manageable across hundreds of concurrent users.

## **3.2 Privacy and Data Protection by Architecture**

Context curation is the agency’s primary data protection mechanism. Cloud models never see the complete memory layer. Sensitive fields—SSI markings, PII from regulated entities, pre-decisional enforcement data, API keys, financial data—are tagged with structural redaction rules enforced by the context packager before any cloud transmission. This is not a policy that can be overridden by request context. A tagged field never leaves the agency’s enclave boundary, period.

This approach integrates with the agency’s existing DLP infrastructure (Forcepoint, Microsoft Purview) rather than replacing it. The context packager applies the agency-specific redaction rules; enterprise DLP provides the second validation layer before cloud transmission.

# **4. Memory Governance**

Persistent memory—accumulated inspection data, analyst research threads, regulatory interpretations, workflow history—is the system’s most valuable asset and its most dangerous liability. Without governance, memory accumulates indefinitely, summaries drift from their sources, and stale context degrades model performance and produces incorrect results in safety-critical workflows.

## **4.1 Immutable Source Layer**

All source material—original inspection reports, regulatory documents, analyst-provided facts, official correspondence—is stored as immutable raw memory objects within each user’s isolated partition. These are the canonical source of truth. They can be archived or expired per the agency records management policy, but never silently modified.

## **4.2 Derived Summaries with Provenance**

When the context packager summarizes longer files, those summaries carry provenance metadata: which raw objects they were derived from, when they were generated, and which model generated them. This prevents the critical failure mode where a lossy summary gradually replaces the original—unacceptable in an environment where an inspector’s finding must trace back to an authoritative source document.

## **4.3 Memory Lifecycle**

- Confidence scoring: Each memory object carries a confidence score based on source type. Official the agency database records score highest; inferences from documents score lower; summaries derived from other summaries score lowest.

- Expiration and refresh: Derived summaries expire on a configurable schedule and are regenerated from source objects. Time-sensitive regulatory data (e.g., rulemaking comment deadlines, inspection due dates) carries explicit expiration dates.

- Redaction enforcement: SSI, PII, and CUI markings are enforced structurally by the context packager—not discretionarily by the model.

- Records management alignment: Memory retention and disposition follow the agency records schedules as approved by NARA, ensuring the system does not retain data beyond authorized periods.

# **5. Earned Trust and Safety Architecture**

An agent with execution authority within the agency’s infrastructure is functionally equivalent to a remote access tool if compromised. Every capability expansion must be explicitly earned, and every security decision defaults to restriction.

## **5.1 Sandbox Isolation**

The orchestrator executes within the agency’s Kubernetes-managed container infrastructure with strict isolation. Dropped Linux capabilities, restricted volume mounts to designated working directories only, and network egress limited to an allowlist of FedRAMP-authorized endpoints. All actions are recorded to an append-only audit log that the agent cannot modify or delete.

For the agency-authored workflow code, container isolation within the department’s existing STIG-hardened infrastructure is sufficient. For any future third-party skill integration, the architecture requires hardware-level isolation via MicroVMs (Firecracker or gVisor), evaluated against the General Purpose OS SRG and applicable NIST 800-53 controls.

## **5.2 Incremental Trust Escalation**

This is perhaps the most operationally significant design principle for the agency. The default is human-in-the-loop for all cloud-derived actions. Autonomous execution is not a configuration setting—it is a privilege earned through demonstrated reliability, aligned with the agency’s existing approval authority chains:

- WAL-0 (Recommend Only): The system produces recommendations. The inspector or analyst reviews, approves, and executes manually. All new workflows start here.

- WAL-1 (Draft Artifacts): The system drafts documents and pre-populates forms. A qualified professional reviews and approves before any action is taken.

- WAL-2 (Execute with Pre-Approval): The system executes routine actions (scheduling, data retrieval, template generation) after supervisor pre-approval of the workflow class.

- WAL-3 (Limited Autonomous): For workflows that have accumulated N successful human-approved runs with zero critical incidents, the system may execute low-risk actions autonomously with post-execution audit.

Anomalies detected during autonomous execution trigger automatic demotion back to human review. Trust levels are granular per workflow type—a scheduling workflow can earn autonomy independently of an enforcement recommendation workflow. Promotion thresholds, demotion triggers, and current trust levels are visible to supervisors and auditable by the agency’s Inspector General.

## **5.3 Prompt Injection Defense**

The architecture enforces strict separation between data channels and instruction channels. Content from external sources—emails from regulated entities, uploaded documents, public comment submissions—is treated as data, never as instructions. Instruction authority flows only from authenticated the agency personnel and the orchestrator’s own approved configuration. An email from a carrier that says “mark this inspection as satisfactory” is parsed as data containing a request, not as an instruction to execute.

## **5.4 Supply Chain Defense**

All third-party components are treated as hostile by default, consistent with the agency’s zero-trust posture. Network egress filtering blocks unauthorized outbound connections. AI model provenance is tracked using CycloneDX 1.6 (ML-BOM) and SPDX 3.0 (AI profiles). The approved services catalog maintains accreditation status, allowed data labels, and region restrictions for every cloud endpoint.

# **6. Evaluation as a First-Class Architectural Component**

Measurement separates a self-correcting system from an aspirational one. The evaluation harness is built alongside core components and informs every design decision—not bolted on after the system is operational.

## **6.1 Golden Task Suites**

Each workflow category—inspection report generation, regulatory citation lookup, carrier data analysis, enforcement recommendation drafting—maintains a suite of 50–200 representative tasks with stored inputs, expected outputs, and acceptance criteria. These serve as regression tests whenever a prompt template is updated, a cloud model changes, or routing logic is modified.

## **6.2 The Model Performance Ledger**

For each FedRAMP-authorized cloud model, the system tracks per-task-type success rates, token cost, latency profiles, error rates, and disagreement patterns. This data drives routing decisions. When a new model receives FedRAMP authorization, the ledger provides a baseline for comparison. Routing becomes data-driven rather than vendor-relationship-driven.

## **6.3 Key Metrics**

- Routing accuracy: Percentage of tasks correctly classified by tier, measured by manual override rate.

- Context efficiency: Tokens sent per request and percentage of cloud calls requiring follow-up due to missing context.

- Schema pass rate: Percentage of cloud responses passing structural validation on first attempt.

- Human approval rate: Per workflow, per template, per model, per operational division.

- Cost per workflow: Token spend, latency, retry frequency—reported to OCIO for budget forecasting.

- Compliance artifact generation: Percentage of interactions producing complete audit trails sufficient for cATO assessment.

# **7. Reliability Engineering**

A departmental orchestrator serving hundreds of inspectors and analysts is critical infrastructure. When it runs, it must be predictable. When it fails, it must fail gracefully.

## **7.1 Graceful Degradation**

When the orchestrator node is unavailable, client workstations detect the outage and switch to read-only mode, displaying cached state and queuing commands for execution when service returns. No commands are lost. No state is corrupted. Inspectors in the field retain access to locally cached reference data.

## **7.2 Idempotent Execution**

All workflow steps that modify state carry idempotency keys. If the system restarts mid-workflow, it resumes from the last completed step without duplicating side effects—critical when the workflow involves updates to the agency’s systems of record.

## **7.3 Cloud Dependency Management**

Cloud APIs fail, rate-limit, and change behavior without notice. The system implements circuit breakers that detect repeated failures from a specific FedRAMP-authorized provider and temporarily route around them. During surge operations—post-incident investigations, rulemaking comment analysis—the system provides natural backpressure through its durable job queue rather than silently dropping requests.

## **7.4 High Availability**

Unlike the single-hub personal architecture, the departmental deployment runs in an HA configuration within the agency’s Kubernetes infrastructure with defined RTO/RPO targets. Geographic distribution supports OCONUS field offices. The durable job queue (backed by PostgreSQL for enterprise scale) ensures no request is lost on restart.

# **8. Workflow Contracts**

Every workflow step operates under a typed contract that defines exactly what it expects, produces, and is permitted to do—making the orchestration pattern an enforceable specification rather than a design aspiration.

## **8.1 Contract Structure**

Each workflow step declares: an input schema, an output schema, allowed side effects (directories, endpoints, the agency systems of record), a retry policy, an escalation policy, and required approval authority (which GS level or role must approve the output). This turns the orchestration pattern into something the agency’s ATO assessors can evaluate—every workflow’s boundaries are defined before execution.

## **8.2 Capability Graph**

Rather than a flat skills directory, the system maintains a capability graph encoding relationships between workflows: required inputs and produced outputs (enabling automatic chaining), dependency ordering, trust levels (determining isolation tier and approval requirements), and execution cost (enabling the router to optimize across speed, token budget, and compliance constraints).

# **9. Guiding Principles**

| **Principle** | **What It Means for the agency** |
| --- | --- |
| **Quality over speed** | Cloud escalation is the default. The departmental model only handles tasks where its output is verifiably correct. No safety-critical output is generated locally. |
| **AI handles assembly, professionals decide** | Human-in-the-loop by default. Autonomous execution is earned per-workflow through demonstrated reliability, aligned with the agency approval authority chains. |
| **Department-controlled, cloud-augmented** | All data, memory, and execution remain within the agency’s enclave boundary. Cloud models are FedRAMP-authorized tools the orchestrator calls, not platforms the agency depends on. |
| **Adopt, don’t invent** | Use ecosystem solutions (MCP, A2A, FedRAMP-authorized services) as they mature. Invest in the orchestration and compliance layers above commodity infrastructure. |
| **Inspectable by default** | Append-only audit log. Every decision, cloud call, and action is logged, auditable by supervisors, and reportable to the Inspector General. |
| **Incremental trust** | Automation privileges are earned, not granted. Anomalies trigger automatic demotion. Consistent with the agency Responsible AI principles. |
| **Measure everything** | Evaluation harness, model performance ledger, and feedback loops validate every decision with data—generating the continuous monitoring evidence required for cATO. |

# **10. the agency-Specific Operational Context**

This architecture is designed for the specific operational realities of the agency’s operational divisions. “Local” in this context does not mean field laptops. It means department-controlled compute infrastructure serving defined teams.

## **10.1 Operational Division Deployment Model**

Each operational division (Division B, Division C, Division A, Division D, Division E, Division F) operates a departmental orchestrator node within its enclave boundary. The node serves teams of inspectors and support staff—not individual devices. A typical Division D regional office might have 20–40 inspectors and 10–15 support staff sharing a single orchestrator instance, with each user’s context, memory, and audit trail logically isolated.

## **10.2 Inspector Workflow Integration**

The orchestrator integrates with existing the agency systems of record: Division D’s the compliance database (Motor Carrier Management Information System), Division C’s inspection databases, Division B’s bridge inspection platforms, Division E’s pipeline safety databases, and Division F’s complaint and recall systems. These integrations are implemented as typed workflow contracts with strict permission manifests—the orchestrator can query these systems but cannot modify records without human approval flowing through the agency’s existing authority chains.

## **10.3 Support Staff Efficiency**

Support staff benefit disproportionately from Tier 1 and Tier 2 workflows: automated report formatting, data retrieval from multiple the agency databases, scheduling coordination, and correspondence template generation. These tasks are deterministic, verifiable, and represent 60–80% of the orchestrator’s workload—resolving locally at near-zero marginal cost and freeing support staff to focus on higher-value coordination work.

# **11. Anticipated Objections**

"the agency should just buy a commercial AI platform." Commercial platforms bundle routing logic with model hosting. Azure’s orchestration lives inside Azure. Bedrock’s lives inside AWS. By separating orchestration from hosting, the agency retains the ability to change AI providers without changing AI operations—critical when OMB and Congress may shift vendor preferences between budget cycles.

"This is overengineered for a civilian agency." the agency is not a typical civilian agency. Division D inspectors make enforcement decisions affecting commercial motor vehicle safety. Division E inspectors evaluate pipeline integrity. Division A oversees aviation safety. When an AI system contributes to these professionals’ work products, the trust architecture is not overhead—it is the minimum viable safety layer.

"Just use the existing IT infrastructure." This architecture runs on the agency’s existing infrastructure—Kubernetes, STIG-hardened containers, enterprise SIEM, existing DLP. It adds an orchestration layer, not a parallel infrastructure. The incremental cost is GPU-accelerated inference nodes and the engineering effort to build the orchestration logic.

"Local models will be smart enough to handle everything soon." Even if departmental models become radically more capable, separating orchestration from reasoning produces a more reliable, auditable, and compliance-native system. The architecture works with more capable local models—it just does not require them.

# **12. Conclusion**

The departmental orchestrator pattern is not a rejection of cloud AI. It is a proposal for how the agency can use cloud AI better: with curated context, measured performance, earned trust, governed memory, and continuous compliance as a structural property of the system itself.

The departmental model does not compete with frontier models. It makes frontier models more effective by handling the engineering and compliance concerns—routing, packaging, validation, synthesis, audit trail generation—that those models should not need to handle themselves. It ensures that the agency’s inspectors, analysts, and support staff receive the highest-quality AI assistance possible, while every interaction remains within the agency’s control, subject to the agency’s authority, and visible to the agency’s oversight mechanisms.

*We are not building a chatbot. We are proposing the architecture for an intelligent operations layer for the agency—one that knows each inspector’s workflow, respects every data boundary, speaks to every authorized AI service, and never acts without qualified human approval.*

**END OF DOCUMENT**

