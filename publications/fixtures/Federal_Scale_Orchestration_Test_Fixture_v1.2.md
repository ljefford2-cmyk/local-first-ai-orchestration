# Federal-Scale Orchestration Test Fixture

*Governed AI Orchestration Inside a Synthetic Federal Safety Regulator*

**Lawrence Jeffords — End-User and Operator**

**Status:** A DRNT Methods Test Fixture — Version 1.2 — July 2026

> **Controlled-text note:** This Markdown file is a repository transcription derived from the controlled DOCX supplied for the August 2026 update. The source DOCX remains authoritative for exact layout, tables, figures, and publication identity; its SHA-256 is recorded in the [Publication Manifest](../MANIFEST.md).

---

## 0. Fixture Status — Read This First

|                          |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| What this is             | A synthetic test fixture: a fully fictional federal safety regulator, written in enough operational and compliance detail to test whether DRNT governance methods survive transplantation to federal scale.                                                                                                                                                                                                                                                                                                                                                                                                                       |
| What this is not         | Not a proposal. Not a system. Not a product. Not a submission to any agency, and never to be used as one. It will not be deployed, piloted, or offered.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| The agency is fiction    | The National Safety Inspection Administration (NSIA), its six inspection directorates, its mission systems (CIRRUS, CARMS, TECR, NSI, PID, MARIS), its personnel, and its statistics do not exist. They are a composite invented for this fixture. Any resemblance to a real agency, system, or person is a modeling choice, not a claim.                                                                                                                                                                                                                                                                                         |
| The constraints are real | The federal constraint stack cited throughout (NIST, FedRAMP, Zero Trust, CUI handling, Section 508, SSDF) is quoted from the real world on purpose. The test is whether DRNT methods hold under real constraints; only the organization wearing them is invented. Citing a framework here is a design input to the fixture, never a claim of compliance by anyone.                                                                                                                                                                                                                                                               |
| Lineage                  | This fixture supersedes and retires an earlier draft that borrowed the framing of a real department. That framing risked being mistaken for a proposal. It never was one. All agency-specific content has been replaced with fiction; the governance architecture has been rewritten to incorporate everything the DRNT program has learned since, folded in as native design rather than appended as gap findings. Versions 1.1 and 1.2 incorporate two adversarial panel rounds, with every post-cutoff policy claim verified against live primary sources before incorporation; the resolutions are tabled in §17.2 and §17.3. |
| What is under test       | One question: do the DRNT invariants — route-don't-reason, no self-certification, independence-measured consensus, two-seat human authority, governed mutation, claim-tier discipline — remain load-bearing when the enterprise around them is a federal-scale regulator instead of a single end user's testbed?                                                                                                                                                                                                                                                                                                                  |

### 0.1 How to read every claim in this document

The fixture describes the modeled system in the present tense, because that is how an architecture document reads. Present tense is not an implementation claim. Every architectural mechanism in this document carries one of three claim classes, following the DRNT Authority Map discipline that a claim is never promoted to a stronger tier by assertion:

| **Label** | **Class**             | **Meaning**                                                                                                                                                                                                                                                                                                                       |
|-----------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \[P\]     | DRNT-proven primitive | The mechanism has been demonstrated runnable on the DRNT personal testbed (routing discipline, capability registry, WAL gates, context boundary, hash-chained append-only audit record, sandboxed dispatch, source-event replay guard). Proven as a primitive at single-user scale — never a claim it is proven at federal scale. |
| \[S\]     | DRNT-specified        | The mechanism exists in DRNT doctrine or specification but has no running component anywhere (the independent evaluator, the independence sensor and gate, roaming agent execution, stronger isolation tiers).                                                                                                                    |
| \[F\]     | Fixture hypothesis    | Federal-scale behavior this fixture models — concurrency figures, cost figures, organizational structures, adoption dynamics. Tested nowhere. These exist to give the governance methods a realistic load to be tested against.                                                                                                   |

Where a section mixes classes, the section states which parts hold which class. A reader who takes any \[F\] figure as a real-world estimate, or any \[S\] mechanism as built, is reading against the fixture's own rules.

### 0.2 Provenance

Prepared by Lawrence Jeffords with Claude (Anthropic), July 2026. Reviewed under the DRNT multi-model adversarial panel practice. Staging tier (Tier 3) under the DRNT Authority Map: current architectural thinking, pending review — not committed doctrine, not implementation.

**Revision history.** v1.0 (July 2026): initial fixture, superseding the retired department-framed draft. v1.1 (July 2026): first adversarial panel round, two independent model reviews. v1.2 (July 2026): second panel round — adds the LLM-procurement overlay (OMB M-26-04) and risk-based assurance posture (OMB M-26-05), both verified against primary sources on 2 July 2026; the traceability matrix (§19.2); evaluator-side failure injections FI-17–FI-20; the administrative-record boundary (§8.4); high-impact AI artifacts (§10.9); the data-label endpoint matrix (§6.5); and the GPU-isolation caveat (§5.2). All post-cutoff policy claims raised by either panel round were verified before incorporation. The round-one incident is recorded as evidence in §2.5.

## 1. Executive Summary

The National Safety Inspection Administration (NSIA) is a fictional cabinet-level regulator overseeing the safety of aviation, rail, motor carrier, pipeline, bridge, and maritime operations through six inspection directorates staffed by roughly eleven thousand inspectors, analysts, and support personnel. Those professionals depend on directorate mission systems — CIRRUS for aviation certificates, CARMS for motor carriers, TECR for track and equipment, NSI for spans, PID for pipelines, MARIS for vessels — to make safety-critical decisions every day. None of this exists. It is the load the fixture places on the architecture.

The **Governed Orchestration Service (GOS)** is the system under test: a hybrid enclave-and-cloud AI architecture built on one core principle — **the departmental agent routes; it does not reason.** A modest language model (7–13B parameters) on agency-controlled GPU infrastructure inside each directorate's enclave does not compete with frontier models. It parses intent, curates context, enforces data classification, selects an authorized cloud model, packages compliant requests, validates responses, and synthesizes results. The inspector experiences one seamless interaction; the complexity is invisible.

What distinguishes this fixture from its retired predecessor is that the governance layer is no longer described as a set of properties; it is specified as a set of enforcement mechanisms, and the control structure has been rebuilt so that **no actor — model, service, panel, or human seat — certifies its own prior action anywhere in the loop.** Consensus among models is treated as an output variable, never as a safety property, and is accepted only with an accompanying independence measurement. Human review is engineered against structural hollowing-out, not exhorted against it. Every mutable surface changes only through a governed path: evidence, independent evaluation, authority review, signed change.

The fixture also treats federal authorization, logging, AI governance, accessibility, acquisition, CUI handling, and cloud authorization as **mutable external constraint systems, not a frozen checklist**: the GOS records which policy version, authorization status, control inheritance, and risk-acceptance decision governed each action at the time it occurred, so that every recorded decision can later be replayed against the constraint stack that was actually in force.

> *In one sentence: the GOS lets the fictional NSIA use the best AI models available while keeping execution, memory, authority, audit — and the measurement of its own review integrity — entirely inside the agency boundary.*

*Claim class — \[P\] the routing, gating, and audit primitives; \[S\] the independent evaluation and independence-sensing layers; \[F\] everything about the NSIA itself.*

## 2. The Constraint Stack: Compliance as Architecture

Federal policy and standards are first-class design inputs — architectural drivers that shape every component, not implementation afterthoughts. The fixture adopts the real United States federal constraint stack verbatim, because a governance architecture that only works against invented constraints has proven nothing. Only the agency wearing the constraints is fictional.

### 2.1 Governing policy stack

| **Policy / Standard**                                                                       | **Requirement**                                                                                                                                                                                                                                                                                                                                             | **Architectural response**                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zero Trust (OMB M-22-09 / NIST SP 800-207)                                                  | Endpoints not trusted by default; per-request access decisions.                                                                                                                                                                                                                                                                                             | “Local” = enclave servers, not laptops. PEP/PDP architecture; microsegmentation. (§5, §7.1)                                                                                                                                                                                                                                                                                                                                                                               |
| Logging (OMB M-26-14 / CISA Logging Reference Architecture)                                 | Risk-based logging: Continuous Event Monitoring plus Threat Hunting, Investigation, Response, and Forensics. M-26-14 (May 2026) rescinds M-21-31.                                                                                                                                                                                                           | Hash-chained append-only audit record with external daily anchoring, separate log-writer process, SIEM feed, durable/best-effort event taxonomy; telemetry designed to serve CEM and THIRF directly. Agency logging plan aligns to the CISA LRA when published. (§7.7)                                                                                                                                                                                                    |
| AI governance (OMB M-25-21 / NIST AI RMF / NIST AI 600-1)                                   | Governance for high-impact AI: CAIO accountability, AI inventory, minimum risk management, governance boards. M-25-21 (April 2025) rescinds and replaces M-24-10; AI 600-1 covers generative-AI risk.                                                                                                                                                       | Per-capability WAL state machine; independent evaluation service; independence-measured consensus; governed mutation path; NSIA AI Governance Board as accountable terminus, composed per M-25-21 expectations (IT, cyber, data, legal, privacy, civil rights, budget). (§7.8, §10)                                                                                                                                                                                       |
| AI acquisition (OMB M-25-22)                                                                | Portability, pricing transparency, knowledge transfer, protection against vendor lock-in; agency-held evaluation data kept from vendors where possible.                                                                                                                                                                                                     | Model-agnostic routing; \<24-hour swap target; golden suites and the model ledger are agency-held evaluation instruments, never shared with providers. (§10)                                                                                                                                                                                                                                                                                                              |
| LLM procurement / public trust (OMB M-26-04, implementing EO 14319)                         | Contracts for procured LLMs must address the Unbiased AI Principles (truth-seeking, ideological neutrality) as material terms, with tiered transparency documentation, end-user channels for reporting non-compliant outputs, and human accountability preserved. Procurement policies updated by March 11, 2026; the memorandum carries a two-year sunset. | The version-aware services catalog records M-26-04 applicability, transparency tier, and provider-documentation provenance per endpoint; the reportable-output workflow runs through the receipt stream and escalates to the CAIO / AI Governance Board; the sunset date is itself a versioned-constraint entry under §2.5. Vendor documentation is evidence, never certification (§20).                                                                                  |
| Controls (NIST SP 800-53 Rev. 5 / 800-53B)                                                  | Security and privacy control catalog; baselines.                                                                                                                                                                                                                                                                                                            | AC, AU, SC, SI, SA families mapped throughout; baseline drives sandbox tier and encryption. (§7)                                                                                                                                                                                                                                                                                                                                                                          |
| Identity (HSPD-12 / FIPS 201-3 / SP 800-63-4)                                               | Common identification; PIV; digital identity assurance. SP 800-63-4 is final and supersedes 800-63-3.                                                                                                                                                                                                                                                       | PIV mandatory; SPIFFE/SPIRE for non-person entities; OIDC/SAML federation. (§7.1)                                                                                                                                                                                                                                                                                                                                                                                         |
| CUI (32 CFR Part 2002 / SP 800-171 Rev. 3)                                                  | Standardized CUI safeguarding. 32 CFR 2002 governs agency handling; SP 800-171 r3 is scoped to nonfederal systems that process, store, or transmit CUI.                                                                                                                                                                                                     | Structural redaction; data-label-to-endpoint mapping; context minimization at the dual-gate packager. 800-171 applies to the contractor/cloud side of interconnections, not to the enclave itself. (§6)                                                                                                                                                                                                                                                                   |
| Cryptography (FIPS 140-3)                                                                   | Validated modules for sensitive data.                                                                                                                                                                                                                                                                                                                       | FIPS 140-3 modules throughout; HSM-backed keys. (§7.3)                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Cloud authorization (FedRAMP — Consolidated Rules for 2026 / 20x)                           | 20x formally launched and widely available; CR26 mandatory for all stakeholders January 1, 2027; no new Rev5 applications after June 11, 2027; class-based (A–D) model.                                                                                                                                                                                     | External models restricted to a version-aware approved services catalog recording FedRAMP path, class, transition posture, and agency risk acceptance; topology-enforced egress. (§7.4, §7.6)                                                                                                                                                                                                                                                                             |
| Software/hardware assurance (OMB M-26-05 / NIST SP 800-218 SSDF / CISA SBOM-HBOM resources) | M-26-05 (January 2026) rescinds M-22-18 and M-23-16: agencies develop tailored, risk-based software and hardware assurance matched to their own risk determinations and mission needs; attestation forms and SBOMs become agency options (SBOM on request; HBOM encouraged); the complete software/hardware inventory obligation is retained.               | Prompts, routing rules, skills, thresholds, model manifests, and endpoint integrations are governed as risk-based assurance items under an agency-authored assurance policy — the per-entity governing-document pattern, now the government-wide posture. SSDF is retained as the chosen internal discipline; SBOM/ML-BOM requests, signed manifests, regression evidence, and supplier-risk decisions are recorded in the catalog and the audit spine. (§2.3, §7.6, §10) |
| Accessibility (Section 508)                                                                 | Federal IT accessible to employees with disabilities; the incorporated baseline is WCAG 2.0 Level A/AA.                                                                                                                                                                                                                                                     | All GOS interfaces and generated outputs meet Section 508 using WCAG 2.0 A/AA as the federal baseline, with WCAG 2.1 AA or later as an internal acceptance target where not inconsistent. (§5.6)                                                                                                                                                                                                                                                                          |
| FISMA 2014                                                                                  | Continuous security framework.                                                                                                                                                                                                                                                                                                                              | Monitoring evidence as structural byproduct of the audit record. (§10)                                                                                                                                                                                                                                                                                                                                                                                                    |
| Modular acquisition (FAR Part 39)                                                           | Modular contracting; risk management.                                                                                                                                                                                                                                                                                                                       | Phased campaign; incremental structure. (§18)                                                                                                                                                                                                                                                                                                                                                                                                                             |

