
[Spec2_Capability_Model.md](https://github.com/user-attachments/files/26163503/Spec2_Capability_Model.md)
# DRNT INTERFACE SPECIFICATION

## Spec 2: Capability Model (WAL → Permissions)

FINAL  •  March 2026  •  Depends on: Spec 1 (Audit/Event Schema)

# 1. Purpose

This spec defines the capability registry and maps each WAL level to concrete, enforceable permissions. Every WAL state change and permission check emits durable events to the audit log (Spec 1 Section 4.3). All WAL events require fsync + ACK before the gated action proceeds (Spec 1 Section 7.3).

# 2. Capabilities Are the Unit of Trust

Each capability has its own WAL level, promotion history, demotion triggers, and action policy set. WAL is never global.

## 2.1 Governing vs. Auxiliary

**Governing:**owns routing, delivery, retry, and model-selection actions. Assigned to a job at classification. Appears in the envelope’s capability_id. Determines the job’s trust ceiling.

**Auxiliary:**owns pipeline support actions (context packaging, memory I/O, transport notification, queue). Invoked as declared pipeline steps. Each check emits its own wal.permission_check with its own capability_id. Never in the envelope.

**Action ownership is exclusive.**No action appears in both a governing and auxiliary capability’s action set.

## 2.2 Dependency Modes

Each auxiliary invocation in a pipeline carries a dependency_mode:

| **Mode** |**Behavior on Block** |
| --- | --- |
| **required** | Pipeline halts. Job cannot proceed. Surfaces to user. |
| **optional** | Step skipped. Pipeline continues. Logged. |
| **best_effort** | Step attempted but failure does not block. Logged. |

# 3. Canonical Action Enum

Authority for the requested_action field in wal.permission_check. No action exists outside this list in v1.

## 3.1 Governing Actions

| **Action** |**Description** | **Owning Capabilities** |
| --- | --- | --- |
| dispatch_local | Send classified request to local Ollama | route.local |
| dispatch_cloud | Send packaged request to cloud endpoint | route.cloud.* |
| dispatch_multi | Send to multiple cloud endpoints | route.multi |
| select_model | Choose target model without surfacing decision | route.cloud.*, route.local |
| deliver_result | Deliver model response to user device | all governing |
| format_result | Apply user prefs to reshape result | all governing |
| auto_retry | Retry failed dispatch. V1: same capability only | all governing |

## 3.2 Auxiliary Actions

| **Action** |**Description** | **Owning Capability** |
| --- | --- | --- |
| package_context | Assemble and strip context for dispatch | context.package |
| read_memory | Read from vector memory | memory.read |
| write_memory | Write job-derived objects to memory | memory.write |
| send_notification | Send notification to transport | notify.watch, notify.phone |
| queue_job | Place job in queue | job.queue |

# 4. Per-Action Gate Model

Every action at every WAL level has a gate policy determining human involvement.

| **Gate** |**Semantics** | **Spec 1 Encoding** |
| --- | --- | --- |
| none | Action proceeds. No human involvement. | result: allowed, delivery_hold: false |
| pre_action | Action held until human approves. Nothing happens until approval. | result: held, hold_type: pre_action |
| pre_delivery | Action (dispatch) proceeds; result held before reaching user. Cost incurred but no downstream action until review. | result: allowed, delivery_hold: true |
| post_action | Action proceeds, result delivered. Review queued, not blocking. | result: allowed, delivery_hold: false |
| on_accept | Action fires only after user accepts/modifies result. Never on rejection. | result: held, hold_type: on_accept |
| cost_approval | Estimated cost exceeds cost_gate_usd. Hard pre-dispatch hold. | result: held, hold_type: cost_approval |

Action policies are objects in the registry: { review_gate, cost_gate_usd, retry_policy: { allowed, max_retries, same_capability_only } }. Local config may tighten spec defaults (stricter gates, lower cost caps, fewer retries) but not relax them. Startup validation rejects configs that relax gates.

# 5. WAL Permission Matrix (Spec Defaults)

## 5.1 WAL-0: Recommend Only

| **Action** |**Gate** | **Cost** |**Retry** | **Notes** |
| --- | --- | --- | --- | --- |
| dispatch_local | pre_delivery | n/a | none | Local runs; result held |
| dispatch_cloud | pre_delivery | $0.25 | none | Cloud call proceeds; result held |
| dispatch_multi | pre_delivery | $0.50 | none | Multiple calls; results held |
| select_model | pre_action | n/a | n/a | Human confirms routing choice |
| deliver_result | pre_delivery | n/a | n/a | Held until review |
| format_result | blocked | n/a | n/a | Raw output at WAL-0 |
| auto_retry | blocked | n/a | n/a | Failures surface to user |
| package_context | none | n/a | n/a | Preparation, not egress |
| read_memory | none | n/a | n/a | Local read |
| write_memory | blocked | n/a | n/a | No writes at WAL-0 |
| send_notification | none | n/a | n/a | Informational |
| queue_job | none | n/a | n/a | Infrastructure |

## 5.2 WAL-1: Draft and Assist (changes from WAL-0)

| **Action** |**Gate** | **Cost** |**Retry** | **Change** |
| --- | --- | --- | --- | --- |
| format_result | pre_delivery | n/a | n/a | User prefs applied before review |
| auto_retry | none | n/a | max 2, same cap | Auto-retry, same capability only |
| write_memory | on_accept | n/a | n/a | Writes after accept/modify only |

## 5.3 WAL-2: Execute Pre-Approved (changes from WAL-1)

| **Action** |**Gate** | **Cost** |**Retry** | **Change** |
| --- | --- | --- | --- | --- |
| deliver_result | post_action | n/a | n/a | Direct delivery; review queued |
| dispatch_cloud | post_action | $0.50 | max 3, same cap | Direct dispatch + delivery |
| select_model | none | n/a | n/a | Auto model selection |
| auto_retry | none | n/a | max 3, same cap | More retries |
| write_memory | none | n/a | n/a | Auto write on delivery |

## 5.4 WAL-3: Limited Autonomous (auxiliary only in v1)

| **Action** |**Gate** | **Change** |
| --- | --- | --- |
| send_notification | none | Unattended, batched, scheduled notifications |
| queue_job | none | Unattended queue management |

# 6. Capability Registry

## 6.1 Files

/var/drnt/config/capabilities.json        ← human-edited (desired state)

/var/drnt/config/capabilities.state.json   ← runtime-managed (effective state)

system.config_change emitted only for human-edited capabilities.json changes. Runtime state persistence does not emit this event.

## 6.2 desired_wal_level vs. effective_wal_level

**desired_wal_level**(capabilities.json): human intent. Changed by edit.

**effective_wal_level**(capabilities.state.json): runtime truth. Used by permission checker.

Startup reconciliation:

- Load both files. Compare config_hash against last system.startup’s config_hash.

- Config unchanged: effective authoritative. No WAL events.

- Config changed: per capability, desired > effective → wal.promoted, ACK, set effective = desired. desired < effective → wal.demoted(trigger: manual), ACK, set effective = desired.

- Suspended recovery: effective = -1, desired = 0 → wal.promoted(from: -1, to: 0).

- Write reconciled state.

## 6.3 Registry Entry Schema

```json
{
  "capability_id": "route.cloud.claude",
  "capability_type": "governing",
  "desired_wal_level": 0,
  "max_wal": 2,
  "declared_pipeline": [
    { "capability_id": "context.package", "dependency_mode": "required" },
    { "capability_id": "memory.read", "dependency_mode": "optional" },
    { "capability_id": "notify.phone", "dependency_mode": "best_effort" },
    { "capability_id": "notify.watch", "dependency_mode": "best_effort" }
  ],
  "provider_dependencies": null,
  "action_policies": {
    "0": { "dispatch_cloud": { "review_gate": "pre_delivery", "cost_gate_usd": 0.25,
             "retry_policy": { "allowed": false, "max_retries": 0, "same_capability_only": true } },
           "deliver_result": { "review_gate": "pre_delivery" },
           "select_model": { "review_gate": "pre_action" },
           "format_result": "blocked", "auto_retry": "blocked" }
  },
  "promotion_criteria": { ... per Section 9.1 ... }
}
```

**provider_dependencies:**null for single-provider. For route.multi: array of capability_ids. Each checked before dispatch; suspended providers excluded; all suspended = job blocked.

## 6.4 State Entry Schema

```json
{
  "capability_id": "route.cloud.claude",
  "effective_wal_level": 0, "status": "active",
  "counters": {
    "evaluable_outcomes": [ ... ring buffer, max 200 ... ],
    "recent_failures": [ ... deque, 24h eviction ... ],
    "first_job_date": null, "last_reset": "2026-04-01T00:00:00Z",
    "last_incident_source_event_id": null
  }
```

}

