# Clinical AI Orchestration Framework

## Engineering Review & Integration Assessment

*A reference architecture for AI-assisted clinical data orchestration in academic medical centers.*

> **Note:** This document describes a reference architecture for a class of clinical environment. It does not represent the position of any specific healthcare institution and was not commissioned, endorsed, or reviewed by any medical organization. All content is derived from publicly available clinical informatics standards, published regulations, and general knowledge of clinical environments.

---

# 1. Purpose and Scope

This document provides the institution's clinical informatics leadership and IT engineering teams with a consolidated architectural assessment of the Clinical AI Orchestration Framework. It synthesizes the strongest technical contributions from three independently developed architectural proposals, addresses identified gaps, incorporates adversarial critique, and presents a unified, refined architecture ready for institutional evaluation.

The patient's accompanying narrative identifies a real and measurable problem: the information disconnect that forces complex-care patients to act as human data routers between specialists while the clinical record fails to surface critical prior test data at the point of acute care. This engineering document provides the structural response to that problem.

This version incorporates five specific architectural refinements not fully resolved in prior drafts:

- Context Packager redefined as a distinct architectural component with explicit boundaries

- Write path draft state model added to the Immutable Source Layer

- Tier 2 verification failure classification logic made explicit

- Multi-model parallel dispatch positioned as a governed optional enhancement

- Patient Narrative Input Path defined as a separate ingestion pathway with guaranteed immutability

# 2. The Clinical Problem: Why This Matters Now

## 2.1 The Information Disconnect in Measurable Terms

When a patient managing complex multi-system disease arrives for a specialist encounter, the physician faces a data assembly problem the current EHR interface was not designed to solve efficiently:

- Clinical notes authored in specialty-specific templates with differing documentation conventions make cross-specialty synthesis a manual cognitive task.

- Problem lists, medication histories, and lab trends exist as discrete structured data, but their longitudinal narrative — the chronological story of how conditions interact — is trapped in unstructured free text across dozens of encounter notes.

- Pre-visit chart review for a complex patient can consume 10–30 minutes of unbillable time, often repeated independently by multiple specialists seeing the same patient within days of each other.

- Patient-reported symptoms and portal messages are frequently disconnected from the clinical timeline, leading to narrative loss at the point of care.

- Prior institutional tests — including cardiopulmonary exercise tests, baseline ECGs, and documented arrhythmias — may fail to surface during acute presentations despite being recorded in the same system, causing comparison algorithms to default to tracings from prior institutions or obsolete baseline states.

## 2.2 Why Existing Tools Fall Short

Current EHR platforms provide robust transactional record-keeping but limited cross-disciplinary synthesis. Chart search, timeline views, and specialty-filtered dashboards help, but they still require the clinician to perform the cognitive assembly work. The gap is not in data availability — it is in data assembly and contextual presentation at the moment of care.

The proposals correctly identify that this is an orchestration problem, not an intelligence problem. The data already exists within the system. What is missing is a governed layer that can assemble, curate, and present that data in a clinically useful format without introducing hallucination risk or compromising patient privacy.

# 3. Architectural Thesis: Bounded Local Reasoning

## 3.1 Why This Principle Is Foundational for Healthcare

The central principle of this framework is that the local AI component should function as a traffic controller — classifying intent, routing tasks to the appropriate execution tier, and curating context — rather than attempting to serve as a clinical reasoning engine. This is architecturally significant for three reasons:

| **1** | *Hallucination Containment — A routing model that translates natural language into structured API calls against authoritative data sources eliminates hallucination for the majority of clinical retrieval tasks. The model is not generating medical facts; it is directing queries to systems of record that return verified data.* |
| --- | --- |

| **2** | *Regulatory Alignment — By limiting the local model's scope to classification and routing, the architecture avoids the regulatory complexity of deploying a model that independently generates clinical conclusions. The system supports clinical decision-making without crossing into autonomous diagnostic territory.* |
| --- | --- |

| **3** | *Failure Mode Predictability — A routing failure (misclassified task tier) is operationally recoverable. A reasoning failure (hallucinated lab value or fabricated medication history) in a clinical context carries direct patient safety risk. The architecture optimizes for failure modes that are safest to recover from.* |
| --- | --- |

## 3.2 Repudiating Intelligence Maximalism

All three source proposals converge on rejecting the assumption that a single, massive frontier model should serve as the central reasoning, routing, and execution engine for all clinical and administrative tasks. Deploying monolithic large language models directly onto raw EHR data creates unacceptable risks:

- Data sovereignty — the institution loses control of what leaves its network boundary

- Hallucination risk — the model generates medical facts probabilistically rather than retrieving them deterministically

- Provenance loss — synthesized clinical statements cannot be traced to authoritative source documents

- Regulatory exposure — autonomous clinical conclusions without human review create medico-legal ambiguity

The route-not-reason principle resolves all four by constraining model scope, enforcing deterministic data paths where possible, and escalating probabilistic synthesis only through governed, privacy-preserving pathways.

## 3.3 Infrastructure Alignment

the institution's existing investment in FHIR-based interoperability, SMART on FHIR application frameworks, and enterprise data platforms provides a strong foundation for the deterministic data plane this architecture requires. The orchestration layer does not replace the EHR; it adds a governed intelligence layer that consumes EHR data through existing supported interfaces and presents synthesized results back to the clinician within the existing workflow.

# 4. Component Architecture: Explicit Boundaries

The architecture consists of six distinct logical components, each with defined responsibilities and explicit boundaries. Prior drafts allowed the Context Packager's responsibilities to overlap with the orchestrator. This version resolves that ambiguity.

## 4.1 Clinician Interface Surfaces

Embedded into existing clinical workflow via SMART on FHIR application pattern. Includes EHR sidebar/app, note composer assistant (draft-only, WAL-1), pre-visit planning dashboard, and In Basket/triage views. Outputs must be role-specific, time-bounded, provenance-first, and designed to reduce clicks, not add another inbox.

## 4.2 Clinical AI Orchestrator (Local)

A Small Language Model (7–13B parameters), quantized to 4-bit precision, running natively on hospital-controlled infrastructure within the institutional firewall. The orchestrator has two sub-components:

- Routing SLM — classifies clinician intent, selects execution tier (1, 2, or 3), produces structured action plans, and enforces workflow trust boundaries. Does not generate clinical content.

- Skills and Workflow Contracts Library — a registry of vetted, institutionally approved capabilities the orchestrator is permitted to execute, with explicit input/output contracts and WAL classifications for each.

The orchestrator does not package context for external transmission. That responsibility belongs exclusively to the Context Packager.

## 4.3 Deterministic Data Plane (Tier 1)

FHIR-based read-only and write-limited APIs to the EHR. Returns structured data directly from systems of record: problems, medications, allergies, labs, vitals, imaging reports, procedure history, appointments, referral status. No probabilistic generation. Zero hallucination risk.

## 4.4 Unstructured Text Plane (Tier 2)

Clinical note retrieval and parsing engine for narrative text, consult letters, discharge summaries, and outside records. Feeds the Tier 2 Extraction Engine with automated dual-validation gates (see Section 5.2 for failure classification logic).

## 4.5 Context Packager (Distinct Component — Refined)

The Context Packager is a controlled preprocessing layer that sits between the orchestrator's Tier 3 escalation decision and the enterprise egress gateway. It has a single, bounded responsibility: preparing a minimum-necessary, PHI-minimized payload for external model transmission.

The Context Packager receives a structured task specification from the orchestrator. It does not make routing decisions. It executes four operations in sequence:

- Semantic retrieval — pulls only the clinical data relevant to the specific task from the patient's isolated data partition

- Field-level allowlist enforcement — applies task-type and role-based rules that determine which data elements are permitted in the outbound payload

- NER-based PHI minimization — scans for all legally defined patient identifiers across structured data, narrative text, and ambient transcripts; replaces with synthetic surrogate tokens that preserve clinical relationships while decoupling identity

- Token budget enforcement — limits payload size, reducing both cost and PHI leakage surface

The completed payload is then submitted to the enterprise egress gateway for policy validation before transmission. The institution controls what leaves. The model vendor never receives unredacted patient identity or the complete memory store of a patient's record.

## 4.6 Immutable Source Layer with Draft State Model (Refined)

Raw data from authoritative sources, patient narratives, and derived artifacts are managed through a write-once object store with explicit version states. This version adds a formal draft state model to address the previously undefined behavior of AI-assisted documentation:

| **State Tag** | **Definition** | **Who Creates It** | **Immutability** |
| --- | --- | --- | --- |
| SOURCE | Raw patient data, portal messages, clinical documents from EHR | System ingestion | Write-once. Never modified. |
| DRAFT-AI | AI-generated artifact (note draft, pre-visit brief, timeline) | Orchestrator via Tier 3 | Write-once. Stored with full provenance chain. |
| DRAFT-REVIEWED | Clinician-edited version of a DRAFT-AI artifact | Clinician edit action | Write-once. Stores delta from DRAFT-AI. |
| CANONICAL | Clinician-signed, legally submitted record | Clinician signature | Write-once. DRAFT-AI and DRAFT-REVIEWED preserved in audit chain. |