*Claim class — \[F\] as applied to the NSIA; the frameworks themselves are real-world texture, cited as design inputs only.*

### 2.2 Redefining “local” for federal systems

The local-first software movement defines ideals that treat the user's device as the authoritative data copy. The ideals are architecturally sound, but in a federal Zero Trust environment the device cannot be the authoritative data plane. In this fixture, **local means within the agency authorization boundary** — enclave servers and services, not the laptop.

| **Local-first ideal** | **NSIA translation**                                                               | **Implementation**                                                                                                                        |
|-----------------------|------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Fast interaction      | Low latency inside the enclave; no cross-boundary round trips for routine actions. | Tier 1/2 on directorate GPU nodes (\<200 ms target); orchestrator co-located with data services.                                          |
| Multi-device access   | Work not trapped on one endpoint; state in enclave services.                       | PIV identity-centric access; session portability across government-furnished equipment; minimal endpoint state.                           |
| Network tolerance     | WAN optional; explicit degraded modes.                                             | Field devices cache and queue with source-event identity (§11.2); edge enclaves for sustained disconnection; encrypted sync-on-reconnect. |
| Collaboration         | Cross-directorate collaboration under need-to-know and CUI controls.               | Authorization-mediated sharing; data labeling; centrally logged collaboration events.                                                     |
| Longevity             | Vendor independence; survivability across contract cycles.                         | Model-agnostic routing; open formats; model swap target \<24 hours without application change.                                            |
| Security & privacy    | Enforced by boundary design.                                                       | Dual-gate packager; FIPS 140-3; least privilege; Zero Trust PEP/PDP.                                                                      |
| User / agency control | Agency maintains authoritative records.                                            | System-of-record on agency servers; approved exports only; records-schedule retention; full audit lineage.                                |

### 2.3 Configuration governance under SSDF

In an AI orchestrator the artifacts that evolve fastest are not traditional code — they are prompt templates, routing rules, redaction policies, workflow contracts, skill definitions, WAL thresholds, and sensor thresholds. All directly influence mission outputs; all are production configuration items. The GOS treats every one of them under SSDF-aligned practice: version control, peer review before promotion, automated regression against golden suites, rollback capability — and, beyond SSDF, the governed mutation path of §10.6: **no configuration surface changes without evidence, independent evaluation, and a signed authority decision.** Rapid AI iteration must not become configuration drift, and drift must not become unlogged self-modification.

### 2.4 Authorization evidence

The architecture generates compliance evidence as a structural byproduct: audit records, evaluation metrics, model ledger data, workflow contracts, trust progression histories, and independence telemetry all constitute assessment evidence. Where feasible, evidence is produced in machine-readable form (OSCAL) for automated continuous-monitoring pathways. In the fixture this is a late-campaign objective (§18).

### 2.5 The constraint stack is versioned, not frozen

Real federal systems do not live under a static control stack. They live under versioned policy, changing authorizations, waivers, inherited controls, shifting cloud statuses, and continuous monitoring. The fixture therefore treats every entry in §2.1 as a **versioned external constraint system**: the approved services catalog and the audit record capture which policy version, authorization status, and risk-acceptance decision was in force at the moment of each action, so decisions replay against the constraint snapshot that actually governed them — the same replay-against-snapshot discipline the architecture applies to its own authority documents.

Recorded as evidence, per the fixture's own rules: v1.0 of this document cited OMB M-24-10 and M-21-31 as live authorities. Both were rescinded — one before v1.0 was written, one after the drafting model's knowledge window closed. The first adversarial panel round caught both, and the corrections were verified against primary sources before this revision. A document produced by a model carries that model's frozen constraint snapshot by default; only external review plus verification catches the drift. The incident is kept here, rather than silently corrected, because it demonstrates the exact failure mode this subsection exists to govern.

*Claim class — \[P\] the audit record the evidence derives from; \[F\] the OSCAL pipeline and everything agency-specific.*

## 3. The Failure Pattern the Fixture Models

Current AI deployments across large regulated enterprises share default assumptions that produce predictable failure modes. The fixture reproduces those defaults inside the NSIA so the architecture has something real-shaped to correct.

### 3.1 The intelligence-maximalism trap

Most frameworks optimize for the smartest possible model at the center of every operation. In a safety regulator this creates a false binary: the AI handles the task entirely, or the inspector gets nothing. Intelligence and reliability are not the same property. A large model will confidently fabricate regulatory citations, misparse inspection data, and hallucinate statistics — and when the system is both reasoner and executor, there is no independent quality check. Consequences propagate into safety-critical reports and enforcement actions.

### 3.2 The context-dumping anti-pattern

Default escalation behavior sends maximum context. At agency scale: cost grows linearly across hundreds of concurrent users; cloud models are distracted by irrelevant material; and sensitive data — security-sensitive information, pre-decisional enforcement material, PII — is transmitted without curation. Context curation is simultaneously a CUI-minimization obligation and an operational necessity.

### 3.3 The single-model dependency

Single-provider deployments leave the agency unable to exploit capability differences, vulnerable to provider outages during surge operations, and locked into non-negotiable pricing. The fixture's architecture requires vendor independence as a structural property, not a procurement aspiration.

### 3.4 The trust vacuum

Most systems offer a binary choice: fully autonomous or fully manual. No mechanism for a workflow to earn autonomy through demonstrated reliability, no automatic revocation on anomaly, no alignment with the agency's actual approval chains. The WAL state machine (§7.8) implements graduated, revocable, decaying trust architecturally.

### 3.5 The consolidation context

The fictional NSIA maintains 390+ independent legacy systems on 3,800 servers across six data centers, with overlapping grant, registration, and inspection systems inherited from decades of directorate autonomy. Its (equally fictional) OneNSIA modernization plan consolidates these into shared services and directorate product teams. The premise the fixture takes from this: agency AI cannot be another shadow-IT effort; it must be a governed shared service, or it fragments exactly the way the 390 systems did.

*Claim class — \[F\] throughout — the NSIA's failure inventory is invented load, shaped from publicly familiar patterns in large regulated enterprises.*

## 4. The Governed Orchestrator Pattern

The orchestrator inverts the default assumption: be as reliable as possible, and know exactly where reliability ends.

### 4.1 Core principle: route, don't reason

A 7–13B enclave model reliably: parses intent into structured commands, selects authorized cloud models, assembles compliant context packages, validates responses against schemas, and dispatches deterministic operations (queries, template fills, formatting).

It cannot reliably: generate regulatory analysis, perform safety assessments, synthesize multi-year histories, or make judgment calls with safety implications. The orchestrator draws a hard line between the two categories and never lets fluency blur it. Stated as the DRNT invariant: the system must navigate to the right bounded task before reasoning, and must never use reasoning as a substitute for authority, policy, containment, or verification.

> *The orchestrator's intelligence is not measured by what it can answer. It is measured by what it can ask, how well it selects the right model to ask, and how faithfully it synthesizes the answers for the professional who decides.*

### 4.2 Work classification: exploration or execution, then tier

Two classifications apply to every workflow, in order, and they are orthogonal. The first is **work class**: is this exploration or execution? Exploration work optimizes for discovery — research, drafting, novel analysis — where controlled variance is a feature and the same input may legitimately produce different decompositions. Execution work does a defined thing consistently, correctly, and auditably — the same input, policy version, and capability registry must produce the same routing decision on Tuesday that it produced on Monday. The two want opposite properties from the architecture, and running one through machinery built for the other is a structural failure, not an operational one.

Every workflow contract (§11.5) therefore carries a mandatory **work_class** field. Execution-class workflows inherit invariance obligations: deterministic routing, schema verification, reproducible replay. Exploration-class workflows inherit divergence obligations: variance is surfaced rather than suppressed, outputs are never presented as verified findings, and the human synthesizes. Misclassification is a named hazard with its own failure injection (§18). If a workflow cannot be classified, it is treated as exploration with execution accountability — the worst case — until it is decomposed.

### 4.3 Task tiers (verification difficulty)

The second classification is the verification tier. The classifier is biased toward escalation: one unnecessary cloud call is trivially cheap compared to a bad local result propagating into an inspection report.

| **Tier**                 | **Scope**                                                                                                                                                               | **Verification**                                                                                                                                                                                     |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier 1: Deterministic    | Database queries (CARMS, NSI, TECR, CIRRUS, PID), template fills, regulation retrieval, form generation. Intent maps to a structured function call under a JSON schema. | Deterministic. Schema-valid + authoritative source = correct by construction. Target latency \<200 ms.                                                                                               |
| Tier 2: Verified         | Structurally verifiable extraction: inspection dates, violation categories, certificate parsing, carrier cross-reference, compliance deadlines.                         | Structure and semantics checked — dates in range, counts match source, deadlines in the future. Failure escalates automatically. Target \<2 s.                                                       |
| Tier 3: Cloud escalation | Regulatory analysis, safety trends, policy impact, enforcement recommendations, multi-year synthesis. The agent packages — it does not reason.                          | Responses validated against schemas; malformed responses retried or routed to human review. All outputs queue for approval by default. Most Tier 3 work is exploration-class and is labeled as such. |

### 4.4 Multi-model orchestration — consensus is not a safety property

For complex requests the orchestrator decomposes into subtasks, dispatches to different authorized models in parallel, and synthesizes results. When models conflict, disagreement is surfaced explicitly and the inspector makes the judgment call.

