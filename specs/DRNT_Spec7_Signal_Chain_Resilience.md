# DRNT SPEC 7: SIGNAL CHAIN RESILIENCE

**Device Failure Modes and WAL Temporal Decay**

*Prepared by: Lawrence Jeffords, DRNT Architecture Project + Claude (Anthropic)*

*Date: March 27, 2026*

*Status: Post-Adversarial Revision (v1.1)*

*Adversarial Review: Gemini (Google) + ChatGPT (OpenAI) | March 2026*

**Design Principle:** The signal chain fails partially, never silently. Every failure is visible to the user, logged in the audit trail, and recoverable without data loss. Jobs are the unit of resilience—if a job was accepted, it will be completed or explicitly failed, never lost.

---

## Part 1: Device Signal Chain Failure Modes

### 1. Scope and Topology

This specification defines what happens when any link in the DRNT signal chain breaks. The signal chain in normal operation is:

**Watch → iPhone → Desktop Hub → Cloud API → Desktop Hub → iPhone → Watch**

Each arrow is a point of failure. This spec covers every one of them. The MacBook Pro serves as a fallback L1 orchestrator but is not in the primary signal chain; its role is defined in Section 5.

The V1 architecture eliminates the Raspberry Pi as a gateway component. The Desktop Hub runs all stateful services: Ollama, the orchestrator, the audit log writer, the job queue, and the Context Packager. The iPhone connects to the Desktop Hub via Tailscale. This simplification removes the Pi SD card fragility and the Pi-to-Desktop failure mode identified in prior adversarial review.

### 2. Failure Detection

Every device in the signal chain must know when its upstream or downstream link is broken. Detection must be fast enough that the user sees a status change, not a spinner.

| Link | Detection Method | Timeout | Check Interval |
|------|-----------------|---------|----------------|
| Watch → iPhone | WatchConnectivity framework reachability | Immediate (OS-level) | Continuous (OS-managed) |
| iPhone → Desktop Hub | Tailscale + HTTP health probe to orchestrator /health endpoint | 5 seconds | 30 seconds |
| Desktop Hub → Cloud API | Per-route health probes (Spec 4, Section 6) | Per provider timeout | check_interval_seconds from egress config |
| Desktop Hub → iPhone | WebSocket heartbeat (primary); push notification (advisory only) | 10 seconds | Heartbeat every 30 seconds |
| iPhone → Watch | WatchConnectivity transferUserInfo delivery confirmation | Immediate (OS-level) | Continuous (OS-managed) |

**Health probe contract:** The orchestrator exposes `GET /health` returning HTTP 200 with a JSON body containing `orchestrator_status`, `audit_log_status`, `ollama_status`, and `last_successful_cloud_probe_timestamp`. The iPhone app treats any non-200 response or timeout as hub unavailable.

#### 2.1 State Transition Hysteresis

**[v1.1 — Added per adversarial review]** Health probe state transitions use asymmetric thresholds to prevent flapping in volatile mobile network environments (weak cellular, cell tower handoffs). Two consecutive probe failures transition to Hub Unavailable. Three consecutive probe successes are required to transition back to Hub Available. Between these states, the iPhone displays "Hub Slow" as a degraded indicator.

This hysteresis prevents the thundering herd problem identified in adversarial review: without it, a briefly restored connection triggers immediate queue replay, the connection drops mid-transmission, and requests are left in an indeterminate state. The asymmetric threshold ensures the hub is genuinely stable before replay begins.

#### 2.2 Push Notifications Are Advisory

**[v1.1 — Added per adversarial review]** Apple Push Notification service (APNs) provides best-effort delivery semantics with no guarantee of receipt. Push notifications in DRNT are a performance optimization for user responsiveness, not a reliability mechanism. The correctness path for result delivery is iPhone polling of `/jobs/pending` on app foreground and on WebSocket reconnection. The system must never depend on push delivery for job completion or state consistency.

### 3. Failure Mode Matrix

