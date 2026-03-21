# Appendix R — Adversarial Review

## Red Team Assessment: Clinical AI Orchestration Framework

*Failure mode enumeration, service level commitments, rollback triggers, and architectural concessions.*

> **Note:** This document describes adversarial findings for a reference architecture applied to a class of clinical environment. It does not represent the position of any specific healthcare institution.

---

**Purpose of This Appendix**

A well-designed architecture document articulates what the system is designed to do. This appendix articulates where it can be wrong, what the institution has explicitly pre-committed to as rollback triggers, and which critiques represent genuine architectural concessions that must be addressed before production deployment. It is intended to accompany the Engineering Review v2.0 in every institutional review meeting.

This appendix is organized around eight adversarial challenge clusters. Each cluster contains: the sharpest version of the challenge, a direct architectural response, a determination of whether the challenge is fully answered or partially conceded, and — where applicable — explicit service level commitments (SLOs) and rollback conditions.

The four questions every institutional reviewer will ask are answered directly in Section R.10.

# R.1  Core Thesis: "Route, Don't Reason" — Partially Oversold

| **CHALLENGE** | **Classification is clinical reasoning at the edges.** Deciding whether a task is Tier 1 vs. Tier 2 vs. Tier 3 is not purely mechanical. Whether something is a 'simple retrieval' or a 'synthesis task' often depends on clinical nuance. Misclassification is a clinical risk, not merely an operationally recoverable routing error. |
| --- | --- |

| **RESPONSE** | **Conceded in part. The thesis should be restated more precisely: the architecture minimizes the scope of probabilistic reasoning, not eliminates it. The routing SLM reasons about task structure, not clinical content. It answers 'does this require retrieval, extraction, or synthesis?' — not 'what does this mean clinically?' That distinction is defensible.** The concession: misclassification at the Tier 1/Tier 2 boundary — treating an extraction task as a deterministic retrieval — does carry clinical risk if unverified output reaches presentation. The mitigation is the Tier 2 verification gate, which must catch errors the routing model introduced. The gate cannot assume the routing was correct. Architectural amendment: Tier 2 verification gates must be scoped to catch errors regardless of whether the task was correctly tiered. They are not a post-classification safety check — they are a pre-presentation safety check. This distinction must be explicit in the implementation specification. |
| --- | --- |

| **CHALLENGE** | **Tier 2 and Tier 3 are still probabilistic reasoning, just displaced.** The architecture rhetorically isolates 'reasoning' to Tier 3, but Tier 2 extraction is also model-driven interpretation of clinical text. 'Verified extraction' doesn't change that a wrong onset date or medication extracted from a note is a clinically consequential probabilistic error. |
| --- | --- |

| **RESPONSE** | **Conceded. The language 'verified extraction' was imprecise. A more accurate formulation: Tier 2 is probabilistic extraction with deterministic verification gates. The probabilistic element is the extraction model's interpretation of narrative text. The deterministic element is the structural and semantic check that validates the extracted claim before clinical presentation.** The clinical risk is not eliminated — it is bounded. The gate determines what reaches the clinician. What the gate cannot catch — a plausible but wrong date that passes structural checks — is a residual risk that must be surfaced in training materials and tracked as a safety metric. Architectural amendment: Section 5 language corrected. Tier 2 is 'probabilistic extraction with deterministic validation gates,' not 'verified extraction.' The distinction matters for medico-legal framing. |
| --- | --- |

| **CHALLENGE** | **'Zero hallucination by construction' is only as strong as the upstream mapping.** Tier 1 claims zero hallucination because it routes to authoritative sources — but the natural language → FHIR query → patient identity/encounter context mapping is itself a failure surface. If the router selects the wrong patient, encounter, or time window, you have delivered real data about the wrong context. Functionally indistinguishable from hallucination at the bedside. |
| --- | --- |

| **RESPONSE** | **Conceded and classified as the highest-priority failure mode in this document. Wrong-patient retrieval at Tier 1 is more dangerous than hallucination, because the output looks authoritative and is harder to detect.** Architectural amendment required: Tier 1 must include an explicit patient/encounter binding confirmation step before any data is surfaced to the clinician. The routing SLM constructs a query; a deterministic binding validator confirms patient MRN, encounter ID, and time window match the active clinical context before execution. This is a pre-retrieval safety gate, not a post-retrieval check. The phrase 'zero hallucination by construction' is retired from the document. Replaced with: 'zero generative hallucination risk — retrieval fidelity is governed by a mandatory patient/encounter binding confirmation gate.' |
| --- | --- |

| **SLO** | **SLO R.1 — Patient/Encounter Binding Accuracy: 99.99% correct patient/encounter binding on all Tier 1 queries, measured against confirmed active clinical context. Any confirmed wrong-patient retrieval event triggers immediate Tier 1 suspension for the affected workflow class pending root cause analysis. This threshold cannot be overridden by governance vote.** |
| --- | --- |

