# Small & Mid-Sized Business — AI Orchestration Framework

## Environment Profile

Small and mid-sized businesses with no dedicated IT staff, operating in potentially regulated industries (healthcare, legal, financial services, construction, insurance). The owner or practice manager is the decision-maker. They may have one generalist IT person or rely on a managed service provider. They have used ChatGPT or Copilot. They are skeptical but curious.

## Documents

This is a six-document series designed to be read and used independently. Each document serves a different purpose and audience.

| Document | Audience | Purpose |
|----------|----------|---------|
| [doc1-overview.md](doc1-overview.md) | Business owner | Introduces the concept: four layers, six friction points, trust tiers, business scenarios, phased roadmap |
| [doc2-readiness-assessment.md](doc2-readiness-assessment.md) | Business owner (alone) | Structured self-evaluation: current AI exposure, data risk, workflow readiness, compliance obligations |
| [doc3-reference-architecture.md](doc3-reference-architecture.md) | IT generalist or MSP | Non-code technical guide: hardware, open-source components, connection patterns, Phase 1 deployment |
| [doc4-vendor-evaluation.md](doc4-vendor-evaluation.md) | Business owner (in procurement) | Buyer's guide: mandatory questions, red flags, scoring rubric, good vs. evasive vendor responses |
| [doc5-regulated-supplement.md](doc5-regulated-supplement.md) | Owner + compliance advisor | Maps architecture to HIPAA, attorney-client privilege, GLBA — what's covered, what's not |
| [doc6-operations-governance.md](doc6-operations-governance.md) | Owner or office manager | Post-deployment: audit logs, WAL promotion/demotion, overreliance detection, incident response, quarterly review |
| [master-context.md](master-context.md) | Document authors | Canonical definitions, audience profile, scope boundaries — the shared foundation for the series |

## Design Principles

The SMB framework was deliberately adapted from the reference architecture with all government-specific language, agency references, and federal compliance citations removed. It preserves the core architectural pattern while translating it for a reader who:

- Does not have a dedicated IT staff
- Is primarily concerned with liability, data privacy, cost control, and vendor independence
- Needs to understand the concept before evaluating the cost
- Responds to concrete examples and plain analogies over technical diagrams

## The Six End-User Friction Points

Every document in the series is written with awareness of the documented failure modes that cause AI adoption to stall in practice:

1. **Prompt engineering burden** — staff must become AI experts to get usable results
2. **Context contamination** — the model injects irrelevant prior conversation history
3. **Generalist drift** — broad models produce average answers for specialized queries
4. **Reason drift** — regulatory questions answered by popular opinion, not authoritative sources
5. **No persistent memory** — months of context cannot be efficiently reloaded
6. **Manual process burden** — staff must copy, paste, file, and transfer AI outputs themselves

## What This Framework Is NOT

- **Not a government architecture.** All federal framing has been deliberately removed.
- **Not Intelligence Maximalism.** A powerful enough AI does not replace the orchestrator pattern.
- **Not a vendor recommendation.** Model-agnostic by design. Open-source tools named as examples, not endorsements.
- **Not a replacement for professional judgment.** WAL-0 is the default. Human review is the baseline.