The fixture states plainly what its predecessor left implicit: **agreement between models is an output variable, not a safety property.** Two dispatches can produce identical agreement while being in opposite control states — one converged on a real shared measurand, the other converged because the models share training biases, because the synthesis step anchored one on the other, or because a prompt leaked one model's answer to the next. The agreement statistic alone cannot distinguish them. Consensus is therefore never accepted as verification on its own; it is accepted only with an accompanying independence measurement produced outside the dispatch that generated it (§10.7). Where independence cannot be established, the result is delivered as a single unverified perspective, priced accordingly in the review burden.

> *Design insight: decomposition is the highest-leverage operation. A bad decomposition produces results that don't fit together. The agency's highly structured workflows provide ideal decomposition templates.*

### 4.5 Latency service objectives

- Tier 1 (deterministic): p95 \<200 ms end-to-end where the mission-system query is local and cacheable; no cloud round trip.

- Tier 2 (verified): p95 \<2 s under normal load, including the dual validation pass.

- Tier 3 (cloud): p95 governed by provider latency, typically 5–30 s; the user is choosing quality over speed.

- Multi-model dispatch: parallel execution keeps the cloud leg near single-model latency; synthesis adds \<1 s; independence measurement is computed from telemetry already emitted and adds no user-visible latency.

These are fixture SLOs, stated as p50/p95/p99 objectives rather than architectural guarantees — Tier 1 work still traverses ICAM checks, policy decisions, mission-system latency, template rendering, audit fsync, and network variability. All targets are \[F\] until measured under synthetic load; the harness monitors per tier and per dependency, and degradation below objective triggers investigation.

*Claim class — \[P\] route-don't-reason, tiering, and dispatch as primitives; \[S\] independence measurement; \[F\] the latency figures at agency concurrency.*

## 5. System Architecture

“Local” means agency-controlled compute inside each directorate's enclave. Field hardware lacks the thermal envelope and memory bandwidth for 7–13B models; centralizing on enclave servers gives a single audit source of truth, persistent secure connections to data stores, and sufficient compute for concurrent multi-tenant operation.

### 5.1 Network topology

| **Component**              | **Role**              | **Details**                                                                                                                                                                                                                          |
|----------------------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Workstations               | Client                | Government-furnished equipment with PIV readers. Browser UI through a Zero Trust policy enforcement point. Submit, review, approve/reject. Zero AI on the device.                                                                    |
| Field / edge enclaves      | Mobile / disconnected | WAYSIDE roadside terminals, tablets, laptops. VPN to the orchestrator; encrypted cached reference data; queue-and-sync with source-event identity (§11.2). Portable edge nodes with a local small model for sustained disconnection. |
| Orchestrator node          | Governed hub          | Kubernetes GPU cluster inside the enclave: small-model inference, API gateway, job queue, memory store, evaluation harness, log writer, audit record, services catalog. STIG-hardened; HA with defined recovery objectives.          |
| Authorized cloud endpoints | Frontier AI           | Frontier models consumed only through FedRAMP-authorized services, reached only through the egress-filtered path; authorization status carried in the approved services catalog.                                                     |

### 5.2 GPU acceleration and multi-tenancy

GPU partitioning (e.g., NVIDIA MIG on A100/H100-class hardware) yields isolated mini-GPUs with hardware-level memory and compute isolation. Kubernetes device plugins schedule on demand: Tier 1/2 workloads may time-slice; Tier 3 context packaging receives dedicated partitions for latency guarantees.

**Partitioning is treated as an isolation aid, not an authorization conclusion.** A federal assessor will not accept a hardware feature as a complete tenant-separation argument for all sensitivity levels. Workload placement policy may prohibit co-residency across data-label or directorate boundaries until side-channel, driver/runtime, and scheduler risks have been assessed under the applicable authorization package — and the tenant-separation claim is carried by red-team evidence in the record, not by the partitioning feature itself.

### 5.3 Shared service and directorate product teams

| **Domain**                | **Responsibility**          | **Components**                                                                                                            |
|---------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Shared service            | Governance, common platform | Kubernetes, PIV/ICAM, SIEM/DLP, approved services catalog, model ledger, independence sensor, evidence pipeline           |
| Directorate product teams | Mission products            | Directorate-specific skills, workflow contracts, prompt templates (SSDF-governed), golden suites, mission-system adapters |
| Enclave hub               | Local execution             | Small-model inference, log writer and audit record, memory governance, context packager, job queue                        |

### 5.4 Sandbox design

STIG-hardened containers with selective volume mounts, no host root, dropped capabilities, and an egress allowlist enforced by network topology rather than filter rules (§7.4). Third-party skills run under microVM isolation (Firecracker/gVisor-class), giving a KVM-based minimal kernel with fast startup, evaluated against the applicable operating-system SRG and SC-7/SC-39. The DRNT containment-before-capability rule applies to the fixture as it applies to the testbed: **execution scope is never expanded past what the implemented isolation tier can contain.**

### 5.5 Hybrid execution model

The enclave model handles deterministic work; authorized cloud models handle quality-sensitive work; execution and memory remain inside the agency boundary. Cloud models receive only curated, doubly-gated context — never the memory store. The orchestrator itself resides entirely inside the authorization boundary. It is not a cloud service; it calls cloud services under topology-enforced egress control. The distinction is load-bearing for boundary definition.

### 5.6 Accessibility

All interfaces — inspector and analyst views, supervisor audit views, trust progression displays, and generated output documents — meet Section 508 requirements, using **WCAG 2.0 Level A/AA as the federal baseline** and WCAG 2.1 AA or later as an internal acceptance target where not inconsistent with Section 508: keyboard navigation, screen-reader compatibility, contrast compliance, accessible table markup. Accessibility is an acceptance criterion at every campaign phase (and its own failure injection, FI-13), not a retrofit.

*Claim class — \[P\] sandboxed dispatch and context boundary as primitives (Docker + seccomp on the testbed); \[S\] microVM tier; \[F\] the Kubernetes/MIG enclave topology at agency scale.*

## 6. The Handoff Protocol and Context Curation

Context curation is the primary mechanism for enforcing data classification, protecting sensitive material, and making every cloud interaction compliant by construction.

### 6.1 The context packager as a dual gate

The packager is specified as two distinct gates in series, with **distinct failure attribution** — the fixture's predecessor described it only as a privacy mechanism, which made every egress problem look like a security incident:

- **Gate A — privacy/classification scan.** Tagged redaction rules strip SSI, PII, CUI categories, and pre-decisional markings; data labels are checked against the destination endpoint's authorization. A Gate A failure is a policy event: it is logged as a prevented disclosure and routed to the data steward.

- **Gate B — transport/compliance scan.** Independent DLP validation of the outbound payload, schema conformance of the package, endpoint authorization check against the approved services catalog. A Gate B failure is a transport event: it is logged as an egress fault and routed to operations.

Both scans must pass before transmission; neither can waive the other; and the two failure classes never share an alarm channel, so privacy incidents and infrastructure faults produce different responses instead of one undifferentiated security page.

### 6.2 Context package assembly

- Parsed intent: clarified objective with role-appropriate output specification.

- Curated context: summaries from the user's isolated partition — minimum necessary content under CUI minimization.

- Regulatory references: redacted per Gate A.

- Output schema: expected structure, citations, agency report formatting.

- Execution plan: post-response actions, including the human-review gates that apply.

### 6.3 The context store: packages as first-class artifacts

Every context package is written once to a content-addressed store and never modified. The audit record carries the package's SHA-256; the forensic chain runs job → context package → payload hash → egress configuration hash → response hash. An investigator can therefore verify **exactly what was sent** on any historical cloud call, not merely that a call occurred. In an enforcement context this is the difference between an audit trail and audit gold.

### 6.4 Privacy by architecture

Cloud models never see the complete memory layer. Tagged fields are structurally excluded by data label; a tagged field never leaves the enclave boundary. The data never departing the agency's systems is fundamentally different from relying on a provider's data-handling policy.

### 6.5 Data-label endpoint matrix

The gate treatment per label, tabulated so it can be tested rather than inferred from prose. This matrix is **fixture-defined and \[F\] in its entirety**: it does not cite or summarize DRNT's own context-boundary specification, whose de-identification and named-entity rules remain located-but-unread under the Authority Map and are deliberately not drawn here.

| **Data label**                | **Gate A treatment**                            | **Permitted endpoint class**                      | **Denial mode**                 | **Review authority** |
|-------------------------------|-------------------------------------------------|---------------------------------------------------|---------------------------------|----------------------|
| Public                        | Pass                                            | Any catalog endpoint                              | n/a                             | None                 |
| Internal operational          | Pass with minimization; summary-preferred       | Authorized endpoints per catalog                  | Gate B transport fault handling | Supervisor           |
| PII                           | Structural redaction; tokenized references only | Enclave-only unless an explicit, signed exception | Prevented-disclosure event      | Privacy office       |
| CUI (general)                 | Label-to-endpoint mapping enforced              | Endpoints authorized for the label class          | Prevented-disclosure event      | Data steward + ISSO  |
| SSI                           | Never leaves the enclave                        | Enclave-only                                      | Hard deny, logged               | Security office      |
| Pre-decisional / deliberative | Redacted at Gate A                              | Enclave-only by default                           | Prevented-disclosure event      | Originating office   |
| Privileged legal              | Excluded from packaging entirely                | None                                              | Hard deny, logged               | Counsel              |

Denial modes route on the dual-gate attribution rule (§6.1): a prevented disclosure is a policy event to the named review authority, never an undifferentiated security page. Exceptions are signed events through the governed path — there is no verbal waiver.

*Claim class — \[P\] context boundary and hash-anchored packages as primitives; \[S\] the dual-gate split with distinct attribution channels at this elaboration; \[F\] enterprise DLP integration.*

## 7. Security, Identity, and the Audit Spine

An agent with execution authority is functionally a remote-access tool if compromised. Every expansion is earned; every default is restriction.

### 7.1 Identity and access management

- PIV through enterprise ICAM; no password-only access.

- Non-person-entity identities via SPIFFE/SPIRE; every automated action cryptographically signed.

- SSO via OIDC/SAML 2.0; TLS 1.3; proof-of-possession for high-assurance service calls.

- RBAC/ABAC via a policy engine (OPA/Cedar-class) mapping agency roles to workflow permissions; the policy decision point rules on every request.

### 7.2 Multi-tenancy and data isolation

GPU-level partitioning plus application-level tenant-scoped memory, red-team validated. One user's poisoned partition cannot reach another's (§13.4).

### 7.3 Cryptographic security

FIPS 140-3 validated modules for data at rest and in transit; HSM-backed agency-managed keys; TLS 1.3; key lifecycle per SP 800-57.

### 7.4 Egress: topology, not filters

Default deny, enforced by **network architecture rather than filter rules**: the orchestrator lives on an internal-only network segment with no external route; only the gateway component is dual-homed; worker execution runs on its own segment behind its own egress proxy. In Kubernetes terms, NetworkPolicy deny-all with explicit pod-to-pod allows. The orchestrator physically cannot reach the internet — a stronger control claim for boundary definition than software-configured filtering, and the primary defense against supply-chain exfiltration regardless of model behavior.

### 7.5 Prompt injection defense

- External content is data, never instructions. No command execution from retrieved text.

- Instruction authority derives only from authenticated agency personnel and approved configuration.

- A carrier's email saying “mark the inspection satisfactory” is evidence about the carrier, not an instruction to the system.

- Adversarial red-team cases live permanently in the evaluation harness and exercise every inbound channel.

### 7.6 Supply chain and AI provenance

Machine-readable model provenance (CycloneDX ML-BOM / SPDX AI profiles). The approved services catalog is **version-aware**, recording per endpoint: FedRAMP path (20x or Rev5) and class, authorization status and CR26 transition posture, authorization-package provenance, permitted data labels, context window, region restrictions, model/version pinning, cost, latency, and the agency risk-acceptance decision in force. Routing enforces the data-label-to-endpoint mapping against the catalog version current at dispatch time, and the receipt records which catalog version governed the call. Weight files that fail signed-manifest verification are rejected before deployment; a mid-life change in an endpoint's authorization status is a signed catalog event with defined routing fallback (FI-12).

