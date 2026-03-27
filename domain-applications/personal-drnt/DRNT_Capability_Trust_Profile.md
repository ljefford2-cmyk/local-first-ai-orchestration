# DRNT Capability Trust Profile

**Version 1.0** · March 2026 · Build Target: Docker on WSL2/Ubuntu

Depends on: [DRNT Event Schema v2.0](DRNT_Event_Schema.md)

Defines the capability registry, the WAL state machine, the per-action permission matrix, and the promotion/demotion rules. A developer implementing the permission checker and trust lifecycle writes to this spec.

---

## 1. Core Invariant

**Capabilities are the unit of trust. WAL is never global.**

Each capability has its own WAL level, promotion history, demotion triggers, and action policy set. A job may route through `route.cloud.claude` at WAL-1 while `context.package` operates at WAL-0 and `notify.watch` at WAL-3. The system composes these per-capability trust levels for each job.

---

## 2. Governing vs. Auxiliary Capabilities

### 2.1 Governing

Owns routing, delivery, retry, and model-selection actions. Assigned to a job at `job.classified`. Appears in the event envelope's `capability_id`. Determines the job's trust ceiling.

### 2.2 Auxiliary

Owns pipeline support actions: context packaging, memory I/O, transport notification, queue management. Invoked as declared pipeline steps. Each auxiliary check emits its own `wal.permission_check` with its own `capability_id` in the **payload** — never in the envelope.

### 2.3 Exclusive Action Ownership

No action appears in both a governing and auxiliary capability's action set. Startup validation enforces this.

### 2.4 Dependency Modes

Each auxiliary invocation in a pipeline carries a dependency mode that determines behavior when the auxiliary blocks:

| Mode | Behavior on Block |
|------|-------------------|
| `required` | Pipeline halts. Job cannot proceed. Surfaces to user. |
| `optional` | Step skipped. Pipeline continues. Logged. |
| `best_effort` | Step attempted but failure does not block. Logged. |

Pipeline is processed in declared order. Most restrictive result wins.

---

## 3. Canonical Action Enum

Authority for the `requested_action` field in `wal.permission_check`. No action exists outside this list in v1.

### 3.1 Governing Actions

| Action | Description | Owning Capabilities |
|--------|-------------|---------------------|
| `dispatch_local` | Send classified request to local Ollama | `route.local` |
| `dispatch_cloud` | Send packaged request to cloud endpoint | `route.cloud.*` |
| `dispatch_multi` | Send to multiple cloud endpoints | `route.multi` |
| `select_model` | Choose target model without surfacing decision | `route.cloud.*`, `route.local` |
| `deliver_result` | Deliver model response to user device | all governing |
| `format_result` | Apply user prefs to reshape result | all governing |
| `auto_retry` | Retry failed dispatch (v1: same capability only) | all governing |

### 3.2 Auxiliary Actions

| Action | Description | Owning Capability |
|--------|-------------|-------------------|
| `package_context` | Assemble and strip context for dispatch | `context.package` |
| `read_memory` | Read from vector memory | `memory.read` |
| `write_memory` | Write job-derived objects to memory | `memory.write` |
| `send_notification` | Send notification to transport | `notify.watch`, `notify.phone` |
| `queue_job` | Place job in queue | `job.queue` |

---

## 4. Gate Model

Every action at every WAL level has a gate policy determining human involvement.

| Gate | Semantics | Event Schema Encoding |
|------|-----------|----------------------|
| `none` | Action proceeds. No human involvement. | `result: allowed`, `delivery_hold: false` |
| `pre_action` | Action held until human approves. Nothing happens until approval. | `result: held`, `hold_type: pre_action` |
| `pre_delivery` | Action (dispatch) proceeds; result held before reaching user. Cost incurred but no downstream action until review. | `result: allowed`, `delivery_hold: true` |
| `post_action` | Action proceeds, result delivered. Review queued, not blocking. | `result: allowed`, `delivery_hold: false` |
| `on_accept` | Action fires only after user accepts/modifies result. Never on rejection. | `result: held`, `hold_type: on_accept` |
| `cost_approval` | Estimated cost exceeds `cost_gate_usd`. Hard pre-dispatch hold. | `result: held`, `hold_type: cost_approval` |

Action policies are objects in the registry:

```json
{
  "review_gate": "pre_delivery",
  "cost_gate_usd": 0.25,
  "retry_policy": {
    "allowed": false,
    "max_retries": 0,
    "same_capability_only": true
  }
}
```