**evaluable_outcomes:**ring buffer of last 200 outcomes: {source_event_id, timestamp, disposition: accepted|modified|rejected|resubmitted|auto_delivered, cost_usd}. auto_delivered: WAL-2+ job delivered, not overridden within 24h.

**recent_failures:**deque with 24h eviction. 3 failures within a rolling 24-hour window triggers demotion. Successes between failures do NOT reset the count; only 24h eviction clears entries.

# 7. V1 Capability Inventory

## 7.1 Governing

| **capability_id** |**Description** | **Init** |**Max** | **Pipeline (dep_mode)** |
| --- | --- | --- | --- | --- |
| route.local | Answer via Ollama | 0 | 2 | mem.read(opt), notify.*(be) |
| route.cloud.claude | Dispatch to Claude | 0 | 2 | ctx.pkg(req), mem.read(opt), notify.*(be) |
| route.cloud.openai | Dispatch to OpenAI | 0 | 2 | ctx.pkg(req), mem.read(opt), notify.*(be) |
| route.cloud.gemini | Dispatch to Gemini | 0 | 2 | ctx.pkg(req), mem.read(opt), notify.*(be) |
| route.multi | Multi-model comparison | 0 | 1 | ctx.pkg(req), mem.read(opt), notify.*(be) |
| system.notify | System notifications | 0 | 3 | notify.*(req) |