Each failure mode defines: what broke, how it is detected, what the user sees, what happens to in-flight jobs, and how recovery works. Every failure emits a `system.connectivity` event to the audit log (when the log writer is reachable) or queues the event locally for replay when connectivity is restored.

#### 3.1 Watch-to-iPhone Link Failure

**Cause:** Bluetooth out of range, iPhone powered off, or Watch in airplane mode.

**Detection:** WatchConnectivity session becomes inactive.

**User experience:** Watch displays "Phone Disconnected" complication state. Voice input is accepted and queued locally on the Watch.

**[v1.1] Watch queue overflow policy:** The Watch can store up to 10 queued requests in local storage. When the queue is full, the oldest queued request is replaced by the newest submission, preserving the most recent user intent. The user receives a haptic notification that the oldest request was dropped. This prevents a hard UX lockout during extended partitions while keeping queue storage bounded.

**In-flight jobs:** Jobs already submitted to the hub are unaffected—they complete normally and results queue on the iPhone. The Watch will not receive delivery notifications until the link is restored.

**Recovery:** When WatchConnectivity reactivates, queued requests are forwarded to the iPhone in submission order via `transferUserInfo` (guaranteed delivery, FIFO). The Watch receives any pending result notifications. No user intervention required.

**Audit:** `system.connectivity` event emitted by the iPhone app when it detects Watch disconnection and reconnection. Queued request timestamps are preserved from original Watch submission time. If any requests were dropped due to queue overflow, the event includes `dropped_request_count`.

#### 3.2 iPhone-to-Desktop Hub Link Failure

**Cause:** Desktop hub offline (reboot, power outage, GPU driver update), Tailscale tunnel down, weak cellular preventing tunnel establishment, or orchestrator process crashed while OS is running.

**Detection:** Health probe failure (HTTP timeout or non-200 response). After 2 consecutive probe failures (60 seconds), the iPhone transitions to Hub Unavailable state. Transition back to Hub Available requires 3 consecutive successful probes (Section 2.1).

**User experience:** iPhone displays "Hub Offline" banner at the top of the job list. New requests are accepted and queued locally on the iPhone with a visible "Queued" badge. The user can continue composing and submitting requests. Previously delivered results remain visible and interactive (accept, reject, modify actions are queued). The Watch shows "Hub Offline" complication state.

**In-flight jobs:** This is the critical case. Jobs have three possible states at the moment of disconnection:

| Job State at Disconnect | What Happens | Recovery |
|------------------------|--------------|----------|
| Submitted but not yet routed | Job record exists in hub job queue. Orchestrator will process it normally when it resumes. If the hub crashed mid-acceptance, the job may not have been persisted—see Section 3.6. | Hub resumes processing. iPhone polls for status on reconnect. |
| Routed, awaiting cloud response | The cloud API call is in flight. The hub will receive the response when it comes back (or timeout). Result is stored in the results store. | Hub stores result. iPhone retrieves on reconnect. If the hub crashed before receiving the response, the job is marked stale and re-dispatched—see Section 4. |
| Result ready, not yet delivered | Result is in the hub results store. Delivery notification cannot reach the iPhone. | iPhone reconnects, polls /jobs/pending, retrieves results. |

**Local queue limits:** The iPhone can queue up to 50 requests locally. This is generous for any realistic disconnection period. The 51st request is rejected with "Local Queue Full." Queued requests are persisted to iPhone local storage (Core Data) and survive app termination and device restart.

**[v1.1] Local storage protection:** Queued requests and decisions persisted in Core Data must use iOS Data Protection class `NSFileProtectionCompleteUntilFirstUserAuthentication` at minimum. This ensures data is encrypted at rest and inaccessible before the user's first unlock after device restart, consistent with OWASP mobile storage guidance.

##### 3.2.1 Offline Decision Replay and Concurrency Control

**[v1.1 — Added per adversarial review]** When the user makes decisions (accept, reject, modify) on previously delivered results while the hub is disconnected, those decisions are queued locally for replay on reconnection. Each queued decision must carry the `result_id` and `response_hash` of the result the user was viewing when the decision was made.