**Local config may tighten spec defaults** (stricter gates, lower cost caps, fewer retries) **but never relax them.** Startup validation rejects configs that relax gates.

**Irreversibility boundary:** `job.dispatched` is the point where data has left the local environment. Before this event, overrides prevent egress. After this event, overrides control response handling but cannot recall data already transmitted. This boundary is the same in all three governance artifacts.

---

## 5. WAL Permission Matrix

### 5.1 WAL-0: Recommend Only

Everything dispatches but all results are held for human review. The system generates answers; the human decides what to do with them.

**Semantic precision:** WAL-0 is "recommend-only" with respect to consequential user-visible action — no result reaches the user without review, no memory writes occur, no formatting is applied. It is *not* recommend-only with respect to internal compute or bounded cloud inference. Cloud dispatch proceeds (incurring real cost) because the system must generate a result before the human can review it. The `pre_delivery` gate ensures the human sees and decides before anything downstream happens.

| Action | Gate | Cost Gate | Retry | Notes |
|--------|------|-----------|-------|-------|
| `dispatch_local` | `pre_delivery` | n/a | none | Local runs; result held |
| `dispatch_cloud` | `pre_delivery` | $0.25 | none | Cloud call proceeds; result held |
| `dispatch_multi` | `pre_delivery` | $0.50 | none | Multiple calls; results held |
| `select_model` | `pre_action` | n/a | n/a | Human confirms routing choice |
| `deliver_result` | `pre_delivery` | n/a | n/a | Held until review |

**`select_model` at WAL-0:** For single-route capabilities where `job.classified` deterministically selects the model (e.g., `route.cloud.claude` always targets Claude), the `pre_action` gate on `select_model` still fires a `wal.permission_check` event. The human approval may be satisfied implicitly when the user submitted the request through a capability-specific channel, or explicitly through a routing confirmation tap. The `wal.permission_check` event records the outcome either way. At WAL-1+, `select_model` retains `pre_action` until WAL-2, where it becomes `none` (auto model selection).
| `format_result` | **blocked** | n/a | n/a | Raw output at WAL-0 |
| `auto_retry` | **blocked** | n/a | n/a | Failures surface to user |
| `package_context` | `none` | n/a | n/a | Preparation, not egress |
| `read_memory` | `none` | n/a | n/a | Local read |
| `write_memory` | **blocked** | n/a | n/a | No writes at WAL-0 |
| `send_notification` | `none` | n/a | n/a | Informational |
| `queue_job` | `none` | n/a | n/a | Infrastructure |

### 5.2 WAL-1: Draft and Assist

Changes from WAL-0:

| Action | Gate | Cost Gate | Retry | Change |
|--------|------|-----------|-------|--------|
| `format_result` | `pre_delivery` | n/a | n/a | User prefs applied before review |
| `auto_retry` | `none` | n/a | max 2, same cap | Auto-retry on failure |
| `write_memory` | `on_accept` | n/a | n/a | Writes only after accept/modify |

### 5.3 WAL-2: Execute Pre-Approved

Changes from WAL-1:

| Action | Gate | Cost Gate | Retry | Change |
|--------|------|-----------|-------|--------|
| `deliver_result` | `post_action` | n/a | n/a | Direct delivery; review queued |
| `dispatch_cloud` | `post_action` | $0.50 | max 3, same cap | Direct dispatch + delivery |
| `select_model` | `none` | n/a | n/a | Auto model selection |
| `auto_retry` | `none` | n/a | max 3, same cap | More retries |
| `write_memory` | `none` | n/a | n/a | Auto write on delivery |

### 5.4 WAL-3: Limited Autonomous

**Auxiliary capabilities only in v1.** No governing capability can reach WAL-3 (`max_wal ≤ 2`).

| Action | Gate | Change |
|--------|------|--------|
| `send_notification` | `none` | Unattended, batched, scheduled notifications |
| `queue_job` | `none` | Unattended queue management |

---

## 6. V1 Capability Inventory

### 6.1 Governing Capabilities

