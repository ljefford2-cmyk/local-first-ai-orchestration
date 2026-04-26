# Why You Cannot Depend on a Single AI Model

**A Reference Architecture for Local-First AI Agent Orchestration**

> **DRNT Architecture v7.0** | Canonical specification source
> Implementation: [local-first-ai-gateway](https://github.com/ljefford2-cmyk/local-first-ai-gateway) (v0.2.1)
> Supersedes: [Local-AI-Orchestrator](https://github.com/ljefford2-cmyk/Local-AI-Orchestrator) (v3 — historical)

---

## What This Is

A design pattern for placing a local AI model between users and cloud frontier models. The local model holds personal context, preferences, and memory. It decides what stays local, what goes to the cloud, and how to package what goes. Cloud models handle complex reasoning. The user retains decision authority.

This is a reference architecture — technology-stack-agnostic, domain-agnostic, and not a product. It was designed and stress-tested across multiple implementation domains and specified to enforcement depth in the DRNT personal AI gateway.

## Core Principle

**Know what you have. Package what you need.**

The local model reasons about two things: *Do I already have this?* And if not, *what does the cloud need to see?*

The first question is retrieval. If the answer is in local context, the request never leaves the local perimeter — no cloud call, no token spend, no privacy exposure. The second question is packaging. When cloud escalation is needed, the local model assembles the tightest possible payload: right context, stripped of what's sensitive, framed to get a good answer on the first pass.

The boundary is not between reasoning and not-reasoning. It is between reasoning where errors are recoverable and reasoning where errors are consequential. The local model gets a reasoning budget proportional to how cheaply its mistakes can be corrected.

## Key Components

| Component | Purpose |
| --- | --- |
| **Context Packager** | Structural privacy gate and prompt engineer. All outbound data passes through it. Sensitive data is stripped by rule, not by judgment. Default is deny. |
| **Execution Safety Layer** | Governs what happens when a decision becomes an action. Runtime isolation at the container level, behavioral governance via WAL, structural privacy on all outbound paths. No layer trusts another layer's enforcement. |
| **Workflow Autonomy Levels (WAL 0–3)** | Graduated trust. Every capability starts at WAL-0 (recommend only). Promotion requires demonstrated reliability. Anomalies trigger automatic demotion. Trust decays with inactivity. |
| **Append-Only Audit Log** | Every routing decision, packaging decision, model response, and human decision is logged in a cryptographically chained journal. Non-optional. The log writer is a separate process — if it is down, the orchestrator halts. |
| **Multi-Model Dispatch** | Route different task types to different cloud models based on measured strengths. Vendor independence is architectural: model swap target is less than 24 hours. |

## Documents

| Document | Description |
| --- | --- |
| [Why You Cannot Depend on a Single AI Model](docs/why-you-cannot-depend-on-a-single-ai-model.md) | Full reference architecture. The six friction points of single-model deployment, the local orchestrator pattern, Context Packager, Execution Safety Layer, WAL governance, audit log, multi-model dispatch, validation methodology. |
| [DRNT Specification Technical Overview](docs/drnt-specification-technical-overview.md) | Concise technical summary of the seven DRNT interface specifications and three governance artifacts. Key architectural decisions and adversarial review methodology. |
| [Lightweight Evaluation Loop](docs/lightweight-evaluation-loop.md) | Personal-scale quality feedback — how deployments with high question diversity and low repetition capture improvement signals without formal test suites. |
| [ADR-001: Project Identity](docs/adr/ADR-001-project-identity.md) | Project identity decision — DRNT as a governed AI-agent gateway and orchestration architecture. Reference architecture is domain-agnostic; current reference implementation is the personal-scale Desktop Hub. |

## Validation

| Document | Description |
| --- | --- |
| [Adversarial Review Methodology](validation/adversarial-review-methodology.md) | Summary of the multi-model review process, key findings, and what changed as a result. |
| [Why One AI Is Not Enough](validation/why-one-ai-is-not-enough.md) | Lessons from multi-model adversarial review of AI system architecture. Methodology, model bias profiles, and the case for heterogeneous review. |

The architecture has been stress-tested through structured adversarial review by four independent AI models — Claude (Anthropic), ChatGPT (OpenAI), Gemini (Google), and Copilot (Microsoft). Reviews were conducted independently with identical prompts and no cross-contamination. Findings were reconciled across all reviews, and convergent criticisms drove structural revisions.

The most significant convergent finding: all four reviewers independently identified that the original core principle created a structural tension with what the architecture actually does. This drove the reframe to the current principle, replacing a binary framing with a spectrum based on error recoverability.

## DRNT Specification Suite

The `specs/` and `governance/` directories contain the DRNT (Don't Reason, Navigate & Task) personal-scale implementation — seven interface specifications and three governance artifacts specified to enforcement depth.

**Status:** Conformance reference implementation: [local-first-ai-gateway](https://github.com/ljefford2-cmyk/local-first-ai-gateway) (implements v7.0). The gateway is sized for single-operator validation on commodity hardware — it is the existence proof that this architecture is realizable, not a product. See the gateway's STATUS.md for per-claim conformance status and current test results.

| Directory | Artifact | Description |
| --- | --- | --- |
| `specs/` | [Spec 1: Audit/Event Schema](specs/DRNT_Spec1_Audit_Event_Schema.md) | Single event format for the append-only audit log. 20+ event types, SHA-256 hash chain, fail-closed guarantee. |
| `specs/` | [Spec 2: Capability Model (WAL → Permissions)](specs/DRNT_Spec2_Capability_Model.md) | Capability registry mapping each WAL level to per-action gate policies. Promotion criteria, demotion triggers. |
| `specs/` | [Spec 3: Context Boundary Specification](specs/DRNT_Spec3_Context_Boundary.md) | Context Packager data structure, sensitivity classification, four-stage transform pipeline. |
| `specs/` | [Spec 4: Egress Policy Binding](specs/DRNT_Spec4_Egress_Policy.md) | Egress endpoint registry, 11-step gateway check sequence, Docker network topology enforcement. |
| `specs/` | [Spec 5: Override Semantics](specs/DRNT_Spec5_Override_Semantics.md) | Four override types, successor job model, conditional demotion separating routing from infrastructure failures. |
| `specs/` | [Spec 6: Silo Runtime Security](specs/DRNT_Spec6_Silo_Runtime_Security.md) | Three-layer enforcement model for worker agent execution. Skill lifecycle, worker egress proxy. |
| `specs/` | [Spec 7: Signal Chain Resilience](specs/DRNT_Spec7_Signal_Chain_Resilience.md) | Device failure modes, health probe hysteresis, stale job recovery, offline decision replay, WAL temporal decay. |
| — | Spec 8: Managed Build Workflow | Gateway-only process spec — governs build discipline for the conformance reference. Lives in the gateway repo. |
| `governance/` | [Event Schema v2.0](governance/DRNT_Event_Schema.md) | Complete field-level reference for all event types. Companion to Spec 1. |
| `governance/` | [Capability Trust Profile](governance/DRNT_Capability_Trust_Profile.md) | Trust lifecycle: ring buffer mechanics, promotion criteria, demotion rules. Companion to Spec 2. |
| `governance/` | [NemoClaw Governance Overlay](governance/DRNT_NemoClaw_Governance_Overlay.md) | Maps DRNT governance to NVIDIA's NemoClaw/OpenShell ecosystem. |

## License

[MIT](LICENSE)

## Author

Lawrence Jeffords — Nahunta, Georgia

Architecture developed and validated March 2026 through multi-model adversarial review.
