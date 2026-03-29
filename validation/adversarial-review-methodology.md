# Adversarial Review Methodology

*How the architecture was validated and what changed as a result*

## The Process

The local orchestrator architecture was subjected to structured adversarial review by four independent AI models across five implementation domains. The purpose was design validation — attacking the architecture to find structural weaknesses before implementation.

### Reviewers

| Model | Provider | Observed Strengths | Observed Limitations |
|---|---|---|---|
| Claude | Anthropic | Most empirically grounded analysis. Strongest on risk identification and boundary definition. | Conservative bias — tends toward caution over actionability. |
| ChatGPT | OpenAI | Most structurally useful critiques. Produced actionable experimental designs and concrete MVP scoping. | Builder-oriented bias — sometimes optimized for "how to build it" over "whether it should be built this way." |
| Gemini | Google | Most theoretically ambitious. Identified long-horizon research implications. | Operationally miscalibrated — recommendations often exceeded the resource constraints of the target environments. |
| Copilot | Microsoft | Most structured, checklist-oriented. Best ROI for experimental planning per token spent. | Shallow coverage — competent but rarely surfaced novel concerns the other three missed. |

For detailed model bias profiles, comparative scoring, and the operational methodology behind this review process, see [Why One AI Is Not Enough](why-one-ai-is-not-enough.md).

### Domains Reviewed

1. **Federal Proposal** — Regulatory enforcement environment with safety-critical consequences and high task repetition.
2. **Medical Field CAOF** — Clinical environment with HIPAA constraints, patient safety stakes, and institutional review requirements.
3. **SMB Framework** — Six-document series for small business adoption with limited IT resources.
4. **DRNT Personal Gateway** — Personal-scale deployment with high question diversity, low repetition, and a single non-technical user.
5. **L2 Discrepancy Engine Specification** — The multi-model reconciliation layer itself, reviewed as a standalone technical specification.

### Protocol

Each review used identical prompts and identical document sets per domain. No model saw another model's review before producing its own. Prompts explicitly instructed adversarial critique — "You are being asked to ATTACK them" — and prohibited summarization, explanation, or praise. Documents were attached in the same order to control for positional effects.

After independent reviews were collected, a cross-domain reconciliation pass traced every structural concern across all implementations to distinguish documentation debt (framing problems) from architectural debt (design problems).

## Key Findings

### Convergent Finding: The Stealth Reasoning Problem

All four reviewers independently identified the same structural tension: the original core principle ("route, don't reason") was contradicted by what the architecture actually does. Intent classification is reasoning. Context selection is reasoning. Deciding whether a request can be answered locally is reasoning.

This convergent finding — the same critique from four models with different training biases — drove the most significant revision in the architecture's history: the v5.0 reframe from a binary principle (route vs. reason) to a spectrum principle based on error recoverability.

**Before (v1–v4):** "Route, don't reason."
**After (v5.0+):** "Know what you have, package what you need." The local model reasons within the recoverable-error boundary. Consequential-error reasoning belongs to cloud models or human judgment.

### Convergent Finding: Components Survived, Framing Did Not

The foundational components — Context Packager, WAL governance, append-only audit log, multi-model dispatch, and the core separation of local and cloud concerns — survived every adversarial review intact. No reviewer identified a component that should be removed or fundamentally redesigned.

What required revision was how the document described the local model's role. The architecture was already doing the right thing. The language was claiming something simpler than the reality.

### Divergent Finding: Model Biases Are Visible in Reviews

Different models produced meaningfully different critiques of the same documents. These differences are themselves evidence for the architecture's core premise: different models have different failure modes, and those failure modes are visible in the output.

- Claude flagged risks the other models normalized.
- ChatGPT proposed implementation paths the other models left abstract.
- Gemini identified theoretical implications the other models missed but proposed solutions that exceeded practical resource constraints.
- Copilot provided systematic coverage but rarely surfaced novel concerns.

### Finding: Attention Bias Toward Complexity

All four reviewers concentrated analytical attention on the most complex component (the L2 two-stage reconciliation protocol, receiving 80%+ of critique) while largely ignoring simpler components (Context Packager pipeline, memory governance, supply chain defense). A human reviewer caught this selection bias — the AI reviewers did not. This reinforces the architecture's principle that human judgment remains essential for meta-level oversight.

### Finding: The Semantic Ambiguity Edge Case

Reviewers correctly identified that some redaction decisions in the Context Packager appear to require semantic judgment (e.g., determining whether a number string is a benign product code or a sensitive medical identifier). The architecture handles this through default-deny: if deterministic rules cannot classify content, the content stays local. False positives (over-redacting) have recoverable consequences. False negatives (failing to redact sensitive data) have irreversible consequences. The system is deliberately calibrated to fail conservatively.

### Finding: Metadata Inference Risk in Audit Logs

The two-stream audit log design (metadata stream + encrypted content stream) was challenged on the grounds that metadata fields carry inferential risk even without raw sensitive content. If the metadata records that a request triggered "Oncology_Taxonomy" redaction rules, an observer can infer health information without decrypting anything. This finding was incorporated as an explicit acknowledgment with guidance to generalize metadata labels where specificity is not required for governance.

## What Changed

| Version | Change | Driven By |
|---|---|---|
| v3.0 | Explicit operational boundary definition (permitted vs. prohibited local model tasks) | Early review findings on ambiguous routing scope |
| v3.0 | Two-stream audit log architecture | Tension between replayability and privacy identified in review |
| v5.0 | Core principle reframed from "route, don't reason" to "know what you have, package what you need" | Convergent four-model finding on stealth reasoning |
| v5.0 | Error recoverability boundary replaces binary route/reason distinction | Same convergent finding |
| v5.0 | Stealth reasoning problem explicitly named and addressed | L2 adversarial review of claim normalization |
| v5.0 | Semantic ambiguity edge case acknowledged in Context Packager | Adversarial review of redaction pipeline |
| v5.0 | Metadata inference risk acknowledged in audit log design | Adversarial review of two-stream architecture |
| v5.0 | Storage lifecycle constraints for append-only logs addressed | Adversarial review of operational feasibility |
| v5.0 | Genre clarification added (reference architecture, not academic paper) | Reviewers applying academic validation expectations |
| v5.1 | Evaluation scaling principle added (Section 7.3) | Cross-domain reconciliation of Federal vs. DRNT evaluation needs |
| v5.1 | DRNT Lightweight Evaluation Loop referenced as personal-scale addendum | Same cross-domain reconciliation |
| v5.1.1 | Evaluation Loop characterized as analogous in purpose, not functionally equivalent, to formal harness | Precision correction on diagnostic capability claims |

## What This Methodology Is and Is Not

This is a design review methodology. It uses multiple AI models as independent architectural critics to surface structural weaknesses through adversarial analysis. It is not experimental validation — there are no datasets, benchmarks, or statistical analysis. The architecture makes design claims about properties of the pattern. Implementation will provide empirical evidence.

The methodology's value is in convergent findings. When four models with different training biases independently identify the same structural problem, the probability that the problem is real is high. When models diverge, the divergence itself is informative — it identifies areas where the design is ambiguous or where different analytical frameworks produce different conclusions.

The review process validated its own premise: multi-model comparison surfaces concerns that single-model review would miss, and model-specific biases are visible and predictable in the output.

---

*Full adversarial review documents are retained in the project archive. This summary covers the methodology, key findings, and architectural changes driven by the review process. For the full analysis of model biases, the operational methodology, and the case for why multi-model review matters, see [Why One AI Is Not Enough](why-one-ai-is-not-enough.md).*