On replay, the hub validates that the job's current state matches the state the user observed. If the job state has changed—because the hub recovered and re-processed the job, because the user acted on another device, or because the job was timed out during hub recovery—the hub rejects the replayed decision with a conflict status. The iPhone surfaces the conflict to the user: "This result changed while you were offline. Please review the current version."

This optimistic concurrency control prevents three failure modes identified in adversarial review: (1) applying a modification to a job that was garbage-collected during hub recovery, (2) rejecting a result that was already approved and acted upon via another device or interface, and (3) replaying decisions against results from a re-dispatched job that superseded the original.

**[v1.1] Throttled queue replay:** Queued requests are submitted in batches of 5 with a 2-second delay between batches, not all at once. This prevents overwhelming the hub on reconnection and reduces the risk of partial transmission if connectivity is unstable. Queued human decisions are replayed after all pending job submissions complete, in their original submission order.

**Recovery:** When the health probe succeeds 3 consecutive times (Section 2.1), the iPhone transitions back to Hub Available. Queued requests are submitted in throttled batches. Queued human decisions are replayed with version-stamp validation. The iPhone polls `/jobs/pending` to retrieve any results that were completed while disconnected. A `system.connectivity` event pair (down/up) is emitted to the audit log with the disconnection duration.

#### 3.3 Desktop Hub-to-Cloud API Failure

**Cause:** Internet outage, cloud API rate limiting, provider outage, TLS failure, or DNS resolution failure.

**Detection:** Egress gateway health probes (Spec 4). Per-route circuit breakers detect consecutive failures.

**User experience:** The hub is still operational. Local-capable jobs process normally. Cloud-bound jobs emit `job.queued` with `reason: connectivity` and the user sees "Waiting for cloud" on the job status. If all cloud routes are down, the orchestrator can still accept and classify requests—it queues the dispatch until a route recovers.

**In-flight jobs:** Jobs already dispatched to a cloud API that fail receive a `model.error` event. The retry policy from the capability's WAL level applies (WAL-0: no retry; WAL-1+: auto-retry per policy). If all retries exhaust, `job.failed` is emitted and the user is notified.

**Recovery:** Automatic. Health probes detect route recovery. Queued jobs dispatch in order. Circuit breakers reset after successful probe. No user intervention required.

**Fallback routing:** If a specific provider is down but others are available, the orchestrator MAY reroute to an alternative provider if the task classification permits it. This is a routing decision, not an automatic fallback—it follows the normal capability and WAL permission model. At WAL-0, model selection requires human approval (`pre_action` gate), so the user explicitly approves any reroute.

#### 3.4 Result Delivery Failure (Hub-to-iPhone)

**Cause:** iPhone app backgrounded, cellular too weak for push notification delivery, or Tailscale tunnel temporarily down.

**Detection:** WebSocket heartbeat failure. Push notification non-delivery is not treated as an authoritative signal (Section 2.2).

**User experience:** From the hub's perspective, the job is complete—the result is in the results store. The hub retries push notification delivery with exponential backoff (10s, 30s, 60s, 5m, 15m, then hourly) as an advisory notification. When the user opens the iPhone app, it polls `/jobs/pending` regardless of notification status.

**In-flight jobs:** Unaffected. The result exists. Only the notification is delayed.

**Recovery:** Automatic on app foreground or WebSocket reconnection. No data loss possible—the result is persisted on the hub.

#### 3.5 iPhone-to-Watch Delivery Failure

**Cause:** Watch out of Bluetooth range when result arrives on iPhone.

**Detection:** WatchConnectivity session inactive.

**User experience:** iPhone has the result and displays it normally. Watch notification is queued. When Watch reconnects, it receives a tap with the job summary.

**Recovery:** Automatic via WatchConnectivity guaranteed delivery. No user intervention.

#### 3.6 Hub Crash During Job Acceptance

