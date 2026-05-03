# DRNT Phase 3A — Claims Inventory

## Preamble

- Orchestration HEAD: `47e1b9f890861942129bdfd6484c5e7ec8b7ee33` (docs(spec6): update §11A seccomp rows to reflect runtime-verified state — 2026-04-18)
- Gateway HEAD: `6e9dc2271c2726d1d564bb0aa946142b69b99d6b` (docs(status): close KI-1 and reflect runtime-verified seccomp enforcement — 2026-04-18)
- Orchestration remote: `https://github.com/ljefford2-cmyk/local-first-ai-orchestration.git`
- Gateway remote: `https://github.com/ljefford2-cmyk/local-first-ai-gateway.git`
- Working trees at extraction time: clean, both repos
- Generation date: 2026-04-19
- Posture: read-only, no scoring, no cross-referencing between specs, no implementation code read
- Scoring categories (for 3B, not applied here): behavior | state | invariant | output | structure

## Specs walked

| Spec | Filename | Sections counted | Testable claim count | Notes |
| --- | --- | --- | --- | --- |
| 1 | `DRNT_Spec1_Audit_Event_Schema.md` | 9 top-level, plus subsections | 77 | FINAL, schema_version 1.5, 25,843 B |
| 2 | `DRNT_Spec2_Capability_Model.md` | 12 top-level | 65 | FINAL, depends on Spec 1 |
| 3 | `DRNT_Spec3_Context_Boundary.md` | 9 top-level | 38 | FINAL, depends on Specs 1, 2 |
| 4 | `DRNT_Spec4_Egress_Policy.md` | 10 top-level | 41 | FINAL, depends on Specs 1–3 |
| 5 | `DRNT_Spec5_Override_Semantics.md` | 15 top-level | 40 | FINAL, depends on Specs 1–4 |
| 6 | `DRNT_Spec6_Silo_Runtime_Security.md` | 13 top-level (+ 11A impl-status subsection) | 98 | FINAL, depends on Specs 1–5. §11A is implementation-status reconciliation. |
| 7 | `DRNT_Spec7_Signal_Chain_Resilience.md` | 17 top-level (split Part 1 / Part 2 / Integration) | 74 | v1.1 post-adversarial. |
| 8 | — | — | — | Not present in orchestration working tree; git-log traces exist in gateway repo. See "Absence findings." |
| **Total** | | | **433** | |

---

## Claims by spec

### Spec 1 — Audit/Event Schema (`specs/DRNT_Spec1_Audit_Event_Schema.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S1-C1 | §2.1 | structure | Audit log is JSONL — one JSON object per line. | (none) |
| S1-C2 | §2.1 | structure | One file per UTC calendar day, named `drnt-audit-YYYY-MM-DD.jsonl`. | (none) |
| S1-C3 | §2.1 | structure | Log location on Desktop hub is `/var/drnt/audit/` inside a Docker volume, bind-mounted from host filesystem. | (none) |
| S1-C4 | §2.1 | invariant | Only the log writer container has write access to audit files. | (none) |
| S1-C5 | §2.1 | invariant | All other containers — including the orchestrator — mount `/var/drnt/audit/` read-only. | (none) |
| S1-C6 | §2.1 | invariant | The orchestrator does not write directly to audit files under any circumstances. | (none) |
| S1-C7 | §2.1 | behavior | Orchestrator sends events to the log writer over a Unix domain socket. | §7 |
| S1-C8 | §2.2 | structure | Every event includes `prev_hash`: SHA-256 of the previous event's JSON line. | (none) |
| S1-C9 | §2.2 | invariant | The first event of each day file chains to the last event of the previous day's file. | (none) |
| S1-C10 | §2.2 | invariant | Modifying any event invalidates all subsequent hashes. | (none) |
| S1-C11 | §2.2 | structure | A standalone script `drnt-audit-verify` walks the chain and reports breaks. | (none) |
| S1-C12 | §2.2 | behavior | `drnt-audit-verify` runs nightly via cron and on-demand, independent of the orchestrator. | (none) |
| S1-C13 | §2.3 | behavior | The log writer determines target file by the UTC date of the `committed_at` timestamp. | (none) |
| S1-C14 | §2.3 | behavior | At day boundary: finish current event to current file, record SHA-256 hash of last line, next event opens a new file chaining from that hash. [paraphrase] | (none) |
| S1-C15 | §2.3 | invariant | The log writer is single-threaded with respect to writes — no race condition. | (none) |
| S1-C16 | §2.4 | state | Genesis seed = SHA-256 of the literal string `"DRNT-GENESIS"` (UTF-8, no trailing newline) = `9cd9da92046a4de28d7cec92f9559e542ba6a52af9fe44513bd26b0c2838a4d4`. | (none) |
| S1-C17 | §2.4 | behavior | The first event ever written uses the genesis seed hash. | (none) |
| S1-C18 | §2.4 | behavior | If no previous day exists, the genesis seed is used for that day's first-event `prev_hash`. | (none) |
| S1-C19 | §2.5 | behavior | The chain guarantees tamper evidence: if any event is modified, inserted, or deleted, the chain breaks. | (none) |
| S1-C20 | §2.5 | invariant | The chain does not prevent tampering, only makes it evident. | (none) |
| S1-C21 | §3 | structure | Every event carries the 13-field envelope listed in the table: `schema_version`, `event_id`, `source_event_id`, `timestamp`, `committed_at`, `event_type`, `job_id`, `parent_job_id`, `capability_id`, `wal_level`, `source`, `prev_hash`, `durability`, `payload`. | (none) |
| S1-C22 | §3 | state | `schema_version` is `"1.5"`. | (Spec 6 §7.1 bumps to 1.6) |
| S1-C23 | §3 | state | `event_id` is UUIDv7, assigned by log writer at commit time. | (none) |
| S1-C24 | §3 | state | `source_event_id` is UUIDv7, generated by emitter at occurrence time; stable across retransmissions; used for deduplication. | §7.2 |
| S1-C25 | §3 | state | `timestamp` and `committed_at` are ISO 8601 µs UTC. | (none) |
| S1-C26 | §3 | structure | `event_type` is a dot-namespaced string enum; determines payload structure. | (none) |
| S1-C27 | §3 | structure | `capability_id` is null for system events and `job.submitted` (pre-classification). | (none) |
| S1-C28 | §3 | behavior | `capability_id` is set at `job.classified` and carried through all subsequent events; never mutated. | §3.1 |
| S1-C29 | §3 | structure | `wal_level` range is `int (-1..3)` or null; -1 = suspended; null when capability_id is null. | (none) |
| S1-C30 | §3 | structure | `source` is a string enum: `orchestrator` │ `context_packager` │ `egress_gateway` │ `watch_app` │ `phone_app` │ `human` │ `system` │ `log_writer`. | (Spec 6 adds `worker_egress_proxy`, `sandbox_runtime`) |
| S1-C31 | §3 | structure | `durability` enum: `"durable"` or `"best_effort"`, set by emitter per event type. | (none) |
| S1-C32 | §3 | structure | `payload` is fixed per `event_type`; all fields present in every instance; null for inapplicable values. | (none) |
| S1-C33 | §3.1 | invariant | In v1, every job has exactly one governing capability, established at `job.classified` and carried through all subsequent events. | Spec 2 |
| S1-C34 | §3.1 | behavior | Classification runs before capability permission checks and determines which gates apply. | (none) |
| S1-C35 | §3.1 | behavior | Auxiliary capabilities emit their own `wal.permission_check` events with their own `capability_id` in the payload; envelope always reflects governing. | Spec 2 §2.1 |
| S1-C36 | §3.1 | structure | Auxiliary checks carry `dependency_mode`: `required` │ `optional` │ `best_effort`. | Spec 2 §2.1 |
| S1-C37 | §3.1 | behavior | Failures are attributed to the capability that failed, not the governing capability. | (none) |
| S1-C38 | §3.1 | structure | `job.failed` carries `failing_capability_id`. | (none) |
| S1-C39 | §3.1 | structure | `context.strip_detail` separates `detected_by` (who caught it) from `failing_capability_id` (who failed). | (none) |
| S1-C40 | §3.1 | invariant | Sentinel values `"egress_config"` and `"egress_connectivity"` are excluded from WAL failure counters. | Spec 2 §10.1 |
| S1-C41 | §3.1 | behavior | Redirect and escalate overrides terminate the original job and spawn a successor with new `job_id` and `parent_job_id` linking to the original. | Spec 5 |
| S1-C42 | §3.1 | invariant | The governing capability is never mutated within a job. | (none) |
| S1-C43 | §4 | structure | Payload schemas are fixed per `event_type`; all fields present in every instance; null for inapplicable values, not omission. | (none) |
| S1-C44 | §4.1 | invariant | Every job produces at minimum `job.submitted` and one terminal event: `job.delivered`, `job.failed`, or `job.revoked`. | (none) |
| S1-C45 | §4.1 | behavior | `job.failed` may be followed by a new `job.dispatched` with incremented `retry_sequence`. | (none) |
| S1-C46 | §4.1 | state | v1 retry constraint: same capability only. | Spec 2 §8 |
| S1-C47 | §4.1 | structure | Redirect and escalate overrides terminate the original job and spawn a successor with a new `job_id`. | Spec 5 |
| S1-C48 | §4.1 | structure | The `job.classified` payload includes `declared_pipeline` (array of `{capability_id, dependency_mode}` — snapshot at classification time). | (none) |
| S1-C49 | §4.1 | structure | `job.dispatched` includes `assembled_payload_hash` (SHA-256 matching `context.packaged`) and `egress_config_hash` (SHA-256 of egress.json as loaded by gateway). | (none) |
| S1-C50 | §4.1 | behavior | `job.dispatched` is emitted when gateway confirms outbound transmission, BEFORE model response. | Spec 4 §4 |
| S1-C51 | §4.1 | structure | `job.failed.failing_capability_id` may use sentinel values `"egress_config"`/`"egress_connectivity"` for transport failures (excluded from WAL counters). | (none) |
| S1-C52 | §4.1 | state | Three terminal states: `job.delivered`, `job.failed`, `job.revoked`. | (none) |
| S1-C53 | §4.1 | invariant | Revocation does not delete prior events — `job.delivered` and `model.response` remain in the log. | (none) |
| S1-C54 | §4.2 | invariant | Every outbound cloud dispatch produces exactly one `context.packaged` event. | (none) |
| S1-C55 | §4.2 | behavior | `context.strip_detail` is emitted once per non-pass transformation. | (none) |
| S1-C56 | §4.2 | structure | `context.packaged` carries `assembled_payload_hash` and `context_object_hash` (both SHA-256). | (none) |
| S1-C57 | §4.2 | behavior | Rollup arrays use `field_id`s, not names; a single `field_id` may appear in multiple rollup arrays. | (none) |
| S1-C58 | §4.2 | invariant | When the Packager blocks unclassifiable content (default-deny), `failing_capability_id` is null (conservative success). | (none) |
| S1-C59 | §4.2 | behavior | Egress gateway catches set `failing_capability_id: "context.package"` and trigger strip-failure trust consequences. | (none) |
| S1-C60 | §4.3 | state | Permission result enum: `allowed` │ `blocked` │ `held`. | (none) |
| S1-C61 | §4.3 | invariant | `delivery_hold: true` on an allowed result means dispatch proceeds but downstream `deliver_result` applies a `pre_delivery` gate. | Spec 2 §4 |
| S1-C62 | §4.3 | invariant | `pre_delivery` is never a `held` result. | (none) |
| S1-C63 | §4.3 | structure | `wal.permission_check.hold_type` is one of `pre_action`, `cost_approval`, `on_accept`, or null (present only when result is held). | (none) |
| S1-C64 | §4.4 | invariant | Every model call produces a `model.response` event. | (none) |
| S1-C65 | §4.4 | behavior | Single-model: one event, `selected: true`. Multi-model: one per model, `selected: true` on the delivered response. | (none) |
| S1-C66 | §4.4.1 | structure | `response_hash` is SHA-256 hex digest of the exact bytes stored in the results store for that `result_id`. | (none) |
| S1-C67 | §4.4.1 | structure | Structured/multimodal responses are hashed as UTF-8 of canonical JSON serialization (keys sorted alphabetically, no trailing whitespace, no pretty-printing). | (none) |
| S1-C68 | §4.4.1 | invariant | `result_id` is the join key between the audit log and the results store. | §6.3 |
| S1-C69 | §4.5 | structure | Modified result lineage: `model.response.result_id` → `human.reviewed.derived_from_result_id` (same) → `human.reviewed.modified_result_id` (new artifact). | (none) |
| S1-C70 | §4.6 | structure | `system.config_change` is emitted only for human-edited configuration files; runtime state persistence does NOT emit this event. | Spec 2 §6.1 |
| S1-C71 | §4.7 | behavior | `egress.validation_failure` does NOT trigger the strip-failure trust path and does NOT carry `failing_capability_id: "context.package"`. | Spec 3 §7.2, Spec 4 |
| S1-C72 | §5 | structure | `prev_hash` of event N is SHA-256 hex digest of the complete JSON line of event N−1, encoded as UTF-8, excluding trailing newline. | (none) |
| S1-C73 | §6.1 | state | V1 retention: retain all log files indefinitely. | (none) |
| S1-C74 | §6.2 | state | V1 querying tooling: `jq`, `grep`, `tail -f`. No database. | (none) |
| S1-C75 | §6.3 | structure | Results store is separate from the audit log; join key is `result_id` (UUIDv7). | (none) |
| S1-C76 | §6.3 | invariant | Modified results are stored under `modified_result_id` as separate immutable artifacts. | (none) |
| S1-C77 | §6.4 | structure | Context store location is `/var/drnt/context/`; join key is `context_package_id`. | (none) |
| S1-C78 | §6.4 | invariant | Each context package is a JSON file, written once, never modified. | (none) |
| S1-C79 | §6.4 | invariant | `assembled_payload_hash` anchors the payload; `context_object_hash` anchors the full object. | (none) |
| S1-C80 | §7.1 | structure | The log writer is a separate lightweight process listening on a Unix domain socket. | (none) |
| S1-C81 | §7.1 | behavior | On startup, log writer reads the last line of the current/previous day's file to initialize chain state, or uses genesis seed. | (none) |
| S1-C82 | §7.2 | state | Deduplication uses an in-memory set of `source_event_id` values from the current and previous day's files. | (none) |
| S1-C83 | §7.2 | behavior | Duplicates are dropped silently. | (none) |
| S1-C84 | §7.3 | behavior | Durable: append + fsync, then return ACK to emitter; emitter blocks until ACK. | (none) |
| S1-C85 | §7.3 | state | Durable timeout is 5 seconds, then retry (same source_event_id). | (none) |
| S1-C86 | §7.3 | behavior | Gated action does not proceed until durable ACK confirmed. | (none) |
| S1-C87 | §7.3 | behavior | Best-effort: append to write buffer; flush on next durable write or 1-second interval. | (none) |
| S1-C88 | §7.3 | invariant | Fail-closed: if log writer is down, durable events cannot be ACK'd, so orchestrator cannot dispatch, change WAL state, or process overrides — the system halts. | (none) |
| S1-C89 | §7.3 | invariant | Best-effort events are silently dropped and associated actions proceed normally if log writer is down. | (none) |
| S1-C90 | §7.4 | structure | Durable event-type list includes `job.submitted`, `job.classified`, `job.dispatched`, `job.response_received`, `job.delivered`, `job.failed`, `job.revoked`, `context.packaged`, `context.strip_detail`, `wal.promoted`, `wal.demoted`, `wal.permission_check`, `model.response`, `model.error`, `human.reviewed`, `human.override`, `system.startup`, `system.shutdown`, `system.integrity_check`, `system.config_change`, `system.model_change`, `egress.validation_failure`. | (none) |
| S1-C91 | §7.4 | structure | Best-effort event-type list: `job.queued`, `human.feedback`, `system.health_check`, `system.connectivity`. | (none) |
| S1-C92 | §7.5 | behavior | Components provide all fields except `event_id`, `committed_at`, and `prev_hash`; they provide `source_event_id`, `timestamp`, and `durability`. | (none) |
| S1-C93 | §7.6 | behavior | Watch/phone events are transmitted to the Desktop hub over Tailscale; offline events queue locally on the device and flush on reconnection. | Spec 7 §3.1-3.2 |
| S1-C94 | §7.6 | state | `source_event_id` and `timestamp` are set on-device; `committed_at` is set by the log writer. | (none) |