Summaries never become canonical. If a source document is corrected or an extraction error is identified, the system traces the lineage through all downstream derived artifacts, expires corrupted derivatives, and regenerates from the preserved original. The patient's direct narrative, once ingested, is never modified by any system process.

## 4.7 Patient Narrative Input Path (New — Refined)

Patient-entered portal messages, structured check-in narratives, and symptom diaries enter through a separate, dedicated ingestion pathway that writes directly to the Immutable Source Layer before any processing occurs. This is an architectural guarantee, not a policy aspiration.

- The orchestrator can read from patient narrative objects but cannot write to or modify them under any circumstances.

- Any synthesis derived from patient narrative carries a mandatory provenance pointer to the exact patient-authored text, preserving the patient's exact words in the audit chain.

- Structured check-in forms that are co-populated by both patient input and clinical staff are stored as two separate objects — the patient's version and the clinical version — with explicit tagging to prevent one overwriting the other.

This directly addresses the failure mode where a patient's lived experience is translated away into template language and billing codes, leaving no recoverable trace of what the patient actually reported.

Two pipeline stages govern patient narrative processing and must not be conflated: (1) Ingestion and immutable storage — the patient’s exact narrative is written as a SOURCE object to the Immutable Source Layer before any processing occurs. This object is never modified. (2) Sanitization for SLM context — a separate copy of the narrative text, not the SOURCE object itself, is passed through an input sanitization layer before it enters the routing SLM context window. This sanitization step detects and neutralizes instruction-like text patterns — whether malicious prompt injections or incidentally command-like formatting — that could cause the routing SLM to treat patient content as operational instructions rather than data to be routed. The sanitized copy is what the orchestrator processes. The SOURCE object remains unmodified. These are two distinct operations with two distinct outputs. Sanitization of the processing copy never touches the immutable source.

## 4.8 Evidence and Governance Plane

Immutable audit log (append-only event stream), evaluation harness with golden task suites, model performance ledger, and continuous improvement engine that converts clinician corrections into labeled training data. Every clinician rejection, edit, or override is captured as a labeled data point tied to the specific workflow, template, and model — transforming human correction from friction into improvement signal.

# 5. The Three-Tier Execution Model

The orchestrator defaults to the safest tier and escalates only when the task genuinely requires probabilistic synthesis. The classification engine is biased toward escalation: the cost of an unnecessary cloud API call is trivially small compared to the operational risk of a hallucinated result reaching clinical presentation.

| **Tier** | **Latency Target** | **Scope** | **Hallucination Risk** | **Verification** |
| --- | --- | --- | --- | --- |
| Tier 1 Deterministic Retrieval | < 500ms | Structured API queries against EHR systems of record. Lab trends, medication timelines, problem lists, prior test results, appointment history, procedure records. | Zero — model routes to authoritative source; does not generate facts | Correct by construction. Output is data from systems of record. |
| Tier 2 Verified Extraction with Validation Gates | < 2 seconds | Local extraction from unstructured clinical notes. Medication reconciliation, symptom onset dates, open referral loops, clinical assertions with provenance pointers. | Contained — automated dual-validation gates catch errors before clinical presentation | Structural + semantic + cross-source checks. Failed checks trigger failure classification (see 5.2). |
| Tier 3 Escalated Synthesis | 5–30 seconds | Cross-specialty longitudinal synthesis. Pre-visit briefs, multi-specialty timelines, contradiction/gap scans, patient narrative preservation. | Managed — output is always a draft artifact with full provenance chain. Clinician review required. | Provenance requirements on every claim. Never auto-generates diagnoses, orders, or treatment plans. |

## 5.1 Tier 1: Zero Generative Hallucination Risk — Retrieval Fidelity Governed by Patient/Encounter Binding Gate

At Tier 1, the AI does not generate or infer medical data. It functions purely to map human intent to structured schemas required by backend systems. Because the final output originates directly from the authoritative source database — not a probabilistic language model — the result is deterministic. This eliminates hallucination risk for routine retrieval and ensures sub-second responsiveness.