**Cause:** Orchestrator process crash, OOM kill, or unclean shutdown between receiving a request and writing the job record to the durable queue.

**Detection:** The iPhone submitted a request and received either no HTTP response or a 5xx error.

**User experience:** The iPhone marks the request as "Submission Unconfirmed" with a retry button. The user can retry manually or wait for automatic retry on reconnect.

**In-flight jobs:** The job may or may not exist on the hub. This is the only case where a job can be truly lost.

**Recovery:** Requests carry a client-generated idempotency key (UUIDv7, generated at submission time on the iPhone). When the iPhone retries, the hub checks the idempotency key against the job queue. If the job exists, it returns the existing job ID. If not, it creates a new job. Duplicate submission is impossible.

**[v1.1] Idempotency store TTL:** The hub's idempotency key store must retain keys for at least 7 days, exceeding the maximum realistic offline queue retention period on the iPhone. This prevents the scenario where the hub purges an idempotency record before a disconnected iPhone replays the request, which would cause the hub to treat the replay as a new job. The 7-day TTL is a V1 default; deployments with longer expected offline periods should increase it.

**Audit:** If the hub did persist the job before crashing, `system.startup` on restart reconciles in-flight jobs. Any job in a non-terminal state older than the crash timestamp is evaluated: jobs with cloud responses in the results store are completed; jobs without responses are re-dispatched; jobs that were never routed are re-classified.

### 4. Stale Job Recovery

On startup after an unclean shutdown, the orchestrator runs a recovery pass over all non-terminal jobs. This is the single mechanism that prevents jobs from being silently abandoned.

| Job State at Crash | Recovery Action | Event Emitted |
|-------------------|----------------|---------------|
| accepted, not routed | Re-classify and route | `job.classified` (new routing) |
| routed, not dispatched | Re-dispatch | `job.dispatched` |
| dispatched, no response | If dispatch was < 5 minutes ago, wait. If > 5 minutes, mark stale and re-dispatch using the original idempotency key. | `job.queued` (reason: recovery) then `job.dispatched` |
| response received, not delivered | Deliver to user | `job.delivered` |
| delivered, awaiting review | No action needed—human decision is pending | None |

**[v1.1 — Critical fix per adversarial review]** Stale job re-dispatch must reuse the original idempotency key, not generate a new one. Both Gemini and ChatGPT independently identified that generating a new key for a stale re-dispatch instructs the cloud provider to treat the retry as a novel request, causing double-execution of stateful operations. The original idempotency key is preserved in the job record at dispatch time and must be used for any subsequent re-dispatch of the same job.

**Stale threshold:** 5 minutes. Any dispatched job without a response after 5 minutes is presumed lost. The original cloud API call may still complete and return a response—if it does, the response is logged. If the re-dispatched job has already received a response, the late arrival is recorded in the audit log but does not override the delivered result. The stale threshold is configurable but must not be set below the longest provider timeout in the egress configuration.

**Re-dispatch cap:** A single job may be re-dispatched at most twice during recovery. If two re-dispatch attempts fail, the job transitions to `job.failed` with `error_class: recovery_exhausted` and the user is notified. This prevents unbounded cost amplification from an adversary who can delay or blackhole cloud responses.

### 5. MacBook Pro Fallback

The MacBook Pro (M5) serves as a fallback L1 orchestrator. It can run a 7B model locally on the M5 GPU. Its role is to keep the signal chain operational when the Desktop Hub is down for an extended period (hardware failure, not a reboot).

**Activation:** Manual. The user launches the DRNT orchestrator on the MacBook. The iPhone's Tailscale configuration includes both the Desktop Hub and MacBook as endpoints. The user switches the active hub in the iPhone app settings. This is a deliberate human decision, not an automatic failover.