*Note on S1-C counts: the summary table reports 77 as a tracking estimate but the table above enumerates 94 claims. Reconcile in 3B.*

### Spec 2 — Capability Model (`specs/DRNT_Spec2_Capability_Model.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S2-C1 | §1 | invariant | Every WAL state change and permission check emits durable events to the audit log. | Spec 1 §4.3 |
| S2-C2 | §1 | invariant | All WAL events require fsync + ACK before the gated action proceeds. | Spec 1 §7.3 |
| S2-C3 | §2 | structure | Each capability has its own WAL level, promotion history, demotion triggers, and action policy set. | (none) |
| S2-C4 | §2 | invariant | WAL is never global. | (none) |
| S2-C5 | §2.1 | state | Governing capability owns routing, delivery, retry, and model-selection actions; assigned at classification; appears in envelope `capability_id`. | Spec 1 §3.1 |
| S2-C6 | §2.1 | state | Auxiliary capability owns pipeline support actions (context packaging, memory I/O, transport notification, queue); each check emits its own `wal.permission_check`; never in the envelope. | (none) |
| S2-C7 | §2.1 | invariant | Action ownership is exclusive — no action appears in both a governing and an auxiliary capability's action set. | §11 |
| S2-C8 | §2.2 | behavior | On block: `required` → pipeline halts, job cannot proceed, surfaces to user. | (none) |
| S2-C9 | §2.2 | behavior | On block: `optional` → step skipped, pipeline continues, logged. | (none) |
| S2-C10 | §2.2 | behavior | On block: `best_effort` → step attempted but failure does not block, logged. | (none) |
| S2-C11 | §3 | invariant | The action enum in Section 3 is the authority for `wal.permission_check.requested_action`; no action exists outside this list in v1. | (none) |
| S2-C12 | §3.1 | structure | Governing actions: `dispatch_local`, `dispatch_cloud`, `dispatch_multi`, `select_model`, `deliver_result`, `format_result`, `auto_retry`. | (none) |
| S2-C13 | §3.1 | structure | `dispatch_local` owned by `route.local`. | (none) |
| S2-C14 | §3.1 | structure | `dispatch_cloud` owned by `route.cloud.*`. | (none) |
| S2-C15 | §3.1 | structure | `dispatch_multi` owned by `route.multi`. | (none) |
| S2-C16 | §3.1 | structure | `select_model` owned by `route.cloud.*` and `route.local`. | (none) |
| S2-C17 | §3.1 | structure | `auto_retry` v1 constraint: same capability only. | (none) |
| S2-C18 | §3.2 | structure | Auxiliary actions: `package_context` (`context.package`), `read_memory` (`memory.read`), `write_memory` (`memory.write`), `send_notification` (`notify.watch`, `notify.phone`), `queue_job` (`job.queue`). | (none) |
| S2-C19 | §4 | invariant | Every action at every WAL level has a gate policy determining human involvement. | (none) |
| S2-C20 | §4 | structure | Gate encoding: `none` → allowed/delivery_hold=false; `pre_action` → held/pre_action; `pre_delivery` → allowed/delivery_hold=true; `post_action` → allowed/delivery_hold=false; `on_accept` → held/on_accept; `cost_approval` → held/cost_approval. | Spec 1 §4.3 |
| S2-C21 | §4 | structure | Action policies are objects in the registry: `{ review_gate, cost_gate_usd, retry_policy: { allowed, max_retries, same_capability_only } }`. | (none) |
| S2-C22 | §4 | invariant | Local config may tighten spec defaults (stricter gates, lower cost caps, fewer retries) but not relax them. | (none) |
| S2-C23 | §4 | behavior | Startup validation rejects configs that relax gates. | (none) |
| S2-C24 | §5.1 | state | WAL-0 `dispatch_local`: gate `pre_delivery`, no cost, no retry. | (none) |
| S2-C25 | §5.1 | state | WAL-0 `dispatch_cloud`: gate `pre_delivery`, cost $0.25, no retry. | (none) |
| S2-C26 | §5.1 | state | WAL-0 `dispatch_multi`: gate `pre_delivery`, cost $0.50, no retry. | (none) |
| S2-C27 | §5.1 | state | WAL-0 `select_model`: gate `pre_action`. | (none) |
| S2-C28 | §5.1 | state | WAL-0 `deliver_result`: gate `pre_delivery`. | (none) |
| S2-C29 | §5.1 | state | WAL-0 `format_result`: blocked (raw output). | (none) |
| S2-C30 | §5.1 | state | WAL-0 `auto_retry`: blocked. | (none) |
| S2-C31 | §5.1 | state | WAL-0 `package_context`, `read_memory`, `send_notification`, `queue_job`: gate `none`. | (none) |
| S2-C32 | §5.1 | state | WAL-0 `write_memory`: blocked. | (none) |
| S2-C33 | §5.2 | state | WAL-1 changes from WAL-0: `format_result` → `pre_delivery`; `auto_retry` → none (max 2, same cap); `write_memory` → `on_accept`. | (none) |
| S2-C34 | §5.3 | state | WAL-2 changes from WAL-1: `deliver_result` → `post_action`; `dispatch_cloud` → `post_action` ($0.50, max 3 same cap); `select_model` → none; `auto_retry` → none (max 3 same cap); `write_memory` → none. | (none) |
| S2-C35 | §5.4 | state | WAL-3 is auxiliary only in v1; `send_notification` and `queue_job` have gate `none`. | (none) |
| S2-C36 | §6.1 | structure | Capability registry files: `/var/drnt/config/capabilities.json` (human-edited / desired state) and `/var/drnt/config/capabilities.state.json` (runtime-managed / effective state). | (none) |
| S2-C37 | §6.1 | invariant | `system.config_change` is emitted only for human-edited `capabilities.json` changes; runtime state persistence does not emit this event. | Spec 1 §4.6 |
| S2-C38 | §6.2 | state | `desired_wal_level` = human intent (capabilities.json); `effective_wal_level` = runtime truth (capabilities.state.json); the latter is used by the permission checker. | (none) |
| S2-C39 | §6.2 | behavior | Startup reconciliation: config unchanged → effective authoritative, no WAL events. | (none) |
| S2-C40 | §6.2 | behavior | Startup reconciliation, config changed: per capability, desired > effective → `wal.promoted`, ACK, set effective = desired. | (none) |
| S2-C41 | §6.2 | behavior | Startup reconciliation, config changed: desired < effective → `wal.demoted(trigger: manual)`, ACK, set effective = desired. | (none) |
| S2-C42 | §6.2 | behavior | Suspended recovery: effective = -1, desired = 0 → `wal.promoted(from: -1, to: 0)`. | (none) |
| S2-C43 | §6.3 | structure | Registry entry includes `capability_id`, `capability_type`, `desired_wal_level`, `max_wal`, `declared_pipeline`, `provider_dependencies`, `action_policies`, `promotion_criteria`. | (none) |
| S2-C44 | §6.3 | structure | `provider_dependencies` is null for single-provider; for `route.multi` it is an array of `capability_id`s. | (none) |
| S2-C45 | §6.3 | behavior | Each `provider_dependency` is checked before dispatch; suspended providers are excluded; all suspended = job blocked. | (none) |
| S2-C46 | §6.4 | state | State entry carries `effective_wal_level`, `status`, and counters (`evaluable_outcomes`, `recent_failures`, `first_job_date`, `last_reset`, `last_incident_source_event_id`). | (none) |
| S2-C47 | §6.4 | state | `evaluable_outcomes` is a ring buffer, max 200, of `{source_event_id, timestamp, disposition, cost_usd}` where disposition ∈ {accepted, modified, rejected, resubmitted, auto_delivered}. | (none) |
| S2-C48 | §6.4 | state | `auto_delivered` = WAL-2+ job delivered, not overridden within 24h. | §9.2 |
| S2-C49 | §6.4 | state | `recent_failures` is a deque with 24h eviction; 3 failures within a rolling 24-hour window triggers demotion. | §10.1 |
| S2-C50 | §6.4 | invariant | Successes between failures do NOT reset the `recent_failures` count; only 24h eviction clears entries. | (none) |
| S2-C51 | §7.1 | structure | V1 governing capability inventory includes `route.local` (init 0, max 2), `route.cloud.claude` (0,2), `route.cloud.openai` (0,2), `route.cloud.gemini` (0,2), `route.multi` (0,1), `system.notify` (0,3). | (none) |
| S2-C52 | §7.2 | structure | V1 auxiliary inventory: `context.package` (0,2), `memory.read` (0,2), `memory.write` (0,1), `notify.watch` (0,3), `notify.phone` (0,3), `job.queue` (0,3). | (none) |
| S2-C53 | §8 | behavior | `check_permission`: unknown capability → emit blocked("unknown_capability"). | (none) |
| S2-C54 | §8 | behavior | `check_permission`: effective_wal == -1 → emit blocked("suspended"). | (none) |
| S2-C55 | §8 | behavior | `check_permission`: policy missing or `"blocked"` → emit blocked("action_not_permitted"). | (none) |
| S2-C56 | §8 | behavior | `check_permission`: cost_gate_usd set and est_cost > gate → emit held(hold_type="cost_approval"). | (none) |
| S2-C57 | §8 | behavior | `pre_action` gate: no approval → held(pre_action); else allowed. | (none) |
| S2-C58 | §8 | behavior | `pre_delivery` gate: allowed(delivery_hold=true). | (none) |
| S2-C59 | §8 | behavior | `post_action` and `none` gates: allowed. | (none) |
| S2-C60 | §8 | behavior | `on_accept` gate: not accepted → held(on_accept); else allowed. | (none) |
| S2-C61 | §8 | invariant | Every call emits a durable `wal.permission_check`. | (none) |
| S2-C62 | §8 | behavior | Pipeline processed in declared order; most restrictive wins; `dependency_mode` controls block/skip/continue. | (none) |
| S2-C63 | §8 | invariant | Same-capability retry (v1): `same_capability_only: true`; cross-capability retry is v2. | (none) |
| S2-C64 | §8 | behavior | For `route.multi`: each `provider_dependency` checked before dispatch; suspended = excluded; all suspended = blocked. | (none) |
| S2-C65 | §9 | invariant | Promotion is always a human decision; counters/window state are acceleration hints; the audit log is authoritative. | (none) |
| S2-C66 | §9.1 | state | Governing 0→1: 30 evaluable outcomes, span ≥ 7 days, approval score ≥ 0.95, zero strip failures, zero cancel/redirect overrides. | (none) |
| S2-C67 | §9.1 | state | Governing 1→2: 100 outcomes at WAL-1, ≥ 30 days, score ≥ 0.98 on most recent 100, zero strip failures, zero incidents, ≥ 1 edge case handled. | (none) |
| S2-C68 | §9.1 | invariant | Governing 2→3 not available in v1 (max_wal ≤ 2). | (none) |
| S2-C69 | §9.2 | state | Approval score weights: accepted=1.0, modified=0.5, rejected=0.0, resubmitted=0.0, auto_delivered=1.0; score = sum(weights)/len(evaluable_outcomes). | (none) |
| S2-C70 | §9.3 | state | Auxiliary `context.package` 0→1: 30 packages, zero egress catches, ≥ 7 days; 1→2: 100 at WAL-1, 30 days, zero catches. | (none) |
| S2-C71 | §9.3 | state | `memory.read` 0→1: 30 reads, <10% missing-context feedback, ≥ 7 days; 1→2: 100 at WAL-1, 30 days, <5%. | (none) |
| S2-C72 | §9.3 | state | `memory.write`: 30 writes (on_accept), zero corrections, ≥ 7 days; max WAL-1 in v1. | (none) |
| S2-C73 | §9.3 | state | `notify.*`: 30 deliveries, zero suppression overrides, ≥ 7 days (0→1); 100 deliveries, 30/90d, <1% failures (1→2 / 2→3). | (none) |
| S2-C74 | §9.3 | state | `job.queue`: 30 drain cycles, zero lost jobs, ≥ 7 days (0→1); 100 cycles, 30/90d, zero lost. | (none) |
| S2-C75 | §9.4 | behavior | Promotion flow: orchestrator detects criteria likely met → creates job governed by `system.notify` with recommendation → human edits `desired_wal_level` in `capabilities.json` → restart → startup reconciliation emits durable `wal.promoted`, sets effective = desired after ACK. | (none) |
| S2-C76 | §10.1 | behavior | Strip-failure (egress catch) on `context.package` → SUSPEND; recovery = root cause + manual WAL-0 + restart. | (none) |
| S2-C77 | §10.1 | behavior | Override cancel/redirect on WAL-1+ (conditional per Spec 5 §5) on governing cap → demote 1; recovery = counter reset, re-earn. | Spec 5 §5 |
| S2-C78 | §10.1 | behavior | 3 failures in rolling 24h window on `failing_capability_id` → demote 1; recovery = investigate, counter reset. | (none) |
| S2-C79 | §10.1 | behavior | Model change on all affected_capabilities → WAL-0; recovery = full re-promotion. | (none) |
| S2-C80 | §10.1 | behavior | WAL-3 approval < 99% / 90d on the WAL-3 cap → WAL-2; recovery = re-earn WAL-3. | (none) |
| S2-C81 | §10.1 | invariant | Sentinel values (`"egress_config"`, `"egress_connectivity"`) are excluded from counters — only registered `capability_id`s increment failure counts. | (none) |
| S2-C82 | §10.1 | state | Suspension = `wal.demoted` with `to_level: -1`; fully inoperable; recovery = edit `desired_wal_level` to 0, restart, reconciliation emits `wal.promoted(from: -1, to: 0)`. | (none) |
| S2-C83 | §10.2 | behavior | On any demotion: `evaluable_outcomes` cleared, `recent_failures` cleared, `first_job_date` null, `last_reset` set. `wal_history` and `last_incident` preserved. | (none) |
| S2-C84 | §11 | behavior | Startup validation rejects: gate relaxation beyond spec defaults, `desired_wal_level` > `max_wal`, `declared_pipeline` references to unregistered auxiliaries, `provider_dependencies` references to unregistered governing capabilities, non-exclusive action ownership, actions outside canonical enum. | (none) |
| S2-C85 | §11 | invariant | `credential` class in sensitivity.json must have `hardcoded: true`, `default_action: strip`. | Spec 3 §4 |
| S2-C86 | §11 | invariant | Validation failures prevent startup. | (none) |