Retrieval fidelity — the guarantee that data returned is from the correct patient, encounter, and time window — is enforced by a mandatory patient/encounter binding confirmation gate that fires before any data is surfaced to the clinician. The binding validator confirms patient MRN, encounter ID, and time window match the active clinical context derived from the EHR SMART App Launch context. This gate is a pre-retrieval safety requirement, not a post-retrieval check. A binding failure suspends the query and alerts the clinician. It does not return data from the incorrect context.

A key failure the patient narrative documents — a cardiopulmonary exercise test conducted at the same institution 16 months prior failing to surface during an acute cardiac presentation — is a Tier 1 retrieval failure. The data existed. The system failed to route to it. That is precisely what Tier 1 is designed to resolve.

## 5.2 Tier 2: Failure Classification Logic (Refined)

Prior drafts stated that failed Tier 2 verification checks trigger escalation to Tier 3 or human review, without specifying the decision logic between those two outcomes. This version makes the classification explicit:

| **Failure Type** | **Examples** | **Escalation Path** | **Rationale** |
| --- | --- | --- | --- |
| Structural failure | Date parsing error, unit normalization failure, entity not mappable to known clinical database | Human review queue | A more capable model cannot resolve a malformed date or an unmapped entity. These require human judgment or source correction. |
| Semantic failure | Chronological logic inconsistency, clinical assertion contradicts structured record, provenance pointer ambiguous | Tier 3 synthesis | A frontier model with broader clinical context can often resolve semantic inconsistencies that a local extraction model cannot. Output remains draft with full provenance. |
| Cross-source conflict | Extracted medication date contradicts pharmacy transaction record | Human review queue with Tier 3 flag | Surface the conflict to the clinician with both the extracted value and the authoritative record. Do not resolve autonomously. |

## 5.3 Tier 3: Escalated Synthesis and Multi-Model Option

Tier 3 handles deep synthesis tasks requiring narrative coherence across multiple specialties, time horizons, and documentation styles. All Tier 3 output is a drafted synthesis artifact with traceable provenance — never the chart record itself.

Multi-Model Parallel Dispatch (Optional Governance Enhancement): Gemini's architectural proposal introduced the concept of dispatching high-stakes Tier 3 tasks to multiple frontier models in parallel and surfacing disagreements to the physician. This is clinically defensible for defined task classes. The refined architecture positions this as follows:

- Default Tier 3 behavior: single institution-approved endpoint per task class.

- Enhanced Tier 3 behavior (opt-in, institutionally governed): for defined high-stakes synthesis tasks — such as differential diagnosis synthesis for patients with complex multi-system involvement — the orchestrator may dispatch to two approved endpoints and surface the output delta to the physician as a WAL-0 display (physician sees both outputs, decides which if either to accept).

- The task classes that trigger multi-model dispatch are defined and maintained by the institution's clinical governance committee, not by the system autonomously.

An important constraint governs this optional enhancement: research on parallel dispatch architectures (AgentLeak framework and related work) demonstrates that inter-agent communication channels — the messages, tool calls, and shared memory states used to adjudicate disagreements between simultaneously dispatched models — leak PHI at significantly higher rates than single-model outputs, even when the final synthesized output presented to the clinician is clean. The Context Packager governs the initial outbound payload per model but does not govern inter-agent channels, which are managed by the external model providers. For this reason, multi-model parallel dispatch for Tier 3 tasks is gated behind a separate inter-agent channel security review before institutional activation. Until that review is complete and inter-agent channel controls are validated, the default Tier 3 behavior is single approved endpoint per task class. Institutions that activate multi-model dispatch accept the inter-agent channel risk as documented in the institutional risk register.

# 6. Privacy by Architecture

## 6.1 The Context Packager Workflow

The Context Packager's operation is fully sequential and auditable. Every step produces a log entry in the immutable audit stream:

| **Step** | **Operation** | **Governance Control** |
| --- | --- | --- |
| 1 | Receive structured task specification from orchestrator (task type, clinician role, time window, required data classes) | Orchestrator produces a signed, schema-validated task spec. Context Packager rejects malformed specs. |
| 2 | Semantic retrieval from patient's isolated data partition — only the clinical data relevant to this specific task | Field-level allowlists by task type and clinician role. Token budget enforced at retrieval. |
| 3 | NER-based PHI detection across all retrieved content — structured data, narrative text, ambient transcripts, scanned documents | Context-aware NER models trained on healthcare data. Covers all 18 HIPAA Safe Harbor identifiers. |
| 4 | Deterministic redaction — sensitive identifiers replaced with synthetic surrogate tokens that preserve clinical relationships (timelines, dosages, biological relationships) | Redaction is non-reversible at the payload level. Surrogate mapping stored in institution-controlled key vault. |
| 5 | Payload submission to enterprise egress gateway for institutional policy validation before external transmission | Gateway validates against institutional policy. Blocks non-compliant payloads. All transmissions logged. |

