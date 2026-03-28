# DRNT — Personal AI Gateway

**Don't Reason, Navigate & Task**

Seven interface specifications and three governance artifacts implementing the local-first AI orchestration architecture at personal scale. DRNT is a wrist-and-pocket gateway — an Apple Watch and iPhone interface to a local AI orchestrator running on consumer desktop hardware — that routes requests to the right intelligence and returns results for human decision-making.

DRNT operates under no external compliance mandate. Its inclusion in this repository demonstrates that the governance components (append-only audit, graduated trust, structural privacy enforcement) remain valuable as self-imposed discipline, not only as regulatory response.

## Specifications

| Spec | Title | Description |
| --- | --- | --- |
| [Spec 1](DRNT_Spec1_Audit_Event_Schema.md) | Audit/Event Schema | Single event format for the append-only audit log. Every routing decision, WAL state change, context packaging action, model response, and human decision conforms to this schema. Cryptographically chained (SHA-256). JSONL on Docker volumes. |
| [Spec 2](DRNT_Spec2_Capability_Model.md) | Capability Model | Maps WAL levels (0–3) to concrete, enforceable permissions. Per-action gate model, capability registry, promotion criteria, demotion rules, and the permission check algorithm. |
| [Spec 3](DRNT_Spec3_Context_Boundary.md) | Context Boundary | Data structure of a request as it moves through the Context Packager. Sensitivity classification rules, memory selection policy, transform pipeline, and the contract between the Packager's primary scan and the egress gateway's secondary scan. |
| [Spec 4](DRNT_Spec4_Egress_Policy.md) | Egress Policy Binding | How Context Packager decisions become routing constraints at the network layer. Egress registry, gateway check sequence, Docker network topology, provider adapters, and forensic reconstruction. |
| [Spec 5](DRNT_Spec5_Override_Semantics.md) | Override Semantics | What happens when the human intervenes. Recording, rollback, WAL adjustment, successor job model, modified result lineage, and conflict resolution. |
| [Spec 6](DRNT_Spec6_Silo_Runtime_Security.md) | Silo Runtime Security | Enforcement model for the execution silo. Three complementary enforcement layers (runtime isolation, behavioral governance, structural privacy), skill lifecycle, worker egress policy, sandbox blueprints, and operator TUI. |
| [Spec 7](DRNT_Spec7_Signal_Chain_Resilience.md) |Signal Chain Resilience | Device failure modes across the Watch → iPhone → Desktop Hub → Cloud signal chain. Failure detection with hysteresis, stale job recovery, idempotency enforcement, MacBook fallback with split-brain prevention, offline decision replay with optimistic concurrency control, and WAL temporal decay. |

## Governance Artifacts

These three documents consolidate and extend the specifications into a complete governance series. The Event Schema supersedes Spec 1 v1.5. The Capability Trust Profile consolidates Spec 2. The NemoClaw Overlay maps DRNT governance onto external execution frameworks.

| Document | Description |
| --- | --- |
| [Event Schema v2.0](DRNT_Event_Schema.md) | Canonical reference for every event the DRNT governance layer can emit. Consolidates and supersedes Spec 1 v1.5 event definitions. Single schema for logging — a developer implementing the log writer writes to this and nothing else. |
| [Capability Trust Profile v1.0](DRNT_Capability_Trust_Profile.md) | Capability registry, WAL state machine, per-action permission matrix, and promotion/demotion rules. A developer implementing the permission checker and trust lifecycle writes to this spec. |
| [NemoClaw Governance Overlay v1.0](DRNT_NemoClaw_Governance_Overlay.md) | Architectural compatibility analysis mapping DRNT governance onto NVIDIA's NemoClaw/OpenShell execution infrastructure. Identifies where they align, where DRNT extends OpenShell, and where OpenShell fills gaps DRNT left to implementation. |

## Spec Dependencies
```
Spec 1 (Audit/Event Schema)
  └── Spec 2 (Capability Model)
       └── Spec 3 (Context Boundary)
            └── Spec 4 (Egress Policy)
                 └── Spec 5 (Override Semantics)
                      └── Spec 6 (Silo Runtime Security)
                           └── Spec 7 (Signal Chain Resilience)
```

Each spec depends on all preceding specs. Spec 1 is the foundation — all other specs write events to its schema. Spec 6 requires a schema version bump (1.5 → 1.6) to accommodate three new worker execution event types. Spec 7 extends Specs 1, 2, and 5: it adds `system.connectivity` and `system.hub_switch` events to the audit schema, adds `decay_policy` to the capability configuration, and defines version-stamp validation for offline override replay.

## Hardware Target

| Device | Role |
| --- | --- |
| **Apple Watch** | Glanceable status, voice input, job notifications. Zero processing. |
| **iPhone** | Primary input/output. Review results, make decisions, approve escalations. Network bridge to desktop. |
| **Windows Desktop** | L1 Orchestrator. Ryzen 7 7800X3D, RTX 5060 Ti 16GB, 64GB RAM. Runs Ollama, Docker, Open WebUI, vector DB. Always-on. |
| **MacBook Pro (M5)** | Fallback L1 orchestrator. Manual activation only — no automatic failover. Starts clean at WAL-0. |

## Status

Architecture specified. Not yet implemented. V1 scope is deliberately minimal: iPhone app + Watch app, local Ollama model for routing (7–13B quantized), API integrations to Claude/ChatGPT/Gemini, Context Packager, append-only audit log, and job-based UX with Watch notifications.

---

*Part of the [Local-First AI Orchestration](../../README.md) reference architecture.*