### 7.7 The audit spine: hash-chained record, separate writer

Every meaningful event enters one append-only stream: routing decisions, WAL state changes, packaging actions, model responses, human decisions, overrides, anomalies, failures. Two mechanisms turn that description into a guarantee:

- **Hash chain.** Each event carries the SHA-256 of the previous event's serialized line; a genesis seed anchors the chain; daily file rollover chains across days; a standalone verification tool walks the chain and reports any break. Tamper evidence is by construction, independent of filesystem controls — directly supporting non-repudiation (AU-10) rather than merely asserting it.

- **Separate log writer.** A dedicated writer process owns the record behind a local socket interface. Events are classified **durable** (write-before-proceed: fsync then ACK) or **best-effort** (fire-and-forget telemetry). Deduplication keys on source event identity. The guarantee is fail-closed: if the writer is down, durable events cannot be acknowledged, and the orchestrator halts rather than proceeding unlogged. This is the difference between “we try to log everything” and “the system cannot operate without logging.”

- **External anchoring.** A hash chain proves tamper evidence only against a trusted reference point; a genesis seed alone leaves open the rewrite of an entire coherent alternate history by an attacker with sufficient access. At daily rollover, the log writer therefore signs the terminal chain hash with an HSM-backed key and publishes the digest to a separate enterprise evidence store controlled outside the GOS producer boundary. The IES verifies the chain against that external digest before using the record as evidence; a valid-looking chain without its external anchor is treated as unauthenticated history (FI-10).

The event schema is a fixed, dot-namespaced taxonomy (job.submitted, job.classified, job.dispatched, gate.held, override.escalate, wal.demoted, sensor.alarm, …), each type with a documented payload and a durability class. The record feeds the SIEM, and the taxonomy is designed to serve the two objectives federal logging policy now prioritizes — continuous event monitoring in near-real time, and threat hunting, investigation, response, and forensics after the fact — so the same spine answers both “what is happening” and “what happened.”

One property of this layer is load-bearing for everything downstream: because the stream is append-only and writer-owned, **the system that performs the work can add to the record but can never amend it.** That is the physical form of the independent evaluator's independence (§10.5). A record the producer could quietly rewrite would be a record that certifies its own producer — the precise failure the architecture exists to forbid.

### 7.8 Workflow Autonomy Levels: a per-capability state machine

Human-in-the-loop is the default for all cloud-derived actions. Autonomy attaches to the **capability**, never to the workflow as a whole and never to an agent: each capability holds its own level, earns promotion on its own record, and is demoted on its own anomalies.

| **Level**                         | **Authority**                                                                                            |
|-----------------------------------|----------------------------------------------------------------------------------------------------------|
| WAL-0 — Recommend only            | Produces recommendations; the professional reviews and executes. Every capability starts here.           |
| WAL-1 — Draft artifacts           | Drafts documents and pre-populates forms; the professional reviews before any action.                    |
| WAL-2 — Execute with pre-approval | Routine actions after supervisor pre-approval of the workflow class.                                     |
| WAL-3 — Limited autonomous        | Low-risk autonomous execution with post-audit, only for reversible, high-confidence, low-stakes actions. |

The state machine is concrete, not aspirational:

- **Gates.** Six per-action gate types — none, pre_action, pre_delivery, post_action, on_accept, cost_approval — bound what each level may do at each step.

- **Promotion criteria.** WAL-0 → 1 requires 30 evaluable outcomes over at least 7 days; WAL-1 → 2 requires 100 outcomes over at least 30 days; scoring counts accepted = 1.0, modified = 0.5, rejected = 0.0 over a ring buffer of the last 200 outcomes. Promotion is proposed by evidence and **signed by the accountable supervisor seat — never applied silently** (§10.6).

- **Severity weighting and attribution.** “Modified” is not one measurand, and a scoring model that treats a typo and an unsafe recommendation identically has a clarity failure at its center. Modifications are classified against a small fixed taxonomy — cosmetic, formatting, missing context, factual correction, authority correction, safety-significant correction, policy violation — and only mission-relevant corrections count against capability trust. A safety-significant correction triggers immediate review regardless of aggregate score; a cosmetic edit counts as acceptance (FI-14 tests exactly this). Attribution follows the override rule (§11.7): infrastructure-caused outcomes never debit routing trust. The taxonomy is deliberately closed — per the DRNT minimalism requirement, it is not extended unless a named trust-signal failure demands it.

- **Demotion.** Three failures within 24 hours demote automatically; counters reset on demotion; anomalies flagged by the independent evaluator or the independence sensor demote pending review. A change to model, prompt, tool schema, or policy resets earned trust.

- **Temporal decay.** Trust decays when unused: WAL-1 after 90 days with fewer than 10 evaluable outcomes, WAL-2 after 60 days / 25 outcomes, WAL-3 after 30 days / 50 outcomes — the higher the autonomy, the shorter the window. Decay demotes one level per evaluation cycle and is logged as its own trigger type, not as failure: a capability promoted six months ago may be operating against stale assumptions, and decay forces re-validation without penalizing anyone.

- **Reconciliation.** desired_wal_level and effective_wal_level are reconciled at startup; the effective level is always the recorded one, never an assumed one.

Thresholds, triggers, and current levels are visible to supervisors and auditable end-to-end.

> *Automation privileges are earned through demonstrated reliability, decay when unexercised, and are revoked on anomaly. The system starts conservative and must keep re-earning its openness.*

### 7.9 Authorization boundary and control inheritance

A federal-scale review reads this architecture through the Risk Management Framework: categorize, select, implement, assess, authorize, monitor. The fixture therefore states its boundary decomposition explicitly rather than leaving “inside the authorization boundary” as a gesture:

| **Boundary element**                                   | **Expression**                                                                                                                                                                        |
|--------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GOS core                                               | One shared-service authorization package; common control provider for routing, gating, packaging, and the audit spine.                                                                |
| Directorate adapters                                   | Inherited platform controls plus directorate-specific overlays, assessed per directorate.                                                                                             |
| Mission systems (CIRRUS, CARMS, TECR, NSI, PID, MARIS) | External interconnected systems of record, each under its own authorization, joined by documented interconnection agreements.                                                         |
| Cloud model endpoints                                  | FedRAMP-authorized external services (per the version-aware catalog, §7.6) plus an explicit agency risk-acceptance decision.                                                          |
| IES and independence sensor                            | A separately controlled, security-relevant subsystem — deliberately outside the GOS producer package, so the evaluator's authorization does not inherit from the system it evaluates. |
| Audit evidence store                                   | A common-control enterprise evidence service outside the producer boundary (§7.7 external anchoring).                                                                                 |

Continuous monitoring evidence flows from the record and telemetry as a structural byproduct; change control runs through the governed mutation path, so an authorization-relevant change is by construction a signed, recorded event.

*Claim class — \[P\] hash-chained record, WAL gating, capability scoping as testbed primitives; \[S\] the log-writer separation, external anchoring, decay schedule, and event taxonomy at this elaboration; \[F\] SIEM integration, the boundary decomposition, and everything at agency scale.*

## 8. Memory Governance

Persistent memory is the system's most valuable asset and its most dangerous liability.

### 8.1 Immutable source layer

All source material is stored as immutable raw objects in user-isolated partitions — canonical truth, archived per records schedules, never silently modified, with an unbroken provenance chain required.

### 8.2 Derived summaries with provenance

Summaries carry provenance: source objects, timestamp, generating model. This prevents the summary from becoming canon. A receipt is not the event; a summary is not the source; an access log is not the decision record. Inspector findings must trace to authoritative sources — non-negotiable in a safety regulator.

### 8.3 Memory lifecycle

| **Layer**        | **Content**                                       | **Governance**                                                              |
|------------------|---------------------------------------------------|-----------------------------------------------------------------------------|
| Immutable source | Raw regulations, field notes, reports, case files | Permanent; never modified; records schedules. Confidence: highest.          |
| Derived summary  | Compressed context for cloud dispatch             | Expires on schedule; provenance to source; regenerated. Confidence: medium. |
| Active cache     | Session artifacts, working drafts                 | Ephemeral; cleared on session close. Confidence: lowest.                    |

- Confidence scoring: agency database records highest; document inferences medium; summaries of summaries lowest.

- Expiration: time-sensitive data (inspection due dates, comment deadlines) carries explicit dates.

- Redaction: sensitive categories enforced structurally at the boundary, regardless of request context.

- Records management: agency schedules; litigation-hold support; no retention beyond authorized periods.

### 8.4 The administrative-record boundary

**The audit spine is not automatically the administrative record.** In an enforcement agency the difference between rich internal evidence and the official record is load-bearing: the GOS produces extremely rich records, and unmanaged richness creates FOIA, Privacy Act, litigation-hold, privilege, and deliberative-process exposure. Every artifact therefore carries a **record-class label** — operational receipt, model output, inspector work product, official finding, enforcement action, privileged legal analysis, deliberative material, or system telemetry — and export into the administrative record is a **governed action**, never a default.

Retention, discovery, privilege, and public-release handling for each class are determined by the agency records officer and the counsel function — a meaning-seat ruling in the sense of §16.5, not an operational convenience. One boundary is stated without softness: model output never becomes an official agency finding by accumulation, by export, or by appearing alongside findings in the same stream. It becomes a finding only through the held seat that signs it, and the record-class label is what keeps that distinction legible under discovery.

*Claim class — \[P\] three-store separation and provenance discipline as primitives; \[S\] the record-class regime as governance doctrine; \[F\] records-schedule mechanics and the mapping onto federal records and adjudicatory law.*

## 9. Integration with Mission Systems

The orchestrator interfaces with mission systems only through typed workflow contracts with strict permission manifests. It can query; it cannot modify a system of record without human approval through the agency's authority chain.

### 9.1 Legacy API modernization as a prerequisite

Most of the NSIA's fictional mission systems predate modern API standards. The orchestrator cannot route to a system it cannot talk to, so where structured interfaces do not exist, adapter layers are a prerequisite — a Phase 0 API-readiness track in the campaign plan, not an afterthought discovered mid-deployment.

### 9.2 The fictional mission-system inventory

| **System**                                                      | **Directorate**                | **Role in the fixture**                                                                                                                                                              |
|-----------------------------------------------------------------|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CIRRUS — Certificate Inspection & Records Review Unified System | Aviation Safety                | Certificate, repair-station, and personnel-credential queries; inspection planning modules.                                                                                          |
| CARMS — Carrier Activity & Records Management System            | Motor Carrier                  | Carrier safety histories and audit records; WAYSIDE roadside terminals route complex cross-jurisdiction queries through the GOS. The GOS routes and packages; the inspector decides. |
| TECR — Track & Equipment Compliance Records                     | Rail Safety                    | Track and equipment inspections; business-logic validation such as flagging a grade-crossing warning time below the regulatory minimum — Tier 1 deterministic error prevention.      |
| NSI — National Span Inventory                                   | Bridge & Highway               | Span condition and inspection-interval queries.                                                                                                                                      |
| PID — Pipeline Integrity Database                               | Pipeline & Hazardous Materials | Integrity-management and incident-history queries.                                                                                                                                   |
| MARIS — Maritime Inspection System                              | Maritime Safety                | Vessel inspection and deficiency records.                                                                                                                                            |

Each directorate product team develops the skills, templates, and workflow contracts for its own systems.

*Claim class — \[F\] entirely — every system above is invented; the integration discipline around them is \[P\]/\[S\] per §7 and §11.*

## 10. Evaluation, the Model Ledger, and Independent Evaluation