**[v1.1] Hub suspension on failover:** When the user switches the active hub in the iPhone app, the app sends a `suspend_processing` command to the old hub if it is reachable. The old hub pauses job dispatch and enters a suspended state, preventing zombie job execution when its connectivity is restored. If the old hub is not reachable at switch time (which is the likely scenario—you're switching because it's down), the hub implements a self-check on startup recovery: if it has not received a heartbeat from any client within a window exceeding its own downtime, it pauses job processing and waits for a client to reconnect and confirm its authority as the active hub. This is lighter than enterprise fencing (STONITH) but prevents the split-brain execution scenario identified in adversarial review.

**Limitations:** The MacBook does not have access to the Desktop Hub's vector database, audit log history, or results store. It starts with a clean job queue. Context Packager operates with default sensitivity rules, not any learned or customized rules from the Desktop. WAL state does not transfer—all capabilities start at WAL-0 on the MacBook.

**Why not automatic failover:** Automatic failover between two L1 orchestrators creates a split-brain risk. If both hubs accept jobs simultaneously, the audit logs diverge, WAL state diverges, and job IDs can collide. Manual activation ensures exactly one hub is authoritative at any time. The cost of manual switching (30 seconds of user effort) is trivially small compared to the cost of split-brain recovery.

**Return to primary:** When the Desktop Hub is back online, the user switches the active hub back in the iPhone app. The iPhone sends a `suspend_processing` command to the MacBook hub. Any jobs completed on the MacBook remain in the MacBook's local log. They are not automatically merged into the Desktop's audit log. A future version may support log reconciliation, but V1 treats MacBook operation as an independent session.

### 6. Connectivity Events

All connectivity state changes are recorded in the audit log. These events are emitted by whichever device detects the state change.

| Event | Emitter | Payload Fields | Durability |
|-------|---------|---------------|------------|
| `system.connectivity` | iPhone app | `link` (enum: watch_phone, phone_hub), `state` (enum: down, up, degraded), `duration_ms` (on up event), `queued_requests` (count at time of down), `replayed_requests` (count at time of up), `dropped_requests` (count, Watch overflow only), `replay_conflicts` (count, version-stamp rejections) | D |
| `system.connectivity` | Orchestrator | `link` (enum: hub_cloud), `state` (enum: down, up, degraded), `affected_routes` (route IDs), `queued_jobs` (count) | D |
| `system.hub_switch` | iPhone app | `from_hub` (hostname), `to_hub` (hostname), `reason` (enum: manual, primary_failure), `pending_jobs_on_old_hub` (count), `suspend_command_delivered` (bool) | D |

**Degraded state:** Used when connectivity exists but is unreliable—health probes succeed intermittently, or response times exceed 3x the normal baseline. The iPhone shows "Hub Slow" rather than "Hub Offline." Jobs are submitted normally but the user is informed that processing may be delayed.

### 7. Design Constraints

**No silent failures.** Every failure mode defined in this spec produces a visible indicator on the user's device and an event in the audit log. If a job cannot be completed, it is explicitly failed with a reason—never silently dropped.

**Jobs survive everything except total data loss.** The only scenario where a job is permanently lost is simultaneous destruction of the hub's job queue storage AND the iPhone's local queue. Any single-device failure is recoverable.

**Idempotency keys are mandatory and immutable.** Every request submitted from any client device carries a client-generated UUIDv7 idempotency key. The orchestrator enforces idempotency at the job creation endpoint. Re-dispatch during stale recovery reuses the original key. The hub retains idempotency records for at least 7 days.

**No automatic hub failover.** Exactly one hub is authoritative at any time. Switching is a human decision. Hub suspension signals prevent zombie execution on the demoted hub.

**Push is advisory, polling is authoritative.** APNs best-effort semantics mean push notifications cannot be relied upon for correctness. The iPhone polls `/jobs/pending` on every foreground event and WebSocket reconnection.

---

## Part 2: WAL Temporal Decay

### 8. Problem Statement

The WAL trust model as specified in the Capability Trust Profile defines promotion, demotion, and counter mechanics but treats trust as static between events. A capability promoted to WAL-2 six months ago, which has not processed a job since, retains WAL-2 privileges indefinitely. This is architecturally unsound for two reasons:

- **Staleness risk.** The conditions that justified promotion may no longer hold. The cloud model may have been updated. The user's context and preferences may have changed. The task category may have shifted in character. A capability that earned trust in January should not coast on that trust in July without fresh evidence.

- **Model change gap.** The Capability Trust Profile already resets capabilities to WAL-0 on model change. But model providers update models continuously without changing the model string. A capability's earned trust may be based on a model version that no longer exists, with no detection mechanism.

Temporal decay solves both problems by requiring capabilities to demonstrate continued reliability to maintain elevated trust levels.

### 9. Decay Mechanism

#### 9.1 Activity Window

Each capability tracks a rolling activity window. The window size varies by WAL level because higher autonomy levels require more recent evidence to justify.

| WAL Level | Activity Window | Minimum Jobs in Window | Decay Action |
|-----------|----------------|----------------------|--------------|
| WAL-0 | No decay | N/A | WAL-0 is the floor. No decay applies. |
| WAL-1 | 90 days | 10 jobs | Demote to WAL-0 if fewer than 10 evaluable outcomes in the last 90 days. |
| WAL-2 | 60 days | 25 jobs | Demote to WAL-1 if fewer than 25 evaluable outcomes in the last 60 days. |
| WAL-3 (aux only) | 30 days | 50 jobs | Demote to WAL-2 if fewer than 50 evaluable outcomes in the last 30 days. |

**Rationale for asymmetric windows:** WAL-2 and WAL-3 grant the system authority to act without human pre-approval. The evidence window for these levels must be shorter because the consequences of stale trust are higher. WAL-1 (draft and assist) is lower-consequence—drafts still require human review—so a longer window is acceptable.

#### 9.2 Decay Evaluation

The orchestrator evaluates decay conditions once daily, at startup, or whichever comes first. The evaluation is simple:

1. For each capability at WAL-1 or above, count the evaluable outcomes in the ring buffer (Section 7.4 of the Capability Trust Profile) whose timestamps fall within the activity window. If the count is below the minimum, emit `wal.demoted` with `trigger: temporal_decay` and demote by one level.

2. Decay demotes by one level per evaluation cycle, not to WAL-0 directly. A WAL-2 capability that has been idle for 90 days will demote to WAL-1 on the first evaluation, then to WAL-0 on the next evaluation 24 hours later (since it will still have insufficient activity in the WAL-1 window). This two-step demotion provides a natural grace period and emits two distinct audit events.

3. Decay demotion resets counters, identical to any other demotion per the Capability Trust Profile. Trust is re-earned from the new level.

**[v1.1] Extended downtime handling:** If the hub has been offline for a period exceeding multiple activity windows (e.g., months), the startup decay evaluation runs its normal per-cycle logic but executes multiple evaluation passes in the same startup sequence until all capabilities reach their warranted level. A WAL-2 capability idle for 120 days will demote to WAL-1 on the first pass, then immediately to WAL-0 on the second pass within the same startup. This prevents a capability from retaining elevated privileges for the first 24 hours after an extended outage.

#### 9.3 Decay Is Not Punitive

Temporal decay is not a failure signal. It does not record a failure in `recent_failures`. It does not contribute to the 3-failures-in-24-hours demotion trigger. It is a separate mechanism with a separate trigger type (`temporal_decay`) that the audit log and any future reporting can distinguish from quality-driven demotions.

A capability that was performing well but simply has not been used recently will demote gracefully and re-promote quickly once it resumes activity—because its first jobs at the lower WAL level will likely succeed, building the evidence base for re-promotion.

### 10. Recency Weighting

The `evaluable_outcomes` ring buffer currently treats all outcomes equally. Temporal decay adds a second lens: not just how many outcomes exist in the window, but how recent they are.

**V1 approach:** No recency weighting. The activity window is a hard cutoff—outcomes inside the window count equally, outcomes outside do not count. This is simple, predictable, and sufficient for personal-scale usage where job volume is modest.

