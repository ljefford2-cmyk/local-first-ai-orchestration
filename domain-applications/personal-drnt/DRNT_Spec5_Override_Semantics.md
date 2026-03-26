# DRNT Interface Specification

## Spec 5: Override Semantics

**FINAL** · March 2026 · Depends on: Spec 1, Spec 2, Spec 3, Spec 4

---

## 1. Purpose

What happens when you intervene from watch or phone: recording, rollback, WAL adjustment, and conflict resolution. Every override is durable. The governing principle: the human decides. DRNT complies immediately and records.

## 2. Override Types

| Type | Meaning | When |
| --- | --- | --- |
| `cancel` | Abort job. Discard results. Post-delivery: becomes revocation. | Any state through response_received. Post-delivery: revoke. |
| `redirect` | Reject routing. Terminate original. Spawn successor with new governing capability. | Before or after delivery. Spawns successor. |
| `modify` | Accept but edit. Both original and modified stored as first-class artifacts. | During review or post-delivery. Full artifact lineage. |
| `escalate` | Request higher quality. Spawn successor with more capable target. | During review or post-delivery. |

## 3. The Successor Job Model

Redirect and escalate terminate the original job and spawn a successor with a new `job_id` + `parent_job_id`. The original's audit trail is complete and immutable. The successor gets its own `job.classified`, `context.packaged`, `job.dispatched` chain with its own governing capability. This preserves the one-job-one-governing-capability invariant.

```
Original (J1, route.cloud.claude):
  job.submitted → job.classified → context.packaged → job.dispatched
  → human.override(redirect) → job.failed(redirected)

Successor (J2, parent: J1, route.cloud.openai):
  job.classified → context.packaged → job.dispatched
  → model.response → job.delivered → human.reviewed
```

## 4. Three Terminal States

- **`job.delivered`:** execution succeeded. Result presented.
- **`job.failed`:** execution did not complete, or pre-delivery cancel/redirect.
- **`job.revoked`:** execution completed and delivered, but human later withdrew. Not a failure—a post-hoc decision.

## 5. The Irreversibility Boundary

`job.dispatched` = data left local. Before: overrides prevent egress. After: overrides control response handling, cannot recall data. User gets informational notification, not confirmation.

## 6. Conditional Demotion

| Override | Condition | WAL Effect | Rationale |
| --- | --- | --- | --- |
| **cancel** | Viable route / held result (no prior sentinel egress failure) | Demote 1 | Routing judgment wrong. |
| **cancel** | Prior sentinel egress failure (egress_config, egress_connectivity) | No demotion | Infrastructure issue, not routing. |
| **redirect** | Viable route / held result | Demote 1 | User rejected routing choice. |
| **redirect** | Prior sentinel failure | No demotion | Recovery from outage. |
| **modify** | Any | None | Quality feedback. Score: 0.5. |
| **escalate** | Any | None | Quality feedback. Score: 0.0. |

The orchestrator checks: did the prior `job.failed` carry a sentinel `failing_capability_id`? If yes, no demotion.

## 7. Modified Result Lineage

`human.reviewed(modified)` carries `modified_result_id` (UUIDv7), `modified_result_hash` (SHA-256), `derived_from_result_id` (original). Both artifacts in results store. Both hashes in audit log. Full diffing always possible.

## 8. Memory Write Interactions

- **WAL-1 (on_accept):** writes after accept/modify only. Cancel/redirect before acceptance = no write.
- **WAL-2 (none):** writes on delivery. modify → supersede + new write. escalate → pending_replacement (supersede on successor accept, revert on reject). revoke → supersede.

Memory objects are never deleted. Superseded or pending. Full lineage preserved.

## 9. Auto-Accept Window (WAL-2+)

Default 24 hours from delivery. Configurable per capability. No override within window: `auto_delivered` (score 1.0). Override within window: modify (0.5), escalate (0.0), cancel/revoke (0.0 + conditional demotion).

## 10. Watch and Phone Overrides

**Watch:** dismiss (swipe) = cancel. Tap "Phone" = transfer for full surface. No redirect/modify/escalate on watch.

**Phone:** cancel (confirmation required), redirect (select target), modify (edit inline), escalate (select target). All four available.

**Offline:** queue on device, flush on reconnection. Retroactive cancel on delivered job → `job.revoked(offline_retroactive)`. Human decision at time T takes precedence.

**Two overrides on one job:** first processed wins. Second logged but no-op on terminal job.

## 11. Override Conflicts

Override wins immediately in all cases. During packaging: Packager stops. During gateway: abort if not yet transmitted, else override-after-dispatch. During model wait: response arrives, is discarded. Cost already incurred.

## 12. Event Sequence: Redirect After Dispatch

```
ORIGINAL (J1, route.cloud.claude):
  1. job.submitted(J1)
  2. job.classified(J1, governing: route.cloud.claude)
  3. context.packaged(J1, pkg: P1, payload_hash: H1)
  4. job.dispatched(J1, route: claude-sonnet, hash: H1)
  5. human.override(J1, redirect, detail: openai)
  6. wal.demoted(route.cloud.claude, trigger: override)
  7. job.failed(J1, redirected after dispatch)

SUCCESSOR (J2, parent: J1, route.cloud.openai):
  8.  job.classified(J2, governing: route.cloud.openai)
  9.  context.packaged(J2, pkg: P2, payload_hash: H2)
  10. job.dispatched(J2, route: openai-gpt4o, hash: H2)
  11. model.response(J2)
  12. job.delivered(J2)
  13. human.reviewed(J2, accepted)
```

Both dispatches in audit log. Both payloads in context store.

## 13. Event Sequence: Modify During Review

```
  1-4. [submitted, classified, packaged, dispatched]
  5. job.response_received(J1, result_id: R1, hash: H_R1)
  6. human.override(J1, modify)
  7. human.reviewed(J1, modified,
       derived_from: R1, modified_result_id: R2, hash: H_R2)
  8. job.delivered(J1, gate: pre_delivery)
```

Results store: R1 (model) + R2 (human-edited). Both hashable.

## 14. Event Sequence: Post-Delivery Escalate (WAL-2+)

```
ORIGINAL (J1, WAL-2):
  1-4. [submitted, classified, packaged, dispatched]
  5. model.response(J1, result_id: R1)
  6. job.delivered(J1, gate: post_action)
  7. [memory.write: M1 from R1]
  8. human.override(J1, escalate, detail: route.multi)
  9. job.revoked(J1, escalation_supersede, successor: J2, superseded: [M1])

SUCCESSOR (J2, parent: J1, route.multi):
  10-14. [classified, packaged, dispatched x2, responses]
  15. job.delivered(J2)
  16. human.reviewed(J2, accepted)
  17. [M1 superseded, M2 written from J2]
```

## 15. Scope Boundaries

- Watch/phone UX design details (interaction design)
- Escalation target selection (routing intelligence)
- Cloud provider request cancellation (none reliably support it)
- Memory governance beyond supersede/pending (future spec)
- Override interaction with in-flight worker agents (Spec 6 Section 9.5)

---

*End of Spec 5 — FINAL. Part of the DRNT Interface Specification series: Spec 1 (Audit/Event Schema), Spec 2 (Capability Model), Spec 3 (Context Boundary), Spec 4 (Egress Policy Binding), Spec 5 (Override Semantics), Spec 6 (Silo Runtime Security).*