## 6.2 Hardware Isolation

The orchestration layer runs on a Kubernetes cluster deployed within the health system's Virtual Private Cloud using hardware-level partitioning. Workloads for different clinical departments operate on separate, isolated compute paths — physically precluding lateral movement, side-channel attacks, or information disclosure between clinical tenants sharing the same physical host.

## 6.3 Regulatory Posture

This approach shifts the privacy enforcement point from the cloud provider’s data handling policies to the institution’s own architectural controls. The institution controls what leaves, not the model vendor. This is a meaningful improvement over trust-based arrangements with external AI providers and aligns with HIPAA’s minimum necessary standard as an architectural constraint rather than a policy aspiration.

The Context Packager’s PHI minimization layer satisfies the HIPAA Safe Harbor method as a compliance floor. For Tier 3 payloads containing rare diagnoses, narrow demographic combinations, or detailed social history elements, the architecture supplements Safe Harbor with quasi-identifier risk assessment and context coarsening. The institution acknowledges that Safe Harbor compliance does not constitute a mathematical re-identification bound against frontier LLM adversaries with access to broad training corpora. For payloads assessed as carrying elevated re-identification risk under quasi-identifier analysis, the institution applies the HIPAA Expert Determination standard — formal statistical risk modeling confirming re-identification probability is mathematically trivial given the specific context of the data release and the capabilities of the recipient model. Expert Determination adds computational overhead to Tier 3 payload preparation and is scoped in the Phase 3 implementation specification with its latency implications documented. Residual re-identification risk is managed through contractual controls: Business Associate Agreements with approved model endpoints include no-training clauses, strict retention controls, and auditable access requirements. The institution accepts and documents this residual risk as an institutional risk acceptance, not an architectural guarantee.

FDA Clinical Decision Support Classification: The architecture’s regulatory posture under FDA’s CDS guidance depends on whether the routing system’s outputs function as clinical decision support in ways that cross the device definition threshold. The key criterion is transparency: CDS software is excluded from device oversight when the clinician can independently review the basis for the recommendation and is not reliant on the software to make the clinical decision. The CAOF is designed to satisfy this criterion through its provenance-first output requirement — every synthesized claim is traceable to its source document, and the clinician can review that source directly. The routing model’s non-explainability is contained to task classification, not clinical recommendation generation; the clinical content surfaced is always attributed to authoritative source records, not generated by the model. Institutions deploying the CAOF should conduct a formal CDS classification analysis with legal counsel before production deployment, particularly for any workflow class where Tier 3 synthesis outputs approach diagnostic or treatment recommendation territory. WAL-0 and WAL-1 governance — where the clinician reviews all output before any clinical action — is the operational mechanism that preserves CDS exclusion eligibility.

The regulatory environment governing systems that touch ePHI is actively tightening. HHS published a Notice of Proposed Rulemaking on January 6, 2025, proposing significant strengthening of the HIPAA Security Rule, including enhanced requirements around technology asset inventories, vulnerability testing, encryption, and restoration objectives. The security architecture described in this document — signed model artifacts, append-only audit logs, HSM-governed surrogate key storage, Zero Trust network segmentation, and continuous regression testing — is designed to exceed current HIPAA Security Rule requirements and align with the direction of the proposed rulemaking. Institutions deploying the CAOF are advised to monitor the final rule and confirm that their implementation specifications remain aligned as the regulatory landscape evolves.

# 7. Workflow Autonomy Levels: Governing Clinical Trust

The WAL framework provides a structured governance model for how AI-assisted capabilities earn operational trust. All new capabilities enter at WAL-0 and must demonstrate measurable reliability before promotion. Demotion is automatic and immediate upon anomaly detection.