| `capability_id` | Description | Init WAL | Max WAL | Pipeline |
|------------------|-------------|----------|---------|----------|
| `route.local` | Answer via Ollama | 0 | 2 | `memory.read`(opt), `notify.*`(be) |
| `route.cloud.claude` | Dispatch to Claude | 0 | 2 | `context.package`(req), `memory.read`(opt), `notify.*`(be) |
| `route.cloud.openai` | Dispatch to OpenAI | 0 | 2 | `context.package`(req), `memory.read`(opt), `notify.*`(be) |
| `route.cloud.gemini` | Dispatch to Gemini | 0 | 2 | `context.package`(req), `memory.read`(opt), `notify.*`(be) |
| `route.multi` | Multi-model comparison | 0 | 1 | `context.package`(req), `memory.read`(opt), `notify.*`(be) |

### 6.2 Operational Capabilities

`system.notify` is classified as **operational**, not governing. It does not own routing, model selection, or delivery for user-submitted jobs. It exists to deliver system-generated messages (promotion recommendations, health alerts, integrity check results) through the same pipeline infrastructure that governing capabilities use. It participates in WAL and emits `wal.permission_check` events, but it never appears as a job's `capability_id` in the envelope for user-initiated jobs.

| `capability_id` | Description | Init WAL | Max WAL | Pipeline |
|------------------|-------------|----------|---------|----------|
| `system.notify` | System-generated notifications | 0 | 3 | `notify.*`(req) |

### 6.3 Auxiliary Capabilities

| `capability_id` | Description | Init WAL | Max WAL | Evidence Type |
|------------------|-------------|----------|---------|---------------|
| `context.package` | Assemble/strip context | 0 | 2 | Strip success rate |
| `memory.read` | Read vector memory | 0 | 2 | Retrieval relevance |
| `memory.write` | Write to memory | 0 | 1 | Write acceptance |
| `notify.watch` | Watch transport | 0 | 3 | Delivery success |
| `notify.phone` | Phone transport | 0 | 3 | Delivery success |
| `job.queue` | Job queuing | 0 | 3 | Queue drain success |

---

## 7. Capability Registry

### 7.1 Files

```
/var/drnt/config/capabilities.json        ← human-edited (desired state)
/var/drnt/config/capabilities.state.json   ← runtime-managed (effective state)
```

`system.config_change` emitted only for human-edited `capabilities.json` changes. Runtime state persistence does NOT emit this event.

### 7.2 Desired vs. Effective WAL

**`desired_wal_level`** (in `capabilities.json`): human intent. Changed by manual edit.

**`effective_wal_level`** (in `capabilities.state.json`): runtime truth. Used by the permission checker.

**Startup reconciliation:**

1. Load both files. Compare `config_hash` against last `system.startup`'s `config_hash`.
2. Config unchanged → effective is authoritative. No WAL events.
3. Config changed → per capability:
   - `desired > effective` → emit `wal.promoted`, ACK, set `effective = desired`
   - `desired < effective` → emit `wal.demoted(trigger: manual)`, ACK, set `effective = desired`
   - Suspended recovery: `effective = -1`, `desired = 0` → emit `wal.promoted(from: -1, to: 0)`
4. Write reconciled state.

### 7.3 Registry Entry Schema

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
    "0": {
      "dispatch_cloud": {
        "review_gate": "pre_delivery",
        "cost_gate_usd": 0.25,
        "retry_policy": { "allowed": false, "max_retries": 0, "same_capability_only": true }
      },
      "deliver_result": { "review_gate": "pre_delivery" },
      "select_model": { "review_gate": "pre_action" },
      "format_result": "blocked",
      "auto_retry": "blocked"
    }
  },
  "promotion_criteria": { }
}
```

**`provider_dependencies`:** Null for single-provider capabilities. For `route.multi`: array of `capability_id`s. Each checked before dispatch; suspended providers excluded; all suspended = job blocked.

### 7.4 State Entry Schema

```json
{
  "capability_id": "route.cloud.claude",
  "effective_wal_level": 0,
  "status": "active",
  "counters": {
    "evaluable_outcomes": [],
    "recent_failures": [],
    "first_job_date": null,
    "last_reset": "2026-04-01T00:00:00Z",
    "last_incident_source_event_id": null
  }
}
```

**`evaluable_outcomes`:** Ring buffer, max 200 entries. Each entry: `{source_event_id, timestamp, disposition, cost_usd}`. Dispositions: `accepted` (1.0), `modified` (0.5), `rejected` (0.0), `resubmitted` (0.0), `auto_delivered` (1.0). `auto_delivered` = WAL-2+ job delivered and not overridden within 24h.

**`recent_failures`:** Deque with 24h eviction. 3 failures within a rolling 24-hour window triggers demotion. Successes between failures do NOT reset the count — only 24h time eviction clears entries.

---

## 8. Permission Check Algorithm

```python
def check_permission(cap_id, action, job_ctx):
    cap = registry.get(cap_id)
    if not cap:
        return emit_blocked("unknown_capability")

    if cap.effective_wal == -1:
        return emit_blocked("suspended")

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
        case "post_action":
            return emit_allowed()
        case "on_accept":
            if not job_ctx.result_accepted:
                return emit_held(hold_type="on_accept")
            return emit_allowed()
        case "none":
            return emit_allowed()