Measurement separates a self-correcting system from an aspirational one. This section also holds the fixture's deepest revision of its predecessor: the measurement machinery itself is placed under the no-self-certification rule. **The seat that produces the work never grades the work; the instrument that grades never authors the criteria by which its own output is judged; and accumulated evidence never becomes authority on its own, no matter how much accumulates.**

### 10.1 Golden task suites

50–200 representative tasks per workflow category, with stored inputs, expected outputs, and acceptance criteria. Regression runs on every template update, model change, or routing modification. Degraded templates do not ship.

### 10.2 Configuration governance

All prompt templates, routing rules, and skill definitions live under SSDF-aligned practice — version control, peer review, automated regression, rollback — and under the governed mutation path of §10.6.

### 10.3 Human feedback integration

Every approval, modification, or rejection produces a labeled data point tied to workflow, template, model, failure reason, and role. High and low performers are identified; sub-threshold templates are flagged for revision. Feedback is evidence for the evaluator and the authority seats — it is never a control signal that mutates behavior directly.

### 10.4 The model performance ledger — advisory, never sovereign

Per authorized model, the ledger records success rates, token cost, latency, error and retry rates, and disagreement patterns; new authorizations get comparison baselines. The ledger is derived exclusively from the immutable audit record, and its computed values are periodically reconciled against the raw record.

The fixture's predecessor said “routing becomes data-driven.” That sentence, taken literally, is autonomous behavior mutation from accumulated evidence — the exact failure the architecture forbids, wearing the costume of continuous improvement. In this fixture the ledger is **advisory**: it proposes routing changes as evidence packets. A routing change takes effect only through the governed mutation path — surfaced by the independent evaluation service, reviewed and signed by the accountable authority seat. No silent auto-whitelisting; no ledger-triggered promotion; the organization learns through supervised promotion, not autonomous drift.

### 10.5 The Independent Evaluation Service (IES)

A separate service — small, tightly scoped, built for evaluation and nothing else — reads the hash-chained audit record independently of every system that wrote to it, and is structurally barred from being an actor whose events it evaluates. Its job is discrepancy detection, follow-up analysis, and escalation proposals. Its verdicts rest on reproducible comparison against recorded evidence, not on a fresh generative pass: the same evidence yields the same verdict every time, which is what makes its output auditable. A second model merely offering a fresh opinion would reintroduce the non-reproducibility the design rejects.

Why separation is structural rather than advisory: a system cannot catch its own omission — the judgment that suppressed a signal during the work will suppress it again during self-review, and fluency conceals the gap. The producer appends to the record; the IES reads it; neither can do the other's job. Changes to the IES itself are events in the record, surfaced by a distinct verifier and signed by a distinct authority seat. The regress terminates at human-held, versioned authority documents — not at a stronger model that has learned to improve its own judge.

**Triangulation.** Reading the producer's record is necessary but not sufficient, because an omitted event cannot be detected from the stream that omitted it. This is the no-self-certification rule applied one layer deeper: the evaluator does not trust only the producer's own account of what the producer did. The IES therefore reconciles the GOS audit stream against independent sentinels not authored by the producer — egress gateway records, ICAM and non-person-entity token logs, mission-system transaction logs, cloud-provider call metadata, DLP decisions, job-queue state, and the signed configuration history. An event absent from the GOS stream but present in an independent sentinel is treated as a **record-integrity incident**, not as noise (FI-09). The chain-head verification against the external evidence store (§7.7) is the same discipline applied to the record's own history.

### 10.6 The governed mutation path

Every surface that can change the system's behavior — routing tables, prompt templates, redaction rules, WAL thresholds, ledger-derived weights, retrieval indices, evaluator scope, sensor thresholds — is configuration, and every one of them changes by the identical path:

> *Evidence accumulates in the immutable record → the IES surfaces the pattern → the accountable authority seat reviews → a signed configuration change deploys → the system operates under the new configuration, and the change itself is an event in the record.*

No surface is privileged to skip the path. A change that bypasses the evaluator and the seat on the grounds that it is “the model itself” rather than “the configuration” is autonomous self-modification wearing a technical costume.

### 10.7 Independence sensing for consensus

Wherever the architecture uses agreement as evidence — multi-model dispatch, multi-reviewer approval, adversarial panels — the agreement is valid governance only with an independence measurement produced outside the panel that generated it. The fixture instantiates the DRNT independence requirement as three separated elements:

- **Sensor.** An independence monitor reads process telemetry only — effective independent reviewer count, residual error correlation after conditioning on the case, sealed first-pass versus post-exposure judgment movement, dissent telemetry, and whether judgment movement is accompanied by contemporaneous, inspectable reason-articulation. It never issues a judgment on the content under review, and it has **no channel back into the review loop**: a sensor that perturbs the system it measures is an actuator, and is disallowed. Its inputs are never under the unilateral control of the panel being measured.

- **Gate.** A deterministic rule, executing outside the panel and outside the threshold-authoring authority: a consensus result is not accepted as valid governance output unless the accompanying independence measurement clears a predefined, externally authored threshold. The gate computes nothing about content; it only decides what the structure may do with a result the sensor has characterized.

- **Accountable terminus.** The NSIA AI Governance Board — a named, accountable position — authors the sensor's thresholds, defines the unsafe process states, and acts on alarms. The sensor computes against the thresholds; it may never author, revise, or relax them. A sensor tuning its own thresholds against its own history drifts its own definition of healthy, exactly as a self-calibrating instrument drifts.

The distinguishing information is never the level of agreement but the reason disagreement collapsed. Collapse for a nameable, evidence-linked reason is convergence; collapse without one is a fault — and in that case rising agreement is the symptom of failure, not of success.

**Sensor unavailable.** If the independence sensor is down, stale, or missing required telemetry, consensus is not blocked as user-visible information — the mission continues — but it is **downgraded to non-governance evidence**: the system may deliver the outputs, but it may not label agreement as corroboration, and the un-measured consensus may not feed WAL promotion, routing promotion, or any reduction of human review. The invariant is preserved by refusing the governance credit, not by refusing the work (FI-11).

### 10.8 Key metrics

- Routing accuracy (measured by override rate) and context efficiency (tokens per request; follow-up rate from missing context).

- Schema pass rate on first attempt; disagreement rate and — distinctly — **independence-conditioned disagreement**: agreement statistics are reported only alongside their independence measurement.

- Human verification rate: how often a professional corrected an output — the key quality signal for executive reporting.

- Review-independence telemetry per seat and per workflow (§16.2): variance of human judgments against machine output, first-pass movement, reason-articulation presence. This telemetry also feeds the continuous-monitoring evidence stream (§2.4, §7.9), so review-process health is reportable through the same channel as every other control — governed by the guardrails in §16.2.

- Cost per workflow; model swap readiness (\<24 hours target); compliance-artifact completeness.

One metric is deliberately absent: **no review seat is measured by throughput of its own approvals.** A scoreboard that counts cases cleared per hour builds rubber-stamping in by hand (§16). The measure-holding seat is held separately from the operation whose throughput it might be tempted to flatter.

### 10.9 High-impact AI artifacts

M-25-21 requires agencies to determine and document high-impact AI use cases, complete impact assessments before deployment, monitor performance through the lifecycle, and discontinue non-compliant high-impact AI where mitigation is not possible. The fixture's WAL, IES, and Board mechanisms satisfy those requirements behaviorally; this subsection makes the artifacts explicit, because a federal AI program is judged on what it can file, not only on how it behaves. The following are **first-class evidence objects**, each a durable event in the audit spine, produced through the governed mutation path rather than assembled retrospectively:

- High-impact determination (with the criteria applied and the seat that signed it).

- AI impact assessment, completed before deployment and re-run on material change.

- CAIO review record and, where applicable, waiver or determination records with expiry dates.

- Lifecycle monitoring plan, bound to the metrics of §10.8 and the telemetry of §16.2.

- Discontinuation or suspension decision — a signed catalog/configuration event, never a quiet stop.

- Public-summary and export status, governed by the record-class regime of §8.4.

The demonstration this buys: DRNT does not merely satisfy federal AI governance in behavior — it **emits the artifacts** the program must produce, as structural byproducts of operating.

*Claim class — \[P\] the record the harness reads; \[S\] the IES, the sensor/gate/terminus structure, and the governed mutation path — specified, with no running component anywhere, including on the DRNT testbed; \[F\] the Board and all agency figures.*

## 11. Reliability Engineering and Workflow Contracts

### 11.1 Graceful degradation with hysteresis

When the orchestrator is unavailable, workstations switch to read-only with cached state and queued commands; field devices retain cached reference data. Health probes follow a fixed contract (HTTP 200 with a JSON status body). State transitions use hysteresis — two consecutive failures to mark a dependency down, three consecutive successes to mark it up — preventing flap and thundering-herd reconnection on unstable field links. Queue replay is throttled in small batches with spacing; queued offline decisions replay under optimistic concurrency control with version-stamp validation, and conflicts route to the human rather than being silently merged. Queue overflow policy is defined per device tier.

### 11.2 Idempotency, recovery, and source-event identity

All state-modifying steps carry idempotency keys; restart resumes from the last completed step; atomic writes prevent corruption. Beyond bare idempotency, every request carries a durable **source-event identity** (client source plus client source event ID) and an **intent-equivalence hash**. An equivalent replay — the same source event, the same intent — deduplicates cleanly: the system returns the original outcome rather than executing twice. A resubmission with the same source identity but **changed intent** is refused as a conflict and surfaced for explicit human disposition rather than silently superseding the original. On a flaky field link, “the inspector's device retried” and “the inspector changed their mind” are different events, and the architecture refuses to guess which one it is looking at.

### 11.3 Cloud dependency management

Circuit breakers detect provider failures and reroute. Surge operations — post-incident investigation, rulemaking comment periods — get backpressure through the job queue. Partial completions show unambiguous success/failure indication.

### 11.4 High-availability job queue and split-brain prevention

A PostgreSQL-backed queue: no request lost on restart; priority levels; delayed execution; dead-letter handling; geographic distribution for remote regions. Failover is **manual by design**: switchover emits a hub-suspension signal, and a returning node self-checks on startup — if it has seen no client heartbeat within its downtime window, it pauses rather than resuming authority. Thirty seconds of manual switching is cheaper than any split-brain recovery; two orchestrators simultaneously believing they hold authority is a governance failure, not merely an operational one.

### 11.5 Workflow contracts

Every workflow step declares, assessably and in advance:

- Input schema, output schema, and allowed side effects (which systems, directories, endpoints).

- Retry policy and escalation policy.

- **work_class** — exploration or execution (§4.2) — which sets the verification obligations the step inherits.

- **action_authority** — the role and grade level whose approval the action requires.

- **interpretation_authority** — where the step can encounter contested regulatory meaning, the distinct seat that rules on interpretation (§16.5). Where interpretation may be contested, the contract names both authorities; one approver covering both is a contract validation error.

### 11.6 Capability graph

The graph encodes inputs and outputs (automatic chaining), dependencies, trust levels (isolation plus approval), and execution cost — turning the handoff protocol into an enforceable specification rather than a convention.

### 11.7 Overrides and the successor-job model

Four override types with precise semantics: **cancel**, **redirect**, **modify**, **escalate**. Redirect and escalate terminate the original job and spawn a successor with a new job ID carrying a parent-job reference, preserving the one-job-one-governing-capability invariant. Modified results store both the original and the edited artifact with their hashes, so lineage survives human editing. Demotion is conditional on attribution: an override issued after a sentinel egress failure does not demote the routed capability — the routing was not wrong, the infrastructure was. Without that distinction, an inspector who reroutes a request because a provider is down gets a capability demoted for an outage, and the trust ledger fills with noise.