**V2 consideration:** Exponential recency weighting, where recent outcomes count more heavily than older ones within the window. This would allow a capability with 25 outcomes to maintain WAL-2 even if most of those outcomes are from early in the 60-day window—but only if the recent ones are high quality. This adds complexity without clear personal-scale benefit and is deferred.

### 11. Interaction with Existing Demotion Triggers

Temporal decay operates alongside, not instead of, the existing demotion mechanisms:

| Trigger | Source | Demotion Behavior | Counter Reset |
|---------|--------|-------------------|---------------|
| 3 failures in 24h | recent_failures deque (Capability Trust Profile §8) | Immediate, by one level | Yes |
| Temporal decay | Daily evaluation of activity window | By one level per cycle | Yes |
| Model change | capabilities.json update | Reset to WAL-0 | Yes |
| Manual demotion | Human config edit | To desired_wal_level | Yes |
| Suspension | Context Packager strip failure | To WAL -1 (suspended) | Yes, manual recovery required |

**Ordering:** If both a failure-triggered demotion and a temporal decay demotion would apply in the same evaluation cycle, the failure-triggered demotion fires first (it is immediate). The temporal decay evaluation then sees the already-demoted level and evaluates against that level's window. In practice, if a capability just failed enough to demote, it almost certainly lacks the activity volume to sustain the lower level either—but the mechanism handles this cleanly.

### 12. Configuration

Decay parameters are defined per-capability in `capabilities.json`, with system defaults that apply when no per-capability values are specified:

```json
{
  "decay_policy": {
    "wal_1": { "window_days": 90, "min_outcomes": 10 },
    "wal_2": { "window_days": 60, "min_outcomes": 25 },
    "wal_3": { "window_days": 30, "min_outcomes": 50 }
  }
}
```

**Startup validation:** The orchestrator validates that `window_days > 0` and `min_outcomes > 0` for all configured levels. Zero values would disable decay, which undermines the architectural guarantee. Disabling decay requires removing the `decay_policy` key entirely, which is a deliberate configuration choice that is logged at startup.

**Per-capability overrides:** A capability may specify its own `decay_policy` that is stricter than the system default (shorter windows, higher minimums) but never more permissive. Startup validation enforces this: per-capability `window_days` must be ≤ system default, per-capability `min_outcomes` must be ≥ system default.

### 13. Audit Events

Temporal decay produces one new event type:

| Field | Type | Description |
|-------|------|-------------|
| event_type | string | `wal.demoted` |
| trigger | enum | `temporal_decay` (new value added to existing trigger enum) |
| capability_id | string | The demoted capability |
| from_level | int | WAL level before demotion |
| to_level | int | WAL level after demotion (always from_level - 1) |
| window_days | int | The activity window that was evaluated |
| outcomes_in_window | int | How many evaluable outcomes were found |
| min_required | int | The threshold that was not met |
| last_outcome_timestamp | string ǀ null | Timestamp of the most recent outcome, or null if none |

The `last_outcome_timestamp` field allows post-hoc analysis of how long a capability was idle before decay triggered. This is informational—the decay decision is based solely on the count within the window, not the recency of the most recent outcome.

---

## Integration Notes

### 14. Relationship to Existing Specs