| **WAL Level** | **Operational Meaning** | **Clinical Examples** | **Safety Contract** |
| --- | --- | --- | --- |
| WAL-0 Recommend | AI retrieves, curates, and suggests. Human executes all actions. | Diagnostic differentials, trend analysis, prior test surfacing, questions to consider. Multi-model output display for defined high-stakes synthesis tasks. | Zero autonomous writes. Builds familiarity and demonstrates value transparently. |
| WAL-1 Draft | AI drafts artifacts. Clinician reviews, edits, and legally signs before submission. | Pre-visit briefs, HPI/Assessment narrative drafts, discharge summary structure, referral packets, ambient visit note drafts. | Explicit review and sign-off required. DRAFT-AI and DRAFT-REVIEWED states both preserved in audit chain. |
| WAL-2 Pre-Approved | Supervisor pre-approves specific routine workflow classes. Clinician initiates with safeguards. | Standard post-op lab orders, prior authorization packet assembly, routine referral routing. | Limited to low-risk, policy-defined, reversible actions. Departmental oversight required. |
| WAL-3 Autonomous | System executes low-risk deterministic tasks independently with full audit logging. | Nightly outside records fetch, duplicate reconciliation, pre-visit brief queuing, scheduling reminders. | Earned exclusively through sustained zero-incident reliability. Automatic demotion on anomalies. |

## 7.1 Promotion and Demotion Criteria

Promotion to a higher WAL level requires measurable thresholds across all of: first-pass schema pass rate, clinician approval rate for WAL-1 drafts, correction/override rate, latency and cost benchmarks, and zero critical safety incidents over a defined run count. Thresholds are set by the institution's clinical governance committee and are specific to each workflow class — not applied globally.

Demotion is automatic on any anomaly that would not have cleared the promotion criteria. Demotion does not require a governance vote. The system demotes immediately and flags for review.

# 8. EHR Integration Pattern

## 8.1 Read Path — Tier 1 and Tier 2

FHIR-based retrieval for structured data. Document APIs for note retrieval. Event and timestamp anchoring for timeline construction. Mapping to internal patient identity and encounter context. The read path requires no modifications to the EHR itself — it operates through existing supported interfaces.

## 8.2 Write Path — WAL-Governed and Strictly Bounded

The write path is the highest-risk integration surface and is governed by explicit WAL constraints:

- WAL-1: Draft note text inserted as a DRAFT-AI object for clinician review. Not inserted into the legal record until the clinician reviews, edits (producing a DRAFT-REVIEWED object if modified), and signs (producing the CANONICAL record).

- WAL-2/3: Only for institutionally approved administrative actions with full reversibility and auditability.

- Autonomous order entry for clinical care is explicitly out of scope without separate institutional governance and patient safety review.

A specific implementation constraint governs WAL-1 draft state tracking: under standard FHIR DocumentReference APIs, the EHR does not broadcast granular edit diffs to external orchestrators when a clinician modifies a preliminary draft within the native EHR interface. Tracking the transition from DRAFT-AI to DRAFT-REVIEWED state therefore requires one of two implementation approaches: (1) vendor-specific middleware integration — such as Epic App Orchard modules — that provides native UI event access, acknowledging that this introduces vendor-specific dependencies managed through the Skills and Workflow Contracts Library; or (2) post-hoc inference by comparing the final CANONICAL document against the DRAFT-AI object using NLP alignment, recovering what changed at the cost of real-time behavioral granularity. The institution selects the approach based on its EHR vendor capabilities. The vendor-neutral mandate applies to the orchestration architecture; EHR-layer integration adapters are explicitly scoped as vendor-specific implementation details. The continuous improvement engine’s labeled training data capture is scoped to what is recoverable via the selected approach, and this scope is documented in the Phase 2 implementation specification.

## 8.3 Asynchronous Pipelines

For ambient monitoring and longitudinal synthesis running prior to patient visits, the orchestrator utilizes a persistent backend system architecture via bulk data APIs, standard messaging feeds, and event-driven webhooks. This enables always-on use cases — maintaining normalized historical context and temporal modeling of the patient timeline — without requiring active clinician sessions.

## 8.4 Legacy System Integration

Where modern FHIR interfaces do not exist in older departmental silos, AI-assisted data mappers are deployed to extract, transform, and load legacy data into the orchestrator's data layer. The local model generates mappers from source field definitions and target specification documentation, cutting integration timelines substantially. Robust security features including tokenization and role-based access controls are embedded during data transit.

# 9. Phased Implementation Approach

Each phase delivers measurable clinical value while building the validation infrastructure required for more advanced capabilities. A failed Tier 3 synthesis does not compromise Tier 1 retrieval. The system degrades gracefully rather than catastrophically.