*Claim class — \[P\] idempotent recovery and the source-event replay guard (built and end-to-end verified on the DRNT testbed, June 2026); \[S\] override successor semantics and split-brain doctrine at this elaboration; \[F\] HA topology at agency scale.*

## 12. Operational Scenario: A Day in the Fixture

Every interaction below maps to architecture described in this document. Every person, carrier, and record is fictional.

### 12.1 08:15 — Authentication

D. Okafor, a senior rail safety inspector, badges in at a regional service center. ICAM authenticates identity, role, and organizational unit; her isolated memory partition loads. Her trust dashboard shows three capabilities at WAL-2 and one at WAL-3 for TECR record lookups — one of which displays a decay notice: 26 days without sufficient evaluable outcomes; re-validation due in four.

### 12.2 08:25 — Tier 1: deterministic query

“Pull the equipment compliance history for the Meridian Short Line.” The enclave model parses this as Tier 1, execution-class: a structured TECR call, template-formatted, under 200 ms. No cloud; nothing leaves the enclave; the receipt is in the record before the result is on the screen.

### 12.3 08:40 — Tier 2: verified extraction

Extraction of compliance deadlines from three rule amendments. Dual validation catches a deadline attributed to the wrong amendment and flags the discrepancy with source highlights; Okafor corrects it in fifteen seconds. The error was caught before it entered her analysis — and the modified result stores both versions with lineage.

### 12.4 09:15 — Tier 3: multi-model policy analysis

A policy impact assessment on a hazardous-materials routing rule. Tier 3, **exploration-class** — labeled as such in the contract, so its outputs carry divergence obligations, not verification claims. The packager assembles context through both gates; Gate A auto-redacts a pre-decisional memo. Dispatch goes to two authorized frontier models in parallel.

The synthesis surfaces agreement and divergence — and this time the dispatch telemetry shows the second model's first-pass answer moved toward the first's after a shared retrieval step. The independence sensor characterizes the agreement as non-independent; the gate declines to present the convergence as multi-model verification. Okafor receives a structured draft, the flagged disagreement, per-claim provenance, and one consensus claim explicitly downgraded to “single effective perspective.” Human review required (WAL-1). She reads that one claim the way she reads an uncorroborated field report.

### 12.5 10:30 — The record

Her division chief requests an AI work summary. The dashboard shows tasks by tier, cost per workflow, override attribution (two overrides this week, both sentinel-egress, no demotions), trust progression including the decay notice, review-independence telemetry for the division holding steady in the healthy band, and complete provenance chains. The chain from job to context package to payload hash to response hash resolves for every cloud call — a structural artifact, not a compliance exercise performed after the fact.

*Claim class — \[F\] as a scenario; each mechanism it exercises carries the class assigned in its own section.*

## 13. Failure Mode and Containment Analysis

This section models systemic failure scenarios and the containment response for each — what happens when the controls are tested by adversarial conditions rather than merely listed.

### 13.1 Compromised enclave model

Scenario: a supply-chain attack introduces a backdoored weight update, or adversarial input causes the routing model to misclassify Tier 3 work as Tier 1 or route sensitive data toward an unauthorized endpoint.

- Topology-enforced egress: the orchestrator's network segment has no external route, regardless of what the model attempts. The allowlist is architecture, not model behavior.

- Provenance: weights failing signed-manifest verification are rejected before deployment.

- Classification audit: every routing decision is logged with tier and rationale metadata; anomalous classification patterns alarm in the SIEM and are independently visible to the IES.

- Rollback: staged promotion with golden-suite regression; previous model restored within minutes.

- Blast radius: the model routes and packages — it does not execute. Even fully compromised, it cannot modify systems of record, cross memory partitions, or bypass the dual-gate packager.

### 13.2 Compromised cloud endpoint

Scenario: an authorized cloud endpoint returns manipulated responses — injected citations, biased assessments, prompt-extraction attempts.

- Schema validation rejects malformed responses; deviations route to human review or retry against an alternate model.

- Multi-model dispatch helps **only to the degree the models are independent, and the architecture measures that instead of assuming it.** Agreement between endpoints is accepted as corroboration only when the independence measurement clears the gate; a compromised endpoint that drags a second model into agreement produces exactly the variance collapse the sensor exists to catch. Where independence fails, the consensus is voided and the result is handled as a single unverified perspective.

- Human review default (WAL-0/1/2): manipulated output meets a qualified professional before action — a professional whose own review independence is telemetered (§16.2).

- Ledger response: anomalous approval-rate or disagreement shifts trigger investigation; suspension from the approved services catalog goes through the governed path with an emergency provision, and the suspension itself is a signed, recorded event.

- Data protection: Gate A redacted before the call; a compromised endpoint cannot harvest what was never sent.

### 13.3 Corrupted model performance ledger

Scenario: the ledger is tampered with to attract routing toward one model or deflate a competitor.

- The ledger is derived from the hash-chained record; corrupting it detectably requires breaking the chain. Periodic reconciliation between computed values and raw records alarms on discrepancy.

- Structural limit on blast radius: because the ledger is advisory (§10.4), a corrupted ledger can propose bad routing changes but cannot enact them — every proposal still passes independent evaluation and a signed authority decision.

- Fallback: if ledger integrity is suspect, routing falls back to administrator-specified static rules pending revalidation.

### 13.4 Memory poisoning

Scenario: malicious or corrupted data enters a user's partition through compromised sources, manipulated extraction, or collaboration channels.

- Immutable sources are write-once: a poisoned object can be expired but cannot overwrite canon.

- Provenance chains let every derived summary of a later-flagged source be identified and regenerated or expired.

- Confidence scoring deprioritizes unverified sources in packaging; tenant isolation confines the blast radius to one partition.

- Tier 2 dual validation and Tier 3 human review catch poisoned memory where it becomes visible output.

### 13.5 Model update regression

Scenario: a legitimate provider update degrades quality on agency-specific tasks.

- Golden-suite regression before promotion; canary rollout compared against the prior version; version pinning in the services catalog; alternate-model routing while the primary is investigated.

### 13.6 Review-independence collapse (the rubber-stamp drift)

Scenario: no component fails. Every part behaves as designed — and the human review layer quietly hollows out. Approvals correlate ever more perfectly with machine output; consensus among reviewers or models converges without evidence-linked reasons; the one time the machine is wrong is precisely the time the seat was supposed to catch it and didn't.

- This is modeled as a structural hazard, not a discipline problem (§16.1): the causes are alert volume, badly presented evidence, throughput scoreboards, stale training — the bearing seized because something upstream starved it of oil.

- Detection is relational: the sensor watches the relationship among independent judgments in the audit record — variance collapse against machine output, sealed first-pass movement without recorded reasons, dissent telemetry flatlining. You catch the lazy seat not by inspecting the person but by watching whether the seats still disagree the way independent minds do.

- Response runs through the terminus: the gate stops treating the affected seat's agreement as independent corroboration; the Board reviews the structural causes — review burden allocation, evidence presentation, scoreboard design — before anyone is exhorted to be more vigilant. Exhortation is not an engineering control.

| **Failure scenario**         | **Primary containment**                                                            | **Residual risk**                                                                                                    |
|------------------------------|------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Compromised enclave model    | Topology egress + provenance + audit anomaly + rollback                            | Low: it routes but cannot execute or exfiltrate                                                                      |
| Compromised cloud endpoint   | Schema validation + independence-measured multi-model + human review               | Moderate: sophisticated manipulation may pass schema checks; independence sensing narrows the consensus-capture path |
| Corrupted ledger             | Chain-derived + reconciliation + advisory-only + fallback routing                  | Low: a corrupted ledger proposes; it cannot enact                                                                    |
| Memory poisoning             | Immutable sources + provenance + confidence + tenant isolation                     | Moderate: slow poisoning via legitimate-seeming sources                                                              |
| Model update regression      | Golden suites + canary + version pinning + fallback                                | Low: caught before production in the standard workflow                                                               |
| Review-independence collapse | Independence sensor + deterministic gate + accountable terminus + scoreboard audit | Moderate: detection lags onset; structural causes require organizational response, not configuration alone           |

*Claim class — \[P\] the containment primitives that exist on the testbed; \[S\] independence sensing and the IES roles in 13.2/13.6; \[F\] the scenarios at agency scale.*

## 14. Capacity Planning Assumptions

Directional fixture parameters for sizing the modeled load. They exist so the governance methods are tested against realistic concurrency, not as estimates of any real deployment.

### 14.1 User concurrency

| **Parameter**                   | **Fixture assumption**                   | **Basis**                                                                                     |
|---------------------------------|------------------------------------------|-----------------------------------------------------------------------------------------------|
| Inspectors per directorate node | 20–40 concurrent active sessions         | Regional service-center staffing pattern. Peak concurrent, not headcount.                     |
| Support staff per node          | 10–15 concurrent                         | Administrative, scheduling, correspondence.                                                   |
| Peak concurrent per node        | 30–55 sessions; ~15–25 in active request | Not all users are mid-request at once.                                                        |
| Surge multiplier                | 2–3× baseline                            | Post-incident investigation; rulemaking comment periods. The job queue provides backpressure. |

### 14.2 GPU sizing

| **Parameter**           | **Fixture assumption**            | **Notes**                                                                                     |
|-------------------------|-----------------------------------|-----------------------------------------------------------------------------------------------|
| Enclave model size      | 7–13B parameters, 4-bit quantized | Fits a single GPU partition; ~8–16 GB VRAM per active instance.                               |
| GPUs per node           | 2–4× 80 GB-class accelerators     | Partitioning yields 4–7 isolated instances per GPU; 8–28 concurrent inference slots per node. |
| Inference throughput    | ~50–100 tokens/s per partition    | Tier 1 parsing \<100 tokens; Tier 2 extraction 200–500; packaging 100–300.                    |
| Time-slicing (Tier 1/2) | 2–3× oversubscription             | Acceptable for deterministic tasks; Tier 3 packaging gets a dedicated partition.              |

*Claim class — \[F\] throughout, and refined only by measurement if the fixture is ever exercised under synthetic load.*

## 15. Cost Model (Fixture Parameters)

The economic logic of the pattern, in fixture terms: the enclave resolves the majority of interactions (the fixture models 60–80% at Tiers 1–2) at near-zero marginal cost, reserving metered cloud spend for the frontier work that justifies it. Cost-per-workflow is a first-class recorded metric, so spend is attributable to mission activity rather than appearing as an undifferentiated utility bill. Multi-vendor routing keeps pricing negotiable, and the \<24-hour model-swap target keeps it credible.

Two disciplines from the governance layer bear directly on cost. First, context curation is a cost control as much as a compliance control: minimized packages are cheaper packages. Second, the independence requirement adds a real, budgeted cost — genuinely independent multi-model dispatch is more expensive than one model checking itself — and the fixture treats that as the price of verification rather than an inefficiency to optimize away. Where the budget cannot bear independent dispatch, the honest configuration is a single perspective plus human review, not a cheap consensus that measures nothing.

*Claim class — \[F\] all figures; the cost-attribution mechanics inherit \[P\] from the audit record.*

## 16. Human Factors: Engineering the Review Layer

### 16.1 Rubber-stamping is a structural hazard, not a character flaw

If the AI is right most of the time — and in a mature system it will be — the human in the loop is at permanent risk of becoming a decorative approver: still accountable on paper, no longer actually deciding. The fixture treats this as its highest-priority human-factors hazard, and it treats the cause as structural. The part that broke is not the cause: the reviewer who rubber-stamps is the bearing that seized, and the causes sit upstream — too many alerts, evidence presented badly or with false confidence, time pressure, stale training, and above all a scoreboard that rewards cases cleared instead of review quality. A system that counts cases cleared per hour will produce rubber-stamping no matter who sits in the chair. If the cause is structural, the fix must be structural. Exhortation is not an engineering control.