# R.2  Routing SLM: Capability, Drift, and Operational Reality

| **CHALLENGE** | **Routing accuracy is not static and will silently decay.** New templates, order sets, specialties, and abbreviations continuously change the distribution of clinical queries. Without automated regression testing tied to deployment gates, routing accuracy will decay without observable signal. |
| --- | --- |

| **RESPONSE** | **Valid. The performance ledger described in the Engineering Review tracks routing accuracy but does not specify the deployment gate mechanism. This is a gap.** Architectural amendment: Routing model deployment is gated on a continuous regression harness. Every deployment candidate must pass against the golden task suite before promotion. The golden task suite is updated on a defined cadence — at minimum, any time a new template, order set, or specialty workflow is added to the system. A deployment that degrades routing accuracy on any existing workflow class by more than the defined threshold is blocked automatically. |
| --- | --- |

| **SLO** | **SLO R.2a — Routing Regression Gate: No deployment proceeds if it degrades routing accuracy on any existing workflow class by more than 2 percentage points versus the current production baseline, measured across the full golden task suite.** SLO R.2b — Golden Task Suite Freshness: The golden task suite must be updated within 10 business days of any new template, order set, or specialty workflow being added to the system. Deployments are blocked if suite freshness is expired. |
| --- | --- |

| **CHALLENGE** | **Class imbalance — highest-risk workflows have the least training data.** Complex multi-system patients, rare diseases, and edge-case consults are precisely the workflows with the fewest training examples. The routing model trained on majority patterns will be most wrong where it most needs to be right. |
| --- | --- |

| **RESPONSE** | **Valid and partially conceded. This is not fully solvable at architecture level — it is a data problem. The architectural response is to make the failure mode safe rather than absent.** Architectural amendment: For any task that falls below a minimum confidence threshold at the routing layer, the system defaults to Tier 3 escalation and flags the task as 'routing-uncertain' in the provenance chain. The physician sees a 'routing confidence: low' indicator on the output. Uncertain routing is never silently resolved by guessing Tier 1 or Tier 2. The routing confidence threshold is a configurable institutional parameter, not hardcoded. Clinical governance sets it per workflow class, with higher thresholds required for workflows involving complex multi-system patients. |
| --- | --- |

| **SLO** | **SLO R.2c — Routing Confidence Floor: Any task where routing confidence falls below the institutional threshold for its workflow class must be escalated to Tier 3 and flagged. Confident-but-wrong routing is a more dangerous failure mode than uncertain routing that escalates safely.** |
| --- | --- |

| **CHALLENGE** | **Latency vs. fidelity tradeoff is underplayed. Where are the circuit breakers?** <500ms for Tier 1 and <2s for Tier 2 requires aggressive truncation, caching, and heuristic shortcuts. What happens when the SLM cannot confidently classify within the latency budget? Block, degrade, or silently guess? |
| --- | --- |

| **RESPONSE** | **Gap confirmed. The prior document specified latency targets without specifying the failure behavior when those targets cannot be met within confidence bounds.** Architectural amendment: The system implements three circuit breaker states: (1) CONFIDENT-IN-BUDGET: proceed normally. (2) CONFIDENT-OVER-BUDGET: surface latency warning, proceed with output, log SLO breach. (3) UNCERTAIN-ANY-LATENCY: escalate to Tier 3 with 'routing-uncertain' flag, never surface a low-confidence result regardless of latency. The system never silently guesses. Uncertainty is always surfaced or escalated. |
| --- | --- |

# R.3  Context Packager and Privacy: Strong Idea, Fragile in Implementation

| **CHALLENGE** | **NER-based PHI minimization is not a hard guarantee.** Even with healthcare-tuned NER, PHI leakage via quasi-identifiers — rare conditions, dates, geographic detail, family structure — is non-trivial. 'Covers all 18 HIPAA Safe Harbor identifiers' is a legal list, not a practical re-identification bound. |
| --- | --- |

| **RESPONSE** | **Conceded. The 18-identifier Safe Harbor list is a compliance floor, not a privacy ceiling. Rare disease + county + age cohort can re-identify a patient without triggering any Safe Harbor violation.** Architectural amendment: The PHI minimization layer is supplemented with a quasi-identifier risk assessment step. For any payload that contains rare diagnoses, geographic data at sub-state level, or narrow age/date combinations, the Context Packager applies additional generalization (coarsening dates to month/year, removing sub-county geography, flagging rare diagnoses for clinical necessity review) before egress. This step is logged in the audit chain. The document no longer claims NER coverage as a privacy guarantee. The correct framing: NER minimization reduces the expected re-identification surface; quasi-identifier assessment reduces it further; residual risk is managed through contractual controls with approved model endpoints (BAA, no-training clauses) and institutional risk acceptance. |
| --- | --- |