```

Every call emits a durable `wal.permission_check` to the audit log. The gated action does not proceed until fsync + ACK.

**`route.multi` provider check:** Each `provider_dependency` is checked before dispatch. Suspended providers are excluded. If all providers are suspended, the job is blocked.

---

## 9. Promotion Rules

**Promotion is always a human decision.** Counters and window state are acceleration hints. The audit log is authoritative.

### 9.1 Governing Promotion Criteria

The following thresholds are **v1 reference defaults**. Deployments may tighten these values (higher outcome counts, longer spans, stricter scores) but may not relax them without explicit operator acknowledgment logged as a `system.config_change` event. The numeric values are policy baselines calibrated for personal-scale use, not universal constants.

| Transition | Required (ALL must be met) | Anti-Gaming |
|-----------|---------------------------|-------------|
| **0 → 1** | 30 evaluable outcomes, span ≥ 7 calendar days. Approval score ≥ 0.95. Zero strip failures (`failing_capability_id` non-null). Zero cancel/redirect overrides. | Calendar spread prevents cramming. `modified` = 0.5 weight. |
| **1 → 2** | 100 outcomes at WAL-1, span ≥ 30 days. Score ≥ 0.98 on most recent 100. Zero strip failures, zero incidents. ≥ 1 edge case handled. | Rolling 100 window. Any demotion resets the period. |
| **2 → 3** | Not available for governing in v1 (`max_wal ≤ 2`). | Explicit ceiling. |

### 9.2 Approval Score Calculation

```
accepted     = 1.0
modified     = 0.5
rejected     = 0.0
resubmitted  = 0.0
auto_delivered = 1.0