## 7.2 Auxiliary

| **capability_id** |**Description** | **Init** |**Max** | **Evidence Type** |
| --- | --- | --- | --- | --- |
| context.package | Assemble/strip context | 0 | 2 | strip success rate |
| memory.read | Read vector memory | 0 | 2 | retrieval relevance |
| memory.write | Write to memory | 0 | 1 | write acceptance |
| notify.watch | Watch transport | 0 | 3 | delivery success |
| notify.phone | Phone transport | 0 | 3 | delivery success |
| job.queue | Job queuing | 0 | 3 | queue drain success |

# 8. Enforcement: Permission Check

```python
def check_permission(cap_id, action, job_ctx):
    cap = registry.get(cap_id)
    if not cap: return emit_blocked("unknown_capability")
    if cap.effective_wal == -1: return emit_blocked("suspended")
    policy = cap.action_policies[cap.effective_wal].get(action)
    if not policy or policy == "blocked":
        return emit_blocked("action_not_permitted")
    if policy.cost_gate_usd and job_ctx.est_cost > policy.cost_gate_usd:
        return emit_held(hold_type="cost_approval")
    match policy.review_gate:
        case "pre_action":
            if not job_ctx.human_approved:
                return emit_held(hold_type="pre_action")
            return emit_allowed()
        case "pre_delivery":
            return emit_allowed(delivery_hold=True)
        case "post_action": return emit_allowed()
        case "on_accept":
            if not job_ctx.result_accepted:
                return emit_held(hold_type="on_accept")
            return emit_allowed()
        case "none": return emit_allowed()
```

Every call emits a durable wal.permission_check. Pipeline processed in declared order; most restrictive wins; dependency_mode controls block/skip/continue.

**Same-capability retry (v1):**same_capability_only: true. Cross-capability retry is v2.

**route.multi provider check:**each provider_dependency checked before dispatch. Suspended = excluded. All suspended = blocked.

# 9. Promotion and Evidence

Promotion is always a human decision. Counters/window state are acceleration hints. The audit log is authoritative.