| **CHALLENGE** | **Surrogate token mapping vault is a new crown jewel.** The surrogate-to-identity mapping stored in the institution-controlled key vault becomes a high-value target. The architecture doesn't address HSMs, split-key schemes, or operational controls around who and what can resolve surrogates back to identity. |
| --- | --- |

| **RESPONSE** | **Valid gap. The key vault was mentioned without operational specification.** Architectural amendment: The surrogate mapping store is governed by the following minimum controls: (1) Hardware Security Module (HSM) storage for all surrogate-to-identity mappings. (2) Split-key scheme requiring two authorized principals for any resolution of surrogate back to identity. (3) Resolution events are append-only logged and reviewed on a defined cadence. (4) The orchestration layer itself cannot resolve surrogates — only a separate, audited identity resolution service with its own access controls can do so. (5) Surrogate mappings are patient-isolated: compromise of one patient's surrogate table does not expose others. |
| --- | --- |

| **CHALLENGE** | **Egress gateway needs semantic inspection, not just structural checks.** If the Context Packager misbehaves or is misconfigured, the gateway needs semantic inspection capabilities — not just schema and size validation — to be a meaningful safety control. |
| --- | --- |

| **RESPONSE** | **Valid. Schema and size validation catch malformed payloads. They do not catch a well-formed payload that contains semantically inappropriate clinical content.** Architectural amendment: The egress gateway implements a two-layer validation: (1) Structural layer — schema compliance, size limits, required fields present. (2) Semantic layer — a separate, lightweight classification model reviews the payload for presence of identifiable quasi-identifier clusters and flags payloads that exceed the institutional re-identification risk threshold for human review before transmission. This classification model is not the same model that built the payload — it is an independent check. |
| --- | --- |

# R.4  Immutable Source Layer: Governance Win, Platform Tax

| **CHALLENGE** | **Storage overhead is not 'modest' — the real cost is indexing and developer complexity.** Every downstream service now has to be provenance-aware, or you risk inconsistent behavior — such as analytics accidentally including DRAFT-AI states in outcome calculations. |
| --- | --- |

| **RESPONSE** | **Valid. 'Modest' was imprecise. The storage cost is modest relative to existing EHR footprint. The developer complexity cost is not modest — it is a platform-level design constraint that every consuming service must honor.** Architectural amendment: The platform enforces state-awareness at the API layer, not at consuming service discretion. Data APIs return only CANONICAL and SOURCE objects by default. Access to DRAFT-AI and DRAFT-REVIEWED states requires an explicit, scoped API flag with logged justification. Analytics pipelines that do not explicitly request draft states cannot accidentally receive them. The burden of provenance-awareness is enforced by the platform, not delegated to consuming teams. |
| --- | --- |

| **CHALLENGE** | **Lineage-based invalidation is a full-blown data platform problem.** 'If a source document is corrected, the system traces the lineage through all downstream derived artifacts, expires corrupted derivatives, and regenerates from the preserved original.' This implies a graph-aware dependency engine with idempotent re-generation — not a side feature. |
| --- | --- |

| **RESPONSE** | **Conceded. This is the largest implementation complexity claim in the document and it was understated.** Architectural amendment: Lineage-based invalidation is scoped as a Phase 3/4 capability. Phase 1 and 2 deployments do not require full graph-aware invalidation. In Phase 1 and 2, source corrections trigger a human review workflow — a clinician is notified that a source document has been amended and that any downstream derived artifacts may need re-review. Automated cascade invalidation and regeneration is a Phase 3 milestone with its own engineering specification. This is an honest concession: the full immutable source guarantee described in the Engineering Review is an architectural target, not a Day 1 deliverable. |
| --- | --- |

| **CHALLENGE** | **What about erroneous patient narrative entries or patient requests to correct their own prior statements?** Guaranteeing that 'the patient's direct narrative is never modified by any system process' is admirable — but what about misattributed messages or patient rights to correct prior statements? |
| --- | --- |

| **RESPONSE** | **Valid patient rights concern. The resolution is a 'superseded but preserved' model.** Architectural amendment: Patients may request that a prior narrative entry be marked as superseded. The original entry is never deleted — it is tagged SUPERSEDED with a timestamp and reason, and a new SOURCE entry is created with the corrected narrative. Both entries are preserved in the audit chain. Clinical systems display the most recent non-superseded entry by default, with a disclosed link to prior versions. This satisfies both immutability (the original is preserved) and patient rights (the corrected version is displayed). It also satisfies the medico-legal requirement to demonstrate what information was available at each point in the patient's care. |
| --- | --- |