score = sum(weights) / len(evaluable_outcomes)
```

### 9.3 Auxiliary Promotion Criteria

| Capability | 0 → 1 | 1 → 2 / 2 → 3 |
|-----------|-------|----------------|
| `context.package` | 30 packages, zero egress catches, ≥ 7 days | 100 at WAL-1, 30d, zero catches |
| `memory.read` | 30 reads, < 10% missing-context feedback, ≥ 7 days | 100 at WAL-1, 30d, < 5% |
| `memory.write` | 30 writes (on_accept), zero corrections, ≥ 7 days | Max WAL-1 in v1 |
| `notify.*` | 30 deliveries, zero suppression overrides, ≥ 7 days | 100 deliveries, 30/90d, < 1% failures |
| `job.queue` | 30 drain cycles, zero lost jobs, ≥ 7 days | 100 cycles, 30/90d, zero lost |

### 9.4 Promotion Flow

1. Orchestrator detects criteria likely met. Creates a job governed by `system.notify` with a promotion recommendation.
2. Human reviews evidence. Edits `desired_wal_level` in `capabilities.json`. Restarts.
3. Startup reconciliation emits durable `wal.promoted`, sets `effective = desired` after ACK.

The human never needs to compute promotion criteria manually — the system surfaces a recommendation with evidence. But the human makes the decision and commits it by editing the config file. There is no auto-promotion.

---

## 10. Demotion Rules

### 10.1 Automatic Triggers

| Trigger | Target | Action | Recovery |
|---------|--------|--------|----------|
| Strip failure (egress catch: `context.strip_detail` with `detected_by: egress_gateway`) | `context.package` | **SUSPEND** (to `-1`) | Root cause analysis, manual edit to WAL-0, restart |
| Override: cancel/redirect on WAL-1+ (conditional — see 10.3) | Governing capability | **Demote 1** | Counter reset, re-earn |
| 3 failures in rolling 24h window | `failing_capability_id` | **Demote 1** | Investigate, counter reset |
| Model change (`system.model_change`) | All `affected_capabilities` | **WAL-0** | Full re-promotion from scratch |
| WAL-3 approval score < 99% over 90d | The WAL-3 capability | **WAL-2** | Re-earn WAL-3 |

### 10.2 Counter Reset

On any demotion:
- `evaluable_outcomes` → cleared
- `recent_failures` → cleared
- `first_job_date` → null
- `last_reset` → set to current timestamp
- `wal_history` and `last_incident` → preserved

### 10.3 Conditional Demotion (Override Context)

Not all overrides trigger demotion. The orchestrator checks whether the prior `job.failed` carried a sentinel `failing_capability_id`:

| Override Type | Condition | WAL Effect | Rationale |
|---------------|-----------|------------|-----------|
| `cancel` | Viable route / held result (no prior sentinel failure) | Demote 1 | Routing judgment was wrong |
| `cancel` | Prior sentinel egress failure (`egress_config`, `egress_connectivity`) | No demotion | Infrastructure issue, not routing |
| `redirect` | Viable route / held result | Demote 1 | User rejected routing choice |
| `redirect` | Prior sentinel failure | No demotion | Recovery from outage |
| `modify` | Any | None | Quality feedback. Score: 0.5 |
| `escalate` | Any | None | Quality feedback. Score: 0.0 |

### 10.4 Failure Attribution

Failures attribute to `failing_capability_id`, not the governing capability. Three sentinel values are **excluded from WAL counters** (they represent infrastructure problems, not routing judgment failures):

| Sentinel | Source | Meaning |
|----------|--------|---------|
| `"egress_config"` | Egress gateway | Route/policy misconfiguration |
| `"egress_connectivity"` | Egress gateway | Transport failure |
| `"worker_sandbox"` | Sandbox runtime | Worker sandbox violation (Spec 6) |

Only registered `capability_id` values increment failure counts.

### 10.5 Suspension

`wal.demoted` with `to_level: -1`. Capability is fully inoperable — all actions blocked. Recovery: edit `desired_wal_level` to `0` in `capabilities.json`, restart, reconciliation emits `wal.promoted(from: -1, to: 0)`.

---

## 11. Worker Agent Trust (Spec 6 Interaction)

Worker agent types are **not capabilities** in the Spec 2 sense. They do not have WAL levels, promotion criteria, or action policies. They are execution units invoked by L1's dispatch.

**Trust ownership:** The governing capability that dispatched the job owns the WAL context.

**Failure attribution split:**
- Quality failures (timeout, attestation failure, completion contract violation) → attribute to governing capability's WAL counters normally
- Sandbox violations (`worker.sandbox_violation`) → attribute to sentinel `"worker_sandbox"`, excluded from WAL counters

**Runtime reliability records:** Each agent type maintains a separate reliability record for operational visibility (total jobs, failure count by type, skills in use at failure). This record is **informational only** — it does not participate in the WAL state machine. It is not stored in `capabilities.state.json`.

### 11.1 Skill Governance

Skills are tools, not capabilities. They do not participate in WAL. The agent type that uses a skill inherits execution risk from that skill. However, skills are not ungoverned:

- Every skill invocation inherits a **bounded execution profile** from the agent type's runtime manifest: filesystem paths, network endpoints, binary allowlist, resource limits. The sandbox enforces this regardless of what the skill attempts.
- `worker.sandbox_violation` and `worker.egress_blocked` events carry `skill_id` when attributable, enabling the operator to identify which skill caused the problem.
- **Skill-correlated failure restriction:** When a specific `skill_id` appears in 3 or more `worker.sandbox_violation` or `worker.egress_blocked` (with `block_reason: sanitizer_credential`) events within a rolling 24h window, the operator is notified via `system.notify` with a recommendation to remove the skill from the agent type's manifest. This is an advisory notification, not automatic removal — the operator investigates and decides. Automatic skill removal is a v2 consideration.
- Repeated skill-correlated failures that flow through to `job.failed` attribute normally to the governing capability's WAL counters. If a skill consistently causes the governing capability to fail, the governing capability demotes — which is correct, because the governing capability chose to dispatch work through a pipeline that includes that skill.

**The invariant:** Capabilities are the unit of WAL trust. Skills and worker agents are execution details inside the silo. The governing capability is accountable for the quality of its dispatched work. Infrastructure integrity events (sandbox violations) are operator problems, not routing problems.

---

## 12. Startup Validation

The orchestrator validates the following before startup completes. Any failure prevents startup.

1. No gate relaxation beyond spec defaults (Section 5)
2. `desired_wal_level ≤ max_wal` for every capability
3. `declared_pipeline` references only registered auxiliary capabilities
4. `provider_dependencies` reference only registered governing capabilities
5. Exclusive action ownership (no action in both governing and auxiliary)
6. All actions from canonical enum (Section 3)
7. `credential` class in `sensitivity.json` has `hardcoded: true`, `default_action: strip`
8. All registered agent types have valid runtime manifests (Spec 6)
9. Runtime manifests reference only registered sandbox blueprints (Spec 6)

---

## 13. State Machine Visualization

```
                    ┌─────────────────────────────────────────────┐
                    │                                             │
                    ▼                                             │