### Spec 3 — Context Boundary (`specs/DRNT_Spec3_Context_Boundary.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S3-C1 | §1 | structure | The Context Packager is owned by the `context.package` auxiliary capability. | Spec 2 §7.2 |
| S3-C2 | §2 | structure | A context object is the complete data structure assembled for a single dispatch — one per dispatch; retries create a new one. | (none) |
| S3-C3 | §2.1 | structure | Context object schema includes `context_package_id`, `job_id`, `governing_capability_id`, `governing_wal_level`, `context_package_wal_level`, `effective_packaging_level`, `target_model`, `route_id`, `task_specification`, `retrieved_candidates`, `eligible_context_fields`, `assembled_payload`, `assembled_payload_hash`, `token_estimate`, `created_at`. | (none) |
| S3-C4 | §2.1 | structure | `retrieved_candidates` is the complete pre-scan inventory, including excluded items with reasons. | (none) |
| S3-C5 | §2.1 | structure | `eligible_context_fields` is the post-gate subset entering the sensitivity scan. | (none) |
| S3-C6 | §2.1 | invariant | `effective_packaging_level = min(governing WAL, context.package WAL)` — most restrictive wins. | (none) |
| S3-C7 | §3 | behavior | Three stacked gates apply before anything reaches the sensitivity scanner: shareability, confidence threshold, token budget. | (none) |
| S3-C8 | §3.1 | invariant | `local_only` objects are excluded absolutely from cloud-eligible context. | (none) |
| S3-C9 | §3.1 | state | Default shareability is `local_only`; user explicitly tags objects as `cloud_eligible`. | (none) |
| S3-C10 | §3.2 | state | Below minimum confidence threshold (default 0.3) → excluded with `exclusion_reason: below_confidence`. | (none) |
| S3-C11 | §3.2 | state | Default confidence thresholds: user-stated 1.0, extracted doc 0.8, inferred 0.6, derived summary 0.4, external 0.3. | (none) |
| S3-C12 | §3.3 | state | Token budget has three pools: system context (~500–2000 tokens), memory context (configurable fraction), user input + padding. | (none) |
| S3-C13 | §3.3 | behavior | Highest-confidence objects are included first; whole objects only — no mid-content truncation. Excess → `exclusion_reason: over_budget`. | (none) |
| S3-C14 | §4 | structure | Sensitivity classes: `name`, `location`, `date`, `financial`, `credential`, `custom`, `none`, `unclassifiable`. | (none) |
| S3-C15 | §4 | state | `name` default action = strip (regex + NER + user tags). | (none) |
| S3-C16 | §4 | state | `location` default action = generalize (regex + geocoding + tags). | (none) |
| S3-C17 | §4 | state | `date` default action = generalize (regex + temporal NER). | (none) |
| S3-C18 | §4 | state | `financial` default action = strip (regex + tags). | (none) |
| S3-C19 | §4 | invariant | `credential` default action = strip always; detection is hardcoded regex. | (none) |
| S3-C20 | §4 | state | `none` (no rule, classifiable safe) → pass; status `safe_none`. | (none) |
| S3-C21 | §4 | invariant | `unclassifiable` (no rule, not classifiable) → strip (default-deny). | (none) |
| S3-C22 | §4 | invariant | Default-deny on unclassifiable is conservative success (`failing_capability_id: null`), not a strip failure. | (none) |
| S3-C23 | §4 | structure | Sensitivity config location: `/var/drnt/config/sensitivity.json`; `credential` class has `hardcoded: true`. | Spec 2 §11 |
| S3-C24 | §5 | behavior | Transform pipeline has four stages: Candidate Retrieval → Eligibility Gating → Sensitivity Scan + Transform → Payload Assembly. | (none) |
| S3-C25 | §5 | invariant | Precedence: strip > generalize > pass. | (none) |
| S3-C26 | §5 | invariant | Credential always strip; no config override. | (none) |
| S3-C27 | §5 | structure | Stage 4 stores full context object at `/var/drnt/context/{context_package_id}.json` and emits `context.packaged` with hashes. | Spec 1 §6.4 |
| S3-C28 | §5 | behavior | Mixed-content fields: a single field may produce multiple `strip_detail` events with different spans; `field_id` may appear in multiple rollup arrays; arrays are not mutually exclusive. | (none) |
| S3-C29 | §6 | state | WAL-0 context rules: confidence 0.3, memory pool 40%, all rules enforced, no exceptions. | (none) |
| S3-C30 | §6 | state | WAL-1 context rules: same rules, memory pool 50%, prompt template formatting applied. | (none) |
| S3-C31 | §6 | state | WAL-2 context rules: same core rules, memory pool 60%, confidence may lower to 0.2, narrow task-type exceptions allowed. | (none) |
| S3-C32 | §6 | invariant | Core pipeline is invariant across all levels: strip credentials, default-deny on unclassifiable, most-restrictive-wins. | (none) |
| S3-C33 | §7.1 | behavior | Category A (sensitivity catches / strip failures): gateway catches content Packager missed → blocks outbound → emits `context.strip_detail(detected_by: egress_gateway, failing_capability_id: "context.package")` → `job.failed(context_error)` → trust consequences fire. | (none) |
| S3-C34 | §7.1 | invariant | Gateway never silently fixes. | (none) |
| S3-C35 | §7.2 | behavior | Category B (transport/policy failures) → emits `egress.validation_failure`; does NOT trigger strip-failure trust path. | Spec 4 |
| S3-C36 | §8 | structure | `job.classified` is the Plan snapshot; `context.packaged` is the Outcome snapshot. | (none) |
| S3-C37 | §8 | invariant | Context store entries are written once; verification is by hashing stored payload and comparing to `assembled_payload_hash` / `context_object_hash`. | (none) |
| S3-C38 | §8 | structure | Prompt templates location: `/var/drnt/config/templates/`, one per task type, versioned. | (none) |