# R.5  WAL Framework: Good Scaffolding, Underspecified Triggers

| **CHALLENGE** | **'Automatic demotion on anomaly' — what counts as an anomaly?** Without explicit, pre-committed thresholds, demotion becomes political rather than automatic. Is a single serious safety event sufficient to demote a WAL-2 workflow to WAL-0? The document doesn't say. |
| --- | --- |

| **RESPONSE** | **Valid. The demotion trigger specification was left to institutional governance, which creates the exact governance capture risk described below.** Architectural amendment: The following thresholds are pre-committed in the platform specification and cannot be modified without a formal patient safety governance process with external clinical review: |
| --- | --- |

| **Event Type** | **Demotion Trigger** | **Recovery Path** |
| --- | --- | --- |
| Any confirmed wrong-patient data surfaced to a clinician | Immediate suspension of the affected workflow class. No override. | Root cause analysis + independent safety review before reinstatement. |
| Any CANONICAL record created from a DRAFT-AI artifact without documented clinician review | Immediate WAL-1 suspension for the affected workflow class. | Audit of all records produced during the gap + safety review. |
| WAL-1 draft approval rate drops below 60% over a 30-day window | Automatic demotion to WAL-0 for that workflow class. | Performance investigation + golden task suite update before re-promotion. |
| WAL-2 or WAL-3 action produces a confirmed clinically material error | Immediate demotion to WAL-0. No exceptions. | Full safety review, external clinical audit, governance re-approval required. |
| Routing accuracy drops >2 percentage points below baseline | Deployment rollback. New model version blocked until passing regression gate. | Root cause analysis + re-benchmarking. |

| **CHALLENGE** | **Governance capture — business pressure will erode WAL promotion criteria over time.** High-value workflows will have strong pressure to promote to higher WAL levels. If the same committee that benefits from efficiency gains also controls promotion criteria, safety margins will erode gradually. |
| --- | --- |

| **RESPONSE** | **Valid structural risk. The architectural response is separation of promotion authority from operational benefit.** Architectural amendment: WAL promotion decisions above WAL-1 require sign-off from a Clinical Patient Safety Officer who is organizationally independent of the teams that benefit from the promoted workflow. Promotion criteria are documented in the platform specification and audited annually by a party independent of the operational team. The audit report is reviewed by the CMO directly. |
| --- | --- |

| **CHALLENGE** | **No versioned model rollback story — WAL demotion doesn't fix a bad model.** Demotion changes how much damage a degraded model can do, but doesn't replace the model. The document is silent on versioned rollback of specific models and skills. |
| --- | --- |

| **RESPONSE** | **Gap confirmed. Demotion is a containment action, not a remediation action.** Architectural amendment: Every model artifact and skill version deployed to the platform is signed and registered in an immutable model registry. Rollback to any prior registered version is a one-command operation with a target latency of under 15 minutes. The model registry enforces that only signed, registered artifacts can run in production — no ad hoc deployments. Supply chain integrity is verified at deployment time via attestation. |
| --- | --- |

# R.6  EHR Integration: "No EHR Changes" Is Only Half the Story

| **CHALLENGE** | **FHIR coverage is uneven and often lossy for complex patients.** Many clinically important nuances live in local extensions, custom flowsheets, or non-standard fields. A Tier 1 deterministic plane that only sees the 'clean' FHIR surface risks missing exactly the edge-case data that matters most. |
| --- | --- |

| **RESPONSE** | **Valid and partially conceded. FHIR R4 coverage at typical Epic and ****Cerner**** installations covers the majority of structured clinical data but does not cover all local extensions and custom flowsheets.** Architectural amendment: The deterministic data plane is documented with an explicit coverage map per installation — which data classes are available via FHIR, which require supplemental connectors, and which are not accessible at all. The pre-visit brief generated by Tier 3 must include a 'data not available' section listing any workflow-relevant data classes that could not be retrieved, so the clinician knows what the system could not see. Silence is not assumed to mean absence. |
| --- | --- |

| **CHALLENGE** | **AI-generated ETL mappers add a probabilistic layer to 'deterministic' Tier 1.** Using the local model to generate mappers from source definitions means Tier 1 depends on probabilistically generated mappings. Where is the validation harness for those mappers? |
| --- | --- |

| **RESPONSE** | **Valid. AI-assisted mapper generation is an efficiency tool, not an accuracy guarantee.** Architectural amendment: All AI-generated mappers are classified as DRAFT-MAPPER state and must pass a validation harness before promotion to production. The validation harness compares mapper output against a golden dataset of known-correct field mappings for that source system. Promotion requires >99% field-level accuracy on the golden dataset. A human data architect reviews and signs every mapper before production deployment. The mapper is versioned and rollback-capable. |
| --- | --- |

