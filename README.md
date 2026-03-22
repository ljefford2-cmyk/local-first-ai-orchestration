
[README.md](https://github.com/user-attachments/files/26159009/README.md)
# The Case for the Local Orchestrator

**A Reference Architecture for Local-First AI Agent Orchestration**

v5.1.1 — Post-Adversarial Edition — March 2026

---

## What This Is

A design pattern for placing a local AI model between users and cloud frontier models. The local model holds personal context, preferences, and memory. It decides what stays local, what goes to the cloud, and how to package what goes. Cloud models handle complex reasoning. The user retains decision authority.

This is a reference architecture — technology-stack-agnostic, domain-agnostic, and not a product. It has been applied and validated across federal regulatory (DOT), clinical (Mayo Clinic), small business, K-12 education, and personal-scale (DRNT) deployments.

## Core Principle

**Know what you have. Package what you need.**

The local model reasons about two things: *Do I already have this?* And if not, *what does the cloud need to see?*

The first question is retrieval. If the answer is in local context, the request never leaves the local perimeter — no cloud call, no token spend, no privacy exposure. The second question is packaging. When cloud escalation is needed, the local model assembles the tightest possible payload: right context, stripped of what's sensitive, framed to get a good answer on the first pass.

The boundary is not between reasoning and not-reasoning. It is between reasoning where errors are recoverable and reasoning where errors are consequential. The local model gets a reasoning budget proportional to how cheaply its mistakes can be corrected.

## Key Components

| Component | Purpose |
|---|---|
| **Context Packager** | Structural privacy gate and prompt engineer. All outbound data passes through it. Sensitive data is stripped by rule, not by judgment. Default is deny. |
| **Workflow Autonomy Levels (WAL 0–3)** | Graduated trust. Every capability starts at WAL-0 (recommend only). Promotion requires demonstrated reliability. Anomalies trigger automatic demotion. |
| **Append-Only Audit Log** | Every routing decision, packaging decision, model response, and human decision is logged in a cryptographically chained journal. Non-optional. |
| **Multi-Model Dispatch** | Route different task types to different cloud models based on measured strengths. V1 is single-endpoint; multi-model dispatch depends on the reconciliation layer. |

## Documents

| Document | Description |
|---|---|
| [The Case for the Local Orchestrator](docs/the-case-for-the-local-orchestrator.md) | Full reference architecture. Core principle, Context Packager, WAL governance, audit log, multi-model dispatch, validation methodology. |
| [Lightweight Evaluation Loop](docs/addenda/lightweight-evaluation-loop.md) | Addendum. Personal-scale quality feedback — how deployments with high question diversity and low repetition capture improvement signals without formal test suites. |
| [Adversarial Review Methodology](validation/adversarial-review-methodology.md) | Summary of the multi-model review process, key findings, and what changed as a result. |

## Validation

The architecture has been stress-tested through structured adversarial review by four independent AI models — Claude (Anthropic), ChatGPT (OpenAI), Gemini (Google), and Copilot (Microsoft) — across five implementation domains. Reviews were conducted independently with identical prompts and no cross-contamination. Findings were reconciled across all reviews, and convergent criticisms drove structural revisions to the architecture.

The most significant finding: all four reviewers independently identified that the original core principle ("route, don't reason") created a structural tension with what the architecture actually does. This convergent criticism drove the v5.0 reframe to the current principle, replacing a binary framing with a spectrum based on error recoverability.

See [Adversarial Review Methodology](validation/adversarial-review-methodology.md) for details.

## What This Architecture Is Not

It is not a product. It is a reference pattern.

It is not a daily planner, productivity suite, or task manager. If a deployment helps someone plan their day, that is because they routed a planning request — not because planning is what the architecture is for.

It is not an argument against cloud AI. The entire architecture depends on cloud models for complex reasoning. It is an argument for local control over what context reaches those models and what actions their outputs can trigger.

It is not a claim that local models don't reason. They do. The architecture defines where that reasoning is safe and where it is not.

It is not complete. The L2 reconciliation layer, temporal decay for WAL trust levels, and device-specific signal chain failure modes are specified but not yet implemented. V1 is the foundation.

## Domain Applications

The `domain-applications/` directory contains scrubbed case studies applying this architecture across four sectors:

- **Federal Regulatory** — unified architecture, executive briefing, and departmental orchestrator case for a large federal agency operating under FedRAMP, FISMA, and Zero Trust mandates
- **Clinical** — engineering review, adversarial appendix, and patient narrative for AI orchestration in a clinical environment with HIPAA and FDA CDS constraints
- **K-12 Education** — feasibility analysis, implementation report, and landscape research for district-level sovereign AI deployment
- **SMB** — six-document framework covering business readiness, reference architecture, vendor evaluation, regulated-industry supplement, and operations governance

Each domain was developed independently and stress-tested through multi-model adversarial review. See [`domain-applications/README.md`](domain-applications/README.md) for the full overview.

 - **Personal** — five interface specifications for DRNT, a personal AI gateway implementing the full route-don't-reason architecture with append-only audit, graduated trust, and structural privacy enforcement

## License

[MIT](LICENSE)

## Author

Lawrence Jeffords — Nahunta, Georgia

Architecture developed and validated March 2026 through multi-model adversarial review across Federal, Medical CAOF, SMB Framework, DRNT, and L2 Specification domains.
