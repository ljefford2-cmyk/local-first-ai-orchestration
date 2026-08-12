# DRNT Architecture Corpus — Authority Map

**Status:** Current corpus control surface — August 2026

Read this file before relying on any architectural or implementation claim in the repository.

## 1. Purpose

This map governs two different questions:

1. **Which source controls when documents differ?**
2. **What kind of claim is being made: demonstrated, specified, or hypothesized?**

Those questions are related but not interchangeable. A source can be architecturally authoritative without proving implementation. A demonstrated primitive can be real at personal-testbed scale without controlling enterprise architecture.

## 2. Controlling source hierarchy

### 2.1 Corpus-level authority

1. **[Project Plan v0.2 — Frozen Architectural Baseline](../../publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md)** controls the three-volume architecture, controlled terminology, volume allocation, organizational claim states, baseline machinery, and change classification.
2. **[Volume 1 — Define Reality](../../publications/controlled-architecture/Volume_1_Define_Reality_Final_Controlled_Manuscript.md)** controls its allocation: governed claims, boundary, assumptions, authorized purpose, authority definitions, consequence classification, measures and measurands, intended external effects, declared organizational operating model, authoritative-source lineage, versions, lifecycle, and governed-target handoff.
3. **[Volume 2 — Evaluate Reality](../../publications/controlled-architecture/Volume_2_Evaluate_Reality_Final_Controlled_Manuscript.md)** controls its allocation: Independent Observation, Runtime Control relationships, Independent Evaluation, evidence quality and sufficiency, consequence windows, effect verification, claim states, findings and closure, Declared/Observed/Assured baselines, evaluator governance, common-mode assurance, Loop 2 organizational learning, governed change, and the functional Volume 2 → Volume 3 evidence/intervention contract.
4. **[Volume 3 — Connect Reality](../../publications/controlled-architecture/Volume_3_Connect_Reality_Final_Controlled_Manuscript.md)** controls its allocation: complete claim-relevant capability paths, discovery, requirement-relative qualification, capability results, Graduated Integration, Runtime Integration Envelopes, evidence custody, continuing qualification, capability health, degradation, requalification, retirement, and the heterogeneous as-built/as-operated enterprise interface.

The three volumes are analytically separable and operationally interdependent. No volume silently takes another volume's function.

### 2.2 Supporting authority

Supporting works provide engineering inheritance only where they map cleanly into the controlling hierarchy:

- **[AI Orchestration Governance: The Agent Is Not the System](../../publications/foundation/AI_Orchestration_Governance_The_Agent_Is_Not_The_System.md)** — public Volume 1 foundation and conceptual entry point.
- **[The Controller Is Part of the System Under Test](../../publications/supporting-papers/The_Controller_Is_Part_of_the_System_Under_Test_Public_Edition.md)** — principal inheritance for item definition, assumptions of use, runtime reconciliation, epistemic independence, and claim licensing.
- **[The Enterprise Under Test](../../publications/enterprise-doctrine/The_Enterprise_Under_Test_Final_Doctrine_v3.md)** — enterprise DRNT and seat-layer inheritance concerning authority surfaces, self-certification, governed mutation, and organizational seams.
- **[The Model Is Not the Claim](../../publications/supporting-papers/The_Model_Is_Not_the_Claim_Discussion_Paper.md)** and **[From Defect Detection to Governed Enterprise Learning](../../publications/supporting-papers/From_Defect_Detection_to_Governed_Enterprise_Learning_Revised.md)** — supporting assurance and operational illustrations.
- **[Federal-Scale Orchestration Test Fixture](../../publications/fixtures/Federal_Scale_Orchestration_Test_Fixture_v1.2.md)** — synthetic stress material. It is not a proposal, deployment, compliance assertion, or proof of enterprise-scale performance.

Where supporting terminology differs, the Project Plan and Final Controlled Manuscripts govern future use.

### 2.3 Implementation-facing specifications

The `specs/`, `governance/`, and implementation-facing architecture documents define technical mechanisms and interfaces. They are authoritative for their bounded technical subject only when they remain consistent with the controlling architecture.

A technical specification does not redefine:

- the organizational claim;
- the claim-relative boundary;
- organizational authority;
- the Volume 2 claim-state vocabulary;
- the three-baseline machinery;
- evidence sufficiency;
- the Volume 2 → Volume 3 contract; or
- the non-ownership boundary among the volumes.

### 2.4 Running implementation truth