| Spec | Integration Point |
|------|-------------------|
| Spec 1 (Audit Event Schema) | `system.connectivity` events added (Section 6). `wal.demoted` trigger enum extended with `temporal_decay` (Section 13). `replay_conflicts` and `dropped_requests` fields added to connectivity payload. |
| Spec 2 (Capability Model) | `decay_policy` added to capability configuration schema (Section 12). Startup validation extended (Section 12). |
| Spec 3 (Context Boundary) | No changes. Context Packager behavior is unaffected by device failures—it processes whatever reaches it. |
| Spec 4 (Egress Policy) | No changes. Egress health probes and circuit breakers are upstream of the device failure modes defined here. |
| Spec 5 (Override Semantics) | Human override queuing during hub disconnection (Section 3.2.1) requires version-stamp validation on replay. Overrides are replayed in submission order with optimistic concurrency control. |
| Spec 6 (Silo Runtime Security) | No changes. Silo security is hub-internal and does not interact with device connectivity. |
| Capability Trust Profile | Ring buffer and counter mechanics are unchanged. Temporal decay reads the existing `evaluable_outcomes` buffer—no new data structures required. |
| Event Schema | `system.connectivity` and `system.hub_switch` events added. `job.queued` reason enum extended with `recovery` for stale job re-dispatch. `job.failed` error_class enum extended with `recovery_exhausted`. |
| Lightweight Evaluation Loop | Feedback taps queued on iPhone during hub disconnection are replayed on reconnection with version-stamp validation. Tap timestamps are preserved from original interaction time. |

### 15. What This Spec Does Not Cover

**Multi-hub synchronization.** V1 does not synchronize audit logs, WAL state, or job queues between Desktop and MacBook. Each hub is an independent authority.

**Offline-capable local inference on iPhone.** The iPhone does not run a local model in V1. All inference requires the hub. A future version could run a small model on-device for classification during extended disconnection, but this is out of scope.

**Watch standalone operation.** The Watch has no independent intelligence. When disconnected from the iPhone, it can queue voice input and nothing else. It does not attempt to reach the hub directly.

**Automated decay parameter tuning.** V1 uses fixed decay windows and minimums. Adjusting these based on observed usage patterns is a V2 optimization.

**Supply-chain security and SBOM.** Dependency management, model provenance verification, and patch SLAs for Ollama and Tailscale are operational concerns addressed through deployment checklists, not architectural specification.

### 16. Deployment Notes

**[v1.1 — Added per adversarial review]** The following operational requirements are outside the scope of this architectural specification but were identified during adversarial review as necessary for secure deployment. They are documented here to ensure they are not lost between specification and implementation.

**Tailscale ACL policy:** Configure deny-by-default ACLs on the Tailscale tailnet. Only the iPhone (and explicitly authorized fallback hub) should be able to reach hub API ports. Do not rely on Tailscale network membership as an authorization mechanism—treat it as encrypted transport only. The orchestrator API must enforce its own authentication and authorization independent of network layer.

**Ollama isolation:** Run the Ollama model server with least-privilege filesystem access, no ambient credentials, and restricted network exposure. Monitor Ollama CVE advisories and pin to known-good versions. Published vulnerabilities include authentication bypass (CVE-2025-63389), arbitrary file deletion (CVE-2025-44779), and parser denial-of-service (CVE-2025-0312). The Docker network isolation specified in Spec 4 (drnt-internal bridge) provides the primary enforcement layer.

**iOS Data Protection:** All locally persisted queued requests, decisions, and cached results must use iOS Data Protection class `NSFileProtectionCompleteUntilFirstUserAuthentication` at minimum. Core Data stores should be configured with this protection class explicitly.

### 17. Revision History

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | March 27, 2026 | Initial specification draft. |
| v1.1 | March 27, 2026 | Post-adversarial revision incorporating findings from Gemini (Google) and ChatGPT (OpenAI) reviews. Ten changes: (1) Stale recovery must reuse original idempotency key. (2) Offline decision replay requires version-stamp concurrency control. (3) Health probe hysteresis added (asymmetric thresholds). (4) Queue replay throttled to batches of 5. (5) Watch queue overflow replaces oldest instead of hard reject. (6) Hub suspension signal on failover. (7) Push notifications explicitly advisory, polling authoritative. (8) Tailscale deny-by-default ACL deployment note. (9) iOS Data Protection class requirement. (10) Idempotency store TTL must exceed offline queue retention. |

---

*This document has been adversarially reviewed by Gemini (Google) and ChatGPT (OpenAI). Findings were selectively incorporated based on architectural relevance to V1 scope.*
