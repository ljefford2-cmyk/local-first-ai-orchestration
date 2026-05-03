# DRNT Phase 3C — Resolved Claims Inventory

## Preamble

- Orchestration HEAD: `47e1b9f890861942129bdfd6484c5e7ec8b7ee33` (unchanged from 3A/3B)
- Gateway HEAD: `6e9dc2271c2726d1d564bb0aa946142b69b99d6b` (unchanged from 3A/3B)
- Orchestration remote: `https://github.com/ljefford2-cmyk/local-first-ai-orchestration.git`
- Gateway remote: `https://github.com/ljefford2-cmyk/local-first-ai-gateway.git`
- Working trees at resolution time: orchestration has untracked `docs/audit/` (expected); gateway clean
- Generation date: 2026-04-19
- Posture: read-only; cross-referencing across specs is the explicit purpose of this phase; no spec or code edits
- Baseline: `DRNT_Phase3B_Scored_Inventory.md` (preserved untouched; this file layers 3C resolutions atop 3B)
- Primary evidence sources: 3B inventory, the seven spec files in `specs/`, `DRNT_Phase1_Phase2_Findings_Inventory.md`
- **FI** = Findings Inventory reference (e.g., `FI §2.6` = Probe 5 findings).
- Baseline claim counts inherited from 3A per-table enumeration: S1:94, S2:86, S3:38, S4:39, S5:40, S6:114, S7:103 (Total 514). Summary-table estimates in 3A (433) are superseded by per-table counts, per 3A handoff direction.
- **3B accounting note:** A row-count audit during 3C found that 3B's scoring summary table reported pending-cross-ref=26 but the actual rows scored `pending-cross-ref` in 3B's tables number **21**. The 3B summary's other column totals also diverge from the per-row counts (see Phase 3C anomalies log). 3C operates on the 21 actual pending-cross-ref rows; the corrected 3B baseline counts are reflected in the 3C summary table below.

## Scoring taxonomy (six states)