| **CHALLENGE** | **Bulk FHIR and asynchronous pipelines are capacity-constrained at scale.** Running longitudinal synthesis and ambient monitoring at scale will stress bulk FHIR APIs, message queues, and downstream storage. There is no capacity planning or backpressure story. |
| --- | --- |

| **RESPONSE** | **Gap confirmed. Capacity planning was out of scope for the architectural blueprint but must be addressed before production sizing.** Architectural amendment for Phase 2/3 engineering specification: Asynchronous pipelines implement explicit backpressure via rate limiting at the FHIR API consumer layer, circuit breakers on bulk data jobs, and priority queuing that ensures active patient encounters are never blocked by background synthesis jobs. Capacity planning for longitudinal synthesis is scoped to the patient population at each deployment phase, not assumed to be uniformly scalable. |
| --- | --- |

# R.7  Security and Multi-Tenant Isolation: Claims vs. Attack Surface

| **CHALLENGE** | **Kubernetes multi-tenancy is notoriously tricky.** Namespaces and node pools are not sufficient isolation. The document doesn't specify whether strict per-tenant clusters, gVisor/Firecracker sandboxes, or logical separation are used. Side-channel risks and misconfiguration are common. |
| --- | --- |

| **RESPONSE** | **Valid. 'Hardware-level partitioning' was used loosely in the Engineering Review.** Architectural amendment: The isolation specification is tiered by data sensitivity. PHI-processing workloads (Context Packager, Tier 2 extraction, patient narrative ingestion) run in per-department dedicated node pools with no cross-tenant scheduling. Shared infrastructure services (audit logging, routing SLM inference for non-PHI task classification) may use namespace-level isolation with network policies enforced via service mesh. The isolation level for each component class is documented in the infrastructure specification and reviewed by the security team annually. |
| --- | --- |

| **CHALLENGE** | **Model artifacts and embeddings are a new leakage vector.** If a shared vector store is used for semantic retrieval, cross-tenant leakage is a real risk unless hard partitioning is enforced at the storage layer. |
| --- | --- |

| **RESPONSE** | **Valid gap. Vector stores were not addressed in the Engineering Review.** Architectural amendment: All vector stores used for semantic retrieval are patient-isolated — no cross-patient or cross-department vector index. Patient-specific embeddings are stored in per-patient partitions with access controls enforced at the storage layer. Shared embeddings (clinical ontologies, institutional knowledge base) are derived from de-identified sources only and reviewed for re-identification risk before deployment. |
| --- | --- |

| **CHALLENGE** | **Supply chain and model integrity are under-addressed.** No mention of signed model artifacts, attestation, or SBOMs. In a safety-critical system, we need a strong story for proving that the model and code running in production is exactly what was validated. |
| --- | --- |

| **RESPONSE** | **Gap confirmed and addressed in R.5 (model rollback). Expanded here:** Architectural amendment: All model artifacts, container images, and orchestration code are signed at build time and verified at deployment time. An SBOM is generated for every production build. Attestation is verified by the deployment gate — any artifact that cannot be verified against its registered signature is blocked from deployment. Runtime integrity monitoring detects and alerts on any deviation between the running artifact and its registered signature. |
| --- | --- |

# R.8  Human Factors and Adoption: The Hardest Part

| **CHALLENGE** | **Cognitive load of new artifacts risks overwhelming clinicians.** Pre-visit briefs, timelines, contradiction scans, multi-model deltas, WAL indicators, provenance links — each is individually reasonable, but together they risk overwhelming clinicians already drowning in alerts and inbox items. |
| --- | --- |

| **RESPONSE** | **Valid and the most operationally important critique in this appendix. The architecture is correct but the UX implementation could defeat it entirely.** Architectural amendment: The clinician interface enforces a strict 'one primary artifact per clinical context' principle. A pre-visit brief is one document with a defined maximum length, not a collection of separately surfaced outputs. Provenance links, multi-model deltas, and WAL indicators are available on demand — they are not displayed by default. The default view is optimized for clinical decision-making, not for demonstrating architectural sophistication. A clinical UX governance committee — including practicing physicians from each specialty that deploys the system — reviews and approves every default display configuration before production. The committee has veto power over any interface design that adds net cognitive load without demonstrated clinical benefit. |
| --- | --- |

| **CHALLENGE** | **No explicit failure UX story — clinicians will ignore alert fatigue.** When Tier 2 fails and routes to human review, what does the clinician see? A blocking alert? A subtle badge? Too many 'needs review' items will be ignored; too few will miss safety events. |
| --- | --- |