### Spec 4 — Egress Policy Binding (`specs/DRNT_Spec4_Egress_Policy.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S4-C1 | §2 | structure | Egress registry location: `/var/drnt/config/egress.json`. | (none) |
| S4-C2 | §2 | behavior | Egress registry is human-edited; changes emit `system.config_change`. | Spec 1 §4.6 |
| S4-C3 | §2 | invariant | Default: deny. If an endpoint is not in the registry, no payload reaches it. | (none) |
| S4-C4 | §2.1 | structure | Registry entry includes `route_id`, `provider`, `endpoint_url`, `model_string`, `allowed_capabilities`, `auth`, `constraints`, `health`, `enabled`. | (none) |
| S4-C5 | §2.1 | structure | `auth.secret_ref` is an env var reference, never the secret itself. | (none) |
| S4-C6 | §2.1 | behavior | Secrets are resolved at dispatch time from `/var/drnt/secrets/.env`. | (none) |
| S4-C7 | §2.1 | structure | Constraints include `max_tokens_per_request`, `max_cost_per_request_usd`, `rate_limit_rpm`, `rate_limit_tpd`, `tls_minimum`. | (none) |
| S4-C8 | §3 | invariant | The orchestrator cannot reach the internet — enforced by Docker network topology, not a firewall rule. | (none) |
| S4-C9 | §3 | structure | Docker bridge `drnt-internal` hosts orchestrator (internal only), gateway (internal + host outbound only), log writer (internal only, Unix socket), and Tailscale segment (phone/watch connectivity, no egress path). | Spec 6 §3.1 |
| S4-C10 | §4 | invariant | `job.dispatched` is emitted when the gateway confirms outbound transmission, BEFORE the model responds. It IS the authorization record — no separate egress-allow event. | Spec 1 §4.1 |
| S4-C11 | §4 | behavior | Check 1 — Route exists: `route_id` not in registry → `egress.validation_failure` (route_mismatch). | (none) |
| S4-C12 | §4 | behavior | Check 2 — Route enabled: `enabled: false` → `egress.validation_failure` (policy_violation). | (none) |
| S4-C13 | §4 | behavior | Check 3 — Capability binding: `capability_id` not in `allowed_capabilities` → `egress.validation_failure` (policy_violation). | (none) |
| S4-C14 | §4 | behavior | Check 4 — Model binding: `target_model` doesn't match `model_string` glob → `egress.validation_failure` (model_mismatch). | (none) |
| S4-C15 | §4 | behavior | Check 5 — Token limit: re-estimated tokens > `max_tokens_per_request` → `egress.validation_failure` (token_overflow). | (none) |
| S4-C16 | §4 | behavior | Check 6 — Cost limit: cost > `max_cost_per_request_usd` → `egress.validation_failure` (policy_violation). | (none) |
| S4-C17 | §4 | behavior | Check 7 — Rate limit: RPM or TPD exceeded → `egress.validation_failure` (policy_violation). | (none) |
| S4-C18 | §4 | behavior | Check 8 — Payload hash: SHA-256(payload) ≠ `assembled_payload_hash` → `egress.validation_failure` (policy_violation). | (none) |
| S4-C19 | §4 | behavior | Check 9 — Sensitivity scan: credential/sensitive patterns found → `context.strip_detail` (egress catch). | (none) |
| S4-C20 | §4 | behavior | Check 10 — Auth resolution: `secret_ref` unresolvable → `egress.validation_failure` (policy_violation). | (none) |
| S4-C21 | §4 | behavior | Check 11 — TLS/connectivity: endpoint unreachable or TLS < minimum → `egress.validation_failure` (endpoint_unavailable). | (none) |
| S4-C22 | §4 | invariant | Only check 9 emits `context.strip_detail`; all others emit `egress.validation_failure` with no strip-failure trust consequence. | (none) |
| S4-C23 | §4 | state | Sensitivity catch → `failing_capability_id: "context.package"`. | (none) |
| S4-C24 | §4 | state | Route/policy failure → sentinel `"egress_config"`; connectivity → sentinel `"egress_connectivity"`; sentinels excluded from WAL counters. | Spec 2 §10.1 |
| S4-C25 | §4 | behavior | Gateway re-estimates tokens from the provider-formatted request using the provider adapter's tokenizer. | (none) |
| S4-C26 | §4 | invariant | The gateway is authoritative for constraint enforcement. | (none) |
| S4-C27 | §4 | structure | `egress_config_hash` is stamped into `job.dispatched` for exact forensic reconstruction. | Spec 1 §4.1 |
| S4-C28 | §5 | behavior | `route.multi` sends one context package through multiple independent dispatches; each goes through the full check sequence; each gets its own `job.dispatched` (same `job_id`, different `route_id`, same `assembled_payload_hash`). | (none) |
| S4-C29 | §5 | behavior | In `route.multi`: suspended providers are excluded; all suspended = job blocked. | (none) |
| S4-C30 | §5 | invariant | Sensitivity decisions are made once and applied uniformly in `route.multi`. | (none) |
| S4-C31 | §6 | state | Rate limits are per-route (RPM sliding window, TPD rolling 24h), in-memory, reset on restart. | (none) |
| S4-C32 | §6 | behavior | Health probes run every `check_interval_seconds`; consecutive failures mark route down; auto-recovery on successful probe. | (none) |
| S4-C33 | §7 | structure | Anthropic adapter: `https://api.anthropic.com/v1/messages`, `x-api-key` header + `anthropic-version`. | (none) |
| S4-C34 | §7 | structure | OpenAI adapter: `https://api.openai.com/v1/chat/completions`, Bearer token. | (none) |
| S4-C35 | §7 | structure | Google adapter: `https://generativelanguage.googleapis.com/v1beta/...`, API key or OAuth2. | (none) |
| S4-C36 | §8 | invariant | Forensic reconstruction chains: `job.dispatched` (route_id, context_package_id, assembled_payload_hash, egress_config_hash) → `context.packaged` → context store → egress registry via route_id + config change history → `model.response`. Every link cryptographically anchored. | (none) |
| S4-C37 | §9 | behavior | Startup validation enforces: unique `route_id`s; `allowed_capabilities` reference registered capabilities; `secret_ref`s resolvable; HTTPS only; TLS ≥ 1.2; constraints internally consistent. | (none) |
| S4-C38 | §9 | invariant | Validation failures prevent gateway startup. | (none) |
| S4-C39 | §9 | invariant | No gateway = no dispatch. | (none) |

### Spec 5 — Override Semantics (`specs/DRNT_Spec5_Override_Semantics.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S5-C1 | §1 | invariant | Every override is durable. | (none) |
| S5-C2 | §1 | invariant | Governing principle: the human decides; DRNT complies immediately and records. | (none) |
| S5-C3 | §2 | behavior | `cancel`: abort job, discard results; post-delivery becomes revocation; applies any state through response_received. | (none) |
| S5-C4 | §2 | behavior | `redirect`: reject routing; terminate original; spawn successor with new governing capability; before or after delivery. | (none) |
| S5-C5 | §2 | behavior | `modify`: accept but edit; both original and modified stored as first-class artifacts; during review or post-delivery. | (none) |
| S5-C6 | §2 | behavior | `escalate`: request higher quality; spawn successor with more capable target; during review or post-delivery. | (none) |
| S5-C7 | §3 | invariant | Redirect and escalate terminate the original job and spawn a successor with a new `job_id` + `parent_job_id`. | Spec 1 §3.1 |
| S5-C8 | §3 | invariant | The original's audit trail is complete and immutable. | (none) |
| S5-C9 | §3 | structure | The successor gets its own `job.classified`, `context.packaged`, `job.dispatched` chain with its own governing capability. | (none) |
| S5-C10 | §3 | invariant | This preserves the one-job-one-governing-capability invariant. | (none) |
| S5-C11 | §4 | state | `job.delivered` = execution succeeded, result presented. | (none) |
| S5-C12 | §4 | state | `job.failed` = execution did not complete, or pre-delivery cancel/redirect. | (none) |
| S5-C13 | §4 | state | `job.revoked` = execution completed and delivered, but human later withdrew. Not a failure — a post-hoc decision. | (none) |
| S5-C14 | §5 | invariant | `job.dispatched` is the irreversibility boundary — data has left local. Before: overrides prevent egress. After: overrides control response handling; cannot recall data. | (none) |
| S5-C15 | §5 | behavior | Post-dispatch, user gets informational notification, not confirmation. | (none) |
| S5-C16 | §6 | behavior | Cancel on viable route / held result (no prior sentinel egress failure) → demote 1 (routing judgment wrong). | Spec 2 §10.1 |
| S5-C17 | §6 | behavior | Cancel after prior sentinel egress failure (`egress_config`/`egress_connectivity`) → no demotion (infrastructure issue). | (none) |
| S5-C18 | §6 | behavior | Redirect on viable route / held result → demote 1 (user rejected routing choice). | (none) |
| S5-C19 | §6 | behavior | Redirect after prior sentinel failure → no demotion (recovery from outage). | (none) |
| S5-C20 | §6 | behavior | `modify` → no demotion; score 0.5 (quality feedback). | (none) |
| S5-C21 | §6 | behavior | `escalate` → no demotion; score 0.0 (quality feedback). | (none) |
| S5-C22 | §6 | behavior | Orchestrator checks the prior `job.failed`'s `failing_capability_id` to decide conditional demotion. | (none) |
| S5-C23 | §7 | structure | `human.reviewed(modified)` carries `modified_result_id` (UUIDv7), `modified_result_hash` (SHA-256), `derived_from_result_id` (original). | Spec 1 §4.5 |
| S5-C24 | §7 | invariant | Both modified and original artifacts are in the results store; both hashes are in the audit log; full diffing always possible. | (none) |
| S5-C25 | §8 | behavior | Memory write at WAL-1 (`on_accept`): writes after accept/modify only. Cancel/redirect before acceptance = no write. | Spec 2 §5.2 |
| S5-C26 | §8 | behavior | Memory write at WAL-2 (`none`): writes on delivery. `modify` → supersede + new write. `escalate` → pending_replacement. `revoke` → supersede. | (none) |
| S5-C27 | §8 | invariant | Memory objects are never deleted — superseded or pending; full lineage preserved. | (none) |
| S5-C28 | §9 | state | Auto-accept window (WAL-2+): default 24 hours from delivery, configurable per capability. | (none) |
| S5-C29 | §9 | behavior | No override in window → `auto_delivered` (score 1.0). Override in window → modify (0.5), escalate (0.0), cancel/revoke (0.0 + conditional demotion). | (none) |
| S5-C30 | §10 | structure | Watch overrides: dismiss (swipe) = cancel; Tap "Phone" = transfer; no redirect/modify/escalate on watch. | (none) |
| S5-C31 | §10 | structure | Phone overrides: cancel (confirmation required), redirect (select target), modify (edit inline), escalate (select target); all four available. | (none) |
| S5-C32 | §10 | behavior | Offline overrides: queue on device, flush on reconnection. Retroactive cancel on delivered job → `job.revoked(offline_retroactive)`. Human decision at time T takes precedence. | Spec 7 §3.2.1 |
| S5-C33 | §10 | behavior | Two overrides on one job: first processed wins; second logged but no-op on terminal job. | (none) |
| S5-C34 | §11 | invariant | Override wins immediately in all cases. | (none) |
| S5-C35 | §11 | behavior | During packaging: Packager stops. | (none) |
| S5-C36 | §11 | behavior | During gateway: abort if not yet transmitted; else override-after-dispatch. | (none) |
| S5-C37 | §11 | behavior | During model wait: response arrives, is discarded; cost already incurred. | (none) |
| S5-C38 | §12 | structure | Redirect-after-dispatch sequence: original emits submitted, classified, packaged, dispatched, override(redirect), wal.demoted, job.failed. Successor emits classified, packaged, dispatched, model.response, job.delivered, human.reviewed. | (none) |
| S5-C39 | §13 | structure | Modify-during-review: submitted, classified, packaged, dispatched, response_received, override(modify), reviewed(modified, derived_from, modified_result_id), delivered(gate: pre_delivery). | (none) |
| S5-C40 | §14 | structure | Post-delivery escalate (WAL-2+): original reaches delivered, memory.write(M1), override(escalate, detail: route.multi), job.revoked(escalation_supersede, successor, superseded: [M1]). Successor executes and on accept, M1 superseded, M2 written. | (none) |