## 9.1 Governing Promotion Criteria

| **Trans** |**Required (ALL)** | **Anti-Gaming** |
| --- | --- | --- |
| **0→1** | 30 evaluable outcomes, span ≥ 7 calendar days Approval score ≥ 0.95 Zero strip failures (failing_capability_id non-null) Zero cancel/redirect overrides | Calendar spread prevents cramming modified = 0.5 weight |
| **1→2** | 100 outcomes at WAL-1, ≥ 30 days Score ≥ 0.98 on most recent 100 Zero strip failures, zero incidents ≥ 1 edge case handled | Rolling 100 window Any demotion resets period |
| **2→3** | Not available for governing in v1 (max_wal ≤ 2) | Explicit ceiling |

## 9.2 Approval Score

accepted=1.0, modified=0.5, rejected=0.0

resubmitted=0.0, auto_delivered=1.0

score = sum(weights) / len(evaluable_outcomes)

## 9.3 Auxiliary Evidence Criteria

| **Capability** |**0→1** | **1→2 / 2→3** |
| --- | --- | --- |
| context.package | 30 packages, zero egress catches, ≥ 7 days | 100 at WAL-1, 30d, zero catches |
| memory.read | 30 reads, < 10% missing-context feedback, ≥ 7 days | 100 at WAL-1, 30d, < 5% |
| memory.write | 30 writes (on_accept), zero corrections, ≥ 7 days | Max WAL-1 in v1 |
| notify.* | 30 deliveries, zero suppression overrides, ≥ 7 days | 100 deliveries, 30/90d, < 1% failures |
| job.queue | 30 drain cycles, zero lost jobs, ≥ 7 days | 100 cycles, 30/90d, zero lost |

## 9.4 Promotion Flow

- Orchestrator detects criteria likely met. Creates job governed by system.notify with recommendation.

- Human reviews. Edits desired_wal_level in capabilities.json. Restarts.

- Startup reconciliation emits durable wal.promoted, sets effective = desired after ACK.

# 10. Demotion Rules

## 10.1 Automatic Triggers

| **Trigger** |**Target** | **Action** |**Recovery** |
| --- | --- | --- | --- |
| Strip failure (egress catch) | **context.package** |**SUSPEND** | Root cause + manual WAL-0 + restart |
| Override: cancel/redirect on WAL-1+ (conditional—see Spec 5 Section 5) | **Governing cap** |**Demote 1** | Counter reset, re-earn |
| 3 failures in rolling 24h window | **failing_capability_id** |**Demote 1** | Investigate, counter reset |
| Model change | **All affected_capabilities** |**WAL-0** | Full re-promotion |
| WAL-3 approval < 99% / 90d | **The WAL-3 cap** |**WAL-2** | Re-earn WAL-3 |

**Attribution:**failures attribute to failing_capability_id. Sentinel values ("egress_config", "egress_connectivity") are excluded from counters—only registered capability_ids increment failure counts.

**Suspension:**wal.demoted with to_level: -1. Fully inoperable. Recovery: edit desired_wal_level to 0, restart, reconciliation emits wal.promoted(from: -1, to: 0).

## 10.2 Counter Reset

On any demotion: evaluable_outcomes cleared, recent_failures cleared, first_job_date null, last_reset set. wal_history and last_incident preserved.

# 11. Startup Validation

- No gate relaxation beyond spec defaults

- desired_wal_level ≤ max_wal

- declared_pipeline references registered auxiliaries

- provider_dependencies reference registered governing capabilities

- Exclusive action ownership

- All actions from canonical enum (Section 3)

- credential class in sensitivity.json has hardcoded: true, default_action: strip

Validation failures prevent startup.

# 12. Scope Boundaries and Cross-References

- Classification logic / task taxonomy (operational config)

- Context rules and sensitivity taxonomy (Spec 3)

- Egress endpoint registry and route_id binding (Spec 4)

- Override mechanics, successor jobs, conditional demotion (Spec 5)

- Feedback-to-behavior loops (Evaluation Loop document)

*End of Spec 2 — FINAL.*