| **RESPONSE** | **Gap confirmed. The failure UX was not designed in the Engineering Review.** Architectural amendment: Failure UX follows a two-tier model. Critical failures — any wrong-patient binding event, any Tier 2 failure involving active medication or allergy data — generate a blocking alert that cannot be dismissed without an explicit clinician action and a logged reason. Non-critical failures — extraction uncertainty on historical notes, provenance ambiguity on older records — are surfaced as non-blocking contextual indicators visible on demand, not as inbox items. The ratio of critical to non-critical failures is tracked as a quality metric. If critical alert volume exceeds a defined threshold per clinician per week, the alerting threshold is reviewed — not silently absorbed. |
| --- | --- |

| **CHALLENGE** | **Patient narrative preservation is only half the battle.** You guarantee patient words are stored immutably — but you don't show how often those words will actually be seen by clinicians in the flow of care versus buried behind another click. |
| --- | --- |

| **RESPONSE** | **Valid. Immutable storage without clinical surfacing is archival, not care improvement.** Architectural amendment: Patient narrative surfacing is a first-class metric. The performance dashboard tracks: what percentage of pre-visit briefs include at least one verbatim patient-authored phrase in the opening summary; what percentage of clinician chart reviews included a click-through to the patient's direct narrative; and patient-reported rate of feeling heard versus having their words translated away. These metrics are reviewed in the same governance cycle as clinical workflow metrics — not treated as 'nice to have' patient experience data. |
| --- | --- |

# R.9  Consolidated Failure Mode Register

The following table enumerates the failure modes identified in this adversarial review, their severity classification, their detection mechanism, and the pre-committed response. This register is a living document — it must be updated any time a new failure mode is identified in production.

| **Failure Mode** | **Severity** | **Detection** | **Pre-Committed Response** |
| --- | --- | --- | --- |
| Wrong-patient data retrieved at Tier 1 | CRITICAL | Patient/encounter binding gate mismatch OR clinician-reported discrepancy | Immediate workflow class suspension. Root cause analysis. No override. |
| Routing model confidence below threshold — silently resolved | CRITICAL | Routing SLO monitoring | Blocked by circuit breaker. Uncertain tasks escalate to Tier 3 or human review. System never silently guesses. |
| CANONICAL record created without documented clinician review of DRAFT-AI | CRITICAL | Audit log review — automated nightly check | Workflow class WAL-1 suspension. Audit of all records in the gap. |
| PHI quasi-identifier leakage in egress payload | HIGH | Semantic egress gateway inspection | Payload blocked. Transmission log reviewed. Context Packager configuration audited. |
| Tier 2 structural failure silently resolved (wrong date/unit passes gate) | HIGH | Per-field verification audit against known-correct records in regression harness | Gate configuration reviewed. Golden task suite updated. Affected workflow class reviewed. |
| Routing model accuracy drift below 2 percentage point threshold | HIGH | Continuous performance ledger vs. baseline | Automatic deployment rollback. New deployment blocked until passing regression gate. |
| WAL-1 draft approval rate below 60% | MEDIUM | Monthly performance dashboard | Automatic WAL-0 demotion. Workflow class review. Prompt template update. |
| Surrogate token vault access outside authorized principals | HIGH | HSM access log — automated alert | Immediate access suspension. Security incident response. Resolution event audit. |
| FHIR data class unavailable — surfaced as absence to clinician | MEDIUM | Coverage map validation at query time | Explicit 'data not available' flag in output. Never surfaced as empty/absent without disclosure. |
| Patient narrative superseded without preserving original | HIGH | Immutable store write audit | Write operation blocked. Supersede workflow enforced — original preserved, new entry created. |
| Clinical content captured in security metadata logs (log as PHI repository) | MEDIUM | Periodic log content audit sampling for clinical payload fragments | Enforce strict separation between clinical content logs and security metadata logs at the logging pipeline level. Security logs contain only event metadata: event type, timestamp, resource type accessed (not content), session identifiers, and policy decisions. Clinical content logs governed by same access controls as clinical record. Two separate log streams with separate retention, access, and audit requirements. |

# R.9a  Incident Response Playbooks

Pre-committed rollback triggers and demotion conditions specify when the system must act. These five playbooks specify what happens operationally after a trigger fires. Each playbook covers triggers, containment, assessment, recovery, and corrective action. Playbooks are maintained by the platform team and reviewed quarterly.

**Playbook A: Wrong-Patient PHI Exposure**

**Triggers: **Clinician reports wrong-patient data; patient/encounter binding gate mismatch alert; cross-patient query anomaly detected.

**Containment: **Disable affected workflow class immediately. Revoke active session tokens for affected clinician. Preserve all logs. Lock down log access to security team.

**Assessment: **Determine whether PHI was unsecured and whether acquisition or access is reasonably believed — aligning with HIPAA Breach Notification Rule threshold (45 CFR 164.400-414). Identify scope of exposure.