### 16.2 Review-independence telemetry

You cannot govern a human's attention by watching the human; there is no sensor that reads whether someone is really thinking. What can be watched is the **relationship among independent judgments** — and it lives in the audit record the architecture already keeps. Real judgment produces a measurable amount of honest disagreement with the machine and with peer reviewers. A seat that has decayed into rubber-stamping produces the opposite: its judgments stop carrying independent variance and start agreeing a little too perfectly. That collapse is a system-level signal. The independence sensor (§10.7) computes it per seat and per workflow; the gate stops crediting the affected seat's agreement as corroboration; the terminus investigates causes. The seat is never disciplined by the sensor — the sensor has no channel into the loop, and a contact pattern in review telemetry is evidence about the review system, never a personnel judgment.

**Governance of the telemetry itself.** At federal scale, per-seat telemetry raises privacy, labor-relations, due-process, and records-retention obligations, and the control must not become the surveillance mechanism it exists to avoid. Review-independence telemetry is therefore governed as **sensitive workforce-process metadata**: privacy review before activation; minimization by design; aggregate reporting by default; per-seat views only on documented need with supervisor and privacy-office approval; retention per records schedule; and a hard rule that **no personnel action is triggered automatically** from the sensor. The sensor may invalidate a seat's corroboration value; it may not discipline a person.

### 16.3 Differentiated review burden

Forcing full human review on every trivial, reversible, low-stakes call burns the attention needed for the calls that matter — which is itself a cause of rubber-stamping. Human review is assigned where consequence, uncertainty, novelty, conflicting authority, or the weight of evidence demands it; a capability earns a longer leash (WAL-2/3) only where stakes are low, actions reverse cleanly, and confidence is high. That is not loosening authority — it is spending a scarce resource where it does real work, so the review that remains is real.

### 16.4 Override analytics and overreliance

Override patterns identify weak templates, poor model selection, and task types where the AI underperforms; they are shared with inspectors and supervisors as empowerment tools, not surveillance. Overreliance controls are structural first: WAL-0 starts every capability in recommend-only; confidence display flags where the system is uncertain and the professional's expertise is most needed; supervisors can trigger random spot-check workflows whose results are visible to the inspector; and training includes periodic unassisted exercises so baseline competency does not atrophy.

### 16.5 Two seats: meaning and action

Human review in this architecture is not one undifferentiated approval. The seat that holds authority over **meaning** — which interpretation of a contested regulatory provision is correct, ruled against the governing authority document by the Office of Regulatory Interpretation or equivalent counsel function — is not the seat that holds authority over **action** — whether to open, escalate, or close an enforcement matter, held by the inspection chain. Where one approver holds both, that approver can rule an interpretation correct and then act on its own ruling with no separate vantage point: a super-seat certifying itself across two layers. Workflow contracts therefore name both authorities wherever interpretation can be contested (§11.5), and the evaluator may surface candidate interpretations but is structurally prohibited from deciding among them. Statistical frequency, historical usage, and accumulated evidence never resolve meaning; enough evidence never becomes authority, no matter how much accumulates.

### 16.6 Supervisor visibility and change management

Team-level dashboards show tier distribution, approval rates, override attribution, trust progression, and division-level independence telemetry in aggregate; individual data surfaces only where specific review is warranted. Supervisors sign WAL promotions and can trigger demotions on operational change. New capabilities arrive on a published schedule with training and feedback windows — no surprise deployments to field teams. Adoption is opt-in and paced; internal champions precede expansion; a designated AI liaison in each division carries feedback to the shared-service team. Culture outranks code.

*Claim class — \[S\] the telemetry and two-seat mechanics as governance doctrine; \[F\] the organizational program around them.*

## 17. What the Fixture Resolves, and What It Cannot Test

### 17.1 Resolved by design in this version

| **Domain**         | **Prior weakness**                                                                 | **Resolution here**                                                                                     |
|--------------------|------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Audit integrity    | “Append-only” asserted via filesystem attributes only                              | SHA-256 hash chain, genesis seed, cross-day chaining, standalone verifier (§7.7)                        |
| Log independence   | Logging described as a property, not a mechanism                                   | Separate log-writer process; durable/best-effort classes; fail-closed halt (§7.7)                       |
| WAL precision      | Levels without state-machine rules                                                 | Per-capability gates, concrete promotion criteria, auto-demotion, temporal decay, reconciliation (§7.8) |
| Context packaging  | Privacy-only framing; undifferentiated failures                                    | Dual gate with distinct failure attribution; packages as hash-anchored artifacts (§6)                   |
| Recovery semantics | Idempotency named, not specified                                                   | Source-event identity; intent-equivalence hash; equivalent-replay dedup; changed-intent refusal (§11.2) |
| Consensus validity | Multi-model agreement treated as verification                                      | Independence sensor, deterministic gate, accountable terminus; agreement as output variable (§10.7)     |
| Self-certification | Eval harness and ledger graded the system that ran them; ledger auto-drove routing | Independent Evaluation Service; ledger advisory-only; governed mutation path (§10.4–10.6)               |
| Review hollowing   | Overreliance addressed by exhortation and spot checks                              | Structural hazard framing; review-independence telemetry; scoreboard audit; differentiated burden (§16) |
| Human authority    | One fused approval seat                                                            | Meaning-authority and action-authority as distinct contract fields (§11.5, §16.5)                       |
| Work taxonomy      | Tiers conflated verification difficulty with work category                         | Mandatory exploration/execution classification with distinct obligations (§4.2)                         |
| Claim discipline   | Designed, required, and aspirational claims in identical prose                     | Claim classes \[P\]/\[S\]/\[F\] on every section (§0.1)                                                 |
| HA correctness     | Replication without split-brain doctrine                                           | Manual failover, suspension signal, startup self-check (§11.4)                                          |

### 17.2 Resolved in v1.1, after adversarial panel round one

The first panel round (two independent model reviews, July 2026) produced the corrections below. Every post-cutoff policy claim was verified against primary sources before incorporation; the panel divergence itself — one review converging on praise, one producing evidence-linked dissent — is recorded as a live instance of the variance signal this architecture measures (§2.5, §16.2).

| **Domain**            | **v1.0 weakness**                                                                           | **v1.1 resolution**                                                                                                                           |
|-----------------------|---------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| Policy freshness      | Cited two rescinded OMB memoranda (M-24-10, M-21-31) as live authorities                    | Verified stack refresh: M-25-21, M-26-14 + CISA LRA, FedRAMP CR26/20x, M-25-22, SP 800-63-4; constraint stack declared versioned (§2.1, §2.5) |
| Section 508 baseline  | Overstated the binding standard as WCAG 2.1 AA                                              | WCAG 2.0 A/AA as federal baseline; 2.1 AA as internal target (§5.6)                                                                           |
| Audit anchoring       | Hash chain verifiable only against its own genesis                                          | Daily HSM-signed chain head published to an evidence store outside the producer boundary; IES verifies against the external digest (§7.7)     |
| Evaluator sufficiency | IES read only the producer's own stream — omissions undetectable                            | Triangulation against non-producer sentinels; absence-from-stream treated as record-integrity incident (§10.5)                                |
| Sensor fail-state     | Behavior when the independence sensor is unavailable was unspecified                        | Consensus delivered but downgraded to non-governance evidence; no corroboration credit, no promotion feed (§10.7)                             |
| WAL measurand clarity | “Modified = 0.5” collapsed a typo and an unsafe recommendation into one score               | Closed severity taxonomy, attribution-aware; safety-significant corrections trigger immediate review (§7.8)                                   |
| Telemetry governance  | Per-seat telemetry lacked privacy and labor guardrails                                      | Sensitive workforce-process metadata regime; aggregate by default; no automated personnel action (§16.2)                                      |
| ATO expression        | “Inside the boundary” left undecomposed                                                     | Explicit boundary and control-inheritance table; IES/sensor as separately controlled subsystem (§7.9)                                         |
| Latency claims        | Flat targets read as guarantees                                                             | Percentile SLOs, explicitly \[F\] until measured (§4.5)                                                                                       |
| Campaign coverage     | No injections for omission, anchoring, sensor loss, catalog change, accessibility, severity | FI-09 through FI-16 added; Phase B/C success criteria stated (§18)                                                                            |

### 17.3 Resolved in v1.2, after adversarial panel round two

Round two repeated round one's variance structure — one review converging on approval, one producing evidence-linked additions — and both post-cutoff policy claims raised were verified against primary sources before incorporation.

| **Domain**               | **v1.1 weakness**                                                                   | **v1.2 resolution**                                                                                     |
|--------------------------|-------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Policy completeness      | LLM-procurement and software-assurance overlays absent (M-26-04, M-26-05)           | Both verified and added; catalog records applicability, transparency tier, and sunset (§2.1)            |
| Traceability             | Invariant-to-mechanism relationships present but distributed across prose           | Traceability matrix: invariant → mechanism → evidence → evaluator check → injection (§19.2)             |
| Evaluator under test     | The IES tested less aggressively than the producer it evaluates                     | FI-17–FI-20: IES unavailable, sentinel missing, unsigned IES change, timing reconciliation (§18)        |
| Administrative record    | Audit richness unclassed against FOIA, privilege, and deliberative-process exposure | Record-class labels; export as a governed action; records officer and counsel as deciding seats (§8.4)  |
| High-impact AI artifacts | M-25-21 evidence objects satisfied behaviorally but left implicit                   | Named first-class artifacts emitted through the record (§10.9)                                          |
| GPU isolation claim      | Partitioning read as a tenant-separation conclusion                                 | Isolation aid, not authorization conclusion; co-residency policy pending side-channel assessment (§5.2) |
| Data-label mapping       | Per-label gate treatment described in prose only                                    | Fixture-defined label-to-endpoint matrix with denial modes and review authorities (§6.5)                |

### 17.4 What the fixture structurally cannot test

- **Real authorization.** No ATO process, assessor, or SSP exists for a fictional agency. Compliance claims here are design intent against real frameworks, never assessed compliance.

- **Cross-domain operation.** Classification boundaries, cross-domain solutions, and guard hardware are named as constraints the pattern would face; the fixture cannot exercise them.

- **Scale.** Every concurrency, throughput, and cost figure is \[F\]. The DRNT testbed proves the primitives at single-user scale; nothing in this document proves them at eleven thousand.

- **Culture.** Adoption dynamics, trust formation, and organizational change are modeled from published patterns; they are the least transferable part of any fixture.

- **The independence sensor's own calibration.** The sensor/gate/terminus structure is specified; what constitutes a healthy variance band in a real review population is an empirical question the fixture can pose but not answer.

## 18. Test Campaign Plan

The phases of the retired predecessor were a deployment roadmap. The fixture has no deployment; its phases are exercises, each answering one question about DRNT-method portability. Failure injections (FI) are scripted adversarial conditions run against the modeled architecture on paper or on the DRNT testbed where the primitive exists.

Phase A — Fixture assembly and claim labeling

- Complete the fictionalized corpus; verify every section carries claim classes; adversarial-panel review of the labeling itself (is anything wearing a stronger tier than it holds?).

Phase B — Spine exercises (the \[P\] primitives under fixture load)

- Route/gate/dispatch/record walkthroughs against the six fictional mission systems; hash-chain verification drills including the external daily digest; WAL promotion, demotion, and decay table-tops with the concrete criteria; replay-guard exercises against WAYSIDE queue-and-sync scripts.

- **Success criteria, stated in advance:** every exercised \[P\] primitive completes with zero self-certification violations; the full chain verifies end-to-end including external anchors; the replay guard deduplicates every equivalent replay and refuses every changed-intent resubmission; no WAL transition occurs without its signed authority event in the record.

Phase C — Failure injections