┌──────┐  human  ┌──────┐  human  ┌──────┐                      │
│WAL-0 │────────▶│WAL-1 │────────▶│WAL-2 │──── governing max ───┘
│      │  edit   │      │  edit   │      │      (v1 ceiling)
└──┬───┘         └──┬───┘         └──┬───┘
   │                │                │
   │  ◀─ demotion ──┘                │
   │  ◀────── demotion ──────────────┘
   │  ◀────── model change ──────────┘
   │
   │                ┌──────┐  aux only  ┌──────┐
   │                │WAL-2 │───────────▶│WAL-3 │
   │                └──────┘            └──┬───┘
   │                                       │
   │  ◀───────── approval decay ───────────┘
   │
   ▼
┌──────────┐
│SUSPENDED │  strip failure → context.package
│  (WAL-1) │  recovery: manual edit to WAL-0 + restart
└──────────┘
```

**Key properties:**
- Promotion is always a human decision (edit config + restart)
- Demotion is always automatic (event-driven, immediate)
- Suspension is the harshest automatic action (requires manual recovery)
- Model changes reset all affected capabilities to WAL-0
- Counters reset on every demotion — trust is re-earned from zero

---

## 14. Querying Trust State

```bash
# Current effective WAL for all capabilities
jq '.[] | {capability_id, effective_wal_level, status}' \
  /var/drnt/config/capabilities.state.json

# Desired vs. effective mismatch (pending reconciliation)
jq -n '
  (input | to_entries) as $desired |
  (input | to_entries) as $effective |
  [ $desired[] as $d |
    ($effective[] | select(.key == $d.key) | .value.effective_wal_level) as $ew |
    select($ew != $d.value.desired_wal_level) |
    { capability_id: $d.key, desired: $d.value.desired_wal_level, effective: $ew }
  ]' /var/drnt/config/capabilities.json /var/drnt/config/capabilities.state.json

# Promotion readiness check (audit log)
jq '[
  select(.event_type == "human.reviewed" and .capability_id == "route.cloud.claude")
  | .payload.decision
] | group_by(.) | map({(.[0]): length})' drnt-audit-*.jsonl

# Recent failures for a capability
jq 'select(.event_type == "job.failed"
    and .payload.failing_capability_id == "route.cloud.claude")
    | {timestamp, error_class: .payload.error_class}' drnt-audit-*.jsonl

# All WAL state changes
jq 'select(.event_type | startswith("wal."))
    | {timestamp, event_type, cap: .payload.capability_id,
       from: .payload.from_level, to: .payload.to_level}' drnt-audit-*.jsonl
```

---

## 15. Cross-References

| Document | Relationship |
|----------|-------------|
| **Event Schema v2.0** | All WAL events (`wal.promoted`, `wal.demoted`, `wal.permission_check`) defined there. This spec defines the rules that produce those events. |
| **Spec 3: Context Boundary** | `context.package` capability defined here. Spec 3 defines the data structure and sensitivity rules it operates on. |
| **Spec 4: Egress Policy** | Egress registry binds `route_id` to `capability_id`. Capabilities defined here. |
| **Spec 5: Override Semantics** | Override-triggered demotion rules (Section 10.3) implement Spec 5's conditional demotion logic. |
| **Spec 6: Silo Runtime Security** | Worker agent trust ownership and failure attribution (Section 11) implement Spec 6's trust model. |
| **Evaluation Loop** | Lightweight feedback provides the quality signal that populates `evaluable_outcomes`. This spec defines the thresholds that consume those signals. |

---

*End of DRNT Capability Trust Profile v1.0. Companion artifacts: [Event Schema](DRNT_Event_Schema.md) (complete), NemoClaw-Governance-Overlay (next).*