| **Phase** | **Capability** | **Scope** | **Risk Profile** |
| --- | --- | --- | --- |
| Phase 1 | Deterministic Foundation (Tier 1) | Structured retrieval: lab trends, medication timelines, problem list consolidation, prior test surfacing, appointment history. | Minimal. No probabilistic generation. No PHI leaves institutional boundary. Output correct by construction. |
| Phase 2 | Verified Extraction with Validation Gates (Tier 2) | Local extraction from unstructured notes with dual-validation gates. Medication reconciliation, symptom onset, open referral loops. | Contained. Verification gates and failure classification prevent unvalidated extractions from reaching clinicians. |
| Phase 3 | Longitudinal Synthesis (Tier 3) | Context Packager deployed. Approved frontier model endpoints established. Pre-visit longitudinal briefs, multi-specialty timelines, contradiction/gap scans. | Managed. All output is DRAFT-AI with full provenance. Clinician review required before any chart interaction. |
| Phase 4 | Ambient Intelligence (WAL-1) | Ambient visit note drafting from encounter dialogue. Structured SOAP formatting. Documentation completeness enhancement. | Governed by WAL-1 constraints. Physician retains full authority over all documentation. DRAFT-AI state mandatory. |

# 10. Adversarial Review: Challenges and Responses

The following challenges represent the strongest critiques this architecture will face from clinical informatics reviewers, IT security, compliance, clinical staff, and institutional leadership. Each is addressed directly.

## Challenge 1: 'This is just another AI wrapper on the EHR. Epic already does this.'

Response: Epic's AI-assisted features operate within Epic's architecture, are limited to Epic's data model, and are governed by Epic's roadmap. This framework adds a governed orchestration layer on top of any EHR system, operates through standard FHIR interfaces, and is not vendor-dependent. More importantly, Epic's ambient documentation and AI tools do not address the cross-specialty longitudinal synthesis problem for complex multi-system patients, and they do not provide the draft state model or immutable patient narrative pathway described here. Epic's tools are Tier 1 retrievers at best. This architecture provides Tier 1 through Tier 3.

## Challenge 2: 'The hallucination risk in clinical settings is unacceptable. We cannot trust AI-generated content in a patient record.'

Response: This is the correct instinct, and this architecture is designed around it. Tier 1 has zero generative hallucination risk — the model does not generate facts, it routes to authoritative sources. Tier 2 has automated verification gates that catch errors before clinical presentation. Tier 3 output is always a DRAFT-AI artifact requiring clinician review before any chart entry — it is never written autonomously to the legal record. The WAL framework ensures no AI-generated content reaches the canonical record without explicit clinician review and signature. The architecture's primary engineering objective is making hallucination either impossible (Tier 1) or caught-before-harm (Tiers 2 and 3).

## Challenge 3: 'Sending any PHI to an external cloud model is a HIPAA violation we cannot accept.'

Response: Correct, which is why the Context Packager exists as a distinct architectural component. PHI is structurally redacted — not as a policy instruction to the model, but as a deterministic preprocessing step under institutional control — before any payload crosses the network boundary. The egress gateway enforces institutional policy and blocks non-compliant payloads. Frontier models receive de-identified, surrogate-tokenized context. The institution controls what leaves. No external model receives unredacted patient identity. This satisfies HIPAA's minimum necessary standard as an architectural constraint. The BAA with any approved external model endpoint is a separate governance requirement that the approved model catalog enforces.

## Challenge 4: 'Clinicians will not trust AI-drafted notes. Adoption will fail.'

Response: The WAL framework is designed specifically for this concern. WAL-0 provides value with no autonomy — the physician sees AI-retrieved context and decides what to do with it. WAL-1 provides draft artifacts that the physician reviews and edits; the DRAFT-AI and DRAFT-REVIEWED states make the physician's contribution visible and auditable. The system earns trust by demonstrating measurable reliability at each WAL level before promotion. Clinician corrections and overrides are captured as labeled training data, so the system improves from physician expertise rather than eroding it. Adoption follows demonstrated value, not mandate.

## Challenge 5: 'A 7–13B parameter model is not capable of the routing accuracy this architecture requires.'

Response: Research on domain-specific fine-tuning of models in the 8B parameter range for intent classification demonstrates accuracy exceeding 90% for structured routing tasks — matching much larger models for this specific use case without the latency or cost overhead. The routing model is not asked to reason clinically; it is asked to classify intent into a bounded set of tier assignments and skill invocations. This is a well-defined classification problem, not open-ended generation. The bifurcated deployment approach — a smaller, faster model for real-time intent classification and a larger context-window model for pre-visit synthesis packaging — further optimizes capability to task. The model performance ledger tracks routing accuracy per task type and enables data-driven refinement.

## Challenge 6: 'The patient narrative input path duplicates existing portal functionality.'

