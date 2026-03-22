
[README.md](https://github.com/user-attachments/files/26163484/README.md)
# DRNT — Personal AI Gateway

**Don't Reason, Navigate & Task**

DRNT is a personal-scale implementation of the [route-don't-reason architecture](../../README.md). It is a wrist-and-pocket AI gateway — a three-device system that routes requests to the right intelligence and returns results for human decision-making.

The mental model is the Pip-Boy from the Fallout video game: the interface to everything, not the intelligence itself.

## Signal Chain

```
User → DRNT (Watch / Phone) → Local AI (Ollama on Desktop) → Cloud Agents as needed → User
```

The local model holds personal context, preferences, and memory. It classifies requests and decides what stays local and what gets packaged for cloud frontier models. Results return through the same chain to the watch or phone.

## The Five Specifications

These specs define the interface contracts between DRNT components. They are dependency-ordered — each spec builds on the ones before it.

| Spec | Document | Purpose |
|------|----------|---------|
| 1 | [Audit/Event Schema](Spec1_Audit_Event_Schema.md) | Single event format for the append-only audit log. The foundation — Specs 2–5 all write events to this schema. |
| 2 | [Capability Model](Spec2_Capability_Model.md) | Capability registry, WAL level permissions, promotion/demotion rules, and the per-action gate model. |
| 3 | [Context Boundary](Spec3_Context_Boundary.md) | Context Packager data flow, sensitivity classification, transform pipeline, and tamper-evident context store. |
| 4 | [Egress Policy Binding](Spec4_Egress_Policy.md) | Egress registry, Docker network topology, gateway check sequence, and forensic reconstruction chain. |
| 5 | [Override Semantics](Spec5_Override_Semantics.md) | Human override types, successor job model, conditional demotion, modified result lineage, and memory interactions. |

## Key Architectural Components

**Context Packager** — Structural privacy gate. All outbound data passes through it. Sensitive data is stripped before leaving the local environment. This is architectural enforcement, not a toggleable setting.

**Workflow Autonomy Levels (WAL 0–3)** — Graduated trust per capability. All new capabilities start at WAL-0 (recommend only). Promotion requires demonstrated reliability over calendar time. Anomalies trigger automatic demotion.

**Job-Based UX** — Interactions are jobs, not chat sessions. Submit → acknowledge → process → deliver. Jobs queue when connectivity is intermittent and resume without losing state.

**Append-Only Audit Log** — Every request, routing decision, model response, and human decision is logged with cryptographic hash chaining. Non-optional. Enables replay, accountability, and the earned-trust state machine governing WAL.

## Target Stack

| Component | Technology |
|-----------|------------|
| Host OS | Windows 11 Pro → WSL2 → Ubuntu |
| Containers | Docker Desktop with GPU passthrough |
| Local inference | Ollama with CUDA (7–13B quantized models) |
| Hardware | Ryzen 7 7800X3D, RTX 5060 Ti 16GB, 64GB RAM |
| Vector memory | ChromaDB or Qdrant |
| Orchestration | LangGraph or CrewAI |
| Remote access | Tailscale |
| Devices | Apple Watch, iPhone, Windows Desktop, MacBook Pro (M5 fallback) |

## Status

**FINAL** — These are reference specifications, stress-tested through multi-model adversarial review. Implementation is a separate effort in a separate repository.

## Relationship to the Reference Architecture

DRNT is one of several domain applications of the route-don't-reason pattern documented in this repository. The same foundational architecture has been applied at federal, clinical, SMB, and education scales. DRNT is the personal instance — built for one person, on hardware he owns, solving problems he encounters every day.