**Recovery: **Patch binding gate configuration. Re-run patient context binding test suite. Notify compliance and legal. Execute breach notification process if threshold met. Reinstate workflow class only after independent safety review.

**Playbook B: Prompt Injection — Unauthorized Tool Calls**

**Triggers: **Spike in blocked policy decisions; anomalous tool invocation sequences; exfil-pattern outputs detected.

**Containment: **Switch to safe mode: read-only Tier 1 retrieval only. Disable document-based retrieval and patient narrative ingestion. Preserve injection event logs.

**Assessment: **Identify injection vector — direct user input, retrieved document, or patient narrative. Determine whether any unauthorized tool calls completed. Assess PHI exposure from any completed calls.

**Recovery: **Patch prompt separation and taint rules. Expand adversarial test corpus with injection variant. Update input sanitization layer. Redeploy only after passing expanded injection test suite.

**Playbook C: Supply Chain Compromise**

**Triggers: **Signature mismatch on model artifact; unexpected model hash at deployment; routing behavior anomaly after update.

**Containment: **Freeze all deployments immediately. Rollback to last-known-good signed version (target: under 15 minutes). Isolate model registry and CI/CD pipeline from production.

**Assessment: **Determine which artifact was compromised and when. Identify scope of production exposure. Preserve build pipeline logs.

**Recovery: **Rotate all signing keys. Re-validate SBOM for entire stack. Implement additional build pipeline controls. Independent security review before next deployment.

**Playbook D: Patient Safety Event Involving Routing**

**Triggers: **Clinician reports unsafe omission or commission attributable to routed output; near-miss safety event; safety event correlation flagged.

**Containment: **Demote affected workflow class to WAL-0 immediately. Require provenance review for all outputs in affected class. Flag for clinical safety review.

**Assessment: **Reconstruct session using audit logs. Verify which source documents were surfaced and which were not. Determine whether the failure was a routing error, extraction error, FHIR coverage gap, or clinician acceptance of an uncertain output.

**Recovery: **Update routing policy and UI forcing functions for the affected workflow class. Review golden task suite for the failure case. Report to patient safety governance committee. Monitor automation bias signals for 90 days post-incident.

**Playbook E: Availability Incident (DoS, Outage, Ransomware)**

**Triggers: **Latency spikes exceeding SLO thresholds; inference timeouts; GPU saturation; ransomware indicators on cluster nodes.

**Containment: **Isolate impacted cluster segments. Activate clinical downtime workflows immediately — the system degrades to Tier 1 deterministic retrieval only; if Tier 1 is also affected, full manual fallback. No patient care delay is acceptable pending AI restoration.

**Assessment: **Assess scope and nature of incident. If ransomware: follow HHS ransomware response guidance. Preserve forensic evidence before recovery actions.

**Recovery: **Restore from validated backups. Verify integrity of all model artifacts before reactivating. Validate audit log continuity. Full system test before returning to WAL-2 or WAL-3 workflows.

# R.10  The Four Questions Every Institutional Reviewer Will Ask

These are the four questions the adversarial review identified as the sharpest institutional challenges. Each is answered directly and completely, with no hedging.

## Question 1: Where, exactly, can this system be wrong in ways that look correct to a busy clinician?

Three places:

- Wrong-patient Tier 1 retrieval. Real data, wrong patient. The output looks authoritative. Detection requires the patient/encounter binding gate — which must fire before data is displayed, not after. This is the highest-priority failure mode in the system.

- Tier 2 extraction errors that pass structural verification. A plausible but wrong medication onset date that is chronologically valid, from the correct patient, will pass the structural gate. The clinician sees a provenance-linked claim that appears sourced. The mitigation is cross-source reconciliation against structured pharmacy records — not all notes are cross-checkable.

- Tier 3 synthesis that confidently omits a relevant specialty's data because it wasn't in the retrieved context bundle. The pre-visit brief looks complete but is missing the pulmonology perspective because that department's notes weren't in the FHIR surface the Context Packager accessed. The 'data not available' disclosure requirement in R.6 mitigates this — but only if the clinician reads it.

## Question 2: What are the hard, pre-committed thresholds that trigger automatic rollback — and who cannot override them?

The thresholds are enumerated in R.5 and R.9. The two that cannot be overridden by any governance vote or operational argument:

- Any confirmed wrong-patient data surfaced to a clinician triggers immediate workflow class suspension. No override. Reinstatement requires root cause analysis and independent safety review.

- Any CANONICAL record created from a DRAFT-AI artifact without documented clinician review triggers immediate WAL-1 suspension for the affected workflow class. No override.