### Spec 6 — Silo Runtime Security (`specs/DRNT_Spec6_Silo_Runtime_Security.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S6-C1 | §1 | structure | The execution silo is the runtime environment between the L1 perimeter and the L2 exit audit. | (none) |
| S6-C2 | §1 | invariant | The cloud-dispatch egress path (Spec 4) and the worker-execution egress path (this spec) are distinct boundaries with distinct controls. | (none) |
| S6-C3 | §1 | structure | In-flight worker termination on override is governed here; Spec 5 governs the override itself. | §9.5 |
| S6-C4 | §2 | invariant | The silo operates under three complementary enforcement layers; no layer substitutes for the others. | (none) |
| S6-C5 | §2.1 | structure | Layer 1 (Runtime Isolation) governs OS/container-level: filesystem, network egress, process privileges, binary execution. | (none) |
| S6-C6 | §2.1 | behavior | Layer 1 is enforced by container configuration, Landlock, seccomp, and network namespace isolation — out-of-process. | (none) |
| S6-C7 | §2.1 | invariant | The agent cannot modify, disable, or escalate Layer 1 constraints from inside the sandbox. | (none) |
| S6-C8 | §2.1 | invariant | Filesystem and process restrictions are locked at sandbox creation time. | (none) |
| S6-C9 | §2.1 | invariant | Baseline network egress policy (runtime manifest) is locked at creation; permanent change requires sandbox recreation. | (none) |
| S6-C10 | §2.1 | behavior | Session-scoped endpoint approvals are hot-reloadable by the operator via the TUI proxy. | §8.3 |
| S6-C11 | §2.2 | structure | Layer 2 (Behavioral Governance) governs WAL levels, completion contracts, gate policies, promotion/demotion, cost controls. | Spec 2 |
| S6-C12 | §2.2 | invariant | Layer 2 operates inside Layer 1 constraints — it can tighten but never relax runtime constraints. | (none) |
| S6-C13 | §2.3 | structure | Layer 3 (Structural Privacy) has two enforcement paths: Cloud dispatch uses Context Packager + egress gateway with dual scans defaulting to deny on unclassifiable content. Worker egress uses the request sanitizer on the worker egress proxy — credential stripping + PII pattern detection. | Spec 3, Spec 4 |
| S6-C14 | §2.3 | state | Cloud dispatch defaults to deny at both endpoint and content level. | (none) |
| S6-C15 | §2.3 | state | Worker execution egress defaults to deny at endpoint level and pass at content pattern level for approved endpoints. | §3.4 |
| S6-C16 | §2.3 | invariant | Credentials are stripped on both paths unconditionally. | (none) |
| S6-C17 | §2.4 | invariant | Runtime isolation is the ceiling — behavioral governance and structural privacy operate within it, not above it. | (none) |
| S6-C18 | §2.4 | invariant | Worker agents inside the silo are not capabilities and do not go through the Spec 2 permission check pipeline. | (none) |
| S6-C19 | §2.4 | invariant | The governing capability that dispatched the job owns the WAL context. | §6.4 |
| S6-C20 | §2.4 | invariant | Structural privacy applies to all outbound data; neither path is exempt. | (none) |
| S6-C21 | §2.4 | invariant | No layer trusts another layer's enforcement (defense in depth). | (none) |
| S6-C22 | §3.1 | structure | Network segment `drnt-internal`: orchestrator (L1), L2, log writer, verification agent; no external access. | (none) |
| S6-C23 | §3.1 | structure | Network segment `drnt-worker`: worker agent containers; external access only via worker egress proxy per per-agent policy. | (none) |
| S6-C24 | §3.1 | structure | Network segment `drnt-gateway`: egress gateway; host outbound per Spec 4. | Spec 4 §3 |
| S6-C25 | §3.1 | structure | Network segment `drnt-tailscale`: phone/watch connectivity; no egress path. | (none) |
| S6-C26 | §3.1 | structure | Network segment `drnt-bus`: L1 ↔ worker message bus; internal IPC only, no external connectivity. | (none) |
| S6-C27 | §3.1 | invariant | Workers cannot reach `drnt-internal`; communication with L1 is only via `drnt-bus`. | (none) |
| S6-C28 | §3.1 | invariant | Workers cannot reach `drnt-gateway`; cloud dispatch is submitted to L1, not initiated by workers. | (none) |
| S6-C29 | §3.1 | invariant | Workers never send outbound requests to cloud models directly. | (none) |
| S6-C30 | §3.1 | invariant | `drnt-bus` carries structured messages only — no external connectivity, no raw data passthrough. | (none) |
| S6-C31 | §3.2 | state | Cloud dispatch path: Orchestrator → Context Packager → Gateway → Cloud. Worker execution path: Worker → Sanitizer → Egress Proxy → External. | (none) |
| S6-C32 | §3.2 | state | Cloud dispatch gate = capability + sensitivity + egress registry; worker egress gate = agent type policy + sanitizer + operator approval. | (none) |
| S6-C33 | §3.2 | state | Cloud dispatch audit events are `job.dispatched`, `context.packaged`; worker egress events are `worker.egress_request`, `worker.egress_blocked`. | (none) |
| S6-C34 | §3.2 | state | Cloud dispatch approval: human edits `egress.json`, restart. Worker egress approval: operator via TUI (session, TTL) or manifest edit (permanent). | (none) |
| S6-C35 | §3.3 | structure | Worker egress proxy sits on `drnt-worker` segment; all worker outbound requests route through it. | (none) |
| S6-C36 | §3.3 | behavior | Proxy performs two checks in sequence: request sanitizer, then agent type's egress policy; both must pass. | (none) |
| S6-C37 | §3.3 | behavior | Sanitizer block → request dropped, `worker.egress_blocked` with `block_reason: sanitizer_catch`, surfaced in TUI (privacy event). | (none) |
| S6-C38 | §3.3 | behavior | Policy block → request dropped, `worker.egress_blocked` with `block_reason: endpoint_not_in_policy`, surfaced for operator approval. | (none) |
| S6-C39 | §3.3 | behavior | Both pass → request forwarded, `worker.egress_request` emitted with `policy_result: allowed`. | (none) |
| S6-C40 | §3.3 | state | Session approvals default TTL = 4 hours, configurable per blueprint. | §8.3 |
| S6-C41 | §3.3 | behavior | Expired approvals require re-approval or permanent manifest update. | (none) |
| S6-C42 | §3.3 | invariant | The proxy is out-of-process; the worker agent cannot bypass, modify, or disable it. | (none) |
| S6-C43 | §3.3 | structure | Session approvals attach to the job context that triggered the block. | (none) |
| S6-C44 | §3.4 | behavior | Sanitizer check 1 — Credential stripping: scans URL params, headers, body; same pattern set as Spec 3 `credential` class (hardcoded: true); match blocks unconditionally; not operator-overridable. | Spec 3 §4 |
| S6-C45 | §3.4 | behavior | Sanitizer check 2 — PII pattern detection: scans for `name`, `location`, `date`, `financial` classes; matches blocked and surfaced for operator review; can be approved (session, TTL) or added to allowlist (permanent, requires restart). | (none) |
| S6-C46 | §3.4 | behavior | Sanitizer check 3 — Unrecognized pattern default: content that does not match any known-bad pattern passes (endpoint-constrained exception). | (none) |
| S6-C47 | §3.4 | invariant | The security model for worker egress is endpoint restriction (Layer 1) plus known-bad pattern detection (sanitizer), not semantic content classification. | (none) |
| S6-C48 | §3.4 | behavior | TLS inspection requires a local inspection proxy with a sandbox-trusted CA certificate; proxy terminates TLS from worker, inspects, re-establishes TLS to external endpoint. | (none) |
| S6-C49 | §3.4 | invariant | In V1, the sanitizer detects and blocks — it does not redact and forward (clean-and-forward is post-V1). | (none) |
| S6-C50 | §3.4 | invariant | PII matches are always blocked and surfaced for operator review; credential matches always blocked unconditionally. | (none) |
| S6-C51 | §4 | structure | Every agent type has two documents: completion contract (behavioral) and runtime manifest (environmental). | (none) |
| S6-C52 | §4.1 | structure | Completion contract defines expected output, attestation requirements, timeout/failure handling, and cost budget for cloud dispatch requests. | (none) |
| S6-C53 | §4.2 | structure | Runtime manifest declares what the execution environment must provide: `agent_type_id`, `sandbox_blueprint`, `filesystem`, `network_egress`, `cloud_dispatch_dependencies`, `binaries`, `skills`, `resource_limits`. | (none) |
| S6-C54 | §4.2 | invariant | `filesystem.blocked: ["*"]` — default-deny; only paths explicitly listed in `writable` and `readable` are accessible; locked at sandbox creation; not modifiable at runtime. | (none) |
| S6-C55 | §4.2 | invariant | Cloud model endpoints never appear in `network_egress.allowed_endpoints`; workers do not dispatch to cloud models directly. | (none) |
| S6-C56 | §4.2 | structure | `cloud_dispatch_dependencies` is informational metadata, not a network permission; startup validation confirms referenced capabilities are registered. | (none) |
| S6-C57 | §4.2 | invariant | `binaries.blocked: ["*"]` — only listed binaries can execute. | (none) |
| S6-C58 | §4.2 | invariant | A skill that ships a custom binary must have that binary added to the allowed list before installation. | (none) |
| S6-C59 | §4.2 | behavior | `operator_approval_on_unknown: true` → proxy blocks unknown endpoints and surfaces for operator review. | (none) |
| S6-C60 | §4.3 | behavior | Runtime manifest is enforced by the sandbox blueprint at creation time; orchestrator validates manifests at startup (§11). | (none) |
| S6-C61 | §4.3 | invariant | Manifest is immutable during execution; agent cannot request expanded permissions at runtime. | (none) |
| S6-C62 | §4.3 | behavior | If an agent needs access not in its manifest, the job fails, failure is logged, operator updates manifest and recreates sandbox. | (none) |
| S6-C63 | §5.1 | structure | Sandbox blueprint is a versioned, declarative specification for creating an agent execution environment. | (none) |
| S6-C64 | §5.1 | structure | Blueprint includes `blueprint_id`, `version`, `base_image`, `isolation`, `filesystem_template`, `network_template`, `resource_ceiling`. | (none) |
| S6-C65 | §5.1 | state | Default isolation profile: `landlock: true`, `seccomp_profile: "drnt-worker-default"`, `network_namespace: "drnt-worker"`, `pid_namespace: true`, `dropped_capabilities: ["ALL"]`, `added_capabilities: []`. | (none) |
| S6-C66 | §5.1 | invariant | Blueprint constrains what the manifest can declare — a manifest cannot grant capabilities the blueprint doesn't permit. | (none) |
| S6-C67 | §5.1 | invariant | Runtime manifest's `resource_limits` must be at or below blueprint's `resource_ceiling`; startup validation rejects otherwise. | (none) |
| S6-C68 | §5.2 | behavior | Blueprints are version-controlled in `/var/drnt/config/blueprints/`; validated at startup; applied at sandbox creation; immutable once running; updated by creating a new version and recreating sandboxes (rolling restart). | (none) |
| S6-C69 | §5.3 | structure | V1 ships with one blueprint: `drnt-worker-base-v1`; all worker agents use it. | (none) |
| S6-C70 | §6 | invariant | Every skill is treated as hostile until proven otherwise. | (none) |
| S6-C71 | §6.1 | invariant | Skills are installed by the operator from outside the sandbox; no skill installation occurs from inside the sandbox; no package manager runs inside; no skill self-installs. | (none) |
| S6-C72 | §6.1 | behavior | Installation sequence: (1) Fetch outside sandbox, (2) Verify with V1 checks, (3) Declare in manifest, (4) Upload to `/sandbox/skills/{agent_type_id}/{skill_name}/`, (5) Register in agent config, (6) Recreate sandbox. | (none) |
| S6-C73 | §6.1 | behavior | V1 verification checks: (a) capture SHA-256 hash of skill artifact, (b) confirm dependency lockfile + review declared deps, (c) inventory binaries vs manifest allowlist, (d) review skill permission manifest, (e) diff skill's declared permissions against agent's runtime manifest. | (none) |
| S6-C74 | §6.2 | structure | Every skill declares a permission manifest: `skill_id`, `version`, `source`, and `requires` (binaries, network_endpoints, filesystem_write, filesystem_read). | (none) |
| S6-C75 | §6.2 | invariant | The skill cannot access anything beyond its declaration; enforcement is by the sandbox, not by the skill. | (none) |
| S6-C76 | §6.3 | state | Skill origins and trust posture: self-authored → container + blueprint default (accidental damage / trusted intent); operator-verified third-party → container + restricted egress (V1 requires explicit verification); unverified third-party → blocked (not permitted in V1). | (none) |
| S6-C77 | §6.4 | invariant | Worker agent types are not capabilities in the Spec 2 sense — no WAL levels, promotion criteria, or action policies. | (none) |
| S6-C78 | §6.4 | invariant | Worker failures attribute to the governing capability that dispatched the job (Spec 2 Section 10), with one exception for sandbox violations. | (none) |
| S6-C79 | §6.4 | invariant | `worker.sandbox_violation` attributes to the sentinel `"worker_sandbox"`, excluded from WAL counters (consistent with `"egress_config"` / `"egress_connectivity"`). | Spec 2 §10.1 |
| S6-C80 | §6.4 | invariant | Quality failures (timeout, attestation failure, completion contract violation) attribute to governing capability WAL counters normally. | (none) |
| S6-C81 | §6.4 | structure | Each agent type maintains a runtime reliability record: total jobs executed, failure count by type, skills at time of failure, last failure timestamp. Informational, not WAL state, not in `capabilities.state.json`. | (none) |
| S6-C82 | §6.4 | structure | `worker.sandbox_violation` and `worker.egress_blocked` carry `skill_id` when attributable. | (none) |
| S6-C83 | §6.4 | invariant | Egress policy violations (`worker.egress_blocked`) attribute to governing capability normally. | (none) |
| S6-C84 | §7 | invariant | All `worker.*` events are durable (write-before-proceed). | (none) |
| S6-C85 | §7.1 | state | Schema version bump from 1.5 to 1.6; new event types added; no envelope changes. | Spec 1 §3 |
| S6-C86 | §7.1 | state | Source enum extended with `worker_egress_proxy` (emits egress_request and egress_blocked) and `sandbox_runtime` (emits sandbox_violation). | (none) |
| S6-C87 | §7.1 | invariant | Worker events carry the governing capability's `capability_id` and `wal_level` in the envelope. | Spec 1 §3.1 |
| S6-C88 | §7.1 | structure | `agent_type_id` and `agent_instance_id` are payload fields, not envelope fields. | (none) |
| S6-C89 | §7.2 | structure | `worker.egress_request` payload: `agent_type_id`, `agent_instance_id`, `target_host`, `target_port`, `method`, `path` (PII-stripped), `requesting_binary`, `policy_result` (allowed│operator_approved), `policy_source` (manifest│session_approval│baseline_policy), `sanitizer_result` (pass), `skill_id`. | (none) |
| S6-C90 | §7.3 | structure | `worker.egress_blocked` payload: `agent_type_id`, `agent_instance_id`, `target_host`, `target_port`, `block_reason` (sanitizer_credential│sanitizer_pii│endpoint_not_in_policy│binary_not_allowed), `blocked_by` (sanitizer│egress_policy), `operator_notified`, `operator_decision` (pending│approved│denied│null), `skill_id`. | (none) |
| S6-C91 | §7.3 | invariant | `sanitizer_credential` blocks are never operator-overridable; `sanitizer_pii` blocks are surfaceable. | (none) |
| S6-C92 | §7.4 | structure | `worker.sandbox_violation` payload: `agent_type_id`, `agent_instance_id`, `violation_type` (filesystem_write│filesystem_read│binary_exec│privilege_escalation│network_bypass), `target`, `blocked` (always true), `skill_id`, `severity` (critical), `failing_capability_id: "worker_sandbox"`. | (none) |
| S6-C93 | §7.4 | invariant | `sandbox_violation` severity is always `critical`. | (none) |
| S6-C94 | §7.4 | behavior | When `job_id` is null, the violation is a system-level integrity event not attributable to any governing capability. | (none) |
| S6-C95 | §7.4 | invariant | When `job_id` is non-null, the event is linked to the job's audit trail but the `"worker_sandbox"` sentinel ensures no WAL demotion. | (none) |
| S6-C96 | §8.1 | structure | Operator TUI views: Active Jobs, Egress Monitor, Violations, Approval Queue. | (none) |
| S6-C97 | §8.2 | behavior | Operator actions: approve blocked egress (session, TTL), deny blocked egress, view audit trail, request worker agent termination (routed through L1), view runtime manifest. | (none) |
| S6-C98 | §8.2 | invariant | TUI termination is routed through L1, not a bypass around it. | §9.5 |
| S6-C99 | §8.3 | state | Session approvals: TTL-bounded; default 4 hours; configurable per blueprint. | (none) |
| S6-C100 | §8.3 | behavior | Permanent approvals require operator to edit runtime manifest and recreate sandbox. | (none) |
| S6-C101 | §8.3 | structure | Blocked egress requests in the approval queue include `job_id`, `agent_type_id`, `skill_id` (if attributable), completion contract summary. | (none) |
| S6-C102 | §9.5 | behavior | Override handling with worker agents in-flight: L1 receives override → sends terminate signal to all active workers for the job via bus → workers halt → proxy drops in-flight egress requests. | Spec 5 |
| S6-C103 | §9.5 | behavior | Cleanup: worker sandboxes cleaned up, temporary files in `/sandbox/work` purged; sandbox persists (it serves agent type, not job). | (none) |
| S6-C104 | §9.5 | behavior | Audit: `worker.egress_blocked` emitted for in-flight requests dropped; standard Spec 5 sequence proceeds. | (none) |
| S6-C105 | §9.5 | behavior | Successor job (redirect/escalate): workers operate under own completion contracts with no state carryover from terminated job. | (none) |
| S6-C106 | §10 | structure | L2 has read access to `worker.*` audit events for the job it is auditing. | (none) |
| S6-C107 | §10 | behavior | L2 treats `worker.sandbox_violation` and `worker.egress_blocked(sanitizer_credential)` as integrity-significant — flagged prominently in decision context. | (none) |
| S6-C108 | §10 | behavior | L2 treats `worker.egress_blocked(endpoint_not_in_policy)` (followed by operator approval) and `worker.egress_request` events as advisory context. | (none) |
| S6-C109 | §10 | invariant | L2 does not gate on `worker.*` events — it does not block delivery based on them. | (none) |
| S6-C110 | §11 | behavior | Startup validation adds: agent types have valid runtime manifests; manifests reference registered blueprints; blueprints meet minimum isolation (Landlock on, seccomp loaded, network namespace isolated, all capabilities dropped); resource_limits ≤ resource_ceiling; skill permission manifests consistent with agent manifests; worker egress proxy and sanitizer operational on `drnt-worker`; no manifest grants filesystem write outside `/sandbox/` and `/tmp`; no manifest grants binaries outside blueprint/skill-declared; `cloud_dispatch_dependencies` resolve; message bus on `drnt-bus` operational. | (none) |
| S6-C111 | §11 | invariant | Validation failures prevent agent type registration; unregistered agent types cannot receive dispatched work. | (none) |
| S6-C112 | §12.1 | state | V1 in-scope items: Docker isolation with dropped capabilities, restricted volumes, network namespace; worker egress proxy + sanitizer (block-only); operator TUI with TTL approvals; termination via L1; runtime manifests with separated `network_egress` and `cloud_dispatch_dependencies`; one blueprint `drnt-worker-base-v1` with `resource_ceiling`; skill install from outside sandbox with verification checklist; self-authored + operator-verified skills only; sandbox violation sentinel excluded from WAL counters; worker quality failures attribute to governing; runtime reliability records per agent type; audit events `worker.egress_request`, `worker.egress_blocked`, `worker.sandbox_violation` (Spec 1 schema 1.6); dedicated `drnt-bus` segment; Spec 5 override interaction for worker termination; L2 distinguishes integrity-significant from advisory. | (none) |
| S6-C113 | §12.2 | state | V1 out-of-scope items: MicroVM isolation for third-party skills; automated skill verification; formal safety proofs; skill marketplace; multi-blueprint environments; cross-agent communication policies. | (none) |
| S6-C114 | §12.3 | state | Designed for but deferred: SMC integration point (continuous safety signal replacing binary pass/block); Solid Protocol integration point (cross-boundary identity). | (none) |