Response: Existing portal functionality captures patient inputs but does not guarantee their immutability, their separation from clinical staff entries, or their provenance preservation through downstream synthesis. Currently, a patient's portal message may be summarized, paraphrased, or translated into clinical language in a way that overwrites the original expression. The Patient Narrative Input Path is not a new data entry surface — it is a governance guarantee about how existing patient inputs are stored and traced. The patient's exact words are preserved as a write-once SOURCE object. Any derived clinical representation carries a mandatory pointer back to that object. This is a structural change to how existing data is handled, not a new user interface.

## Challenge 7: 'Immutable source objects and provenance chains add significant storage overhead.'

Response: The storage overhead of maintaining write-once source objects and provenance chains is modest relative to the existing EHR storage footprint and is substantially offset by the medico-legal value of a complete, auditable evidence chain. In the current absence of provenance chains, correcting a downstream error requires manual tracing through potentially dozens of encounter notes — a labor cost that dwarfs the storage cost of the alternative. Furthermore, derived artifacts (DRAFT-AI, DRAFT-REVIEWED) are significantly smaller than the source documents from which they are assembled, and the token budget constraints on the Context Packager limit payload sizes structurally.

## Challenge 8: 'Who is liable if an AI-drafted note contains a clinical error the physician missed during review?'

Response: The physician who reviews, edits if necessary, and signs the CANONICAL record bears clinical and legal responsibility for its contents — exactly as they do today when reviewing and signing a dictated note or scribe-authored documentation. The DRAFT-AI state is legally equivalent to a dictated draft: the AI is acting as a transcription and synthesis aid, and the physician's signature is the legal act that creates the official record. The DRAFT-AI state preserved in the audit chain actually provides more transparency than current practice: it shows precisely what the AI generated and what the physician accepted, modified, or rejected. This strengthens the medico-legal record rather than weakening it.

# 11. Measurable Outcomes for Leadership Review

The following metrics demonstrate whether the architecture is solving the information disconnect the patient narrative describes. They are organized by stakeholder interest.

## Clinical Workflow Metrics

- Reduction in pre-visit chart review time for complex multi-specialty patients

- Percentage of visits where a cross-specialty longitudinal brief is available at check-in

- Ratio of physician time spent in chart versus direct patient interaction

- Reduction in duplicated diagnostic testing attributable to missing prior cross-specialty context

- Rate at which prior institutional tests are surfaced automatically at acute presentations

## Quality and Safety Metrics

- Provenance completeness rate — percentage of synthesized claims traceable to source documents

- Tier 2 structural failure rate, semantic failure rate, and respective escalation accuracy

- WAL-1 draft approval, edit, and rejection rates by workflow type

- Incidence of clinically material inaccuracies (tracked as safety events)

- Multi-model dispatch disagreement rate for defined high-stakes task classes

## Patient Experience Metrics

- Patient-reported burden of re-explaining medical history across specialties

- Consistency of care plans and follow-up closure rates across departments

- Patient satisfaction scores for complex-care encounters — pre and post implementation comparison

- Rate at which patient-entered narrative is preserved verbatim versus translated away in clinical documentation

# 12. Conclusion

The patient's accompanying narrative identifies a systemic failure that the current EHR infrastructure was not designed to address: the inability to synthesize a complex patient's cross-specialty history into a coherent, clinician-ready narrative at the point of care — and the failure, at an acute presentation, to surface prior institutional tests that were directly relevant to the presenting complaint.

This refined architecture provides a technically sound, regulatory-aligned, and operationally phased response to that failure. The route-not-reason principle is not a rejection of advanced AI capabilities. It is a disciplined engineering approach that maximizes safety and reliability by constraining local models to tasks they can perform deterministically and escalating synthesis tasks through governed, privacy-preserving pathways to more capable systems.

The five architectural refinements in this version — explicit Context Packager boundaries, a write path draft state model, Tier 2 failure classification logic, governed multi-model dispatch, and a dedicated Patient Narrative Input Path — address the specific gaps identified in prior drafts and strengthen the architecture's ability to withstand adversarial institutional review.

The patient who submitted this proposal is asking for something measurable and achievable: stop making me act as a human data router between your specialists, and stop letting the system reach for a 14-year-old comparison tracing when 16 months of institutional cardiac data exists in the same record. The technology to solve this is mature. What is needed is an architectural framework that deploys it responsibly. This document provides that framework.

**END OF DOCUMENT**

*This document is vendor-neutral and product-agnostic. No specific hardware, software, or commercial platform is endorsed.*