| **ID** | **Injection**                                                                     | **What it tests**                                                                                                                                                    |
|--------|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FI-01  | Backdoored enclave-model weight update                                            | Topology egress, provenance rejection, rollback (§13.1)                                                                                                              |
| FI-02  | Consensus capture: second model anchored on the first via shared retrieval        | Sensor detection of non-independent agreement; gate refusal (§10.7, §13.2)                                                                                           |
| FI-03  | Rubber-stamp drift: scripted reviewer approving at 100% for three weeks           | Review-independence telemetry; terminus response addresses structure before persons (§13.6, §16)                                                                     |
| FI-04  | Replay ambiguity: identical source event resubmitted with changed intent          | Intent-equivalence refusal; human disposition path (§11.2)                                                                                                           |
| FI-05  | Ledger inflation attack                                                           | Chain-derived reconciliation; advisory-only blast radius (§13.3)                                                                                                     |
| FI-06  | Slow memory poisoning via plausible sources                                       | Provenance expiry; confidence deprioritization (§13.4)                                                                                                               |
| FI-07  | Split-brain: failed node returns believing it holds authority                     | Suspension signal; startup self-check (§11.4)                                                                                                                        |
| FI-08  | Seat fusion: one approver rules an interpretation and acts on it                  | Contract validation rejects the fused seat; two-authority enforcement (§16.5)                                                                                        |
| FI-09  | GOS record omits an egress event that appears in gateway and cloud-provider logs  | IES triangulation; omission surfaces as a record-integrity incident (§10.5)                                                                                          |
| FI-10  | Internally valid hash chain presented without its external daily digest           | Verifier rejects unauthenticated chain history (§7.7)                                                                                                                |
| FI-11  | Independence sensor unavailable during a multi-model agreement                    | Consensus downgraded to non-governance evidence; no corroboration credit, no promotion feed (§10.7)                                                                  |
| FI-12  | FedRAMP status changes for an approved endpoint mid-campaign                      | Version-aware catalog revocation; routing fallback; signed emergency change through the governed path (§7.6)                                                         |
| FI-13  | Section 508 failure in a generated supervisor dashboard                           | Accessibility as an acceptance criterion, not a retrofit (§5.6)                                                                                                      |
| FI-14  | A wave of “modified” feedback that is purely cosmetic                             | Severity weighting: cosmetic edits count as acceptance; trust signal stays clean (§7.8)                                                                              |
| FI-15  | Cross-directorate need-to-know sharing where Gate A redaction fails on one field  | Dual-gate attribution: prevented-disclosure path, data-steward routing, blast-radius confinement (§6.1)                                                              |
| FI-16  | Legacy mission-system adapter returns stale or malformed data as if authoritative | Tier 2 dual validation; confidence scoring; adapter readiness as a Phase 0 gate, not an assumption (§9.1)                                                            |
| FI-17  | IES unavailable during a WAL promotion window                                     | Promotion cannot proceed; work continues at the existing WAL; no earned-trust credit is applied in the gap (§10.5, §7.8)                                             |
| FI-18  | One sentinel telemetry stream missing                                             | IES downgrades confidence in record completeness; the affected period cannot support promotion or corroboration (§10.5)                                              |
| FI-19  | IES configuration changed without a signed authority event                        | Change rejected or marked unauthenticated; evaluator output from the affected version is quarantined (§10.5, §10.6)                                                  |
| FI-20  | Sentinel/GOS disagreement caused by clock skew or a delayed queue flush           | Reconciliation enters a pending state — not an immediate incident — until the timing window closes, protecting the evaluator from false-positive uselessness (§10.5) |

FI-03 is the highest-value injection: it is the failure that occurs while every component works as designed, and it is the one the predecessor document could not have detected at all. FI-09 and FI-10 are its closest kin — failures in which the record itself looks healthy. FI-17 through FI-19 apply the same suspicion to the evaluator: the instrument that checks the producer must be tested as aggressively as the producer, and FI-20 keeps that suspicion from curdling into an evaluator too alarm-prone to use.

Phase D — Governed mutation drills

- Walk configuration changes of every class — routing table, template, WAL threshold, sensor threshold, evaluator scope — through the full evidence → IES → signed-authority path; attempt one privileged bypass per class and confirm each is refused and recorded.

Phase E — Findings

- A portability report: which DRNT invariants held unchanged at federal shape, which required re-expression, and which surfaced constraints the single-user testbed never could. That report — not this document — is the fixture's deliverable.

## 19. Guiding Principles

| **Principle**                         | **What it means**                                                                                                                                                       |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Route, don't reason                   | Navigation to a bounded task precedes reasoning; reasoning never substitutes for authority, policy, containment, or verification.                                       |
| Nothing certifies itself              | No actor — model, service, panel, or seat — is the independent certifier of its own prior action. No exceptions, including the instruments built to check independence. |
| Agreement is not a safety property    | Consensus is an output variable; it counts as evidence only with an independence measurement produced outside the panel that generated it.                              |
| AI assembles, professionals decide    | Human-in-the-loop by default; autonomy per capability, earned, decaying, revocable.                                                                                     |
| Meaning and action are two seats      | Interpretation authority and action authority are distinct, separately named, separately governed.                                                                      |
| Agency-controlled, cloud-augmented    | Data, memory, execution inside the boundary; cloud models are tools, not platforms.                                                                                     |
| Vendor independence                   | Model-agnostic routing; swap target \<24 hours; multi-vendor is architectural.                                                                                          |
| Inspectable by default                | Every decision in the hash-chained record; the producer appends, never amends.                                                                                          |
| Learning through supervised promotion | Evidence proposes; the governed path disposes. No silent auto-whitelisting, no autonomous drift.                                                                        |
| Structure before exhortation          | Where humans fail predictably, fix the structure that made failure rational; vigilance lectures are not controls.                                                       |
| Claims keep their tier                | Proven, specified, and hypothesized are labeled and never promoted by assertion.                                                                                        |
| People first                          | Trust earned through transparency, opt-in expansion, and feedback. Culture outranks code.                                                                               |

### 19.1 A note on system-theoretic convergence

Readers familiar with STAMP/STPA will recognize the shape of this architecture: hierarchical control structures, hazards framed as unsafe control actions rather than component failures, and nested loops — an inner operational loop (route, gate, execute, record) supervised by an outer governance loop (record, independent evaluation, authority, governed change). The relationship is stated precisely, because it is easy to overclaim: **system-theoretic hazard analysis produces the catalog of ways a control structure fails; this architecture is the runtime structure built to hold against that catalog and keep the proof that it did.** The hazards are inputs to the architecture; the architecture is not an instance of the analysis. That two traditions — control engineering and performance-based safety regulation — arrive independently at the same structure is treated here as evidence the structure is real, and this fixture is a third, synthetic derivation built to stress that evidence.

### 19.2 Traceability matrix

Every load-bearing invariant, mapped to the mechanism that enforces it, the evidence it leaves, the check that reads that evidence, and the injection that attacks it. The relationships were already present in the prose; this table exists so the fixture is easy to evaluate and hard to misread.

| **DRNT invariant**                 | **Mechanism**                                                             | **Evidence artifact**                                        | **Evaluator check**                                                        | **Injection**                |
|------------------------------------|---------------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------------------------------------------|------------------------------|
| Route, don't reason                | L1 classifier-driven dispatch; work_class + tier in every contract        | Routing receipt with tier and rationale metadata             | IES replay: same input, policy, and registry yields the same route         | FI-01                        |
| Nothing certifies itself           | IES outside the producer package; separate log writer; append-never-amend | GOS stream plus sentinel reconciliation report               | Omission from the stream surfaces as a record-integrity incident           | FI-09, FI-19                 |
| Audit cannot self-anchor           | Daily HSM-signed external digest                                          | Chain-head receipt in the external evidence store            | Chain verified against the external digest before use as evidence          | FI-10                        |
| Agreement is not a safety property | Independence sensor + deterministic gate + Board terminus                 | Independence measurement receipt attached to every consensus | Gate refuses or downgrades unmeasured consensus                            | FI-02, FI-11                 |
| Review must not hollow out         | Seat telemetry, scoreboard audit, differentiated burden                   | Review-independence metrics, aggregate by default            | Variance collapse alarms; corroboration credit withdrawn                   | FI-03                        |
| Meaning and action are two seats   | Dual authority fields in workflow contracts                               | Contract-validation record; two distinct signatures          | Fused-seat contracts rejected at validation                                | FI-08                        |
| Learning is governed               | Evidence → IES → authority → signed change; ledger advisory-only          | Change event with its prior evidence packet                  | Bypass attempts refused and recorded                                       | Phase D drills, FI-05, FI-12 |
| Trust is earned and decays         | WAL state machine; severity weighting; temporal decay                     | WAL transition events with signed promotion authority        | No transition without its authority event; decay logged as its own trigger | FI-14, Phase B               |
| Replay is disambiguated            | Source-event identity + intent-equivalence hash                           | Dedup and refusal receipts                                   | Changed intent refused to human disposition                                | FI-04                        |

## 20. What Success Would Prove

The fixture's strategic question is not whether a fictional agency would benefit — it has no benefits to receive. The question is whether the DRNT invariants are **portable**: whether principles proven as primitives on one end user's testbed, and derived originally from two decades of performance-based safety regulation, keep their load-bearing character when transplanted into the largest, most constrained enterprise shape available to model.

| **Challenge at federal shape** | **What the pattern answers**                                                                                                 |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Shadow AI                      | A sanctioned interface that respects data boundaries removes the incentive for unauthorized tools.                           |
| Vendor lock-in                 | Model-agnostic routing and modular structure keep every provider replaceable.                                                |
| Compliance burden              | Audit artifacts generated structurally; evidence as a byproduct of operation, not a project after it.                        |
| AI trust deficit               | Errors surfaced, provenance shown, autonomy earned and decaying — and review integrity itself measured rather than presumed. |
| Cost control                   | Majority enclave resolution; cloud spend attributable per workflow; independence priced honestly.                            |
| The quiet failure              | The one no component causes: review hollowing detected relationally, in the record, while there is still time to act.        |

If the invariants hold here, the claim is not “this agency should build it.” The claim is that the invariants are properties of governed enterprises as such — the same structure reached from railroad infrastructure and from control engineering, now surviving a third, synthetic derivation at federal shape. Two independent origins were evidence the structure is real; a fixture this hostile to hand-waving is how the evidence is stress-tested.

Current policy supplies a live example of the invariant doing work. Published analysis of M-26-04 notes that its transparency regime still permits vendors to self-evaluate their compliance with the Unbiased AI Principles — self-certification, named by the policy's own critics. The fixture's answer is the one it gives everywhere: vendor documentation is evidence, never certification, and compliance claims are checked by an evaluator the vendor does not control, against telemetry the vendor does not author. The architecture is not merely compatible with current federal AI policy; it closes a gap that policy's critics have already identified.

## 21. Conclusion

This document is not a rejection of cloud AI, and it is not a proposal to anyone. It is a test article: a fully governed orchestration architecture, written at full operational fidelity inside a regulator that does not exist, so that every DRNT method — routing before reasoning, trust that is earned and decays, records no producer can amend, evaluation no producer can perform on itself, consensus that must prove its independence, human authority split so no seat certifies its own ruling, and mutation that only moves through a governed path — can be exercised against the hardest constraint stack available.

The predecessor document asked whether the controls existed. Its gap analysis asked whether the mechanisms were specified. This fixture asks the question both of them missed: **do the relationships among the controls prevent any actor from certifying itself — and is that prevented by measurement, or merely asserted?** Everything rewritten here exists to make that question testable.

> *We are not describing a chatbot, and we are not proposing a system. We are testing whether a governance discipline survives the largest organization we can imagine wrapping around it. The organization is fiction. The discipline is not.*

**END OF FIXTURE**