Claims about what is built, runnable, wired, tested, partial, or absent are controlled by the separate **[`local-first-ai-gateway`](https://github.com/ljefford2-cmyk/local-first-ai-gateway)** repository.

The source of truth is its current:

- code;
- automated and adversarial tests;
- receipts and operating evidence;
- [`STATUS.md`](https://github.com/ljefford2-cmyk/local-first-ai-gateway/blob/main/STATUS.md); and
- observed behavior at the stated scale.

This repository may define a mechanism as required without asserting that the mechanism is implemented.

### 2.5 Drafting and staging

Uncommitted drafts, review memoranda, prompts, redlines, and working files are staging material. They have no authority over committed controlled documents unless a later governed decision explicitly incorporates them.

## 3. Two orthogonal classification systems

### 3.1 Source authority tier

| Tier | Meaning | Settled by |
|---|---|---|
| **Tier 1 — Implementation truth** | What the current personal testbed actually enforces. | Gateway code, tests, receipts, STATUS matrix, and observed operation. |
| **Tier 2 — Architecture and specification truth** | What the organizational architecture and DRNT specifications require, including mechanisms not yet built. | Frozen Plan, Final Controlled Manuscripts, committed doctrine, specifications, and ADRs under the hierarchy above. |
| **Tier 3 — Staging truth** | Current drafting or review material not yet committed as authority. | Nothing until governed incorporation. |

### 3.2 Implementation-claim label

The publication corpus also uses `[P]`, `[S]`, and `[F]`:

| Label | Meaning | Limit |
|---|---|---|
| **[P] Demonstrated primitive** | A mechanism has been shown runnable at the stated scale. | Does not establish enterprise-scale fitness, complete system operation, or a stronger organizational claim. |
| **[S] Specified mechanism** | A mechanism exists in architecture or specification but is not established as a running component. | Prose, diagrams, and design completeness do not promote it to `[P]`. |
| **[F] Fixture or implementation hypothesis** | Synthetic behavior, scaling assumption, organizational realization, or proposed implementation behavior. | Useful for stress testing; not evidence of deployment, compliance, cost, throughput, or effectiveness. |

The classifications are orthogonal. A Final Controlled Manuscript is Tier 2 architecture authority and may contain `[P]`, `[S]`, and `[F]` implementation claims. A gateway test is Tier 1 implementation evidence but does not control the three-volume architecture.

## 4. Controlled vocabulary and non-collapse rules

The following distinctions govern repository interpretation:

- mission is not claim;
- purpose is not authority;
- claim is not metric;
- measure is not measurand;
- model or agent is not the organizational system;
- output is not execution;
- authorization is not execution evidence;
- dispatch is not execution;
- acknowledgment is not external effect;
- execution is not verified effect;
- technical qualification is not accountable authorization;
- authorization does not strengthen evidence;
- repeated practice does not create legitimacy;
- lifecycle status is not a Volume 2 evidentiary claim state;
- detection is not final evaluation;
- remediation is not technical closure; and
- accumulated evidence is not authority to change the system.

The controlling Volume 2 claim states are:

- Supported;
- Narrowed;
- Indeterminate;
- Unsupported; and
- Contradicted / Failed.

Pending is a workflow state only.

## 5. Relationship between project volumes and DRNT layers

The project volumes and DRNT implementation layers use similar numbers but answer different questions.

| Controlled project volume | Function | Not equivalent to |
|---|---|---|
| **Volume 1 — Define Reality** | Defines the governed organizational target. | The DRNT L1 gateway/router. |
| **Volume 2 — Evaluate Reality** | Defines independent operational assurance and governed learning. | A single L2 model, auditor, or evaluator service. |
| **Volume 3 — Connect Reality** | Defines the complete enterprise-interaction and continuing-qualification layer. | A roaming agent or unrestricted execution layer. |

Within DRNT, L1 routing, an independently governed evaluation realization, and bounded L3 execution can implement parts of the volume architecture. They do not replace the volumes or inherit their authority merely by sharing a number.

## 6. Current implementation-claim discipline

The repository must not freeze implementation status inside architectural prose when the gateway repository can change independently.

Therefore:

- architecture documents describe required functions and bounded inheritance;
- implementation tables should link to the gateway STATUS matrix rather than repeat stale test counts;
- a `[P]` statement names the demonstrated primitive and scale;
- a model, component, or service may contribute evidence about its own behavior but may not become the sole final authority where its failure could conceal loss of the required property;
- a fixture may exercise architecture against realistic constraints without becoming proof of compliance or deployment; and
- a title, diagram, commit message, or successful output does not establish organizational assurance.

## 7. Conflict and drift handling

Conflicts are resolved by the narrowest controlling source at the highest applicable authority level.

1. Apply the Project Plan.
2. Apply the controlling volume for the function in dispute.
3. Apply subordinate doctrine or specification only where consistent.
4. For a running-system claim, inspect gateway code, tests, receipts, and current status.
5. Preserve unresolved conflict explicitly; do not silently harmonize titles, versions, claim states, or implementation status.

A future finding is classified as:

| Class | Meaning | Treatment |
|---|---|---|
| **Architecture Defect** | Materially invalidates or contradicts the frozen architecture or exposes a load-bearing unsupported assumption. | May justify controlled amendment of the baseline. |
| **Volume 2 Engineering Issue** | Concerns assurance, evidence, evaluation, findings, closure, learning, or evaluator governance. | Resolve in Volume 2 without reopening unrelated architecture. |
| **Volume 3 Integration Issue** | Concerns discovery, evidence acquisition, interfaces, legacy systems, scalability, privacy, or as-operated connectivity. | Resolve in Volume 3. |
| **Implementation Constraint** | Concerns cost, resources, latency, organizational resistance, technical feasibility, deployment, or local role assignment. | Document, test, bound, and design around it; do not automatically treat it as an architecture defect. |

Implementation difficulty alone does not reopen settled architecture.

## 8. Required reading order

1. This Authority Map.
2. The root [`README.md`](../../README.md).
3. The [`Project Plan`](../../publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md).
4. Volume 1, Volume 2, and Volume 3 in sequence.
5. Supporting publications and doctrine as needed.
6. DRNT specifications and governance artifacts for implementation-facing detail.
7. The gateway STATUS matrix for any claim about the running system.

## 9. Governing rule

> A claim is never promoted to a stronger authority level or implementation class by assertion.

Architecture may define the target. Only controlled authority can change the target. Only evidence can establish what occurred. Only independent evaluation can determine what the evidence supports. Only code, tests, receipts, and observed behavior can establish what the running implementation enforces.
