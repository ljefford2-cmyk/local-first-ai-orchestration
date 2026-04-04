# DRNT Specification Technical Overview

**Seven Interface Specifications + Three Governance Artifacts**

*A Personal-Scale Proof of Concept for Local-First AI Orchestration*

March 2026 • Lawrence Jeffords

**github.com/ljefford2-cmyk/local-first-ai-orchestration**

---

## What This Document Covers

DRNT (Don't Reason, Navigate & Task) is a personal AI gateway that implements the local-first AI orchestration pattern described in "[Why You Cannot Depend on a Single AI Model](why-you-cannot-depend-on-a-single-ai-model.md)." It has been specified to interface-contract depth: not just what the system should do, but the exact enforcement mechanism that guarantees it.

The specification suite consists of seven interface specifications (Specs 1–7) and three governance artifacts. Every specification has been adversarially reviewed by at least two independent AI models (Gemini + ChatGPT), with findings selectively incorporated based on architectural relevance. Spec 7 went through two adversarial review cycles with ten findings accepted and incorporated.

This document provides a concise technical summary of each artifact. The full specifications are published in the GitHub repository linked above.

**Status:** All seven specifications implemented with 574 passing tests. Working implementation: [local-first-ai-gateway](https://github.com/ljefford2-cmyk/local-first-ai-gateway).

## The Specification Suite

| Artifact | What It Defines | Status |
|---|---|---|
| **Spec 1: Audit Event Schema** | Single event format for the append-only audit log. 20+ event types with fixed payload schemas, SHA-256 hash chain, separate log writer process with durable/best-effort write classes and fail-closed guarantee. | FINAL (v1.5) |
| **Spec 2: Capability Model** | Capability registry mapping each WAL level to per-action gate policies. Six gate types. Concrete promotion criteria with outcome counts, time spans, and approval scores. Ring buffer state tracking. Automatic demotion triggers. desired vs. effective WAL reconciliation. | FINAL |
| **Spec 3: Context Boundary** | Context Packager data structure, sensitivity classification taxonomy, four-stage transform pipeline (retrieve → gate → scan → assemble), default-deny on unclassifiable content, field-level and span-level audit precision. | FINAL |
| **Spec 4: Egress Policy Binding** | Egress endpoint registry, 11-step gateway check sequence, Docker network topology enforcing that the orchestrator cannot reach the internet (topology, not filtering), payload hash verification, dual-scan failure attribution. | FINAL |
| **Spec 5: Override Semantics** | Four override types (cancel, redirect, modify, escalate). Successor job model preserving one-job-one-capability invariant. Conditional demotion separating routing failures from infrastructure failures. Modified result lineage with SHA-256 hashes. | FINAL |
| **Spec 6: Silo Runtime Security** | Three-layer enforcement model for worker agent execution: runtime isolation (container/OS level), behavioral governance (WAL/capability level), structural privacy (data level). Skill lifecycle with verification checklist. Worker egress proxy with request sanitization. | FINAL |
| **Spec 7: Signal Chain Resilience** | Device failure modes across the Watch → iPhone → Desktop Hub → Cloud signal chain. Health probe hysteresis, queue overflow policies, stale job recovery with idempotency key reuse, offline decision replay with concurrency control, MacBook failover with split-brain prevention. WAL temporal decay. | v1.1 (Post-Adversarial) |
| **Event Schema (Governance)** | Complete field-level reference for all event types, payload schemas, and cross-spec integration points. Companion to Spec 1. | Complete |
| **Capability Trust Profile** | Detailed trust lifecycle: ring buffer mechanics, promotion criteria with anti-gaming measures, demotion rules with failure attribution, counter reset behavior, startup reconciliation. Companion to Spec 2. | Complete |
| **NemoClaw Governance Overlay** | Maps DRNT governance concepts to NVIDIA's NemoClaw/OpenShell ecosystem, demonstrating interoperability between the WAL trust model and industry agent governance frameworks. | Complete |

## Key Architectural Decisions

Several decisions in the DRNT specifications address problems that apply to any local-first AI orchestration deployment, not just the personal-scale case:

**The log writer is a separate process with fail-closed semantics.** If the log writer is down, the orchestrator halts. This means the system cannot operate without logging — it is not possible for the orchestrator to silently bypass the audit trail. (Spec 1, Section 7)

**The Context Packager and egress gateway perform independent scans with distinct failure attribution.** A privacy failure (the Packager missed sensitive content) has trust consequences — the packaging capability is suspended. A transport failure (wrong route, token overflow) does not. Without this separation, every egress problem triggers a security investigation, and incident fatigue undermines governance. (Specs 3–4)

**Override-triggered demotion is conditional on failure type.** If an inspector re-routes a request because the cloud provider was down (an infrastructure failure), the routing capability is not demoted. Demotion only fires when the routing judgment itself was wrong. This prevents the trust system from punishing capabilities for problems they did not cause. (Spec 5, Section 6)

**Stale job re-dispatch must reuse the original idempotency key.** This was independently identified by both adversarial reviewers (Gemini and ChatGPT). Generating a new key for a crash-recovery re-dispatch instructs the cloud provider to treat it as a new request, causing double-execution of stateful operations. The fix is simple but architecturally significant — it prevents silent duplicate work after any unclean shutdown. (Spec 7, Section 4)

**WAL trust decays with inactivity.** A capability that earned WAL-2 but has not been exercised in months may be operating against stale assumptions. Temporal decay forces re-validation on an asymmetric schedule: higher autonomy levels have shorter windows because the consequences of stale trust are greater. Decay is not a failure signal — it is a separate mechanism that re-promotes quickly when usage resumes. (Spec 7, Part 2)

**Network isolation is topology, not filtering.** The orchestrator container is on an internal-only Docker network with no external access. Only the egress gateway has both internal and host-network interfaces. The orchestrator physically cannot reach the internet. This is a stronger security property than firewall rules, which can be misconfigured. (Spec 4, Section 3)

## Adversarial Review Methodology

Every major specification went through at least two independent model reviews, typically Gemini (Google) and ChatGPT (OpenAI). The review process is deliberately structured to surface genuine architectural gaps rather than generic feedback:

**Prompts name the target system explicitly.** Generic review prompts cause models to analyze the wrong system. Effective adversarial prompts list all source documents by name, require document-level citations, and structure output into defined sections.

**Each model has known biases that inform interpretation.** Gemini tends toward cloud-first solutions and generalized summarization. ChatGPT tends toward more disciplined structural analysis. Triangulating across reviewers with known lenses is more reliable than trusting any single source.

**Findings are categorized, not accepted wholesale.** Each finding is classified as Accept (incorporated), Partially Accept (modified and incorporated), or Reject (documented with reasoning). This prevents adversarial review from becoming adversarial compliance.

Spec 7 produced the most significant adversarial findings: ten accepted changes including the idempotency key reuse fix, offline decision replay requiring concurrency control, health probe hysteresis for volatile mobile networks, and hub suspension signaling to prevent split-brain execution on failover.

## External Validation

The architecture has received external engagement beyond the adversarial review process:

**NVIDIA NemoClaw Community.** RFC #442 (WAL Integration Profile for OpenShell) was submitted to the NemoClaw GitHub repository and triaged by an NVIDIA engineer with 'OpenShell' and 'enhancement: feature' labels. A subsequent comment from Ariel Jalali (Paragon Technology Solutions) confirmed that RFC #442 directly addressed a gap in his OpenShell policy design. The DRNT NemoClaw Governance Overlay maps DRNT's trust model to the OpenShell ecosystem.

**Cross-Domain Application.** The orchestration pattern has been applied and documented across four domains: a federal regulatory agency, a clinical environment, small-to-medium business (six-document framework series), and the personal-scale DRNT gateway. Each domain application validates the same core architecture against different constraint sets.

## What This Specification Suite Demonstrates

The DRNT specifications are not a product. They are a proof of concept that the local-first AI orchestration pattern — described at the reference architecture level in "[Why You Cannot Depend on a Single AI Model](why-you-cannot-depend-on-a-single-ai-model.md)" — can be specified to enforcement depth.

Specifically, the suite demonstrates that:

**Context control can be specified as an enforceable pipeline,** not a policy aspiration. The four-stage Context Packager with dual-scan verification and default-deny on unclassifiable content is a concrete, implementable mechanism. (Specs 3–4)

**Graduated trust can be specified as a state machine,** not a conceptual framework. The WAL model with per-action gates, measurable promotion criteria, automatic demotion triggers, and temporal decay is a concrete, auditable enforcement mechanism. (Spec 2, Capability Trust Profile, Spec 7 Part 2)

**An audit log can provide verifiable tamper evidence,** not just a compliance claim. The SHA-256 hash chain with a separate fail-closed log writer is a concrete guarantee that the audit trail is complete and unmodified. (Spec 1)

**Signal chain resilience can be specified for every failure mode,** not hand-waved as 'graceful degradation.' Every link in the signal chain has a defined detection method, user experience, in-flight job handling, and recovery procedure. (Spec 7 Part 1)

*The design provides the architecture. Implementation provides the evidence. The DRNT specification suite bridges the gap between the two — providing enough enforcement precision that an implementer knows exactly what to build, and an auditor knows exactly what to verify.*