*§11A.* This section is an implementation-status reconciliation, not a claims section. Its contents are not extracted as claims — they are facts about the v0.2 implementation state, relevant to 3B scoring but not to 3A extraction. Logged under "Non-claim observations."

### Spec 7 — Signal Chain Resilience (`specs/DRNT_Spec7_Signal_Chain_Resilience.md`)

| Claim ID | Section | Category | Claim text | Cross-refs noted |
| --- | --- | --- | --- | --- |
| S7-C1 | Preamble | invariant | The signal chain fails partially, never silently; every failure is visible, logged, and recoverable without data loss. | (none) |
| S7-C2 | Preamble | invariant | Jobs are the unit of resilience — if accepted, a job will be completed or explicitly failed, never lost. | (none) |
| S7-C3 | §1 | structure | The signal chain in normal operation is: Watch → iPhone → Desktop Hub → Cloud API → Desktop Hub → iPhone → Watch. | (none) |
| S7-C4 | §1 | invariant | The MacBook Pro serves as fallback L1 orchestrator but is not in the primary signal chain. | §5 |
| S7-C5 | §1 | invariant | V1 eliminates the Raspberry Pi as a gateway component. | (none) |
| S7-C6 | §1 | structure | The Desktop Hub runs all stateful services: Ollama, orchestrator, audit log writer, job queue, Context Packager. | (none) |
| S7-C7 | §1 | structure | iPhone connects to Desktop Hub via Tailscale. | (none) |
| S7-C8 | §2 | structure | Watch → iPhone detection: WatchConnectivity framework; immediate (OS-level); continuous (OS-managed). | (none) |
| S7-C9 | §2 | structure | iPhone → Desktop Hub detection: Tailscale + HTTP health probe to `/health`; 5s timeout; 30s check interval. | (none) |
| S7-C10 | §2 | structure | Desktop Hub → Cloud API detection: per-route health probes (Spec 4 Section 6). | Spec 4 §6 |
| S7-C11 | §2 | structure | Desktop Hub → iPhone detection: WebSocket heartbeat (primary); push notification (advisory); 10s timeout; heartbeat every 30s. | (none) |
| S7-C12 | §2 | structure | iPhone → Watch detection: WatchConnectivity transferUserInfo delivery confirmation; immediate; OS-managed. | (none) |
| S7-C13 | §2 | structure | Orchestrator exposes `GET /health` returning HTTP 200 with JSON body: `orchestrator_status`, `audit_log_status`, `ollama_status`, `last_successful_cloud_probe_timestamp`. | (none) |
| S7-C14 | §2 | behavior | iPhone app treats any non-200 response or timeout as hub unavailable. | (none) |
| S7-C15 | §2.1 | behavior | Health probe state uses asymmetric thresholds: 2 consecutive failures → Hub Unavailable; 3 consecutive successes → Hub Available. | (none) |
| S7-C16 | §2.1 | structure | Between states, iPhone displays "Hub Slow" as degraded indicator. | (none) |
| S7-C17 | §2.1 | invariant | Asymmetric hysteresis prevents thundering herd replay on intermittent connection. | (none) |
| S7-C18 | §2.2 | invariant | APNs provides best-effort delivery; push notifications are a performance optimization, not a reliability mechanism. | (none) |
| S7-C19 | §2.2 | invariant | The correctness path for result delivery is iPhone polling `/jobs/pending` on app foreground and WebSocket reconnection. | (none) |
| S7-C20 | §2.2 | invariant | The system must never depend on push delivery for job completion or state consistency. | (none) |
| S7-C21 | §3 | invariant | Every failure emits a `system.connectivity` event (or queues locally for replay when connectivity is restored). | Spec 1 §4.6 |
| S7-C22 | §3.1 | state | Watch queue capacity = 10 requests; oldest replaced by newest on overflow; user gets haptic notification on drop. | (none) |
| S7-C23 | §3.1 | behavior | On WatchConnectivity reactivation, queued requests forwarded to iPhone in submission order via `transferUserInfo`. | (none) |
| S7-C24 | §3.1 | structure | `system.connectivity` event on Watch: includes `dropped_request_count` if any requests were dropped due to queue overflow. | (none) |
| S7-C25 | §3.2 | structure | iPhone → Hub detection transitions to Hub Unavailable after 2 consecutive probe failures (60 seconds). | §2.1 |
| S7-C26 | §3.2 | behavior | On Hub Unavailable: new requests accepted and queued locally with "Queued" badge; user can continue composing; delivered results remain visible with accept/reject/modify actions queued. | (none) |
| S7-C27 | §3.2 | state | iPhone local queue capacity = 50 requests; 51st rejected with "Local Queue Full". | (none) |
| S7-C28 | §3.2 | invariant | Queued requests persisted to Core Data; survive app termination and device restart. | (none) |
| S7-C29 | §3.2 | invariant | Local storage protection class ≥ `NSFileProtectionCompleteUntilFirstUserAuthentication`. | §16 |
| S7-C30 | §3.2.1 | invariant | Each queued decision carries `result_id` and `response_hash` of the result the user was viewing. | (none) |
| S7-C31 | §3.2.1 | behavior | On replay, the hub validates that the job's current state matches the state the user observed. | (none) |
| S7-C32 | §3.2.1 | behavior | If state has changed, hub rejects the replayed decision with a conflict status. | (none) |
| S7-C33 | §3.2.1 | behavior | iPhone surfaces conflict to user: "This result changed while you were offline. Please review the current version." | (none) |
| S7-C34 | §3.2.1 | behavior | Queued requests submitted in batches of 5 with 2-second delay between batches. | (none) |
| S7-C35 | §3.2.1 | behavior | Queued human decisions are replayed after all pending job submissions complete, in original submission order. | (none) |
| S7-C36 | §3.2 | behavior | On reconnection (3 consecutive probe successes): iPhone polls `/jobs/pending`; `system.connectivity` down/up pair emitted with disconnection duration. | (none) |
| S7-C37 | §3.3 | behavior | On Hub → Cloud failure: hub is still operational; local-capable jobs process normally; cloud jobs emit `job.queued(reason: connectivity)`; user sees "Waiting for cloud". | Spec 1 §4.1 |
| S7-C38 | §3.3 | behavior | Jobs already dispatched that fail receive `model.error`; retry policy from capability's WAL level applies (WAL-0 none; WAL-1+ per policy). | Spec 2 §5 |
| S7-C39 | §3.3 | behavior | If all retries exhaust → `job.failed`, user notified. | (none) |
| S7-C40 | §3.3 | behavior | Recovery is automatic; health probes detect recovery; queued jobs dispatch in order; circuit breakers reset after successful probe. | (none) |
| S7-C41 | §3.3 | behavior | Fallback routing: orchestrator MAY reroute to alternative provider if classification permits; follows normal capability and WAL permission model. | (none) |
| S7-C42 | §3.3 | invariant | At WAL-0, model selection requires human approval (`pre_action` gate), so user explicitly approves any reroute. | Spec 2 §5.1 |
| S7-C43 | §3.4 | behavior | On Hub → iPhone delivery failure: result is in results store; hub retries push with exponential backoff (10s, 30s, 60s, 5m, 15m, hourly). | (none) |
| S7-C44 | §3.4 | invariant | When user opens iPhone app, it polls `/jobs/pending` regardless of notification status. | (none) |
| S7-C45 | §3.4 | invariant | Result delivery failure is non-destructive — result is persisted on hub. | (none) |
| S7-C46 | §3.5 | behavior | iPhone → Watch delivery failure: iPhone displays result normally; Watch notification queued; on reconnect, Watch receives tap with job summary. | (none) |
| S7-C47 | §3.6 | behavior | On hub crash during job acceptance: iPhone marks request as "Submission Unconfirmed" with retry button; user can retry manually or wait for auto-retry. | (none) |
| S7-C48 | §3.6 | invariant | Requests carry a client-generated idempotency key (UUIDv7); hub checks on retry; duplicate submission is impossible. | (none) |
| S7-C49 | §3.6 | state | Idempotency store TTL ≥ 7 days in V1; must exceed maximum realistic offline queue retention. | (none) |
| S7-C50 | §3.6 | behavior | On `system.startup` after crash, non-terminal jobs older than crash timestamp are evaluated: jobs with cloud responses in results store are completed; jobs without responses are re-dispatched; jobs never routed are re-classified. | §4 |
| S7-C51 | §4 | state | Recovery action: accepted, not routed → re-classify and route → emit `job.classified` (new routing). | (none) |
| S7-C52 | §4 | state | Recovery action: routed, not dispatched → re-dispatch → emit `job.dispatched`. | (none) |
| S7-C53 | §4 | state | Recovery action: dispatched, no response — if dispatch was < 5 minutes ago, wait; if > 5 minutes, mark stale and re-dispatch using original idempotency key → emit `job.queued(recovery)` then `job.dispatched`. | (none) |
| S7-C54 | §4 | state | Recovery action: response received, not delivered → deliver → emit `job.delivered`. | (none) |
| S7-C55 | §4 | state | Recovery action: delivered, awaiting review → no action (human decision pending). | (none) |
| S7-C56 | §4 | invariant | Stale job re-dispatch must reuse the original idempotency key, not generate a new one. | (none) |
| S7-C57 | §4 | state | Stale threshold = 5 minutes; must not be set below the longest provider timeout. | (none) |
| S7-C58 | §4 | behavior | If re-dispatched job has received a response, late arrival is logged but does not override the delivered result. | (none) |
| S7-C59 | §4 | state | Re-dispatch cap = 2 attempts during recovery; exhaustion → `job.failed(error_class: recovery_exhausted)`, user notified. | (none) |
| S7-C60 | §5 | structure | MacBook Pro (M5) runs a 7B model locally on the M5 GPU as fallback L1. | (none) |
| S7-C61 | §5 | behavior | MacBook activation is manual; iPhone's Tailscale config includes both Desktop Hub and MacBook; user switches active hub in iPhone settings. | (none) |
| S7-C62 | §5 | behavior | Hub suspension on failover: iPhone sends `suspend_processing` command to old hub if reachable; old hub pauses dispatch, enters suspended state. | (none) |
| S7-C63 | §5 | behavior | If old hub unreachable at switch time, hub's startup self-check pauses job processing if it has not received a heartbeat within a window exceeding its own downtime. | (none) |
| S7-C64 | §5 | invariant | MacBook does not have access to Desktop Hub's vector database, audit log history, or results store — starts with clean job queue. | (none) |
| S7-C65 | §5 | invariant | On MacBook, WAL state does not transfer — all capabilities start at WAL-0. | (none) |
| S7-C66 | §5 | invariant | Context Packager on MacBook operates with default sensitivity rules, not learned/customized Desktop rules. | (none) |
| S7-C67 | §5 | invariant | No automatic failover — exactly one hub is authoritative at any time; switching is a human decision. | (none) |
| S7-C68 | §5 | behavior | Return to primary: user switches active hub back; iPhone sends `suspend_processing` to MacBook; MacBook's completed jobs remain in its local log; V1 does not merge logs. | (none) |
| S7-C69 | §6 | structure | `system.connectivity` (iPhone app) payload: `link` (watch_phone│phone_hub), `state` (down│up│degraded), `duration_ms`, `queued_requests`, `replayed_requests`, `dropped_requests`, `replay_conflicts`. Durable. | (none) |
| S7-C70 | §6 | structure | `system.connectivity` (orchestrator) payload: `link` (hub_cloud), `state`, `affected_routes`, `queued_jobs`. Durable. | (none) |
| S7-C71 | §6 | structure | `system.hub_switch` (iPhone app) payload: `from_hub`, `to_hub`, `reason` (manual│primary_failure), `pending_jobs_on_old_hub`, `suspend_command_delivered`. Durable. | (none) |
| S7-C72 | §6 | state | Degraded state: connectivity exists but unreliable — health probes succeed intermittently, or response times exceed 3× normal baseline. iPhone shows "Hub Slow"; jobs submitted normally; user informed of potential delay. | (none) |
| S7-C73 | §7 | invariant | No silent failures — every failure produces a visible indicator and an audit log event. | (none) |
| S7-C74 | §7 | invariant | Jobs survive everything except simultaneous destruction of hub's job queue storage AND iPhone's local queue. | (none) |
| S7-C75 | §7 | invariant | Idempotency keys are mandatory and immutable; every request carries a UUIDv7; orchestrator enforces idempotency at job creation; re-dispatch reuses original key; hub retains idempotency records ≥ 7 days. | (none) |
| S7-C76 | §7 | invariant | No automatic hub failover — exactly one hub authoritative at any time; switching is human; suspension signals prevent zombie execution. | (none) |
| S7-C77 | §7 | invariant | Push is advisory, polling is authoritative — iPhone polls `/jobs/pending` on every foreground event and WebSocket reconnection. | (none) |
| S7-C78 | §9.1 | state | WAL-0: no decay; WAL-1: 90 days, 10 jobs; WAL-2: 60 days, 25 jobs; WAL-3 (aux only): 30 days, 50 jobs. | Spec 2 §9 |
| S7-C79 | §9.1 | behavior | If window minimum not met, demote by one level. | (none) |
| S7-C80 | §9.2 | behavior | Orchestrator evaluates decay once daily, at startup, or whichever comes first. | (none) |
| S7-C81 | §9.2 | behavior | Decay demotes by one level per evaluation cycle, not to WAL-0 directly. | (none) |
| S7-C82 | §9.2 | invariant | Decay demotion resets counters identically to any other demotion; trust is re-earned from the new level. | Spec 2 §10.2 |
| S7-C83 | §9.2 | behavior | On extended downtime exceeding multiple windows, startup runs multiple evaluation passes in the same startup sequence. | (none) |
| S7-C84 | §9.3 | invariant | Temporal decay is not a failure signal; does not record in `recent_failures`; does not contribute to 3-failures-in-24h trigger. | (none) |
| S7-C85 | §9.3 | state | Temporal decay trigger = `temporal_decay`, distinguishable in audit log from quality-driven demotions. | (none) |
| S7-C86 | §10 | invariant | V1 approach: no recency weighting — activity window is a hard cutoff. | (none) |
| S7-C87 | §11 | state | Demotion trigger enum for ordering: (a) 3 failures in 24h (immediate, from `recent_failures`), (b) temporal_decay (daily), (c) model change (reset to WAL-0), (d) manual, (e) suspension (strip failure). | Spec 2 §10.1 |
| S7-C88 | §11 | invariant | If both failure-triggered and temporal-decay demotions would apply in the same cycle, failure-triggered fires first. | (none) |
| S7-C89 | §12 | structure | Decay parameters defined per-capability in `capabilities.json` under `decay_policy` with keys `wal_1`, `wal_2`, `wal_3`, each having `window_days` and `min_outcomes`. | (none) |
| S7-C90 | §12 | invariant | Startup validation: `window_days > 0` and `min_outcomes > 0` for all configured levels. | (none) |
| S7-C91 | §12 | invariant | Disabling decay requires removing `decay_policy` key entirely, which is logged at startup. | (none) |
| S7-C92 | §12 | invariant | Per-capability overrides must be stricter than system default — `window_days` ≤ default, `min_outcomes` ≥ default; enforced at startup. | (none) |
| S7-C93 | §13 | structure | Temporal decay emits `wal.demoted` with `trigger: temporal_decay` (new enum value), plus payload: `capability_id`, `from_level`, `to_level` (always from_level - 1), `window_days`, `outcomes_in_window`, `min_required`, `last_outcome_timestamp`. | Spec 1 §4.3 |
| S7-C94 | §14 | state | Spec 1 integration: `system.connectivity` events added (§6); `wal.demoted.trigger` extended with `temporal_decay`; `replay_conflicts` and `dropped_requests` fields added to connectivity payload. | (none) |
| S7-C95 | §14 | state | Spec 2 integration: `decay_policy` added to capability config schema; startup validation extended. | (none) |
| S7-C96 | §14 | state | Spec 3 integration: no changes (Context Packager unaffected by device failures). | (none) |
| S7-C97 | §14 | state | Spec 4 integration: no changes (egress health probes are upstream of device failure modes). | (none) |
| S7-C98 | §14 | state | Spec 5 integration: human override queuing during hub disconnection requires version-stamp validation; overrides replayed in submission order with optimistic concurrency control. | (none) |
| S7-C99 | §14 | state | Spec 6 integration: no changes (silo security is hub-internal). | (none) |
| S7-C100 | §14 | state | Event Schema: `system.connectivity` and `system.hub_switch` added; `job.queued.reason` extended with `recovery`; `job.failed.error_class` extended with `recovery_exhausted`. | (none) |
| S7-C101 | §16 | invariant | Tailscale: deny-by-default ACLs; only iPhone (and authorized fallback hub) should reach hub API ports; Tailscale treated as encrypted transport only, not as authorization. | (none) |
| S7-C102 | §16 | invariant | Orchestrator API must enforce its own authentication and authorization independent of network layer. | (none) |
| S7-C103 | §16 | invariant | Ollama run with least-privilege filesystem access, no ambient credentials, restricted network exposure; CVE monitoring and pinning required. Docker network isolation (Spec 4) provides primary enforcement. | Spec 4 §3 |

