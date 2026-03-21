# Clinical Environment — Clinical AI Orchestration Framework (CAOF)

## Environment Profile

A major academic medical center with multi-specialty integrated care, complex multi-system patients, EHR-based clinical workflows, and institutional governance requirements spanning patient safety, privacy, regulatory compliance, and clinical informatics.

## Documents

| Document | Description |
|----------|-------------|
| [patient-narrative-redacted.md](patient-narrative-redacted.md) | A patient's documented experience of the information disconnect — the structural argument for why this architecture matters |
| [engineering-review.md](engineering-review.md) | Complete architectural specification: three-tier execution model, Context Packager, WAL framework, privacy architecture, phased implementation |
| [adversarial-appendix.md](adversarial-appendix.md) | Red team assessment: failure modes, service level commitments, rollback triggers, and architectural concessions |

## Reading Order

The documents are designed to be read in the order listed above. The patient narrative establishes the problem from lived experience. The engineering review provides the structural response. The adversarial appendix demonstrates that the architecture has been stress-tested and its failure modes are pre-committed.

## The Problem

When a complex multi-system patient presents for acute care, the treating physician faces a data assembly problem that current EHR interfaces were not designed to solve efficiently. Clinical notes exist in specialty-specific templates. The longitudinal narrative — the chronological story of how conditions interact — is trapped in unstructured free text across dozens of encounter notes. Prior institutional tests may fail to surface during acute presentations despite existing in the same system.

The patient narrative documents a specific instance: an acute cardiac presentation where the EKG comparison system selected a 14-year-old tracing while a comprehensive cardiopulmonary exercise test from 16 months earlier — performed at the same institution — was not surfaced. The treating physician's clinical judgment was sound. The information architecture failed to assemble what the institution already knew.

## Key Architectural Adaptations

The reference architecture required specific adaptations for clinical environments:

- **HIPAA as architectural driver.** The Context Packager enforces minimum necessary standard through structural redaction — deterministic preprocessing under institutional control, not policy instruction to a model. PHI is stripped before any payload crosses the network boundary.

- **Three-tier clinical execution model.** Tier 1 (deterministic retrieval from systems of record — zero hallucination risk), Tier 2 (probabilistic extraction with deterministic verification gates), Tier 3 (cloud-escalated synthesis, always presented as DRAFT-AI requiring clinician review and signature).

- **Immutable Source Layer and Patient Narrative Input Path.** Patient-provided inputs are stored as write-once source objects. The patient's exact words are preserved. Any derived clinical representation carries a mandatory provenance pointer back to the original.

- **Write path draft state model.** AI-generated clinical content follows a governed state progression: DRAFT-AI → DRAFT-REVIEWED → CANONICAL. Only the clinician's review and signature creates the legal medical record. The draft state is preserved in the audit trail, providing more medico-legal transparency than current dictation workflows.

- **FDA CDS awareness.** The architecture is designed to avoid crossing into autonomous diagnostic territory. The system supports clinical decision-making by assembling data — it does not independently generate clinical conclusions.

## Compliance Framework

- HIPAA Privacy Rule (minimum necessary standard, de-identification)
- HIPAA Security Rule (risk analysis, audit controls, access controls)
- FDA Clinical Decision Support Guidance (CDS criteria, independent review basis)
- NIST AI Risk Management Framework
- OWASP LLM Top 10 / MITRE ATLAS
- Institutional IRB and clinical governance requirements

## Adversarial Review History

The architecture was subjected to independent adversarial review by three AI models (Claude/Anthropic, GPT-4/OpenAI, Gemini/Google). Key findings incorporated include:

- FHIR write-back gap in the continuous improvement engine (Gemini finding)
- De-identification causal graph paradox for retained clinical context (Gemini finding)
- Inter-agent channel leakage in multi-model parallel dispatch (Gemini finding)
- Patient narrative input sanitization as a distinct pipeline stage (ChatGPT finding)
- FDA CDS device exposure under non-explainable routing (ChatGPT finding)
- Five incident response playbooks (ChatGPT finding)
- Clinical content log separation from security metadata (ChatGPT finding)

## Disclaimer

These documents describe a reference architecture for a *class* of clinical environment. They do not represent the position of any specific healthcare institution and were not commissioned, endorsed, or reviewed by any medical organization. The patient narrative contains the author's own medical information, shared with his consent. All architectural content is derived from publicly available clinical informatics standards, published regulations, and general knowledge of clinical environments.