- **aligned** — code matches the claim
- **partial** — code implements some of the claim; specific gap noted
- **missing** — claim has no implementation; no counter-behavior either
- **contradicted** — code implements something that disagrees with the claim
- **ambiguous** — claim unclear, self-contradictory, or multi-read (defaults for 3A's 18 pre-flagged items)
- **pending-cross-ref** — score depends on another spec; deferred to 3C
- **not-testable-from-code** — governance/policy/intent claim not mappable to code behavior

---

## Claims by spec

### Spec 1 — Audit/Event Schema

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S1-C1 | §2.1 | structure | Audit log is JSONL — one JSON object per line. | (none) | aligned | FI §1.7 + FI §2.3 |
| S1-C2 | §2.1 | structure | One file per UTC calendar day, named `drnt-audit-YYYY-MM-DD.jsonl`. | (none) | aligned | FI §6 (12 daily files named thus) |
| S1-C3 | §2.1 | structure | Log location on Desktop hub is `/var/drnt/audit/` inside a Docker volume, bind-mounted from host filesystem. | (none) | aligned | docker-compose.yml:8-11 (audit-logs volume) |
| S1-C4 | §2.1 | invariant | Only the log writer container has write access to audit files. | (none) | aligned | docker-compose.yml (audit-log-writer mounts rw; orchestrator mounts :ro) |
| S1-C5 | §2.1 | invariant | All other containers — including the orchestrator — mount `/var/drnt/audit/` read-only. | (none) | aligned | docker-compose.yml:52 (`audit-logs:/var/drnt/audit:ro`) |
| S1-C6 | §2.1 | invariant | The orchestrator does not write directly to audit files under any circumstances. | (none) | aligned | docker-compose.yml (`:ro` mount enforces) |
| S1-C7 | §2.1 | behavior | Orchestrator sends events to the log writer over a Unix domain socket. | §7 | aligned | FI §2.4 (Unix domain socket `/var/drnt/sockets/audit.sock`) |
| S1-C8 | §2.2 | structure | Every event includes `prev_hash`: SHA-256 of the previous event's JSON line. | (none) | aligned | FI §2.3 |
| S1-C9 | §2.2 | invariant | The first event of each day file chains to the last event of the previous day's file. | (none) | aligned | FI §2.3 (directory-mode verification) |
| S1-C10 | §2.2 | invariant | Modifying any event invalidates all subsequent hashes. | (none) | aligned | FI §2.3 (all three tamper classes caught) |
| S1-C11 | §2.2 | structure | A standalone script `drnt-audit-verify` walks the chain and reports breaks. | (none) | aligned | FI §2.3 (CLI verifier exercised) |
| S1-C12 | §2.2 | behavior | `drnt-audit-verify` runs nightly via cron and on-demand, independent of the orchestrator. | (none) | missing | FI §2.3 (only CLI verifier exercised; no cron scheduling observed) |
| S1-C13 | §2.3 | behavior | The log writer determines target file by the UTC date of the `committed_at` timestamp. | (none) | aligned | audit-log-writer/src/file_manager.py (UTC daily file selection) |
| S1-C14 | §2.3 | behavior | At day boundary: finish current event, record SHA-256 hash of last line, next event opens a new file chaining from that hash. | (none) | aligned | FI §2.3 (cross-day chain verified directory-mode) |
| S1-C15 | §2.3 | invariant | The log writer is single-threaded with respect to writes — no race condition. | (none) | aligned | audit-log-writer/src/log_writer.py (single writer process) |
| S1-C16 | §2.4 | state | Genesis seed = SHA-256 of `"DRNT-GENESIS"` = `9cd9da92...a4d4`. | (none) | aligned | audit-log-writer/src/hash_chain.py:5 (`GENESIS_STRING = "DRNT-GENESIS"`) |
| S1-C17 | §2.4 | behavior | The first event ever written uses the genesis seed hash. | (none) | aligned | FI §2.3 (verifier unconditionally initializes prev_hash=sha256("DRNT-GENESIS")) |
| S1-C18 | §2.4 | behavior | If no previous day exists, the genesis seed is used for that day's first-event `prev_hash`. | (none) | partial | FI §2.3 (verifier unconditionally uses genesis — single-file mode fails for post-genesis days; claim is technically aligned for writer behavior but verifier mode mismatch surfaces issue) |
| S1-C19 | §2.5 | behavior | The chain guarantees tamper evidence: if any event is modified, inserted, or deleted, the chain breaks. | (none) | aligned | FI §2.3 (all three tested) |
| S1-C20 | §2.5 | invariant | The chain does not prevent tampering, only makes it evident. | (none) | aligned | FI §2.3 |
| S1-C21 | §3 | structure | Every event carries the 13-field envelope. | (none) | aligned | audit-log-writer/src/models.py:27-40 (envelope fields) |
| S1-C22 | §3 | state | `schema_version` is `"1.5"`. | (Spec 6 §7.1 bumps to 1.6) | contradicted | audit-log-writer/src/models.py:20 (`SCHEMA_VERSION = "2.0"`) |
| S1-C23 | §3 | state | `event_id` is UUIDv7, assigned by log writer at commit time. | (none) | aligned | audit-log-writer/src/log_writer.py (writer assigns event_id) |
| S1-C24 | §3 | state | `source_event_id` is UUIDv7, generated by emitter at occurrence time; stable across retransmissions; used for deduplication. | §7.2 | aligned | FI §2.7 (audit-layer dedup by source_event_id) |
| S1-C25 | §3 | state | `timestamp` and `committed_at` are ISO 8601 µs UTC. | (none) | aligned | orchestrator/events.py (ISO UTC format in builders) |
| S1-C26 | §3 | structure | `event_type` is a dot-namespaced string enum; determines payload structure. | (none) | aligned | audit-log-writer/src/event_validator.py:64-66 (string type, builders enforce namespacing) |
| S1-C27 | §3 | structure | `capability_id` is null for system events and `job.submitted` (pre-classification). | (none) | ambiguous | 3A pre-flag (S1-C27/C28 transition rule needs testing against actual events) |
| S1-C28 | §3 | behavior | `capability_id` is set at `job.classified` and carried through all subsequent events; never mutated. | §3.1 | ambiguous | 3A pre-flag |
| S1-C29 | §3 | structure | `wal_level` range is `int (-1..3)` or null; -1 = suspended. | (none) | aligned | FI §2.6 (permission_checker.py:99-270 suspended branch; capabilities.json supports levels) |
| S1-C30 | §3 | structure | `source` enum: `orchestrator`, `context_packager`, `egress_gateway`, `watch_app`, `phone_app`, `human`, `system`, `log_writer`. | (Spec 6 adds `worker_egress_proxy`, `sandbox_runtime`) | contradicted | audit-log-writer/src/models.py:7-16 (VALID_SOURCES; Spec 6 additions NOT present — see S6-C86) |
| S1-C31 | §3 | structure | `durability` enum: `"durable"` or `"best_effort"`. | (none) | aligned | audit-log-writer/src/models.py:18 (VALID_DURABILITY frozenset) |
| S1-C32 | §3 | structure | `payload` is fixed per `event_type`; all fields present in every instance; null for inapplicable. | (none) | aligned | orchestrator/events.py (each builder emits fixed shape) |
| S1-C33 | §3.1 | invariant | In v1, every job has exactly one governing capability, established at `job.classified` and carried through all subsequent events. | Spec 2 | aligned | FI §2.6 (governing_capability_id assigned at classification) |
| S1-C34 | §3.1 | behavior | Classification runs before capability permission checks and determines which gates apply. | (none) | aligned | FI §2.6 + orchestrator/job_manager.py classify→permission_check sequence |
| S1-C35 | §3.1 | behavior | Auxiliary capabilities emit their own `wal.permission_check` events; envelope always reflects governing. | Spec 2 §2.1 | aligned | Spec 1 §3.1 (claim) + Spec 2 §2.1 (cross-ref consistent: "Each check emits its own `wal.permission_check` with its own `capability_id`. Never in the envelope.") + FI §2.6 |
| S1-C36 | §3.1 | structure | Auxiliary checks carry `dependency_mode`: `required` │ `optional` │ `best_effort`. | Spec 2 §2.1 | aligned | Spec 1 §3.1 (claim) + Spec 2 §2.1 (cross-ref consistent: dependency_mode table required/optional/best_effort with halt/skip/log behavior) + FI §2.6 (dependency_mode field present in wal.permission_check payload) |
| S1-C37 | §3.1 | behavior | Failures are attributed to the capability that failed, not the governing capability. | (none) | aligned | FI §2.6 (wal.permission_check payload carries capability_id, block_reason) |
| S1-C38 | §3.1 | structure | `job.failed` carries `failing_capability_id`. | (none) | aligned | orchestrator/events.py:429 (event_job_failed payload) |
| S1-C39 | §3.1 | structure | `context.strip_detail` separates `detected_by` from `failing_capability_id`. | (none) | aligned | orchestrator/events.py:668 (event_context_strip_detail builder) |
| S1-C40 | §3.1 | invariant | Sentinel values `"egress_config"` and `"egress_connectivity"` are excluded from WAL failure counters. | Spec 2 §10.1 | aligned | orchestrator/override_types.py:17 (SENTINEL_FAILURE_IDS) |
| S1-C41 | §3.1 | behavior | Redirect and escalate overrides terminate the original and spawn a successor. | Spec 5 | aligned | Spec 1 §3.1 (claim) + Spec 5 §2/§3 (cross-ref consistent: "Redirect and escalate terminate the original job and spawn a successor with a new `job_id` + `parent_job_id`") + orchestrator/override_types.py:29 (SPAWNS_SUCCESSOR frozenset) |
| S1-C42 | §3.1 | invariant | The governing capability is never mutated within a job. | (none) | aligned | FI §2.6 (single governing per job) |
| S1-C43 | §4 | structure | Payload schemas are fixed per `event_type`; null for inapplicable, not omission. | (none) | aligned | orchestrator/events.py (builders emit full shape) |
| S1-C44 | §4.1 | invariant | Every job produces at minimum `job.submitted` and one terminal event (delivered/failed/revoked). | (none) | aligned | FI §1.7 (11-event happy path) + FI §2.8 |
| S1-C45 | §4.1 | behavior | `job.failed` may be followed by a new `job.dispatched` with incremented `retry_sequence`. | (none) | not-testable-from-code | FI §4 (retry path not exercised) |
| S1-C46 | §4.1 | state | v1 retry constraint: same capability only. | Spec 2 §8 | aligned | Spec 1 §4.1 (claim) + Spec 2 §8 (cross-ref consistent: "**Same-capability retry (v1):** `same_capability_only: true`. Cross-capability retry is v2.") + config/capabilities.json (`same_capability_only: true`) |
| S1-C47 | §4.1 | structure | Redirect and escalate spawn a successor with a new `job_id`. | Spec 5 | aligned | Spec 1 §4.1 (claim) + Spec 5 §3 (cross-ref consistent: "Redirect and escalate terminate the original job and spawn a successor with a new `job_id` + `parent_job_id`") + orchestrator/override_types.py:29 |
| S1-C48 | §4.1 | structure | `job.classified` payload includes `declared_pipeline`. | (none) | aligned | orchestrator/events.py:160 (event_job_classified) |
| S1-C49 | §4.1 | structure | `job.dispatched` includes `assembled_payload_hash` and `egress_config_hash`. | (none) | aligned | FI §2.2 + orchestrator/events.py:250 |
| S1-C50 | §4.1 | behavior | `job.dispatched` emitted when gateway confirms outbound transmission, BEFORE model response. | Spec 4 §4 | aligned | Spec 1 §4.1 (claim) + Spec 4 §4 (cross-ref consistent: "`job.dispatched` is emitted when the gateway confirms outbound transmission, BEFORE the model responds. It IS the authorization record—no separate egress-allow event.") + orchestrator/events.py:250 |
| S1-C51 | §4.1 | structure | `job.failed.failing_capability_id` may use sentinel values for transport failures. | (none) | aligned | orchestrator/override_types.py:17 |
| S1-C52 | §4.1 | state | Three terminal states: `job.delivered`, `job.failed`, `job.revoked`. | (none) | aligned | FI §2.8 (all three observed) |
| S1-C53 | §4.1 | invariant | Revocation does not delete prior events. | (none) | aligned | FI §2.8 (events remain after revoke) |
| S1-C54 | §4.2 | invariant | Every outbound cloud dispatch produces exactly one `context.packaged` event. | (none) | partial | FI §1.3 (gated on `routing=="cloud"`; local paths do NOT emit — claim holds for cloud only) |
| S1-C55 | §4.2 | behavior | `context.strip_detail` emitted once per non-pass transformation. | (none) | aligned | orchestrator/events.py:668 |
| S1-C56 | §4.2 | structure | `context.packaged` carries `assembled_payload_hash` and `context_object_hash`. | (none) | aligned | orchestrator/events.py:455 |
| S1-C57 | §4.2 | behavior | Rollup arrays use `field_id`s, not names; a single `field_id` may appear in multiple rollup arrays. | (none) | not-testable-from-code | Deep payload structure; not exercised |
| S1-C58 | §4.2 | invariant | Default-deny on unclassifiable content → `failing_capability_id` null (conservative success). | (none) | aligned | orchestrator/context_packager.py (pattern block convention) |
| S1-C59 | §4.2 | behavior | Egress gateway catches set `failing_capability_id: "context.package"`. | (none) | partial | FI §1.4 (`event_egress_validation_failure` dead; egress-gateway emits no audit events) |
| S1-C60 | §4.3 | state | Permission result enum: `allowed` │ `blocked` │ `held`. | (none) | aligned | FI §2.6 (three results with block_reason branches) |
| S1-C61 | §4.3 | invariant | `delivery_hold: true` on allowed result → `pre_delivery` gate applied downstream. | Spec 2 §4 | aligned | Spec 1 §4.3 (claim) + Spec 2 §4 gate-encoding table (cross-ref consistent: `pre_delivery` = "result: allowed, delivery_hold: true") + FI §2.6 |
| S1-C62 | §4.3 | invariant | `pre_delivery` is never a `held` result. | (none) | aligned | Spec 1 §4.3 (claim — "This is the canonical encoding—`pre_delivery` is never a held result.") + Spec 2 §4 (cross-ref consistent: encoding table maps pre_delivery to allowed-with-delivery_hold, and held only to pre_action/cost_approval/on_accept) + FI §2.6 |
| S1-C63 | §4.3 | structure | `wal.permission_check.hold_type` ∈ {`pre_action`, `cost_approval`, `on_accept`, null}. | (none) | aligned | FI §2.6 (hold_type field present) |
| S1-C64 | §4.4 | invariant | Every model call produces a `model.response` event. | (none) | aligned | FI §1.7 (happy path emits model.response) |
| S1-C65 | §4.4 | behavior | Single-model vs multi-model `selected: true` semantics. | (none) | ambiguous | 3A pre-flag |
| S1-C66 | §4.4.1 | structure | `response_hash` is SHA-256 hex of exact bytes in results store. | (none) | aligned | orchestrator/events.py:275 (event_model_response response_hash field) |
| S1-C67 | §4.4.1 | structure | Structured responses hashed as UTF-8 canonical JSON. | (none) | not-testable-from-code | Deep serialization detail |
| S1-C68 | §4.4.1 | invariant | `result_id` is the join key between audit log and results store. | §6.3 | aligned | orchestrator/events.py (result_id in response payloads) |
| S1-C69 | §4.5 | structure | Modified result lineage fields on `human.reviewed`. | (none) | not-testable-from-code | Spec 1 §4.5 (claim — modified_result_id, modified_result_hash, derived_from_result_id) + Spec 5 §7 (cross-ref consistent: "Both artifacts in results store. Both hashes in audit log. Full diffing always possible.") + FI §4 (modify path not live-tested; spec-vs-spec consistent but runtime fields not exercised) |
| S1-C70 | §4.6 | structure | `system.config_change` only for human-edited config; runtime persistence does NOT emit. | Spec 2 §6.1 | aligned | orchestrator/events.py:108 (event_system_config_change) |
| S1-C71 | §4.7 | behavior | `egress.validation_failure` does NOT trigger strip-failure trust path and does NOT carry `"context.package"`. | Spec 3 §7.2, Spec 4 | partial | FI §1.4 + §2.2 (event builder is dead; orchestrator emits `event_job_failed` with `error_class=<failure_type>` instead) |
| S1-C72 | §5 | structure | `prev_hash` of event N is SHA-256 hex of complete JSON line of event N-1, UTF-8, excluding trailing newline. | (none) | aligned | audit-log-writer/src/hash_chain.py + FI §2.3 |
| S1-C73 | §6.1 | state | V1 retention: retain all log files indefinitely. | (none) | not-testable-from-code | Operational; no retention policy code |
| S1-C74 | §6.2 | state | V1 querying tooling: `jq`, `grep`, `tail -f`. No database. | (none) | not-testable-from-code | Operational/external tooling |
| S1-C75 | §6.3 | structure | Results store is separate from audit log; join key is `result_id` (UUIDv7). | (none) | aligned | docker-compose.yml (results-store volume separate from audit-logs) |
| S1-C76 | §6.3 | invariant | Modified results stored under `modified_result_id` as separate immutable artifacts. | (none) | not-testable-from-code | FI §4 (modify not live-tested) |
| S1-C77 | §6.4 | structure | Context store at `/var/drnt/context/`; join key `context_package_id`. | (none) | aligned | docker-compose.yml (drnt-context-store volume, mount path) |
| S1-C78 | §6.4 | invariant | Each context package is a JSON file, written once, never modified. | (none) | aligned | orchestrator/context_packager.py (Stage 4 writes once) |
| S1-C79 | §6.4 | invariant | `assembled_payload_hash` + `context_object_hash` anchor the payload. | (none) | aligned | orchestrator/events.py:455 |
| S1-C80 | §7.1 | structure | Log writer is a separate lightweight process on Unix domain socket. | (none) | aligned | FI §2.4 + docker-compose (audit-log-writer service) |
| S1-C81 | §7.1 | behavior | Writer startup reads last line to initialize chain state. | (none) | aligned | FI §2.3 (verifier behavior consistent; writer init mirrors) + audit-log-writer/src/file_manager.py |
| S1-C82 | §7.2 | state | In-memory dedup set spans current and previous day's files. | (none) | ambiguous | 3A pre-flag |
| S1-C83 | §7.2 | behavior | Duplicates dropped silently. | (none) | aligned | FI §2.7 (audit-layer dedup by source_event_id; duplicates silent) |
| S1-C84 | §7.3 | behavior | Durable: append + fsync then ACK; emitter blocks until ACK. | (none) | aligned | FI §2.4 |
| S1-C85 | §7.3 | state | Durable timeout is 5 seconds, then retry (same source_event_id). | (none) | partial | FI §2.4 (3 retries w/ exp backoff 100ms→200ms→400ms capped 5000ms — matches timeout cap but not the "then retry" phrasing) |
| S1-C86 | §7.3 | behavior | Gated action does not proceed until durable ACK confirmed. | (none) | partial | FI §2.4 holds at submission; FI §2.5 shows 55/58 mid-pipeline sites have no TimeoutError handler — holds only at submission |
| S1-C87 | §7.3 | behavior | Best-effort: append to buffer; flush on next durable write or 1s interval. | (none) | partial | FI §2.4 (best-effort silently swallows; flush-interval detail not tested) |
| S1-C88 | §7.3 | invariant | Fail-closed: orchestrator halts on writer down. | (none) | partial | FI §2.4 (submission fail-closed holds, HTTP 503) + FI §2.5 (mid-pipeline does NOT halt — 55/58 emit sites no handler, _worker_loop catch-all doesn't replay) |
| S1-C89 | §7.3 | invariant | Best-effort events silently dropped if writer down. | (none) | aligned | FI §2.4 (emit_best_effort swallows at debug) |
| S1-C90 | §7.4 | structure | Durable event-type list (22 types). | (none) | partial | orchestrator/events.py has all builders, BUT FI §1.4 shows `event_egress_validation_failure` + `event_model_error` are dead — two types unreachable in production |
| S1-C91 | §7.4 | structure | Best-effort event-type list: `job.queued`, `human.feedback`, `system.health_check`, `system.connectivity`. | (none) | partial | job.queued emitter exists (events.py:996); system.connectivity exists; others not verified |
| S1-C92 | §7.5 | behavior | Components provide all fields except `event_id`, `committed_at`, `prev_hash`. | (none) | aligned | orchestrator/audit_client.py + orchestrator/events.py (builders omit these; writer assigns) |
| S1-C93 | §7.6 | behavior | Watch/phone events transmitted over Tailscale; offline events queue locally. | Spec 7 §3.1-3.2 | not-testable-from-code | Spec 1 §7.6 (claim) + Spec 7 §3.1 (Watch local queue cap 10, transferUserInfo on reconnect) + Spec 7 §3.2 (iPhone Tailscale link, queued locally, Core Data persistence) — cross-ref consistent across specs; iOS layer not in repo so behavior not testable from code |
| S1-C94 | §7.6 | state | `source_event_id` + `timestamp` set on-device; `committed_at` set by log writer. | (none) | not-testable-from-code | Spec 1 §7.6 (claim) + Spec 7 §3 (cross-ref consistent: "every failure emits a `system.connectivity` event ... or queues the event locally for replay" — implies on-device emitter assigns timestamps) + audit-log-writer assigns `committed_at` (FI §1.7, §2.3); on-device half not testable since iOS apps absent from repo |

### Spec 2 — Capability Model

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S2-C1 | §1 | invariant | Every WAL state change and permission check emits durable events. | Spec 1 §4.3 | aligned | FI §2.6 (wal.permission_check durable) |
| S2-C2 | §1 | invariant | All WAL events require fsync+ACK before gated action proceeds. | Spec 1 §7.3 | partial | FI §2.4 (submission only) + FI §2.5 (mid-pipeline bypass via 55/58 no-handler sites) |
| S2-C3 | §2 | structure | Each capability has own WAL level, promotion history, demotion triggers, action policy set. | (none) | aligned | config/capabilities.json (full schema per capability) |
| S2-C4 | §2 | invariant | WAL is never global. | (none) | aligned | config/capabilities.json (per-capability) |
| S2-C5 | §2.1 | state | Governing capability owns routing/delivery/retry/model-selection; in envelope. | Spec 1 §3.1 | aligned | FI §2.6 + config/capabilities.json (capability_type: governing) |
| S2-C6 | §2.1 | state | Auxiliary capability owns pipeline support; emits own `wal.permission_check`; never in envelope. | (none) | pending-cross-ref | Depends on Spec 1 §3.1 |
| S2-C7 | §2.1 | invariant | Action ownership exclusive — no action in both governing and auxiliary sets. | §11 | aligned | config/capabilities.json (dispatch_*, auxiliary actions non-overlapping) |
| S2-C8 | §2.2 | behavior | Block on `required` → pipeline halts. | (none) | missing | FI §2.6 (PipelineEvaluator defined but not wired into /jobs runtime) |
| S2-C9 | §2.2 | behavior | Block on `optional` → step skipped. | (none) | missing | FI §2.6 (PipelineEvaluator not wired) |
| S2-C10 | §2.2 | behavior | Block on `best_effort` → step attempted but failure doesn't block. | (none) | missing | FI §2.6 (PipelineEvaluator not wired) |
| S2-C11 | §3 | invariant | Action enum is authoritative for `requested_action`. | (none) | aligned | orchestrator/permission_checker.py (action branches) |
| S2-C12 | §3.1 | structure | Governing actions: `dispatch_local`, `dispatch_cloud`, `dispatch_multi`, `select_model`, `deliver_result`, `format_result`, `auto_retry`. | (none) | aligned | config/capabilities.json (action_policies keys) |
| S2-C13 | §3.1 | structure | `dispatch_local` owned by `route.local`. | (none) | aligned | config/capabilities.json:13 |
| S2-C14 | §3.1 | structure | `dispatch_cloud` owned by `route.cloud.*`. | (none) | aligned | config/capabilities.json:55 (route.cloud.claude has dispatch_cloud) |
| S2-C15 | §3.1 | structure | `dispatch_multi` owned by `route.multi`. | (none) | aligned | config/capabilities.json (route.multi entry) |
| S2-C16 | §3.1 | structure | `select_model` owned by `route.cloud.*` and `route.local`. | (none) | aligned | config/capabilities.json |
| S2-C17 | §3.1 | structure | `auto_retry` v1 constraint: same capability only. | (none) | aligned | config/capabilities.json (`same_capability_only: true`) |
| S2-C18 | §3.2 | structure | Auxiliary actions mapping. | (none) | aligned | config/capabilities.json (aux caps present) |
| S2-C19 | §4 | invariant | Every action at every WAL has a gate. | (none) | aligned | config/capabilities.json (all levels populate review_gate) |
| S2-C20 | §4 | structure | Gate encoding table. | Spec 1 §4.3 | aligned | orchestrator/permission_checker.py (matches encoding) |
| S2-C21 | §4 | structure | Action policy object structure. | (none) | aligned | config/capabilities.json |
| S2-C22 | §4 | invariant | Local config may tighten spec defaults but not relax. | (none) | aligned | orchestrator/startup_validator.py |
| S2-C23 | §4 | behavior | Startup validation rejects configs that relax gates. | (none) | aligned | orchestrator/startup_validator.py |
| S2-C24 | §5.1 | state | WAL-0 `dispatch_local`: `pre_delivery`, no cost, no retry. | (none) | aligned | config/capabilities.json:15 |
| S2-C25 | §5.1 | state | WAL-0 `dispatch_cloud`: `pre_delivery`, $0.25, no retry. | (none) | aligned | config/capabilities.json:58 |
| S2-C26 | §5.1 | state | WAL-0 `dispatch_multi`: `pre_delivery`, $0.50, no retry. | (none) | aligned | config/capabilities.json (route.multi entry) |
| S2-C27 | §5.1 | state | WAL-0 `select_model`: `pre_action`. | (none) | aligned | config/capabilities.json:16 |
| S2-C28 | §5.1 | state | WAL-0 `deliver_result`: `pre_delivery`. | (none) | aligned | config/capabilities.json:17 |
| S2-C29 | §5.1 | state | WAL-0 `format_result`: blocked. | (none) | aligned | config/capabilities.json:18 (`"blocked"`) |
| S2-C30 | §5.1 | state | WAL-0 `auto_retry`: blocked. | (none) | aligned | config/capabilities.json:19 |
| S2-C31 | §5.1 | state | WAL-0 aux gates `none`. | (none) | aligned | config/capabilities.json (aux caps) |
| S2-C32 | §5.1 | state | WAL-0 `write_memory`: blocked. | (none) | aligned | config/capabilities.json (memory.write entry) |
| S2-C33 | §5.2 | state | WAL-1 changes: `format_result`→pre_delivery; `auto_retry`→none (max 2); `write_memory`→on_accept. | (none) | aligned | config/capabilities.json (level "1" objects) |
| S2-C34 | §5.3 | state | WAL-2 changes: various transitions. | (none) | aligned | config/capabilities.json (level "2" objects) |
| S2-C35 | §5.4 | state | WAL-3 aux only in v1; `send_notification`/`queue_job` gate `none`. | (none) | aligned | config/capabilities.json (WAL-3 only for aux) |
| S2-C36 | §6.1 | structure | Registry files: `capabilities.json` + `capabilities.state.json`. | (none) | aligned | docker-compose.yml env vars `DRNT_CAPABILITIES_CONFIG` + `DRNT_CAPABILITIES_STATE` |
| S2-C37 | §6.1 | invariant | `system.config_change` emitted only for human-edited changes. | Spec 1 §4.6 | aligned | orchestrator/events.py:108 |
| S2-C38 | §6.2 | state | `desired_wal_level` human; `effective_wal_level` runtime; permission checker uses effective. | (none) | aligned | orchestrator/capability_state.py |
| S2-C39 | §6.2 | behavior | Reconciliation: config unchanged → no WAL events. | (none) | not-testable-from-code | Reconciliation flow not exercised in FI |
| S2-C40 | §6.2 | behavior | Config changed (desired>effective) → `wal.promoted`. | (none) | partial | orchestrator/capability_state.py has logic; not exercised in FI |
| S2-C41 | §6.2 | behavior | Config changed (desired<effective) → `wal.demoted(trigger: manual)`. | (none) | partial | orchestrator/capability_state.py has logic; not exercised |
| S2-C42 | §6.2 | behavior | Suspended recovery: -1→0 via `wal.promoted`. | (none) | partial | orchestrator/capability_state.py; not exercised in FI |
| S2-C43 | §6.3 | structure | Registry entry fields. | (none) | aligned | config/capabilities.json |
| S2-C44 | §6.3 | structure | `provider_dependencies` null or array. | (none) | aligned | config/capabilities.json (`"provider_dependencies": null` seen) |
| S2-C45 | §6.3 | behavior | Each provider_dep checked before dispatch; suspended excluded; all suspended → blocked. | (none) | partial | FI §2.6 (PipelineEvaluator not wired; provider_dependencies pre-check absent in runtime /jobs flow) |
| S2-C46 | §6.4 | state | State entry fields. | (none) | aligned | orchestrator/capability_state.py |
| S2-C47 | §6.4 | state | `evaluable_outcomes` ring buffer max 200. | (none) | partial | orchestrator/capability_state.py has deque; size cap TBD |
| S2-C48 | §6.4 | state | `auto_delivered` = WAL-2+ delivered, not overridden within 24h. | §9.2 | aligned | docker-compose.yml DRNT_AUTO_ACCEPT_WINDOW (default 86400s = 24h) |
| S2-C49 | §6.4 | state | `recent_failures` 24h eviction; 3 failures in 24h → demotion. | §10.1 | partial | orchestrator/demotion_engine.py + capability_state.py exist; not exercised in FI |
| S2-C50 | §6.4 | invariant | Successes between failures do NOT reset count. | (none) | partial | Per capability_state.py logic; not exercised |
| S2-C51 | §7.1 | structure | V1 governing inventory. | (none) | aligned | config/capabilities.json |
| S2-C52 | §7.2 | structure | V1 auxiliary inventory. | (none) | aligned | config/capabilities.json |
| S2-C53 | §8 | behavior | Unknown capability → blocked("unknown_capability"). | (none) | aligned | FI §2.6 (permission_checker.py branches include unknown) |
| S2-C54 | §8 | behavior | Suspended (effective_wal==-1) → blocked("suspended"). | (none) | aligned | FI §2.6 (suspended branch) |
| S2-C55 | §8 | behavior | Policy missing/blocked → blocked("action_not_permitted"). | (none) | aligned | FI §2.6 (action_not_permitted branch) |
| S2-C56 | §8 | behavior | cost_gate_usd > gate → held("cost_approval"). | (none) | aligned | FI §2.6 (cost_gate_usd branch) |
| S2-C57 | §8 | behavior | pre_action no approval → held(pre_action). | (none) | aligned | FI §2.6 (review_gate branches) |
| S2-C58 | §8 | behavior | pre_delivery → allowed(delivery_hold=true). | (none) | aligned | FI §2.6 |
| S2-C59 | §8 | behavior | post_action + none → allowed. | (none) | aligned | FI §2.6 |
| S2-C60 | §8 | behavior | on_accept not accepted → held(on_accept). | (none) | aligned | FI §2.6 (on_accept branch) |
| S2-C61 | §8 | invariant | Every call emits a durable `wal.permission_check`. | (none) | aligned | FI §2.6 + FI §5 (positive finding) |
| S2-C62 | §8 | behavior | Pipeline processed in declared order; most restrictive wins; dependency_mode controls. | (none) | missing | FI §2.6 (PipelineEvaluator defined/unit-tested but not imported by runtime /jobs flow) |
| S2-C63 | §8 | invariant | Same-capability retry v1. | (none) | aligned | config/capabilities.json (`same_capability_only: true`) |
| S2-C64 | §8 | behavior | route.multi provider deps check. | (none) | partial | FI §2.6 (PipelineEvaluator not wired; provider_dependencies not checked in /jobs) |
| S2-C65 | §9 | invariant | Promotion is always human decision. | (none) | not-testable-from-code | Governance principle (operational) |
| S2-C66 | §9.1 | state | Governing 0→1 criteria. | (none) | aligned | config/capabilities.json (promotion_criteria objects) |
| S2-C67 | §9.1 | state | Governing 1→2 criteria. | (none) | aligned | config/capabilities.json |
| S2-C68 | §9.1 | invariant | Governing 2→3 not available v1. | (none) | aligned | config/capabilities.json (`"2_to_3": null`) |
| S2-C69 | §9.2 | state | Approval score weights. | (none) | partial | orchestrator/promotion_monitor.py has logic; not exercised in FI |
| S2-C70 | §9.3 | state | `context.package` promotion thresholds. | (none) | partial | config/capabilities.json + promotion_monitor.py; not exercised |
| S2-C71 | §9.3 | state | `memory.read` thresholds. | (none) | partial | Same |
| S2-C72 | §9.3 | state | `memory.write` 30 writes (on_accept); max WAL-1 v1. | (none) | partial | config/capabilities.json |
| S2-C73 | §9.3 | state | `notify.*` thresholds. | (none) | partial | Same |
| S2-C74 | §9.3 | state | `job.queue` thresholds. | (none) | partial | Same |
| S2-C75 | §9.4 | behavior | Promotion flow: orchestrator→job→human edit→restart→reconciliation. | (none) | not-testable-from-code | Operational flow; not exercised |
| S2-C76 | §10.1 | behavior | Strip-failure on `context.package` → SUSPEND; recovery = manual. | (none) | partial | FI §2.2 (`event_egress_validation_failure` dead) — strip-failure path exists in code but not connected to gateway emission |
| S2-C77 | §10.1 | behavior | Override cancel/redirect on WAL-1+ → demote 1 (conditional). | Spec 5 §5 | ambiguous | 3A pre-flag |
| S2-C78 | §10.1 | behavior | 3 failures in 24h on failing_capability_id → demote 1. | (none) | partial | orchestrator/demotion_engine.py exists; not exercised in FI |
| S2-C79 | §10.1 | behavior | Model change → WAL-0. | (none) | not-testable-from-code | No model change event tested |
| S2-C80 | §10.1 | behavior | WAL-3 approval <99%/90d → WAL-2. | (none) | not-testable-from-code | No WAL-3 cap active |
| S2-C81 | §10.1 | invariant | Sentinels excluded from counters. | (none) | aligned | orchestrator/override_types.py:17 |
| S2-C82 | §10.1 | state | Suspension = `wal.demoted(to_level:-1)`; recovery edit config+restart. | (none) | partial | orchestrator/capability_state.py logic; not exercised live |
| S2-C83 | §10.2 | behavior | On any demotion: outcomes cleared; last_reset set; wal_history preserved. | (none) | partial | orchestrator/capability_state.py; not exercised live |
| S2-C84 | §11 | behavior | Startup validation list. | (none) | aligned | orchestrator/startup_validator.py |
| S2-C85 | §11 | invariant | `credential` class must have `hardcoded:true`, `default_action:strip`. | Spec 3 §4 | pending-cross-ref | Depends on Spec 3 §4 |
| S2-C86 | §11 | invariant | Validation failures prevent startup. | (none) | aligned | orchestrator/startup_validator.py |

### Spec 3 — Context Boundary

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S3-C1 | §1 | structure | Context Packager owned by `context.package`. | Spec 2 §7.2 | pending-cross-ref | Depends on Spec 2 §7.2 |
| S3-C2 | §2 | structure | Context object = complete data for single dispatch; retries create new. | (none) | aligned | orchestrator/context_packager.py:167 (ContextPackager class, `package` method) |
| S3-C3 | §2.1 | structure | Context object schema (15 fields). | (none) | aligned | orchestrator/context_packager.py (package method output shape) |
| S3-C4 | §2.1 | structure | `retrieved_candidates` includes excluded w/ reasons. | (none) | not-testable-from-code | Runtime shape detail; not in FI |
| S3-C5 | §2.1 | structure | `eligible_context_fields` post-gate subset. | (none) | not-testable-from-code | Same |
| S3-C6 | §2.1 | invariant | `effective_packaging_level = min(governing WAL, context.package WAL)`. | (none) | not-testable-from-code | Not exercised in FI |
| S3-C7 | §3 | behavior | Three stacked gates: shareability, confidence, token budget. | (none) | not-testable-from-code | Gating not exercised in FI (no memory retrieval path tested) |
| S3-C8 | §3.1 | invariant | `local_only` objects excluded absolutely from cloud. | (none) | not-testable-from-code | No retrieval path tested |
| S3-C9 | §3.1 | state | Default shareability `local_only`. | (none) | not-testable-from-code | Same |
| S3-C10 | §3.2 | state | Below 0.3 confidence → excluded. | (none) | not-testable-from-code | Same |
| S3-C11 | §3.2 | state | Default confidence thresholds per source type. | (none) | ambiguous | 3A pre-flag |
| S3-C12 | §3.3 | state | Token budget three pools. | (none) | not-testable-from-code | Same |
| S3-C13 | §3.3 | behavior | Highest-confidence first; whole objects only. | (none) | not-testable-from-code | Same |
| S3-C14 | §4 | structure | Sensitivity classes: name, location, date, financial, credential, custom, none, unclassifiable. | (none) | aligned | config/sensitivity.json |
| S3-C15 | §4 | state | `name` default action strip. | (none) | aligned | config/sensitivity.json |
| S3-C16 | §4 | state | `location` default generalize. | (none) | aligned | config/sensitivity.json |
| S3-C17 | §4 | state | `date` default generalize. | (none) | aligned | config/sensitivity.json |
| S3-C18 | §4 | state | `financial` default strip. | (none) | aligned | config/sensitivity.json |
| S3-C19 | §4 | invariant | `credential` default strip always; hardcoded regex. | (none) | aligned | config/sensitivity.json (`"hardcoded": true, "action": "strip"`) |
| S3-C20 | §4 | state | `none` pass; status `safe_none`. | (none) | aligned | orchestrator/context_packager.py:286 |
| S3-C21 | §4 | invariant | `unclassifiable` → strip (default-deny). | (none) | partial | orchestrator/context_packager.py handles known classes; explicit unclassifiable branch not verified |
| S3-C22 | §4 | invariant | Default-deny = conservative success (null failing_capability_id). | (none) | partial | Consistent with orchestrator convention; not exercised |
| S3-C23 | §4 | structure | sensitivity.json location; credential hardcoded. | Spec 2 §11 | aligned | config/sensitivity.json |
| S3-C24 | §5 | behavior | Four stages: Retrieval → Gating → Scan+Transform → Assembly. | (none) | aligned | orchestrator/context_packager.py (package method pipeline) |
| S3-C25 | §5 | invariant | Precedence strip>generalize>pass. | (none) | aligned | orchestrator/context_packager.py:_merge_overlapping_spans |
| S3-C26 | §5 | invariant | Credential always strip; no config override. | (none) | aligned | config/sensitivity.json + packager transform logic |
| S3-C27 | §5 | structure | Stage 4 stores at context store; emits context.packaged. | Spec 1 §6.4 | aligned | orchestrator/context_packager.py + events.py:455 |
| S3-C28 | §5 | behavior | Mixed-content → multiple strip_detail events; field_id in multiple arrays. | (none) | not-testable-from-code | Integration detail; not in FI |
| S3-C29 | §6 | state | WAL-0 context rules (confidence 0.3, 40% memory). | (none) | not-testable-from-code | Not exercised |
| S3-C30 | §6 | state | WAL-1 (50% memory, prompt template). | (none) | not-testable-from-code | Same |
| S3-C31 | §6 | state | WAL-2 (confidence 0.2, 60% memory, exceptions). | (none) | ambiguous | 3A pre-flag |
| S3-C32 | §6 | invariant | Core pipeline invariant across levels: strip creds, default-deny, most-restrictive. | (none) | aligned | orchestrator/context_packager.py (transform logic fixed) |
| S3-C33 | §7.1 | behavior | Category A: gateway catches missed content → blocks → emits strip_detail → job.failed + trust consequences. | (none) | partial | FI §2.2 (egress-gateway emits no audit events; orchestrator converts to `event_job_failed` with error_class) |
| S3-C34 | §7.1 | invariant | Gateway never silently fixes. | (none) | aligned | FI §2.2 (block-only behavior) |
| S3-C35 | §7.2 | behavior | Category B: transport/policy failures → egress.validation_failure; no strip-failure trust. | Spec 4 | pending-cross-ref | Depends on Spec 4 |
| S3-C36 | §8 | structure | `job.classified` = Plan snapshot; `context.packaged` = Outcome. | (none) | aligned | orchestrator/events.py (event_job_classified + event_context_packaged) |
| S3-C37 | §8 | invariant | Context store entries written once; verify by hash. | (none) | aligned | orchestrator/context_packager.py (Stage 4 write-once) |
| S3-C38 | §8 | structure | Prompt templates at `/var/drnt/config/templates/`. | (none) | missing | No templates/ directory found in config path (`ls config/`) |

### Spec 4 — Egress Policy Binding

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S4-C1 | §2 | structure | Egress registry at `/var/drnt/config/egress.json`. | (none) | aligned | docker-compose.yml:31 + config/egress.json |
| S4-C2 | §2 | behavior | Registry human-edited; changes emit `system.config_change`. | Spec 1 §4.6 | pending-cross-ref | Depends on Spec 1 §4.6 |
| S4-C3 | §2 | invariant | Default: deny. | (none) | aligned | FI §2.2 (Check 1 route_mismatch) |
| S4-C4 | §2.1 | structure | Entry schema fields. | (none) | aligned | config/egress.json |
| S4-C5 | §2.1 | structure | `auth.secret_ref` is env var reference, not secret. | (none) | aligned | config/egress.json (secret_ref pattern) |
| S4-C6 | §2.1 | behavior | Secrets resolved at dispatch from `/var/drnt/secrets/.env`. | (none) | aligned | docker-compose.yml:83 (`DRNT_SECRETS_PATH=/var/drnt/secrets/.env`) |
| S4-C7 | §2.1 | structure | Constraints fields. | (none) | aligned | config/egress.json |
| S4-C8 | §3 | invariant | Orchestrator cannot reach internet via Docker topology. | (none) | aligned | docker-compose.yml (orchestrator on drnt-internal only) |
| S4-C9 | §3 | structure | Docker bridges: `drnt-internal` hosts services; gateway outbound; log writer internal. | Spec 6 §3.1 | partial | docker-compose.yml defines only `drnt-internal` + `drnt-external`; no separate `drnt-gateway` or `drnt-tailscale` segments; topology is coarser than spec suggests |
| S4-C10 | §4 | invariant | `job.dispatched` emitted on gateway outbound transmission BEFORE response. | Spec 1 §4.1 | aligned | orchestrator/events.py:250 + egress-gateway flow |
| S4-C11 | §4 | behavior | Check 1 — Route exists → route_mismatch. | (none) | aligned | FI §2.2 |
| S4-C12 | §4 | behavior | Check 2 — Enabled → policy_violation. | (none) | aligned | FI §2.2 (egress-gateway/main.py:106-143 reject flow) |
| S4-C13 | §4 | behavior | Check 3 — Capability binding → policy_violation. | (none) | aligned | FI §2.2 |
| S4-C14 | §4 | behavior | Check 4 — Model binding → model_mismatch. | (none) | aligned | FI §2.2 |
| S4-C15 | §4 | behavior | Check 5 — Token limit → token_overflow. | (none) | partial | FI §2.2 (checks 1-5 have zero historical occurrences; check 5 not probed live) |
| S4-C16 | §4 | behavior | Check 6 — Cost limit → policy_violation. | (none) | partial | Same |
| S4-C17 | §4 | behavior | Check 7 — Rate limit → policy_violation. | (none) | partial | egress-gateway/rate_limiter.py + FI §2.2 |
| S4-C18 | §4 | behavior | Check 8 — Payload hash mismatch → policy_violation. | (none) | not-testable-from-code | Not probed |
| S4-C19 | §4 | behavior | Check 9 — Sensitivity scan → context.strip_detail. | (none) | not-testable-from-code | Not probed; egress-gateway emits no audit events per FI §2.2 |
| S4-C20 | §4 | behavior | Check 10 — Auth resolution → policy_violation. | (none) | not-testable-from-code | Not probed |
| S4-C21 | §4 | behavior | Check 11 — TLS/connectivity → endpoint_unavailable. | (none) | aligned | FI §2.2 (only endpoint_unavailable from 6-7 has fired in production) |
| S4-C22 | §4 | invariant | Only check 9 emits `context.strip_detail`. | (none) | ambiguous | 3A pre-flag |
| S4-C23 | §4 | state | Sensitivity catch → failing_capability_id "context.package". | (none) | partial | FI §1.4 + §2.2 (`event_egress_validation_failure` dead; egress-gateway emits NO audit events) |
| S4-C24 | §4 | state | Route/policy → `egress_config`; connectivity → `egress_connectivity`; excluded from WAL counters. | Spec 2 §10.1 | aligned | orchestrator/override_types.py:17 (SENTINEL_FAILURE_IDS) |
| S4-C25 | §4 | behavior | Gateway re-estimates tokens. | (none) | not-testable-from-code | Not probed |
| S4-C26 | §4 | invariant | Gateway authoritative for constraint enforcement. | (none) | aligned | egress-gateway service (separate container in compose) |
| S4-C27 | §4 | structure | `egress_config_hash` stamped into `job.dispatched`. | Spec 1 §4.1 | aligned | orchestrator/events.py:250 (event_job_dispatched) |
| S4-C28 | §5 | behavior | `route.multi` same package multiple dispatches. | (none) | not-testable-from-code | Not probed |
| S4-C29 | §5 | behavior | Suspended providers excluded; all suspended = blocked. | (none) | not-testable-from-code | Not probed |
| S4-C30 | §5 | invariant | Sensitivity decisions made once, applied uniformly. | (none) | not-testable-from-code | Not probed |
| S4-C31 | §6 | state | Per-route rate limits (RPM sliding, TPD rolling 24h), in-memory, reset on restart. | (none) | aligned | egress-gateway/rate_limiter.py |
| S4-C32 | §6 | behavior | Health probes; consecutive failures → down; auto-recovery. | (none) | aligned | orchestrator/connectivity_monitor.py |
| S4-C33 | §7 | structure | Anthropic adapter. | (none) | aligned | egress-gateway/providers/ |
| S4-C34 | §7 | structure | OpenAI adapter. | (none) | aligned | egress-gateway/providers/ |
| S4-C35 | §7 | structure | Google adapter. | (none) | aligned | egress-gateway/providers/ |
| S4-C36 | §8 | invariant | Forensic reconstruction chain. | (none) | aligned | FI §2.3 (chain verified) + events.py:250 hash-anchored |
| S4-C37 | §9 | behavior | Startup validation: unique routes, caps resolvable, secrets resolvable, HTTPS, TLS≥1.2. | (none) | aligned | orchestrator/startup_validator.py + egress-gateway/registry.py |
| S4-C38 | §9 | invariant | Validation failures prevent gateway startup. | (none) | aligned | startup_validator + depends_on service_healthy in compose |
| S4-C39 | §9 | invariant | No gateway = no dispatch. | (none) | aligned | docker-compose.yml:70 (orchestrator depends_on egress-gateway.service_healthy) |

### Spec 5 — Override Semantics

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S5-C1 | §1 | invariant | Every override is durable. | (none) | aligned | FI §2.8 (human.override durable) |
| S5-C2 | §1 | invariant | Human decides; DRNT complies+records. | (none) | not-testable-from-code | Governance principle |
| S5-C3 | §2 | behavior | `cancel`: abort job, discard results; post-delivery=revoke. | (none) | aligned | FI §2.8 Step 3 (delivered-cancel → revoke; HTTP 200) |
| S5-C4 | §2 | behavior | `redirect`: reject routing; spawn successor. | (none) | partial | FI §4 (not live-tested); orchestrator/override_types.py:10 defines OverrideType.redirect |
| S5-C5 | §2 | behavior | `modify`: accept+edit; both artifacts stored. | (none) | partial | FI §4 (not live-tested); OverrideType.modify defined |
| S5-C6 | §2 | behavior | `escalate`: request higher quality; spawn successor. | (none) | partial | FI §4 (not live-tested); OverrideType.escalate defined |
| S5-C7 | §3 | invariant | Redirect/escalate spawn successor w/ new job_id + parent_job_id. | Spec 1 §3.1 | aligned | orchestrator/override_types.py:29 (`SPAWNS_SUCCESSOR = frozenset({redirect, escalate})`) |
| S5-C8 | §3 | invariant | Original's audit trail immutable. | (none) | aligned | FI §2.8 (events remain after override) |
| S5-C9 | §3 | structure | Successor gets own event chain. | (none) | not-testable-from-code | Not live-tested |
| S5-C10 | §3 | invariant | Preserves one-job-one-governing. | (none) | aligned | FI §2.6 + override_types.py (new job_id conceptually decoupled) |
| S5-C11 | §4 | state | `job.delivered` = success. | (none) | aligned | FI §2.8 |
| S5-C12 | §4 | state | `job.failed` = did not complete or pre-delivery cancel. | (none) | aligned | FI §2.8 Step 4 (pre-delivery override → failed) |
| S5-C13 | §4 | state | `job.revoked` = post-delivery withdrawal. | (none) | aligned | FI §2.8 Step 3 |
| S5-C14 | §5 | invariant | `job.dispatched` = irreversibility boundary. | (none) | aligned | FI §2.8 (terminal state semantics consistent) |
| S5-C15 | §5 | behavior | Post-dispatch user gets informational notification. | (none) | not-testable-from-code | UI behavior outside gateway repo |
| S5-C16 | §6 | behavior | Cancel on viable → demote 1. | Spec 2 §10.1 | partial | FI §2.8 Step 3 (delivered-cancel fires wal.demoted -1; pre-delivery cancel silent no-op for demotion — governing_capability_id None) |
| S5-C17 | §6 | behavior | Cancel after sentinel → no demotion. | (none) | not-testable-from-code | FI §4 (sentinel demotion suppression not live-tested) |
| S5-C18 | §6 | behavior | Redirect viable → demote 1. | (none) | not-testable-from-code | FI §4 (redirect not live-tested) |
| S5-C19 | §6 | behavior | Redirect after sentinel → no demotion. | (none) | not-testable-from-code | Same |
| S5-C20 | §6 | behavior | modify → no demote; score 0.5. | (none) | not-testable-from-code | Not live-tested |
| S5-C21 | §6 | behavior | escalate → no demote; score 0.0. | (none) | not-testable-from-code | Not live-tested |
| S5-C22 | §6 | behavior | Orchestrator checks prior `failing_capability_id` for demotion. | (none) | aligned | orchestrator/override_types.py:22 (`is_sentinel_failure`) |
| S5-C23 | §7 | structure | `human.reviewed(modified)` fields (modified_result_id, derived_from, hash). | Spec 1 §4.5 | not-testable-from-code | Modify not live-tested |
| S5-C24 | §7 | invariant | Both artifacts in results store; both hashes in log. | (none) | not-testable-from-code | Same |
| S5-C25 | §8 | behavior | WAL-1 memory write on_accept only. | Spec 2 §5.2 | pending-cross-ref | Depends on Spec 2 §5.2 |
| S5-C26 | §8 | behavior | WAL-2 memory write on delivery; escalate/modify/revoke semantics. | (none) | ambiguous | 3A pre-flag |
| S5-C27 | §8 | invariant | Memory objects never deleted. | (none) | not-testable-from-code | No memory.write exercised |
| S5-C28 | §9 | state | Auto-accept window 24h default, configurable. | (none) | aligned | docker-compose.yml:49 (`DRNT_AUTO_ACCEPT_WINDOW:-86400`) |
| S5-C29 | §9 | behavior | Score mapping per override-in-window type. | (none) | not-testable-from-code | Not live-tested |
| S5-C30 | §10 | structure | Watch: dismiss=cancel; no redirect/modify/escalate. | (none) | not-testable-from-code | Watch app not in repo |
| S5-C31 | §10 | structure | Phone: all four overrides. | (none) | not-testable-from-code | Phone app not in repo |
| S5-C32 | §10 | behavior | Offline queue; retroactive cancel on delivered → revoked(offline_retroactive). | Spec 7 §3.2.1 | ambiguous | 3A pre-flag |
| S5-C33 | §10 | behavior | Two overrides on one job: first wins; second no-op. | (none) | aligned | FI §2.8 Step 6 (`already_overridden` guard; second call returns no_op with zero durable events) |
| S5-C34 | §11 | invariant | Override wins immediately in all cases. | (none) | aligned | FI §2.8 (HTTP override handled atomically at orchestrator level) |
| S5-C35 | §11 | behavior | During packaging: Packager stops. | (none) | not-testable-from-code | Not probed |
| S5-C36 | §11 | behavior | During gateway: abort if not transmitted. | (none) | not-testable-from-code | Not probed |
| S5-C37 | §11 | behavior | During model wait: response arrives, discarded. | (none) | partial | FI §2.8 Step 4 ("zombie pipeline" — pre-delivery override but pipeline completes; model output discarded at no durable trace) |
| S5-C38 | §12 | structure | Redirect-after-dispatch event sequence. | (none) | partial | FI §4 (redirect not live-tested) |
| S5-C39 | §13 | structure | Modify-during-review event sequence. | (none) | not-testable-from-code | Not live-tested |
| S5-C40 | §14 | structure | Post-delivery escalate event sequence. | (none) | not-testable-from-code | Not live-tested |

### Spec 6 — Silo Runtime Security

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S6-C1 | §1 | structure | Execution silo between L1 perimeter and L2 exit audit. | (none) | aligned | orchestrator (L1) + worker-proxy (silo border) in docker-compose |
| S6-C2 | §1 | invariant | Cloud-dispatch egress and worker-execution egress are distinct boundaries. | (none) | aligned | egress-gateway vs worker-proxy (separate services) |
| S6-C3 | §1 | structure | In-flight worker termination governed here; Spec 5 governs override. | §9.5 | pending-cross-ref | Depends on Spec 5 §9.5 |
| S6-C4 | §2 | invariant | Three layers, none substitutes for others. | (none) | not-testable-from-code | Architectural principle |
| S6-C5 | §2.1 | structure | Layer 1 = OS/container isolation. | (none) | aligned | Dockerfile + seccomp-default.json |
| S6-C6 | §2.1 | behavior | Layer 1 by container config + Landlock + seccomp + netns. | (none) | partial | FI §1.7 (seccomp runtime-verified) + seccomp-default.json; Landlock not verified in code |
| S6-C7 | §2.1 | invariant | Agent cannot modify Layer 1 from inside sandbox. | (none) | aligned | Seccomp out-of-process; no code exposes Layer 1 mutation API |
| S6-C8 | §2.1 | invariant | Filesystem/process restrictions locked at sandbox creation. | (none) | aligned | orchestrator/runtime_manifest.py + sandbox_blueprint.py |
| S6-C9 | §2.1 | invariant | Baseline egress policy locked at creation. | (none) | aligned | orchestrator/runtime_manifest.py |
| S6-C10 | §2.1 | behavior | Session-scoped endpoint approvals hot-reloadable via TUI proxy. | §8.3 | missing | No TUI proxy found in repo |
| S6-C11 | §2.2 | structure | Layer 2 = WAL levels, gates, promotion/demotion, cost. | Spec 2 | aligned | orchestrator/permission_checker.py + capability_state.py + demotion_engine.py |
| S6-C12 | §2.2 | invariant | Layer 2 operates inside Layer 1; tightens but never relaxes. | (none) | aligned | Architectural consistency |
| S6-C13 | §2.3 | structure | Layer 3 two paths: cloud + worker. | Spec 3, Spec 4 | aligned | egress-gateway + worker-proxy + context_packager |
| S6-C14 | §2.3 | state | Cloud dispatch defaults deny at endpoint+content. | (none) | aligned | egress.json default-deny + sensitivity.json |
| S6-C15 | §2.3 | state | Worker execution defaults deny at endpoint, pass at content (for approved endpoints). | §3.4 | ambiguous | 3A pre-flag |
| S6-C16 | §2.3 | invariant | Credentials stripped on both paths unconditionally. | (none) | aligned | config/sensitivity.json (`credential` `hardcoded: true`) |
| S6-C17 | §2.4 | invariant | Runtime isolation = ceiling. | (none) | not-testable-from-code | Architectural |
| S6-C18 | §2.4 | invariant | Worker agents inside silo are not capabilities. | (none) | aligned | No worker entries in config/capabilities.json |
| S6-C19 | §2.4 | invariant | Governing cap dispatched job owns WAL context. | §6.4 | aligned | orchestrator/job_manager.py (governing_capability_id on dispatch) |
| S6-C20 | §2.4 | invariant | Structural privacy applies to all outbound. | (none) | not-testable-from-code | Architectural |
| S6-C21 | §2.4 | invariant | No layer trusts another (defense in depth). | (none) | not-testable-from-code | Architectural |
| S6-C22 | §3.1 | structure | `drnt-internal` segment — orchestrator, L2, log writer, verification agent. | (none) | partial | docker-compose.yml:160 (drnt-internal exists); membership substantially matches (orchestrator, audit-log-writer, worker-proxy); no "verification agent" found |
| S6-C23 | §3.1 | structure | `drnt-worker` segment — worker agents; egress via proxy. | (none) | contradicted | docker-compose.yml defines only `drnt-internal` and `drnt-external`; no `drnt-worker` network |
| S6-C24 | §3.1 | structure | `drnt-gateway` segment — egress gateway. | Spec 4 §3 | contradicted | docker-compose.yml: gateway on `drnt-internal` + `drnt-external`; no `drnt-gateway` network |
| S6-C25 | §3.1 | structure | `drnt-tailscale` segment — phone/watch; no egress. | (none) | contradicted | No `drnt-tailscale` network in compose |
| S6-C26 | §3.1 | structure | `drnt-bus` segment — L1↔worker message bus. | (none) | contradicted | No `drnt-bus` network in compose |
| S6-C27 | §3.1 | invariant | Workers cannot reach `drnt-internal`. | (none) | contradicted | worker-proxy is ON drnt-internal (docker-compose.yml:131); the claim's segmentation doesn't match topology |
| S6-C28 | §3.1 | invariant | Workers cannot reach `drnt-gateway`. | (none) | aligned | Workers don't have direct gateway routes (functional outcome achieved despite network-naming divergence) |
| S6-C29 | §3.1 | invariant | Workers never send outbound to cloud models directly. | (none) | aligned | Worker has no egress-gateway access; cloud dispatch path originates at orchestrator |
| S6-C30 | §3.1 | invariant | `drnt-bus` carries structured messages only. | (none) | contradicted | `drnt-bus` does not exist |
| S6-C31 | §3.2 | state | Cloud path: Orchestrator → Packager → Gateway → Cloud. Worker path: Worker → Sanitizer → Proxy → External. | (none) | aligned | Matches service graph in docker-compose + code structure |
| S6-C32 | §3.2 | state | Cloud gate vs worker gate components. | (none) | aligned | egress.json (cloud) vs worker-proxy-registry.json (worker) |
| S6-C33 | §3.2 | state | Cloud events: job.dispatched, context.packaged; worker events: worker.egress_request, worker.egress_blocked. | (none) | contradicted | orchestrator/egress_events.py:27,59 emit `egress.authorized` / `egress.denied` (NOT `worker.egress_request` / `worker.egress_blocked`) |
| S6-C34 | §3.2 | state | Cloud approval = edit egress.json + restart; worker approval = TUI (session) or manifest edit. | (none) | partial | Cloud path matches; TUI missing |
| S6-C35 | §3.3 | structure | Worker egress proxy on `drnt-worker` segment. | (none) | partial | worker-proxy service exists (docker-compose.yml:120-139) but on `drnt-internal`, not `drnt-worker` |
| S6-C36 | §3.3 | behavior | Proxy two checks: sanitizer then policy. | (none) | partial | worker-proxy/main.py + orchestrator/egress_proxy.py have check logic; sanitizer integration not directly confirmed |
| S6-C37 | §3.3 | behavior | Sanitizer block → worker.egress_blocked(sanitizer_catch). | (none) | contradicted | orchestrator/egress_events.py:59 emits `egress.denied` (event name differs) |
| S6-C38 | §3.3 | behavior | Policy block → worker.egress_blocked(endpoint_not_in_policy). | (none) | contradicted | Same (event name is `egress.denied`) |
| S6-C39 | §3.3 | behavior | Both pass → worker.egress_request(policy_result: allowed). | (none) | contradicted | orchestrator/egress_events.py:27 emits `egress.authorized` (event name differs) |
| S6-C40 | §3.3 | state | Session approvals default TTL 4h. | §8.3 | missing | No TTL configuration found |
| S6-C41 | §3.3 | behavior | Expired approvals require re-approval. | (none) | not-testable-from-code | No session approval code |
| S6-C42 | §3.3 | invariant | Proxy out-of-process; worker can't bypass. | (none) | aligned | worker-proxy is separate container on dedicated drnt-worker-proxy service |
| S6-C43 | §3.3 | structure | Session approvals attach to job context. | (none) | not-testable-from-code | No session approval storage code |
| S6-C44 | §3.4 | behavior | Sanitizer check 1 credential strip (same hardcoded patterns as Spec 3). | Spec 3 §4 | partial | config/sensitivity.json has credential hardcoded=true; direct integration into worker-proxy sanitizer not verified |
| S6-C45 | §3.4 | behavior | Check 2 PII detection (name/location/date/financial). | (none) | partial | Same — classifier patterns available; worker-side integration unclear |
| S6-C46 | §3.4 | behavior | Check 3 unrecognized pass. | (none) | ambiguous | 3A pre-flag |
| S6-C47 | §3.4 | invariant | Security = endpoint restriction + known-bad pattern detection. | (none) | aligned | Architecture |
| S6-C48 | §3.4 | behavior | TLS inspection with sandbox-trusted CA. | (none) | missing | No TLS inspection proxy found |
| S6-C49 | §3.4 | invariant | V1 sanitizer detects+blocks, does not redact+forward. | (none) | aligned | Design; no redaction code found |
| S6-C50 | §3.4 | invariant | PII surfaced; credentials unconditionally. | (none) | partial | sensitivity.json hardcoded credentials; operator-review surfacing not verified |
| S6-C51 | §4 | structure | Every agent type has completion contract + runtime manifest. | (none) | partial | orchestrator/runtime_manifest.py exists; completion contract structure unclear |
| S6-C52 | §4.1 | structure | Completion contract fields. | (none) | not-testable-from-code | No completion contract code found |
| S6-C53 | §4.2 | structure | Runtime manifest schema fields. | (none) | aligned | orchestrator/runtime_manifest.py + manifest_validator.py |
| S6-C54 | §4.2 | invariant | `filesystem.blocked: ["*"]` default-deny; locked at creation. | (none) | aligned | orchestrator/runtime_manifest.py (blocked default) |
| S6-C55 | §4.2 | invariant | Cloud endpoints never in `network_egress.allowed_endpoints`. | (none) | aligned | Architecture (workers route to proxy only) |
| S6-C56 | §4.2 | structure | `cloud_dispatch_dependencies` is informational metadata. | (none) | aligned | orchestrator/manifest_validator.py |
| S6-C57 | §4.2 | invariant | `binaries.blocked: ["*"]`. | (none) | aligned | orchestrator/runtime_manifest.py |
| S6-C58 | §4.2 | invariant | Skill custom binary must be added to allowed list. | (none) | aligned | orchestrator/manifest_validator.py |
| S6-C59 | §4.2 | behavior | `operator_approval_on_unknown:true` surfaces unknowns. | (none) | partial | Config field present; TUI surfacing missing |
| S6-C60 | §4.3 | behavior | Manifest enforced at creation; validated at startup. | (none) | aligned | orchestrator/manifest_validator.py + blueprint_engine.py |
| S6-C61 | §4.3 | invariant | Manifest immutable during execution. | (none) | aligned | runtime_manifest.py (no runtime mutation API) |
| S6-C62 | §4.3 | behavior | If access not in manifest → job fails; operator updates. | (none) | not-testable-from-code | Not exercised |
| S6-C63 | §5.1 | structure | Sandbox blueprint = versioned declarative spec. | (none) | aligned | orchestrator/sandbox_blueprint.py + blueprint_engine.py |
| S6-C64 | §5.1 | structure | Blueprint fields. | (none) | aligned | orchestrator/sandbox_blueprint.py |
| S6-C65 | §5.1 | state | Default isolation: Landlock true, seccomp drnt-worker-default, netns, pid ns, ALL caps dropped. | (none) | partial | config/seccomp-default.json exists + FI §1.7 seccomp runtime-verified; Landlock not verified in code |
| S6-C66 | §5.1 | invariant | Blueprint constrains manifest. | (none) | aligned | orchestrator/manifest_validator.py (enforced) |
| S6-C67 | §5.1 | invariant | `resource_limits ≤ resource_ceiling`; startup rejects otherwise. | (none) | aligned | orchestrator/manifest_validator.py |
| S6-C68 | §5.2 | behavior | Blueprints in `/var/drnt/config/blueprints/`; validated at startup; rolling restart. | (none) | partial | orchestrator/sandbox_blueprint.py exists; blueprints/ directory not found in config |
| S6-C69 | §5.3 | structure | V1 ships one blueprint `drnt-worker-base-v1`. | (none) | partial | docker-compose.yml worker profile build; blueprint config file not verified |
| S6-C70 | §6 | invariant | Every skill treated as hostile until proven. | (none) | not-testable-from-code | Principle |
| S6-C71 | §6.1 | invariant | Skills installed from outside sandbox only. | (none) | not-testable-from-code | Operational |
| S6-C72 | §6.1 | behavior | Install sequence (fetch/verify/declare/upload/register/recreate). | (none) | not-testable-from-code | Operational |
| S6-C73 | §6.1 | behavior | V1 verification checks. | (none) | ambiguous | 3A pre-flag |
| S6-C74 | §6.2 | structure | Skill permission manifest fields. | (none) | not-testable-from-code | No skill permission manifest code found |
| S6-C75 | §6.2 | invariant | Skill can't access beyond declaration. | (none) | not-testable-from-code | Same |
| S6-C76 | §6.3 | state | Skill origin trust posture. | (none) | not-testable-from-code | Same |
| S6-C77 | §6.4 | invariant | Worker agent types are not capabilities. | (none) | aligned | config/capabilities.json has no worker entries |
| S6-C78 | §6.4 | invariant | Worker failures attribute to governing cap (except sandbox violation). | (none) | aligned | orchestrator/override_types.py:17 (SENTINEL_FAILURE_IDS includes `worker_sandbox`) |
| S6-C79 | §6.4 | invariant | `worker.sandbox_violation` → `"worker_sandbox"` sentinel, excluded from counters. | Spec 2 §10.1 | partial | Sentinel exists in override_types.py:17 but NO `worker.sandbox_violation` event emitter found in orchestrator/events.py |
| S6-C80 | §6.4 | invariant | Quality failures attribute normally. | (none) | aligned | Default path via failing_capability_id (non-sentinel) |
| S6-C81 | §6.4 | structure | Runtime reliability record per agent type. | (none) | missing | No per-agent-type reliability record code found |
| S6-C82 | §6.4 | structure | `worker.sandbox_violation` + `worker.egress_blocked` carry `skill_id`. | (none) | contradicted | No `worker.sandbox_violation` emitter; event builder `egress.denied` is different from `worker.egress_blocked` and doesn't match claimed payload |
| S6-C83 | §6.4 | invariant | `worker.egress_blocked` attributes to governing cap normally. | (none) | contradicted | Event name `worker.egress_blocked` not emitted (code uses `egress.denied`) |
| S6-C84 | §7 | invariant | All `worker.*` events durable. | (none) | contradicted | No `worker.*` events emitted by code; workforce egress events use `egress.*` namespace |
| S6-C85 | §7.1 | state | Schema bump 1.5 → 1.6; new event types added. | Spec 1 §3 | contradicted | audit-log-writer/src/models.py:20 (`SCHEMA_VERSION = "2.0"`) |
| S6-C86 | §7.1 | state | Source enum extended with `worker_egress_proxy` and `sandbox_runtime`. | (none) | contradicted | audit-log-writer/src/models.py:7-16 (VALID_SOURCES lacks both) |
| S6-C87 | §7.1 | invariant | Worker events carry governing cap_id + wal_level in envelope. | Spec 1 §3.1 | contradicted | No `worker.*` events; envelope rule cannot apply |
| S6-C88 | §7.1 | structure | `agent_type_id` + `agent_instance_id` are payload fields. | (none) | not-testable-from-code | No event builders |
| S6-C89 | §7.2 | structure | `worker.egress_request` payload fields. | (none) | contradicted | Event not emitted; egress.authorized emitted instead w/ different payload |
| S6-C90 | §7.3 | structure | `worker.egress_blocked` payload fields. | (none) | contradicted | Event not emitted; egress.denied emitted instead |
| S6-C91 | §7.3 | invariant | `sanitizer_credential` never overridable; `sanitizer_pii` surfaceable. | (none) | partial | sensitivity.json credential hardcoded aligned; override/surfacing integration not verified |
| S6-C92 | §7.4 | structure | `worker.sandbox_violation` payload fields. | (none) | missing | No event emitter |
| S6-C93 | §7.4 | invariant | `sandbox_violation` severity always critical. | (none) | missing | No event emitter |
| S6-C94 | §7.4 | behavior | `job_id` null = system-level integrity event. | (none) | missing | Same |
| S6-C95 | §7.4 | invariant | `job_id` non-null linked to job trail; sentinel prevents demotion. | (none) | ambiguous | 3A pre-flag |
| S6-C96 | §8.1 | structure | Operator TUI views. | (none) | missing | No TUI code |
| S6-C97 | §8.2 | behavior | Operator actions. | (none) | missing | Same |
| S6-C98 | §8.2 | invariant | TUI termination through L1. | §9.5 | missing | Same |
| S6-C99 | §8.3 | state | Session approvals TTL 4h default. | (none) | missing | No TTL code |
| S6-C100 | §8.3 | behavior | Permanent approvals require manifest edit. | (none) | not-testable-from-code | No session approval flow |
| S6-C101 | §8.3 | structure | Approval queue entry fields. | (none) | missing | No approval queue |
| S6-C102 | §9.5 | behavior | Override handling: L1→terminate signal→workers halt. | Spec 5 | pending-cross-ref | Depends on Spec 5 |
| S6-C103 | §9.5 | behavior | Cleanup: sandboxes cleaned; temp files purged; sandbox persists. | (none) | aligned | orchestrator/worker_lifecycle.py (teardown emits worker.teardown per FI §1.7) |
| S6-C104 | §9.5 | behavior | Audit: `worker.egress_blocked` emitted for in-flight dropped. | (none) | contradicted | Event name; code uses `egress.denied` |
| S6-C105 | §9.5 | behavior | Successor runs under own completion contracts. | (none) | not-testable-from-code | Not live-tested |
| S6-C106 | §10 | structure | L2 reads `worker.*` events. | (none) | not-testable-from-code | No L2 code path exercised |
| S6-C107 | §10 | behavior | L2 treats sandbox violation + sanitizer_credential as integrity-significant. | (none) | missing | No L2 code |
| S6-C108 | §10 | behavior | L2 treats endpoint_not_in_policy as advisory. | (none) | missing | Same |
| S6-C109 | §10 | invariant | L2 does not gate on worker events. | (none) | not-testable-from-code | No L2 |
| S6-C110 | §11 | behavior | Startup validation: manifests valid, blueprints register, min isolation, resource caps, skill permissions, proxy+sanitizer, no write outside /sandbox, binaries, cloud_dispatch_deps resolve, bus on drnt-bus. | (none) | partial | orchestrator/startup_validator.py exists w/ many checks; drnt-bus check unverifiable since segment doesn't exist |
| S6-C111 | §11 | invariant | Validation failures prevent agent type registration. | (none) | aligned | orchestrator/startup_validator.py (hub_startup_blocked event) |
| S6-C112 | §12.1 | state | V1 in-scope list. | (none) | partial | Most items present; TUI missing; `drnt-bus` missing; worker.* event names differ |
| S6-C113 | §12.2 | state | V1 out-of-scope list. | (none) | not-testable-from-code | Descriptive |
| S6-C114 | §12.3 | state | Designed-for-but-deferred: SMC + Solid Protocol. | (none) | not-testable-from-code | Descriptive |

### Spec 7 — Signal Chain Resilience

| Claim ID | Section | Category | Claim text | Cross-refs noted | Score | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| S7-C1 | Preamble | invariant | Fails partially, never silently; every failure visible, logged, recoverable. | (none) | contradicted | FI §3.1 (nine silent-path findings across probes) + FI §2.5 (mid-pipeline silent failures leave no durable trace) |
| S7-C2 | Preamble | invariant | Jobs never lost once accepted. | (none) | contradicted | FI §2.5 (Target 2 β classification: model output produced then silently dropped — "audit log: structurally incapable of describing what happened") |
| S7-C3 | §1 | structure | Signal chain: Watch→iPhone→Hub→Cloud→Hub→iPhone→Watch. | (none) | partial | Hub side exists (docker-compose); watch/phone apps not in repo |
| S7-C4 | §1 | invariant | MacBook = fallback, not primary. | §5 | not-testable-from-code | No MacBook config |
| S7-C5 | §1 | invariant | V1 eliminates Pi. | (none) | aligned | No Pi references in repo |
| S7-C6 | §1 | structure | Desktop Hub runs stateful services: Ollama, orchestrator, log writer, queue, packager. | (none) | aligned | docker-compose.yml (all five services) |
| S7-C7 | §1 | structure | iPhone via Tailscale. | (none) | not-testable-from-code | iPhone app not in repo |
| S7-C8 | §2 | structure | Watch→iPhone via WatchConnectivity. | (none) | not-testable-from-code | iOS layer |
| S7-C9 | §2 | structure | iPhone→Hub Tailscale + /health probe 5s/30s. | (none) | not-testable-from-code | iOS |
| S7-C10 | §2 | structure | Hub→Cloud per-route health probes. | Spec 4 §6 | pending-cross-ref | Depends on Spec 4 §6 |
| S7-C11 | §2 | structure | Hub→iPhone WebSocket heartbeat 10s/30s. | (none) | not-testable-from-code | iOS + WS detail |
| S7-C12 | §2 | structure | iPhone→Watch WatchConnectivity. | (none) | not-testable-from-code | iOS |
| S7-C13 | §2 | structure | `GET /health` JSON fields: orchestrator_status, audit_log_status, ollama_status, last_successful_cloud_probe_timestamp. | (none) | partial | FI §1.1 (/health exists but returns only "available"/"unavailable" for ollama_status; claim's fuller field set not confirmed) |
| S7-C14 | §2 | behavior | iPhone treats non-200 as unavailable. | (none) | not-testable-from-code | iOS |
| S7-C15 | §2.1 | behavior | Asymmetric thresholds: 2 failures down, 3 successes up. | (none) | not-testable-from-code | iOS |
| S7-C16 | §2.1 | structure | "Hub Slow" degraded indicator. | (none) | not-testable-from-code | iOS UX |
| S7-C17 | §2.1 | invariant | Asymmetric hysteresis prevents thundering herd. | (none) | not-testable-from-code | iOS |
| S7-C18 | §2.2 | invariant | APNs best-effort. | (none) | not-testable-from-code | iOS |
| S7-C19 | §2.2 | invariant | Correctness path = polling /jobs/pending + WebSocket. | (none) | not-testable-from-code | No /jobs/pending in FI |
| S7-C20 | §2.2 | invariant | Never depend on push. | (none) | not-testable-from-code | Principle |
| S7-C21 | §3 | invariant | Every failure emits system.connectivity or queues for replay. | Spec 1 §4.6 | partial | orchestrator/events.py:880-936 (connectivity event builders exist); full-coverage claim not FI-confirmed |
| S7-C22 | §3.1 | state | Watch queue cap 10; oldest dropped; haptic on drop. | (none) | not-testable-from-code | Watch |
| S7-C23 | §3.1 | behavior | On reactivation, queued forwarded in order. | (none) | not-testable-from-code | Watch |
| S7-C24 | §3.1 | structure | system.connectivity on Watch includes dropped_request_count. | (none) | not-testable-from-code | Watch |
| S7-C25 | §3.2 | structure | iPhone→Hub transitions to Unavailable after 2 failures (60s). | §2.1 | not-testable-from-code | iOS |
| S7-C26 | §3.2 | behavior | Hub unavailable: new requests queued locally; "Queued" badge. | (none) | not-testable-from-code | iOS |
| S7-C27 | §3.2 | state | iPhone queue cap 50; 51st rejected. | (none) | not-testable-from-code | iOS |
| S7-C28 | §3.2 | invariant | Queued persisted to Core Data. | (none) | not-testable-from-code | iOS |
| S7-C29 | §3.2 | invariant | Local storage protection class. | §16 | ambiguous | 3A pre-flag |
| S7-C30 | §3.2.1 | invariant | Queued decision carries result_id + response_hash. | (none) | not-testable-from-code | iOS |
| S7-C31 | §3.2.1 | behavior | Hub validates state matches on replay. | (none) | not-testable-from-code | Spec 5/iOS interaction not live-tested |
| S7-C32 | §3.2.1 | behavior | Hub rejects replayed decision with conflict status. | (none) | not-testable-from-code | Same |
| S7-C33 | §3.2.1 | behavior | iPhone surfaces conflict. | (none) | not-testable-from-code | iOS |
| S7-C34 | §3.2.1 | behavior | Queued submitted in batches of 5 w/ 2s delay. | (none) | not-testable-from-code | iOS |
| S7-C35 | §3.2.1 | behavior | Queued decisions after pending submissions in order. | (none) | not-testable-from-code | iOS |
| S7-C36 | §3.2 | behavior | On reconnection (3 successes): iPhone polls /jobs/pending; connectivity pair emitted. | (none) | partial | system.connectivity events exist in code; iPhone side not in repo |
| S7-C37 | §3.3 | behavior | Hub→Cloud failure: local-capable process; cloud emit job.queued(connectivity). | Spec 1 §4.1 | partial | orchestrator/events.py:996 (event_job_queued builder) + connectivity_monitor.py; full flow not probed |
| S7-C38 | §3.3 | behavior | Dispatched-then-failed → model.error + WAL retry policy. | Spec 2 §5 | partial | orchestrator/events.py has model error handling; FI §2.5 shows mid-pipeline errors don't emit retroactive events cleanly |
| S7-C39 | §3.3 | behavior | All retries exhaust → job.failed; user notified. | (none) | partial | orchestrator/events.py:429 (event_job_failed) + FI §2.5 fail path observed |
| S7-C40 | §3.3 | behavior | Recovery automatic; probes detect; queued dispatch in order. | (none) | partial | connectivity_monitor.py + stale_recovery.py; auto-recovery flow not fully probed |
| S7-C41 | §3.3 | behavior | Fallback routing to alternative provider. | (none) | not-testable-from-code | Not probed |
| S7-C42 | §3.3 | invariant | WAL-0 model selection pre_action. | Spec 2 §5.1 | pending-cross-ref | Depends on Spec 2 §5.1 |
| S7-C43 | §3.4 | behavior | Hub→iPhone delivery failure: result in store; retry w/ exp backoff. | (none) | not-testable-from-code | Push layer outside repo |
| S7-C44 | §3.4 | invariant | iPhone app polls /jobs/pending on open. | (none) | not-testable-from-code | iOS |
| S7-C45 | §3.4 | invariant | Result delivery non-destructive. | (none) | aligned | docker-compose.yml (results-store volume persists independently of delivery) |
| S7-C46 | §3.5 | behavior | iPhone→Watch delivery failure: displayed normally; Watch queued. | (none) | not-testable-from-code | iOS |
| S7-C47 | §3.6 | behavior | Hub crash during acceptance: iPhone marks Unconfirmed, retry. | (none) | partial | orchestrator/stale_recovery.py handles recovery; iPhone side not in repo |
| S7-C48 | §3.6 | invariant | Requests carry client idempotency key; duplicate impossible. | (none) | contradicted | FI §2.7 (idempotency is OPT-IN — if omitted one is auto-generated per-call; HTTP Idempotency-Key header ignored) |
| S7-C49 | §3.6 | state | Idempotency store TTL ≥ 7 days. | (none) | ambiguous | 3A pre-flag |
| S7-C50 | §3.6 | behavior | `system.startup` recovery: non-terminal jobs evaluated. | §4 | aligned | orchestrator/stale_recovery.py (run_recovery, _recover_job) |
| S7-C51 | §4 | state | Recovery: accepted, not routed → reclassify. | (none) | aligned | orchestrator/stale_recovery.py:199 (_recover_submitted) |
| S7-C52 | §4 | state | Recovery: routed, not dispatched → redispatch. | (none) | aligned | orchestrator/stale_recovery.py:216 (_recover_classified) |
| S7-C53 | §4 | state | Recovery: dispatched no response; <5min wait; >5min stale+redispatch w/ same key. | (none) | partial | orchestrator/stale_recovery.py:233 (_recover_dispatched); "wait" semantics ambiguous (3A pre-flag) |
| S7-C54 | §4 | state | Recovery: response received, not delivered → deliver. | (none) | aligned | orchestrator/stale_recovery.py:275 (_recover_response_received) |
| S7-C55 | §4 | state | Recovery: delivered awaiting review → no action. | (none) | aligned | orchestrator/stale_recovery.py (terminal branch) |
| S7-C56 | §4 | invariant | Stale re-dispatch reuses original idempotency key. | (none) | aligned | orchestrator/stale_recovery.py logic |
| S7-C57 | §4 | state | Stale threshold 5 minutes. | (none) | aligned | orchestrator/stale_recovery.py:55 (_get_stale_threshold) |
| S7-C58 | §4 | behavior | Late arrival after re-dispatch logged, doesn't override. | (none) | not-testable-from-code | Race not exercised |
| S7-C59 | §4 | state | Re-dispatch cap 2; exhaustion → job.failed(recovery_exhausted). | (none) | partial | stale_recovery logic; not fully exercised |
| S7-C60 | §5 | structure | MacBook Pro M5 7B fallback L1. | (none) | not-testable-from-code | No MacBook code |
| S7-C61 | §5 | behavior | MacBook activation manual via iPhone settings. | (none) | not-testable-from-code | Same |
| S7-C62 | §5 | behavior | Hub suspension on failover: suspend_processing command. | (none) | not-testable-from-code | Same |
| S7-C63 | §5 | behavior | Old hub startup self-check suspends if missed heartbeat. | (none) | partial | orchestrator/hub_state.py has awaiting_authority per FI §2.1 |
| S7-C64 | §5 | invariant | MacBook has no access to Desktop vector DB/audit/results. | (none) | not-testable-from-code | No MacBook code |
| S7-C65 | §5 | invariant | MacBook WAL state does not transfer; WAL-0 reset. | (none) | not-testable-from-code | Same |
| S7-C66 | §5 | invariant | MacBook Packager uses default sensitivity rules. | (none) | not-testable-from-code | Same |
| S7-C67 | §5 | invariant | No auto failover; exactly one hub authoritative. | (none) | not-testable-from-code | Governance principle |
| S7-C68 | §5 | behavior | Return to primary: V1 does not merge logs. | (none) | not-testable-from-code | No MacBook |
| S7-C69 | §6 | structure | `system.connectivity` (iPhone) payload schema. | (none) | partial | orchestrator/events.py:903 (event_system_connectivity_device); iPhone emission not in repo |
| S7-C70 | §6 | structure | `system.connectivity` (orchestrator hub_cloud) payload schema. | (none) | aligned | orchestrator/events.py:880 (event_system_connectivity_hub_cloud) |
| S7-C71 | §6 | structure | `system.hub_switch` (iPhone) payload schema. | (none) | aligned | orchestrator/events.py:937 (event_system_hub_switch) |
| S7-C72 | §6 | state | Degraded state definition and UX. | (none) | partial | connectivity_monitor.py has health state; iPhone UX not in repo |
| S7-C73 | §7 | invariant | No silent failures. | (none) | contradicted | FI §3.1 (nine silent-path findings across Probes 1,2,4,4b,5,6,7) |
| S7-C74 | §7 | invariant | Jobs survive everything except simultaneous storage destruction. | (none) | not-testable-from-code | Boundary scenario |
| S7-C75 | §7 | invariant | Idempotency keys mandatory, re-dispatch reuses, 7d retention. | (none) | contradicted | FI §2.7 (idempotency opt-in, not mandatory; HTTP header ignored) |
| S7-C76 | §7 | invariant | No auto hub failover. | (none) | not-testable-from-code | Governance |
| S7-C77 | §7 | invariant | Push advisory, polling authoritative. | (none) | not-testable-from-code | iOS |
| S7-C78 | §9.1 | state | Decay windows: WAL-0 none; WAL-1 90d/10; WAL-2 60d/25; WAL-3 30d/50. | Spec 2 §9 | ambiguous | 3A pre-flag |
| S7-C79 | §9.1 | behavior | Window minimum not met → demote by one. | (none) | aligned | orchestrator/decay_evaluator.py (DecayAction demotion logic) |
| S7-C80 | §9.2 | behavior | Decay evaluation daily or at startup. | (none) | aligned | orchestrator/decay_evaluator.py (module docstring + load_decay_config) |
| S7-C81 | §9.2 | behavior | Demotes one level per cycle not to WAL-0 directly. | (none) | aligned | orchestrator/decay_evaluator.py (DecayAction to_level=from_level-1) |
| S7-C82 | §9.2 | invariant | Decay demotion resets counters. | Spec 2 §10.2 | ambiguous | 3A pre-flag |
| S7-C83 | §9.2 | behavior | Extended downtime → multiple passes in one startup. | (none) | aligned | orchestrator/decay_evaluator.py docstring + multi-pass logic |
| S7-C84 | §9.3 | invariant | Decay not failure signal; no recent_failures. | (none) | aligned | orchestrator/decay_evaluator.py module header |
| S7-C85 | §9.3 | state | Temporal decay trigger = `temporal_decay`, distinguishable. | (none) | aligned | orchestrator/events.py:963 (event_wal_demoted_temporal_decay) |
| S7-C86 | §10 | invariant | V1: no recency weighting. | (none) | aligned | orchestrator/decay_evaluator.py (no weighting in load_decay_config) |
| S7-C87 | §11 | state | Demotion trigger enum ordering: failures→decay→model change→manual→suspension. | Spec 2 §10.1 | partial | orchestrator/demotion_engine.py exists; exact ordering not exercised |
| S7-C88 | §11 | invariant | Failure-triggered fires before decay in same cycle. | (none) | ambiguous | 3A pre-flag |
| S7-C89 | §12 | structure | Decay parameters in capabilities.json under `decay_policy` with wal_1/wal_2/wal_3. | (none) | missing | config/capabilities.json has NO `decay_policy` key (grep returned empty); decay_evaluator.py:67-68 logs "decay_policy key absent ... temporal decay disabled" |
| S7-C90 | §12 | invariant | Startup validation: window_days>0, min_outcomes>0. | (none) | aligned | orchestrator/decay_evaluator.py:load_decay_config validation |
| S7-C91 | §12 | invariant | Disable decay by removing decay_policy key; logged at startup. | (none) | aligned | orchestrator/decay_evaluator.py:67-68 (logged "temporal decay disabled") |
| S7-C92 | §12 | invariant | Per-cap overrides stricter; enforced at startup. | (none) | aligned | orchestrator/decay_evaluator.py:83-89 |
| S7-C93 | §13 | structure | `wal.demoted(trigger:temporal_decay)` payload. | Spec 1 §4.3 | aligned | orchestrator/events.py:963 |
| S7-C94 | §14 | state | Spec 1 integration: system.connectivity + trigger enum + connectivity fields. | (none) | partial | event builders exist; connectivity_fields full coverage not FI-confirmed |
| S7-C95 | §14 | state | Spec 2 integration: decay_policy in config + validation. | (none) | missing | config/capabilities.json has no decay_policy key (see S7-C89) |
| S7-C96 | §14 | state | Spec 3 integration: no changes. | (none) | aligned | No Context Packager changes found |
| S7-C97 | §14 | state | Spec 4 integration: no changes. | (none) | aligned | No egress health changes found |
| S7-C98 | §14 | state | Spec 5 integration: version-stamp validation. | (none) | not-testable-from-code | Not live-tested |
| S7-C99 | §14 | state | Spec 6 integration: no changes. | (none) | aligned | Silo security is hub-internal |
| S7-C100 | §14 | state | Event Schema additions. | (none) | partial | orchestrator/events.py has builders; some claimed fields unverified |
| S7-C101 | §16 | invariant | Tailscale deny-by-default ACLs; encrypted transport only. | (none) | not-testable-from-code | Operational Tailscale config |
| S7-C102 | §16 | invariant | Orchestrator API must enforce own authentication independent of network. | (none) | contradicted | FI §2.8 + FI §3.4 (no authn/authz anywhere; override surface unauthenticated by design) |
| S7-C103 | §16 | invariant | Ollama least-privilege FS, no ambient creds, restricted network. | Spec 4 §3 | not-testable-from-code | Operational (Ollama container config) |

---

## Phase 3B scoring summary

| Spec | Total | Aligned | Partial | Missing | Contradicted | Ambiguous | Pending-cross-ref | Not-testable |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| S1 | 94 | 55 | 10 | 1 | 2 | 4 | 13 | 9 |
| S2 | 86 | 46 | 18 | 4 | 0 | 1 | 4 | 13 |
| S3 | 38 | 14 | 4 | 1 | 0 | 2 | 3 | 14 |
| S4 | 39 | 23 | 5 | 0 | 0 | 1 | 1 | 9 |
| S5 | 40 | 13 | 6 | 0 | 0 | 2 | 1 | 18 |
| S6 | 114 | 32 | 20 | 14 | 17 | 3 | 2 | 26 |
| S7 | 103 | 25 | 16 | 2 | 5 | 5 | 2 | 48 |
| **Total** | **514** | **208** | **79** | **22** | **24** | **18** | **26** | **137** |

---

## Phase 3B anomalies log

Items encountered during scoring that don't fit cleanly, logged without resolution:

1. **Schema version triple-divergence.** Spec 1 §3 claims `"1.5"`; Spec 6 §7.1 normatively claims bump to `1.6`; code `audit-log-writer/src/models.py:20` has `SCHEMA_VERSION = "2.0"`. Three different values, three different sources. 3A pre-flagged S1-C22 and S6-C85 independently; 3B scores both contradicted against code, but does not resolve the spec-vs-spec discrepancy.

2. **Source-enum divergence.** Spec 1 §3 lists 8 sources; Spec 6 §7.1 adds `worker_egress_proxy` + `sandbox_runtime`; code `audit-log-writer/src/models.py:7-16` only lists the Spec 1 set. Both Spec 6 claims (S6-C86) and the two-spec divergence scored contradicted.

3. **Worker-event-namespace contradiction.** Spec 6 §7.2/§7.3/§7.4 normatively specifies event types `worker.egress_request`, `worker.egress_blocked`, `worker.sandbox_violation`. Code `orchestrator/egress_events.py:27,59` emits `egress.authorized` + `egress.denied`; no `worker.*` emitters exist. This drives contradicted scores for S6-C33, C37-C39, C82-C84, C87-C90, C104 and missing scores for S6-C92-C94. The sentinel `worker_sandbox` is in code (`override_types.py:17`) but the event that would carry it is not emitted.

4. **Docker network topology divergence.** Spec 6 §3.1 specifies five network segments (`drnt-internal`, `drnt-worker`, `drnt-gateway`, `drnt-tailscale`, `drnt-bus`). Code `docker-compose.yml:158-164` defines only two: `drnt-internal` + `drnt-external`. This drives contradicted scores for S6-C23 through S6-C30 (six claims).

5. **Decay config absent from capabilities.json.** Spec 7 §12 (S7-C89, S7-C95) specifies `decay_policy` key in `capabilities.json`. grep of `config/capabilities.json` returns zero matches for `decay_policy`. `decay_evaluator.py:67-68` explicitly logs "decay_policy key absent — temporal decay disabled" in that case. Runtime temporal decay is therefore off, though the code is ready.

6. **Prompt templates directory absent.** Spec 3 §8 (S3-C38) specifies `/var/drnt/config/templates/` location. `ls config/` returns no `templates/` entry. Scored missing.

7. **Orchestration commit `5db743d` regex anomaly carried forward from 3A.** No new information gathered; noted only.

8. **Idempotency contract divergence.** Spec 7 §3.6 (S7-C48) and §7 (S7-C75) claim idempotency keys are "mandatory" and that duplicate submission is "impossible". FI §2.7 documents opt-in behavior with auto-generation when omitted and HTTP Idempotency-Key header ignored. Clear code-vs-claim divergence.

9. **Silent-paths claims.** Spec 7 Preamble + §7 (S7-C1, S7-C2, S7-C73) make absolute "no silent failures / never silently" claims. FI §3.1 lists nine silent-path findings. Scored contradicted.

10. **`egress.validation_failure` dead-emitter.** Spec 1 §7.4 (S1-C90) and §4.7 (S1-C71) assume the event type is used in production. FI §1.4 confirms the builder has zero callers; FI §2.2 confirms it is unreachable at runtime. Scored partial, evidence anchors to FI §1.4 + §2.2.

11. **§11A of Spec 6.** Per 3A non-claim-observation, §11A is an implementation-status reconciliation and is not itself a claims section. 3B does not score §11A rows but recommends 3C/later-phase cross-reference against FI.

12. **Worker-proxy not on a "drnt-worker" segment.** Spec 6 §3.3 (S6-C35) places the proxy on `drnt-worker`. Code places worker-proxy on `drnt-internal` (docker-compose.yml:131). Functional outcome differs from stated topology; scored partial rather than contradicted because the proxy does exist and is reachable by workers.

---

## Phase 3B deferrals to 3C

Pending-cross-ref claims (26 total), grouped by the spec cross-reference each depends on:

**Depends on Spec 1:**
- S2-C6 (auxiliary cap emits own wal.permission_check — depends on Spec 1 §3.1)
- S4-C2 (egress.json changes emit system.config_change — depends on Spec 1 §4.6)

**Depends on Spec 2:**
- S1-C46 (v1 retry same capability only — depends on Spec 2 §8)
- S1-C61 (delivery_hold:true applies pre_delivery — depends on Spec 2 §4)
- S1-C62 (pre_delivery never held — depends on Spec 2 §4)
- S3-C1 (Context Packager owned by context.package — depends on Spec 2 §7.2)
- S5-C25 (WAL-1 memory write on_accept — depends on Spec 2 §5.2)
- S7-C42 (WAL-0 model selection pre_action — depends on Spec 2 §5.1)

**Depends on Spec 3:**
- S2-C85 (credential class hardcoded+strip — depends on Spec 3 §4)
- S6-C44 (sanitizer credential stripping same patterns as Spec 3 credential — depends on Spec 3 §4)

**Depends on Spec 4:**
- S1-C50 (job.dispatched emitted on gateway outbound — depends on Spec 4 §4)
- S3-C35 (Category B transport failures — depends on Spec 4)
- S7-C10 (per-route health probes — depends on Spec 4 §6)

**Depends on Spec 5:**
- S1-C41 (redirect/escalate spawn successor — depends on Spec 5)
- S1-C47 (redirect/escalate spawn successor with new job_id — depends on Spec 5)
- S1-C69 (modified result lineage on human.reviewed — depends on Spec 5)
- S6-C3 (in-flight worker termination — depends on Spec 5 §9.5)
- S6-C102 (override handling worker termination — depends on Spec 5)

**Depends on Spec 7:**
- S1-C93 (watch/phone over Tailscale, offline queue — depends on Spec 7 §3.1-3.2)
- S1-C94 (source_event_id + timestamp on-device — depends on Spec 7)
- S5-C32 (offline queue retroactive cancel — depends on Spec 7 §3.2.1) *(also ambiguous)*

---

## Phase 3B Handoff

- Orchestration HEAD at phase end: `47e1b9f890861942129bdfd6484c5e7ec8b7ee33` (unchanged)
- Gateway HEAD at phase end: `6e9dc2271c2726d1d564bb0aa946142b69b99d6b` (unchanged)
- Working tree status: orchestration untracked `docs/audit/` only (as expected); gateway clean
- Output file: `C:\Users\ljeff\local-first-ai-orchestration\docs\audit\DRNT_Phase3B_Scored_Inventory.md`
- 3A inventory unchanged (preserved as baseline per scope)
- Claims scored: 514 / 514
- Distribution: aligned=208, partial=79, missing=22, contradicted=24, ambiguous=18, pending-cross-ref=26, not-testable=137
- Pending-cross-ref count handed to 3C: 26 (grouped by dependency spec above)
- Anomalies logged: 12 (see anomalies log)
- Deferrals outside 3B scope:
  - Severity weighting / risk ranking (Phase 4)
  - Remediation proposals (Phase 4)
  - Cross-reference resolution between specs (Phase 3C)
  - SPEC-8 claim extraction (separate phase — file exists at `docs/SPEC-8-MANAGED-BUILD-WORKFLOW.md` in gateway, was not extracted in 3A)
  - §11A Spec 6 reconciliation against FI — noted but not scored (non-claim observation carried forward)
- No commits. One new file written: this one.

---

*End of Phase 3B Scored Inventory. Prepared 2026-04-19.*