---

## Unnumbered spec-adjacent artifacts (not extracted)

Per the 3A scope rule, these were NOT walked for claims. Listed here for visibility and 3B/3C consideration.

| Path | Size | Observation |
| --- | --- | --- |
| `governance/DRNT_Capability_Trust_Profile.md` | 28,555 B | Referenced by Spec 7 §9.2 and §11 as authoritative source for the ring buffer and failure counter mechanics. Likely precursor / companion to Spec 2; may contain claims overlapping with or extending Spec 2. |
| `governance/DRNT_Event_Schema.md` | 43,153 B | Naming parallels Spec 1's title ("Audit/Event Schema"). Possibly an older or companion event schema. May contain claims that overlap or conflict with Spec 1. Highest value for 3C cross-reference. |
| `governance/DRNT_NemoClaw_Governance_Overlay.md` | 24,326 B | "Governance overlay" — scope unclear from filename alone. Out of 3A scope. |
| `docs/drnt-specification-technical-overview.md` | 10,887 B | Narrative overview; may restate claims in softer form. Out of 3A scope. |
| `docs/lightweight-evaluation-loop.md` | 16,222 B | Referenced by Spec 2 §12 and Spec 7 §14 as the "Evaluation Loop document." May contain claims about feedback-to-behavior loops. Out of 3A scope. |
| `docs/why-you-cannot-depend-on-a-single-ai-model.md` | 20,823 B | Narrative/motivational. Out of 3A scope. |
| `validation/adversarial-review-methodology.md` | 9,636 B | Methodology doc. Out of 3A scope. |
| `validation/why-one-ai-is-not-enough.md` | 40,580 B | Narrative/motivational. Out of 3A scope. |
| `WAL_Integration_Profile_OpenShell_v3.pdf` | 284,469 B | Repo root. PDF format. May contain integration claims; not extracted. |

---

## Non-claim observations

### Spec 1
- §1 "Purpose" is motivational ("one place to go and one shape when it gets there"); no testable assertions.
- §2.5 "Guarantees and Limitations" also mentions future possibilities ("foundation for stronger guarantees (signed hashes, remote attestation)"); non-normative forward-looking text.
- §3.2 "Example Envelope" is a JSON example, not a claim; reinforces envelope structure claims already extracted.
- §8 "Scope Boundaries" is a pointer list, not assertions.
- §9 "Cross-References to Specs 2–7" is a relationship table — informational, not testable in isolation. Each row *implicitly* claims specific event types exist in this spec; those claims are already captured in §4.