These two thresholds are encoded in the platform specification, not in governance policy. Changing them requires an amendment to the platform specification with CMO sign-off and external clinical safety review.

## Question 3: How do we prove, six months in, that routing, extraction, and synthesis are not silently drifting?

Three mechanisms in combination:

- Continuous regression harness against the golden task suite, executed on every deployment and weekly against production traffic samples. Any accuracy delta >2 percentage points triggers a deployment block or rollback alert.

- Clinician correction and override capture. Every time a clinician edits a DRAFT-AI artifact, rejects a suggested retrieval result, or overrides a routing decision, that event is captured as a labeled data point. The correction rate trend — not just the absolute correction rate — is the early warning signal for drift. A rising correction rate is drift.

- Six-month formal review. A structured review of routing accuracy, extraction failure rates, WAL-1 approval/edit/reject trends, and patient narrative surfacing rates is conducted at six months post-deployment and presented to the CMO. This is not a dashboard — it is a structured report with a pass/fail determination against the pre-committed SLOs.

## Question 4: What is the minimal end-to-end slice that actually fixes the human data router problem without introducing a new class of silent failures?

Phase 1, scoped to one deployment: Tier 1 deterministic retrieval of cross-specialty prior test results for complex multi-system patients, surfaced as a pre-visit context panel in the clinician's existing workflow. No Tier 2. No Tier 3. No DRAFT-AI. No ambient documentation.

What this fixes: the specific failure documented in the patient narrative — a cardiopulmonary exercise test and baseline ECG conducted 16 months prior at the same institution failing to surface during an acute cardiac presentation. Tier 1, scoped to prior test retrieval, with the patient/encounter binding gate, fixes this failure with zero generative risk.

What this does not introduce: probabilistic extraction errors, hallucination risk in synthesis, draft state confusion, ambient recording consent issues, or WAL promotion pressure. The failure mode register for Phase 1 scoped to Tier 1 is limited to wrong-patient binding errors and FHIR coverage gaps — both of which have explicit detection mechanisms and pre-committed responses.

The case for deploying this slice alone is that it solves the documented problem, demonstrates institutional value, and builds the trust infrastructure needed for Phases 2 through 4 — without asking the institution to accept risks it hasn't earned confidence in yet.

# R.11  FHIR Write-Back Gap and Continuous Improvement Engine Scope

**CHALLENGE**

The continuous improvement engine’s claim to capture “every clinician rejection, edit, or override as a labeled data point” cannot be realized through standard FHIR channels. Under standard FHIR DocumentReference APIs, the EHR does not broadcast granular edit diffs to external orchestrators when a clinician modifies a preliminary draft within the native EHR interface. The DRAFT-AI to DRAFT-REVIEWED state transition is architecturally broken under standard FHIR.

**RESPONSE**

Conceded. Two implementation approaches exist: (1) vendor-specific middleware integration — such as Epic App Orchard modules — that provides native UI event access, acknowledging vendor-specific dependencies; or (2) post-hoc inference by comparing the final CANONICAL document against the DRAFT-AI object using NLP alignment, recovering what changed at the cost of real-time behavioral granularity. The institution selects the approach based on its EHR vendor capabilities. The continuous improvement engine’s training data capture is honestly scoped to what is recoverable under the selected approach. What granularity of edit behavior is lost under each approach is documented in the Phase 2 implementation specification.

**SLO**

SLO R.11 — The implementation specification for Phase 2 must document which approach is selected, what granularity of edit behavior is captured, and what training signal quality this produces relative to the architecture’s improvement engine design.

# R.12  Multi-Model Parallel Dispatch — Inter-Agent Channel Security

**CHALLENGE**

Research on parallel dispatch architectures (AgentLeak framework and related work) demonstrates that inter-agent communication channels — the messages, tool calls, and shared memory states used to adjudicate disagreements between simultaneously dispatched models — leak PHI at 2.5x the rate of single-model outputs, even when the final synthesized output is clean. The Context Packager governs the initial outbound payload but does not govern inter-agent channels, which are managed by the external model providers.

**RESPONSE**

Conceded. Multi-model parallel dispatch for Tier 3 tasks is gated behind a separate inter-agent channel security review before institutional activation. The default Tier 3 behavior is single approved endpoint per task class. Parallel dispatch is an institutional opt-in after the security review is completed. Institutions that activate multi-model dispatch accept the inter-agent channel risk as documented in the institutional risk register.

**SLO**

SLO R.12 — No workflow class may activate multi-model parallel dispatch without a documented inter-agent channel security review signed by the institution’s CISO and reviewed by the clinical privacy officer.

**END OF APPENDIX R**

*This appendix is a living document. It must be updated any time a new failure mode is identified in production, any SLO threshold is revised, or any pre-committed rollback trigger is amended.*