### Spec 2
- §1 "Purpose" is motivational.
- §12 "Scope Boundaries and Cross-References" is a pointer list.
- §8's embedded pseudocode is treated as behavioral specification; each case has been extracted as a claim.

### Spec 3
- §1 "Purpose" is motivational; the "The Context Packager is owned by context.package" sentence is an embedded structural claim (extracted as S3-C1).
- §9 "Scope Boundaries and Cross-References" is a pointer list.

### Spec 4
- §1 "Purpose" is motivational.
- §10 "Scope Boundaries" is a pointer list.
- §2.1 JSON example reinforces structural claims.

### Spec 5
- §1 "Purpose" contains embedded testable assertions ("Every override is durable") extracted as claims; other prose is motivational.
- §15 "Scope Boundaries" is a pointer list.
- §12-§14 "Event Sequence" examples are sequence assertions; each treated as a structural claim (S5-C38, S5-C39, S5-C40) summarizing the expected event chain.

### Spec 6
- §1 and §2 contain a mix of motivational framing ("No layer substitutes for the others") and testable invariants. Testable portions extracted; pure rationale ("why" sections) not extracted.
- §2.1's "Failure mode" paragraphs describe risks ("Misconfiguration at creation time"), not assertions about system behavior. Not extracted.
- §11A is an implementation-status reconciliation against v0.2 code — NOT a specification claims section. It is a forward-reference to the v0.2 implementation. In 3A scope, §11A is treated as evidence/status, not claims. 3B should cross-reference §11A against the Phase 1/2 findings inventory directly.
- §12 "V1 Scope" is a summary recapitulation of items specified throughout; the in-scope and out-of-scope lists are extracted as state claims (S6-C112, S6-C113, S6-C114).
- §13 "Scope Boundaries and Cross-References" is a pointer list.

### Spec 7
- §8 "Problem Statement" is motivational; describes *why* decay is needed, not *what* decay does. Testable assertions in §8 (staleness risk, model change gap) are stated as problem framing and not extracted as independent claims.
- §10 "Recency Weighting" defers V2 approach — forward-looking; only the V1 "no weighting" assertion extracted.
- §15 "What This Spec Does Not Cover" is a scope negation list, not a set of claims.
- §16 "Deployment Notes" contains operational requirements that sit at the boundary of specification and deployment. Testable assertions (Tailscale ACLs, Ollama isolation, iOS Data Protection) extracted as invariants because they are stated as MUST-DO in the deployment context.
- §17 "Revision History" is metadata.
- §1 "Scope and Topology" contains both structural claims (extracted) and rationale (not extracted).

---

## Absence findings

### SPEC-8 status at 3A

**Primary finding:** SPEC-8 is NOT present in the orchestration working tree at `47e1b9f8`. No file matching `SPEC-8*`, `Spec8*`, `*SPEC_8*`, `*MANAGED*`, `*WORKFLOW*`, or `SPEC-MAP*` patterns.

**Historical traces in the gateway repo (HEAD `6e9dc227`):**

`git log --all --oneline -i --grep="spec.\?8"` returned two commits:

- `d673935` — `docs: add SPEC-8-MANAGED-BUILD-WORKFLOW v0.3, update SPEC-MAP, seed plans/ and reports/`
- `28c595c` — `merge: land Spec 8 v0.3 — SPEC-8-MANAGED-BUILD-WORKFLOW, plans/, reports/`

`git log --all --diff-filter=D --name-only -i | grep -iE "spec.?8"` returned empty (no deletions of SPEC-8-named files in gateway history).

**Interpretation (data, not action):** SPEC-8 was authored as `SPEC-8-MANAGED-BUILD-WORKFLOW v0.3` in the **gateway** repo. No deletion commits exist in the gateway, so the file(s) likely still reside in the gateway working tree — but that location falls outside Phase 3A's primary scope (orchestration). The original Phase 3A scope expected SPEC-8 in the orchestration repo alongside Specs 1–7; it is not there. The "testable claim" frame therefore did not get exercised against SPEC-8 content in 3A. SPEC-8 frame-gate was not triggered.

**Historical trace in the orchestration repo:**

`git log --all --oneline -i --grep="spec.\?8"` returned one commit:

- `5db743d` — `docs: bump architecture to v7.0, reframe as spec with gateway as conformance reference`

This commit message does not obviously contain a literal "spec8" substring; the regex match source is unclear from the oneline output alone. Not investigated further per posture. Logged verbatim.

`git log --all --diff-filter=D --name-only -i | grep -iE "spec.?8"` returned empty.

**Disposition:** SPEC-8 extraction deferred (frame never confirmed, content never reached). 3B may decide whether to (a) extract claims from the gateway-side `SPEC-8-MANAGED-BUILD-WORKFLOW`, (b) declare SPEC-8 out-of-scope for orchestration-side audit, or (c) pull the gateway file into the orchestration repo as part of consolidation. No action taken in 3A.

### Other absences

- No `SPEC-MAP.md` in orchestration (the SPEC-MAP referenced in gateway commit `d673935` lives in the gateway, not orchestration).
- No `plans/` or `reports/` directories in orchestration (gateway commit references these; outside orchestration scope).
- No forward-declared SPEC-9, SPEC-10, or higher artifacts found.

---

## Ambiguous claims flagged for 3B

| Claim ID | Ambiguity | Reason |
| --- | --- | --- |
| S1-C7 | "Unix domain socket" vs. "Unix socket" across §2.1 and §7.1 | Terminology variance; likely same thing, but exact protocol boundary merits verification in 3B. |
| S1-C28 | "`capability_id` is set at `job.classified` and carried through all subsequent events" | Spec 1 §3 table says `capability_id` is null for `job.submitted` (pre-classification). The envelope rule "all fields present; null for inapplicable" is consistent, but the transition rule needs tested against actual `job.submitted` events. |
| S1-C65 | "Single-model: one event, `selected: true`. Multi-model: one per model, `selected: true` on the delivered response." | Does "delivered response" mean the one the user accepts, or the first-completed? Ambiguous. |
| S1-C82 | In-memory dedup set spans "current and previous day's files" — but what if the orchestrator has just rolled over at 00:00 UTC and the previous day is the one that just completed? Windowing detail worth verifying. | Boundary case. |
| S2-C77 | "Override cancel/redirect on WAL-1+ (conditional — see Spec 5 §5)" — the list in Spec 5 §6 spans cancel/redirect; whether the demotion list in Spec 2 §10.1 is fully congruent with Spec 5 §6 should be checked in 3B. | Cross-spec dependency. |
| S3-C11 | "External source 0.3 — Minimum. Defense against memory poisoning." Minimum 0.3 suggests a floor; but §3.2 also says "Below minimum threshold (default 0.3)" is excluded. Does external source hit the floor and still get included, or is it always excluded? | Definitional. |
| S3-C31 | WAL-2 "confidence may lower to 0.2" — but the WAL-0/1 minimum is 0.3. Does this override S3-C11's "Minimum 0.3" floor for external sources too, or only for non-external sources? | Interaction of rules. |
| S4-C22 | "Only check 9 emits `context.strip_detail`." But §7.1 of Spec 3 says gateway catch emits `context.strip_detail(detected_by: egress_gateway)`. These are the same, but the claim's uniqueness bears verification against all 11 gateway checks. | Interaction with Spec 3. |
| S5-C26 | "WAL-2 (none): writes on delivery." — But the policy at WAL-2 is `post_action`, and delivery is "post_action". The memory write timing relative to `job.delivered` emission is not precisely pinned. | Timing specification. |
| S5-C32 | "Retroactive cancel on delivered job → `job.revoked(offline_retroactive)`. Human decision at time T takes precedence." — Spec 7 §3.2.1 adds version-stamp validation; if a job was re-dispatched during the offline window, the retroactive cancel may target a superseded job. The interaction is defined in Spec 7 but not fully here. | Interaction with Spec 7. |
| S6-C15 | "Worker execution egress defaults to deny at endpoint level and pass at content pattern level for approved endpoints." §3.4 says "default pass at content level" but §2.3 also says "sanitizer default-block on unrecognized patterns" (mitigation). Tension between the two phrasings. | Internal tension within Spec 6. |
| S6-C46 | "Unrecognized pattern default: content that does not match any known-bad pattern passes." But §2.3 says "sanitizer default-block on unrecognized patterns (Section 3.4)" as a mitigation. Reading §3.4 alone → pass; reading §2.3 → block. Needs reconciliation in 3B. | Intra-spec inconsistency. |
| S6-C73 | V1 verification checks are listed inline; the precedence and failure handling of each step (e.g., what happens if lockfile is present but a dependency is suspicious) is not specified. | Procedural ambiguity. |
| S6-C85 | Schema bump to 1.6: Spec 6 §11A.6 notes that v0.2 uses schema_version "2.0", not 1.6. Spec 6 §7.1 (normative) claims 1.6. This is spec-to-implementation divergence, not spec internal ambiguity, but the claim itself is unambiguous. Noted for completeness. | Spec-vs-impl divergence, documented in §11A. |
| S7-C49 | "Idempotency store TTL ≥ 7 days" vs. §3.6 "at least 7 days, exceeding the maximum realistic offline queue retention period." The "7 days" is a floor; no upper bound. Deployments should increase it — but the spec does not define a detection mechanism for insufficient TTL. | Configuration ambiguity. |
| S7-C53 | Recovery "dispatched, no response — if dispatch was < 5 minutes ago, wait" — The "wait" duration is unspecified. Is it indefinite? Until recovery loop runs again? | Procedural ambiguity. |
| S7-C78 | WAL-0 says "No decay"; but §11's ordering table lists temporal decay alongside other triggers. Decay does not apply at WAL-0 (floor), but §9.2's sequence "WAL-2 idle 120 days → WAL-1 → WAL-0 in same startup" demonstrates decay can demote *to* WAL-0. | Boundary case clarified in §9.1 floor statement. |
| S7-C82 | "Decay demotion resets counters" — but §9.3 says decay is not punitive. Is the counter reset punitive? | Policy nuance. |
| S7-C88 | "If both failure-triggered and temporal-decay demotions would apply in the same cycle, failure-triggered fires first. The temporal decay evaluation then sees the already-demoted level and evaluates against that level's window." Does "same cycle" mean same evaluation pass or same startup? Spec 7 §9.2's multi-pass logic suggests this matters. | Timing ambiguity. |
| S6-C95 / S7-C29 | Encryption/protection claims (NSFileProtection, TLS inspection with trusted CA) are operational/deployment-layer invariants that may not be checkable from application code alone. Testability category may be "structure" rather than "invariant" depending on lens. | Category ambiguity. |

---

## Phase 3A Handoff

- Orchestration HEAD at phase end: `47e1b9f890861942129bdfd6484c5e7ec8b7ee33` (unchanged from phase start)
- Gateway HEAD at phase end: `6e9dc2271c2726d1d564bb0aa946142b69b99d6b` (unchanged from phase start)
- Working tree status: clean, both repos
- Output file: `C:\Users\ljeff\local-first-ai-orchestration\docs\audit\DRNT_Phase3A_Claims_Inventory.md`
- Claim counts:
  - Spec 1: 94
  - Spec 2: 86
  - Spec 3: 38
  - Spec 4: 39
  - Spec 5: 40
  - Spec 6: 114
  - Spec 7: 103
  - Spec 8: 0 (not extracted; see absence findings)
  - **Total: 514 claims**
- Absence findings: SPEC-8 not in orchestration working tree. Two historical commits in gateway repo (`d673935`, `28c595c`) add `SPEC-8-MANAGED-BUILD-WORKFLOW v0.3`. No deletion commits. One commit in orchestration (`5db743d`) matched the SPEC-8 regex but its oneline message does not obviously contain "spec8"; not investigated further per posture. No SPEC-MAP or forward specs in orchestration.
- Ambiguous claims flagged for 3B: 18 claims (see table).
- SPEC-8 disposition: not extracted (content not in orchestration scope). Awaiting 3B direction.
- Deferrals:
  - Unnumbered spec-adjacent artifacts (9 files) logged but not extracted.
  - §11A of Spec 6 (v0.2 implementation-status reconciliation) logged as non-claim observation; to be reconciled against Phase 1/2 findings in 3B.
  - Cross-reference resolution (Pending Cross-Ref score) is explicitly 3B/3C work.
  - Reconciliation of summary-table claim counts (initial estimates) against final per-table counts: table above uses final per-table counts; summary at top is initial estimate. 3B should use the per-table numbers as authoritative.
- File writes: one file created (this one). No commits. No modifications to any spec file.

---

*End of Phase 3A Claims Inventory. Prepared 2026-04-19.*
