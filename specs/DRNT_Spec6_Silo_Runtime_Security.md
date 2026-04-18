# DRNT Interface Specification

## Spec 6: Silo Runtime Security

**FINAL** · March 2026 · Depends on: Spec 1 (Audit/Event Schema), Spec 2 (Capability Model), Spec 3 (Context Boundary), Spec 4 (Egress Policy), Spec 5 (Override Semantics)

**Revision History:** v1 → v2: Resolved outbound privacy contradiction, clarified trust ownership, added Spec 1 integration, separated manifest fields, added Spec 5 override interaction, added session approval TTL. v2 → FINAL: Resolved sanitizer default posture language, fixed policy immutability/hot-reload scope, added resource_template to blueprints, added sentinel for sandbox violation WAL attribution, routed TUI force-terminate through L1, added concrete V1 skill verification checklist, distinguished L2 integrity-significant events from advisory context, removed sanitizer clean-and-forward (V1 is block-only), acknowledged TLS inspection requirement.

---

## 1. Purpose

This spec defines the enforcement model for the execution silo — the runtime environment where worker agents, the verification agent, and their dependencies operate between the L1 perimeter and the L2 exit audit. It establishes three complementary enforcement layers, the skill lifecycle that governs how third-party code enters the silo, and the runtime egress policy that governs what worker agents can access during execution.

Specs 2–4 govern what L1 dispatches, how context is packaged, and what leaves the silo for cloud models. This spec governs what happens inside the silo while agents execute. Spec 4 covers the cloud-dispatch egress path. This spec covers the worker-execution egress path — a distinct boundary with distinct controls. Spec 5 governs override handling, including the termination of in-flight worker agents when a human intervenes (Section 9.4).

## 2. The Three Enforcement Layers

The silo operates under three complementary layers. Each governs a different class of risk. No layer substitutes for the others.

### 2.1 Layer 1: Runtime Isolation (Environment-Level)

**What it governs:** What the execution environment permits at the OS and container level. Filesystem access, network egress, process privileges, binary execution.

**Enforcement mechanism:** Container configuration, Linux security modules (Landlock, seccomp), network namespace isolation. Out-of-process. The agent cannot modify, disable, or escalate these constraints from inside the sandbox.

**Key property:** Locked at sandbox creation time for filesystem and process restrictions. Baseline network egress policy (the runtime manifest) is also locked at creation and requires sandbox recreation to change permanently. Session-scoped endpoint approvals are hot-reloadable by the operator via the TUI proxy (Section 8.3), providing temporary access without modifying the baseline. Even if the agent is fully compromised — prompt injection, malicious skill, corrupted model output — the runtime isolation holds. This is the floor.

**Failure mode:** Misconfiguration at creation time. Too permissive = exposure. Too restrictive = agents can't function. Mitigation: validated sandbox blueprints (Section 5), startup validation (Section 11).

### 2.2 Layer 2: Behavioral Governance (Application-Level)

**What it governs:** What the system is allowed to do within the bounds the runtime permits. WAL levels, completion contracts, gate policies, promotion/demotion, cost controls.

**Enforcement mechanism:** The capability model (Spec 2), permission checks, the audit log. In-process. The orchestrator evaluates every action against the capability registry before execution.

**Key property:** Graduated. Trust is earned through demonstrated reliability, not granted by configuration. Anomalies trigger automatic demotion. The behavioral layer operates inside the constraints the runtime layer sets — it can further restrict but never relax runtime constraints.

**Failure mode:** Heuristic drift. The behavioral model uses thresholds and counters, not formal safety proofs. A capability can accumulate trust through high-volume low-stakes operations and then fail on a high-stakes edge case. Mitigation: promotion criteria include edge-case requirements (Spec 2 Section 9.1), and the runtime layer prevents catastrophic outcomes regardless of behavioral trust level.

### 2.3 Layer 3: Structural Privacy (Data-Level)

**What it governs:** What data leaves the local environment and in what form. Sensitivity classification, context packaging, egress gateway checks, and outbound request sanitization.

**Enforcement mechanism:** Two enforcement paths. Cloud dispatch: the Context Packager (Spec 3) and egress gateway (Spec 4), providing full sensitivity classification with dual independent scans, defaulting to deny on unclassifiable content. Worker execution egress: the request sanitizer on the worker egress proxy (Section 3.4), providing credential stripping and PII pattern detection on outbound URLs, headers, and request bodies. The two paths have different default postures: cloud dispatch defaults to deny at both the endpoint and content level; worker execution egress defaults to deny at the endpoint level (unknown endpoints blocked) and defaults to pass at the content pattern level for requests to approved endpoints (see Section 3.4 for rationale).

**Key property:** All outbound data is subject to privacy enforcement, but the enforcement model differs by path. Cloud-bound payloads receive full semantic privacy classification (Spec 3). Worker execution requests receive request-level sanitization — pattern matching against known sensitive data types, not semantic classification of assembled context. Credentials are stripped on both paths unconditionally. The distinction matters: cloud dispatch carries assembled personal context from local memory; worker execution requests are typically structured API queries to approved endpoints. The risk profiles are different, and the enforcement depth matches.

**Failure mode:** Classification gap on the cloud path. Pattern gap on the worker path. Mitigation: default-deny on unclassifiable (Spec 3 Section 4), gateway secondary scan (Spec 3 Section 7), and sanitizer default-block on unrecognized patterns (Section 3.4).

### 2.4 Layer Interaction Rules

Runtime isolation is the ceiling. Behavioral governance and structural privacy operate within it, not above it.

Behavioral governance can tighten what runtime permits, never relax it. A WAL-0 capability in a sandbox with network access to a cloud endpoint still requires `pre_delivery` gate approval (Spec 2) before the orchestrator dispatches the payload. Worker agents inside the silo are not capabilities — they do not go through the Spec 2 permission check pipeline. The governing capability that dispatched the job owns the WAL context.

Structural privacy enforcement applies to all outbound data. Cloud dispatch receives the full Spec 3/Spec 4 pipeline. Worker execution egress receives request sanitization (Section 3.4). Neither path is exempt.

No layer trusts another layer's enforcement. The gateway does not assume the Context Packager caught everything. The runtime does not assume behavioral governance prevented the agent from attempting a disallowed action. The sanitizer does not assume the worker agent excluded sensitive data from its request. Defense in depth.

## 3. Silo Network Architecture

The existing DRNT network architecture (Spec 4 Section 3) defines the orchestrator-to-gateway path for cloud dispatch. This section extends it to cover worker agent network access during execution.

### 3.1 Network Segments

| Segment | Members | External Access |
| --- | --- | --- |
| `drnt-internal` | Orchestrator (L1), L2, log writer, verification agent | None |
| `drnt-worker` | Worker agent containers | Per-agent egress policy via worker egress proxy only |
| `drnt-gateway` | Egress gateway | Host outbound (cloud endpoints per Spec 4) |
| `drnt-tailscale` | Phone/watch connectivity | No egress path |
| `drnt-bus` | L1 ↔ worker message bus | None (internal IPC only) |

**`drnt-worker` is new.** Worker agents operate on an isolated network segment with per-agent egress policies. They cannot reach `drnt-internal` — communication with L1 is via a dedicated message bus on the `drnt-bus` segment, which carries only structured job messages (dispatch commands, completion signals, failure reports). Workers cannot reach `drnt-gateway` — cloud dispatch requests are submitted to L1 via the bus, packaged by the Context Packager, and dispatched by the gateway. Workers never send outbound requests to cloud models directly.

**`drnt-bus` is new.** A dedicated IPC segment connecting L1 to worker containers. Carries structured messages only. No external connectivity. No raw data passthrough. The bus interface is the sole communication channel between the orchestrator and worker agents.

### 3.2 Worker-to-External Egress

Worker agents may need external access during execution — vendor APIs, reference data sources. This is a different egress path than cloud model dispatch and requires its own policy and its own privacy enforcement.

| Property | Cloud Dispatch (Spec 4) | Worker Execution Egress (this spec) |
| --- | --- | --- |
| Path | Orchestrator → Context Packager → Gateway → Cloud | Worker → Sanitizer → Egress Proxy → External |
| Gate | Capability + sensitivity + egress registry | Agent type policy + sanitizer + operator approval |
| Privacy enforcement | Full sensitivity classification (Spec 3) | Lightweight request sanitizer (Section 3.4) |
| Default | Deny (not in registry = blocked) | Deny (not in agent policy = blocked, surfaced for approval) |
| Audit | `job.dispatched`, `context.packaged` | `worker.egress_request`, `worker.egress_blocked` |
| Who approves | Human edits egress.json, restart | Operator via TUI (session, with TTL) or edits manifest (permanent) |

### 3.3 Worker Egress Proxy

A lightweight proxy on the `drnt-worker` segment. All worker outbound requests route through it. The proxy performs two checks in sequence: first the request sanitizer (Section 3.4), then the agent type's egress policy (Section 4.2). Both must pass for the request to proceed.

**If sanitizer blocks:** request dropped, `worker.egress_blocked` emitted with `block_reason: sanitizer_catch`, surfaced in operator TUI. This is a privacy event, not a policy event.

**If policy blocks:** request dropped, `worker.egress_blocked` emitted with `block_reason: endpoint_not_in_policy`, surfaced in operator TUI for approval decision.

**If both pass:** request forwarded, `worker.egress_request` emitted with `policy_result: allowed`.

**Session approvals:** operator approves a blocked endpoint via TUI. Persists for a configurable TTL (default: 4 hours), not until sandbox recreation. Expired approvals require re-approval or permanent manifest update. This prevents long-lived rubber-stamped permissions from accumulating.

**Permanent approvals:** operator edits agent type manifest, recreates sandbox.

*The proxy is out-of-process. The worker agent cannot bypass, modify, or disable it. Session approvals attach to the job context that triggered the block, providing the operator with the full decision context rather than an isolated endpoint name.*

### 3.4 Request Sanitizer

The request sanitizer is the privacy enforcement point for the worker execution egress path. It is not the full Context Packager (Spec 3) — worker egress requests are structurally different from cloud-bound assembled payloads. The sanitizer enforces three checks on every outbound worker request:

**1. Credential stripping.** Scans URL parameters, headers, and request body for API keys, tokens, passwords, and authentication credentials. Same pattern set as the `credential` sensitivity class in Spec 3 Section 4 (`hardcoded: true`). Any match blocks the request unconditionally. Not operator-overridable.

**2. PII pattern detection.** Scans URL parameters, headers, and request body for patterns matching the `name`, `location`, `date`, and `financial` sensitivity classes. Matches are blocked and surfaced for operator review. The operator can approve the specific request (session, with TTL) or update the sanitizer allowlist (permanent, requires restart).

**3. Unrecognized pattern default.** Content that does not match any known-bad pattern passes. This is a deliberate, endpoint-constrained exception to the broader DRNT privacy posture, not a general relaxation. The exception is justified by two structural constraints: Layer 1 already restricts which endpoints the worker can reach (the blast radius of any undetected leak is limited to approved endpoints), and worker egress requests are structurally different from cloud dispatch payloads (they are typically key-value API queries, not assembled personal context from memory). Applying full Spec 3 default-deny to every outbound API request would make the system non-functional. The security model for worker egress is endpoint restriction (Layer 1) plus known-bad pattern detection (sanitizer), not semantic content classification.

**TLS inspection:** The sanitizer must inspect request content that traverses HTTPS connections. This requires a local inspection proxy with a sandbox-trusted CA certificate, standard practice for egress inspection in container environments. The proxy terminates TLS from the worker, inspects the request, then re-establishes TLS to the external endpoint. Implementation details (certificate generation, trust store injection, proxy configuration) are operational, not architectural, and are documented in the implementation guide.

*The sanitizer is lighter than the full Spec 3 pipeline by design. In V1, the sanitizer detects and blocks — it does not attempt to redact and forward (clean-and-forward is a post-V1 capability requiring validated redaction logic). PII matches are always blocked and surfaced for operator review. Credential matches are always blocked unconditionally.*

## 4. Agent Runtime Manifests

Every agent type registered in the agent registry carries two documents: a completion contract (behavioral — what "done" looks like) and a runtime manifest (environmental — what the agent needs to function).

### 4.1 Completion Contract

Defines what output is expected, attestation requirements (confidence, coverage, limitations), timeout and failure handling, and cost budget for cloud dispatch requests. Completion contracts are specified per agent type and included in the dispatch payload when L1 assigns work via the agent registry.

### 4.2 Runtime Manifest

The runtime manifest declares what the execution environment must provide for this agent type. It is the agent type's permission boundary.

```json
{
  "agent_type_id": "worker.reconciliation",
  "sandbox_blueprint": "drnt-worker-base-v1",
  "filesystem": {
    "writable": ["/sandbox/work", "/tmp"],
    "readable": ["/sandbox/skills/{agent_type_id}", "/sandbox/shared/data"],
    "blocked": ["*"]
  },
  "network_egress": {
    "default": "deny",
    "allowed_endpoints": [],
    "operator_approval_on_unknown": true
  },
  "cloud_dispatch_dependencies": [
    {
      "capability_id": "route.cloud.claude",
      "note": "Reconciliation may request cloud reasoning via L1"
    }
  ],
  "binaries": {
    "allowed": ["node", "python3", "jq"],
    "blocked": ["*"]
  },
  "skills": {
    "required": [],
    "optional": [],
    "blocked_categories": []
  },
  "resource_limits": {
    "max_memory_mb": 2048,
    "max_cpu_shares": 512,
    "max_execution_time_seconds": 300
  }
}
```

**`filesystem.blocked: ["*"]`** — default-deny. Only paths explicitly listed in `writable` and `readable` are accessible. Locked at sandbox creation. Not modifiable at runtime.

**`network_egress.allowed_endpoints`** — direct network endpoints the worker agent can reach during execution. Cloud model endpoints never appear here. Workers do not dispatch to cloud models directly.

**`cloud_dispatch_dependencies`** — documents which cloud capabilities the agent's work may eventually reach via the L1 → Context Packager → Gateway path. This is informational metadata, not a network permission. It enables startup validation to confirm that referenced capabilities are registered and operational.

**`binaries.blocked: ["*"]`** — only listed binaries can execute. A skill that ships a custom binary must have that binary added to the allowed list before installation.

**`operator_approval_on_unknown: true`** — when the agent hits an endpoint not in its policy, the proxy blocks and surfaces for operator review.

### 4.3 Manifest Enforcement

The runtime manifest is enforced by the sandbox blueprint at creation time. The orchestrator validates manifests at startup (Section 11). Invalid manifests prevent the agent type from registering.

The manifest is immutable during execution. The agent cannot request expanded permissions at runtime. If an agent needs access to something not in its manifest, the job fails, the failure is logged, and the operator updates the manifest and recreates the sandbox.

## 5. Sandbox Blueprints

A sandbox blueprint is a versioned, declarative specification for creating an agent execution environment. Blueprints translate runtime manifests into container configurations.

### 5.1 Blueprint Structure

```json
{
  "blueprint_id": "drnt-worker-base-v1",
  "version": "1.0.0",
  "base_image": "drnt-worker:v1",
  "isolation": {
    "landlock": true,
    "seccomp_profile": "drnt-worker-default",
    "network_namespace": "drnt-worker",
    "pid_namespace": true,
    "dropped_capabilities": ["ALL"],
    "added_capabilities": []
  },
  "filesystem_template": {
    "writable_base": ["/sandbox/work", "/tmp"],
    "readable_base": ["/sandbox/skills", "/sandbox/shared"],
    "agent_extensions": "from_manifest"
  },
  "network_template": {
    "default": "deny",
    "proxy": "drnt-worker-egress-proxy",
    "sanitizer": "drnt-request-sanitizer",
    "agent_extensions": "from_manifest"
  },
  "resource_ceiling": {
    "max_memory_mb": 4096,
    "max_cpu_shares": 1024,
    "max_execution_time_seconds": 600
  }
}
```

**`agent_extensions: "from_manifest"`** — the blueprint defines the base isolation profile. The runtime manifest for each agent type adds its specific paths and endpoints on top. The blueprint constrains what the manifest can declare — a manifest cannot grant capabilities the blueprint doesn't permit.

**`resource_ceiling`** — the blueprint sets maximum resource limits. The runtime manifest's `resource_limits` must be at or below these ceilings. Startup validation rejects manifests that exceed blueprint ceilings. This prevents any single agent type from consuming disproportionate resources.

### 5.2 Blueprint Lifecycle

Created by the system operator. Version-controlled in `/var/drnt/config/blueprints/`. Validated at startup against isolation requirements (Section 11). Applied at sandbox creation. Once a sandbox is running, the blueprint is immutable. Updated by creating a new version, validating, and recreating sandboxes. Rolling restart, not hot update.

### 5.3 V1 Blueprints

V1 ships with one blueprint: `drnt-worker-base-v1`. All worker agents use it. Blueprint diversity is a post-V1 concern — V1 establishes the pattern.

## 6. Skill Lifecycle

Third-party skills are the primary supply chain attack surface. Every skill is treated as hostile until proven otherwise. The skill lifecycle governs how code enters the silo, what it can do once inside, and how trust is established.

### 6.1 Skill Installation Path

Skills are installed by the operator from outside the sandbox. No skill installation occurs from inside the sandbox. No package manager runs inside the sandbox. No skill self-installs.

Installation sequence:

**1. Fetch.** Operator obtains skill from source (direct download, version control, or future skill registry). Outside the sandbox.

**2. Verify.** Operator runs V1 verification checks before any skill enters the silo. Required checks: (a) capture SHA-256 hash of the skill artifact as downloaded — this becomes the skill's identity for audit purposes; (b) confirm dependency lockfile is present and review all declared dependencies; (c) inventory all binaries the skill ships or requires and cross-check against the runtime manifest's binary allowlist; (d) review the skill's permission manifest (Section 6.2) and verify every declared network endpoint, filesystem path, and binary is justified; (e) diff the skill's declared permissions against the agent type's current runtime manifest to confirm all required extensions are accounted for. Future: automated scanning (VirusTotal, reputation scoring) supplements but does not replace manual review.

**3. Declare.** Operator adds skill to the agent type's runtime manifest under `skills.required` or `skills.optional`. The manifest declares what the skill needs (binaries, network endpoints, filesystem paths).

**4. Upload.** Operator uploads verified skill to `/sandbox/skills/{agent_type_id}/{skill_name}/` via sandbox upload tool.

**5. Register.** Operator adds skill entry to agent configuration (`skills.entries` object, not array).

**6. Recreate sandbox.** Sandbox is recreated from blueprint + updated manifest. New filesystem and network policies take effect. Old sandbox is destroyed.

*Why recreate instead of restart: Filesystem restrictions are locked at creation time. Adding a skill that requires new filesystem paths or new binary permissions requires a new sandbox instance. This is a security feature, not a limitation — it prevents runtime expansion of the attack surface.*

### 6.2 Skill Permission Manifest

Every skill declares what it needs. The skill cannot access anything beyond its declaration. Enforcement is by the sandbox, not by the skill.

```json
{
  "skill_id": "solid-agent-storage",
  "version": "1.0.0",
  "source": "direct",
  "requires": {
    "binaries": ["jq"],
    "network_endpoints": [
      {
        "host": "storage.provider.example",
        "port": 443,
        "purpose": "External storage communication"
      }
    ],
    "filesystem_write": ["/sandbox/work/solid-storage"],
    "filesystem_read": ["/sandbox/skills/solid-agent-storage"]
  }
}
```

The operator reviews this manifest during the verification step. If the skill declares network access to an endpoint the operator doesn't recognize, the operator investigates before installation. The manifest is the conversation starter, not the enforcement mechanism — enforcement is the sandbox.

### 6.3 Skill Trust Levels

Skills do not participate in WAL. Skills are tools, not capabilities (see Section 6.4 for trust ownership). The agent type that uses a skill inherits execution risk from that skill.

| Skill Origin | Isolation Tier | Trust Posture |
| --- | --- | --- |
| Self-authored (operator wrote it) | Container (blueprint default) | Accidental damage. Trusted intent. |
| Operator-verified third-party | Container + restricted egress | Untrusted intent. Manually verified behavior. V1 requires explicit operator verification. |
| Unverified third-party | Not permitted in V1 | Blocked. |

*Future (post-V1): MicroVM isolation. Third-party skills execute in Firecracker or gVisor MicroVMs providing hardware-level isolation with a minimal kernel. Required when the threat model is malicious intent, not just accidental damage.*

### 6.4 Trust Ownership and Failure Attribution

Worker agent types are not capabilities in the Spec 2 sense. They do not have WAL levels, promotion criteria, or action policies. They are execution units invoked by L1's dispatch to the agent registry. The governing capability (e.g., `route.cloud.claude`) owns the job and the WAL context.

**Failure attribution:** Worker failures attribute to the governing capability that dispatched the job, with one exception. Quality failures (timeout, attestation failure, completion contract violation) attribute to the governing capability's WAL counters normally per Spec 2 Section 10. Sandbox violations (`worker.sandbox_violation`) attribute to the sentinel value `"worker_sandbox"`, which is excluded from WAL counters, consistent with the sentinel pattern for `"egress_config"` and `"egress_connectivity"` in Spec 2 Section 10.1. Rationale: a sandbox violation is an infrastructure integrity event — the operator needs to fix the skill or agent configuration, not retrain the routing judgment. Quality failures reflect on the governing capability's dispatch judgment; infrastructure violations do not.

**Runtime reliability record:** Each agent type maintains a separate runtime reliability record for operational visibility. This record tracks: total jobs executed, failure count by failure type, skills in use at time of failure, and last failure timestamp. This record is informational — it helps the operator diagnose which agent type or skill is causing problems. It does not participate in the WAL state machine. It is not stored in `capabilities.state.json`.

**Skill-induced failures:** The `worker.sandbox_violation` and `worker.egress_blocked` events carry `skill_id` when attributable. The operator uses this to investigate whether a specific skill should be removed from the manifest. Sandbox violations use the `"worker_sandbox"` sentinel and are excluded from WAL counters. Egress policy violations (`worker.egress_blocked`) attribute to the governing capability normally — the capability's execution pipeline attempted an action outside its declared permissions, which reflects on dispatch judgment.

*This separation preserves Spec 2's invariant: capabilities are the unit of WAL trust. Worker agents and skills are execution details inside the silo. The governing capability is accountable for the quality of its dispatched work. Infrastructure integrity events (sandbox violations) are operator problems, not routing problems, and are excluded from the trust state machine accordingly.*

## 7. Worker Egress Audit Events

New event types for worker execution egress and sandbox violations. These follow the Spec 1 event envelope (`schema_version`, `event_id`, `source_event_id`, `timestamp`, `committed_at`, `event_type`, `job_id`, `parent_job_id`, `capability_id`, `wal_level`, `source`, `prev_hash`, `durability`, `payload`). All `worker.*` events are durable (write-before-proceed). Requires Spec 1 schema bump to 1.6 and source enum extension.

### 7.1 Spec 1 Integration

**Schema version:** 1.5 → 1.6. New event types added; no envelope changes.

**Source enum extension:** Add `worker_egress_proxy` and `sandbox_runtime` to the source enum (Spec 1 Section 3). `worker_egress_proxy` emits `worker.egress_request` and `worker.egress_blocked`. `sandbox_runtime` emits `worker.sandbox_violation`.

**Envelope scoping:** Worker events carry the governing capability's `capability_id` and `wal_level` in the envelope, consistent with Spec 1 Section 3.1 (one job, one governing capability). The `agent_type_id` and `agent_instance_id` are payload fields, not envelope fields.

**Durability:** All `worker.*` events are durable. A sandbox violation or egress block is a security-relevant event that must be written before the system proceeds.

### 7.2 worker.egress_request

Emitted by the worker egress proxy for every outbound request that passes both the sanitizer and the policy check.

| Payload Field | Type | Description |
| --- | --- | --- |
| `agent_type_id` | string | e.g. `"worker.reconciliation"` |
| `agent_instance_id` | UUIDv7 | Specific running instance |
| `target_host` | string | Requested endpoint host |
| `target_port` | integer | Requested endpoint port |
| `method` | string | HTTP method |
| `path` | string | Request path (sanitized — PII stripped) |
| `requesting_binary` | string | e.g. `"node"` |
| `policy_result` | enum | `allowed` │ `operator_approved` |
| `policy_source` | enum | `manifest` │ `session_approval` │ `baseline_policy` |
| `sanitizer_result` | enum | `pass` (no sensitive patterns detected) |
| `skill_id` | string │ null | Skill that initiated the request, if attributable |

### 7.3 worker.egress_blocked

Emitted when a request is blocked by either the sanitizer or the policy check.

| Payload Field | Type | Description |
| --- | --- | --- |
| `agent_type_id` | string | Agent type that attempted the request |
| `agent_instance_id` | UUIDv7 | Specific running instance |
| `target_host` | string | Blocked endpoint |
| `target_port` | integer | Blocked endpoint port |
| `block_reason` | enum | `sanitizer_credential` │ `sanitizer_pii` │ `endpoint_not_in_policy` │ `binary_not_allowed` |
| `blocked_by` | enum | `sanitizer` │ `egress_policy` |
| `operator_notified` | boolean | Whether TUI surfaced the request |
| `operator_decision` | enum │ null | `pending` │ `approved` │ `denied` (null if not surfaceable, e.g. credential block) |
| `skill_id` | string │ null | Skill attribution, if identifiable |

*`sanitizer_credential` blocks are never operator-overridable. Credential patterns are hardcoded, same as Spec 3's credential class. `sanitizer_pii` blocks are surfaceable — the operator can approve if the PII pattern is a false positive.*

### 7.4 worker.sandbox_violation

Emitted by the sandbox runtime when the isolation layer catches a prohibited action. `job_id` in the envelope may be null if the violation occurs while the worker is idle between dispatches.

| Payload Field | Type | Description |
| --- | --- | --- |
| `agent_type_id` | string | Agent type that caused the violation |
| `agent_instance_id` | UUIDv7 | Specific running instance |
| `violation_type` | enum | `filesystem_write` │ `filesystem_read` │ `binary_exec` │ `privilege_escalation` │ `network_bypass` |
| `target` | string | Path or resource that was blocked |
| `blocked` | boolean | Always true |
| `skill_id` | string │ null | Skill attribution, if identifiable |
| `severity` | enum | `critical` (always for sandbox violations) |
| `failing_capability_id` | string | `"worker_sandbox"` (sentinel, excluded from WAL counters per Spec 2 Section 10.1) |

*`sandbox_violation` is always severe. Any violation triggers immediate operator investigation regardless of whether a job was active. When `job_id` is null, the violation is a system-level integrity event not attributable to any governing capability. When `job_id` is non-null, the event is linked to the job's audit trail but the `"worker_sandbox"` sentinel ensures no WAL demotion fires — sandbox violations are infrastructure problems, not routing problems.*

## 8. Operator TUI

The operator terminal UI provides real-time visibility into worker agent execution. It is a break-glass monitoring and approval tool, not a routine part of job execution. If the operator finds themselves routinely approving the same endpoints, the correct response is updating the runtime manifest, not rubber-stamping session approvals.

### 8.1 Views

| View | Content |
| --- | --- |
| Active Jobs | Running jobs, agent assignments, completion status |
| Egress Monitor | Live egress requests — allowed, blocked, pending approval |
| Violations | Sandbox violations with severity, agent type, skill attribution |
| Approval Queue | Blocked egress requests with full job context |

### 8.2 Operator Actions

Approve blocked egress endpoint (session, with TTL). Deny blocked egress endpoint. View full audit trail for any agent instance. Request worker agent termination (routed through L1, which executes the standard bus signal, cleanup, and audit sequence from Section 9.5 — the TUI is the operator's interface to L1, not a bypass around it). View current runtime manifest for any agent type.

### 8.3 Approval Semantics

**Session approvals:** TTL-bounded. Default: 4 hours. Configurable per sandbox blueprint. Expired approvals require re-approval or permanent manifest update. TTL prevents long-lived permissions from accumulating silently.

**Permanent approvals:** Operator edits runtime manifest, recreates sandbox. Deliberate friction.

**Job context attachment:** Blocked egress requests in the approval queue include the `job_id`, `agent_type_id`, `skill_id` (if attributable), and the completion contract summary. The operator sees why the agent is making the request, not just the endpoint name.

*The TUI is exceptional, not routine. Routine approval patterns indicate a manifest gap, not normal operation. The system should trend toward fewer TUI approvals over time as manifests stabilize.*

## 9. Relationship to Existing Specs

### 9.1 Spec 1 (Audit/Event Schema)

Spec 6 introduces three new event types (`worker.egress_request`, `worker.egress_blocked`, `worker.sandbox_violation`) that require integration into the Spec 1 event catalog. This requires: schema version bump from 1.5 to 1.6, source enum extension to include `worker_egress_proxy` and `sandbox_runtime`, addition of the new event types to the durability table (Section 7.4 of Spec 1) as durable events, and cross-reference table update (Spec 1 Section 9).

### 9.2 Spec 2 (Capability Model)

Spec 2 governs behavioral trust — what the system is allowed to do at each WAL level. This spec governs runtime enforcement — what the environment permits regardless of WAL level. A capability at WAL-2 with permission to `dispatch_cloud` still operates inside a sandbox with restricted filesystem and network access. Behavioral trust does not expand runtime permissions.

Worker agent types are not capabilities. They do not appear in the capability registry, do not have WAL levels, and do not go through the permission check pipeline. Failures in worker execution attribute to the governing capability that dispatched the job (Section 6.4). This preserves Spec 2's invariant: capabilities are the unit of trust.

### 9.3 Spec 3 (Context Boundary)

Spec 3 governs the full privacy pipeline for cloud-bound data. This spec governs the runtime environment where worker agents generate data that may eventually enter that pipeline. The two are sequential: worker agents produce output → output enters L1 via the message bus → L1 invokes Context Packager (Spec 3) → packaged context goes to gateway (Spec 4) → cloud.

Worker execution egress has its own privacy enforcement (Section 3.4) that is lighter than the full Spec 3 pipeline. Both paths share the same credential pattern set. The sanitizer's PII patterns are a subset of Spec 3's sensitivity classes. The two paths differ in default behavior: Spec 3 default-denies unclassifiable content; the sanitizer passes unrecognized patterns (see Section 3.4 rationale).

### 9.4 Spec 4 (Egress Policy)

Spec 4's egress registry covers cloud model endpoints. This spec's worker egress policy covers endpoints worker agents access during execution. These are different registries, different network segments, different approval flows. Cloud endpoints never appear in worker `network_egress.allowed_endpoints`. Worker endpoints never appear in Spec 4's `egress.json`. The separation is structural.

### 9.5 Spec 5 (Override Semantics)

Spec 5 defines what happens when the human intervenes. When an override (cancel, redirect, escalate) targets a job that has worker agents mid-execution inside the silo, the following sequence applies:

**1. Override receipt.** L1 receives the override via the standard Spec 5 path.

**2. Worker termination.** L1 sends a terminate signal to all active worker agents for the job via the message bus. Workers receive the signal and halt. In-flight egress requests are dropped by the proxy (the proxy checks job status before forwarding).

**3. Cleanup.** Worker sandboxes for the terminated job are cleaned up. Temporary files in `/sandbox/work` are purged. The sandbox itself persists (it serves the agent type, not the job).

**4. Audit.** `worker.egress_blocked` events are emitted for any in-flight requests that were dropped. The standard Spec 5 event sequence (`human.override` → `job.failed` or `job.revoked`) proceeds.

**5. Successor job.** If the override spawns a successor (redirect, escalate), the successor may dispatch to the same or different agent types. Worker agents for the successor operate under their own completion contracts with no state carryover from the terminated job.

*Override wins immediately, same as Spec 5 Section 11. The silo adds worker termination and cleanup to the existing override sequence. No new override types are introduced.*

## 10. L2 Exit Audit Interaction

The execution silo operates between L1 and L2. This spec focuses on L1-side enforcement (what enters the silo, what executes inside, what egresses during execution). L2's role as exit auditor is unchanged: L2 receives verified output from the silo (after the verification agent signs off), performs governance review, and structures the decision menu for the human.

L2 has read access to the `worker.*` audit events for the job it is auditing. These events fall into two categories that L2 treats differently:

**Integrity-significant events:** `worker.sandbox_violation` and `worker.egress_blocked` with `block_reason: sanitizer_credential`. These indicate that the silo's security boundary was tested during execution. L2 flags these prominently in the decision context presented to the human. A job that completed despite a sandbox violation during execution carries a fundamentally different integrity profile than one that executed cleanly.

**Advisory context:** `worker.egress_blocked` with `block_reason: endpoint_not_in_policy` (followed by operator approval) and `worker.egress_request` events. These provide operational transparency but do not indicate integrity compromise. L2 includes them as background context, not as flags.

L2 does not gate on `worker.*` events — it does not block delivery based on them. But it distinguishes between events that signal integrity risk and events that signal normal operational friction, and presents them accordingly.

## 11. Startup Validation

In addition to existing startup validation (Specs 2, 4):

- All registered agent types have valid runtime manifests
- Runtime manifests reference only registered sandbox blueprints
- Blueprint isolation profiles meet minimum requirements (Landlock enabled, seccomp profile loaded, network namespace isolated, all capabilities dropped)
- Runtime manifest `resource_limits` are at or below the referenced blueprint's `resource_ceiling`
- Skill permission manifests are consistent with agent runtime manifests (no skill declares permissions its agent type's manifest doesn't include)
- Worker egress proxy and request sanitizer are operational on `drnt-worker` segment
- No runtime manifest grants filesystem write access outside `/sandbox/` and `/tmp`
- No runtime manifest grants binary execution for binaries not in blueprint's base allowlist or skill's declared requirements
- `cloud_dispatch_dependencies` reference registered capabilities in Spec 2's capability registry
- Message bus on `drnt-bus` segment is operational

*Validation failures prevent agent type registration. Unregistered agent types cannot receive dispatched work.*

## 11A. v0.2 Implementation Status

This section reconciles the Spec 6 design surface against the v0.2.1 reference implementation in the `drnt-project` repository. It is a real-code audit, not a forward-looking commitment. Status terms follow the project's `STATUS.md` definitions: **Implemented** (code exists, wired, tested), **Partial** (code exists with documented gaps), **Aspirational** (described here, not yet built).

### 11A.1 Three Enforcement Layers (Section 2)

| Layer | Status | Notes |
| --- | --- | --- |
| Layer 1 — Runtime Isolation | Implemented | Docker container per worker. `cap_drop: ["ALL"]`, `read_only: true`, `no-new-privileges`, `network_mode: "none"` (default), tmpfs-only writable, image registry, mem/pids/wall-timeout caps. Enforcement is on the `worker-proxy` HTTP boundary via Pydantic validators (`worker-proxy/models.py`) backed by `config/worker-proxy-registry.json`. |
| Layer 2 — Behavioral Governance | Implemented | Spec 2 capability registry, WAL state machine, `permission_checker.py`, `demotion_engine.py`, `promotion_monitor.py`. Worker agent types are not capabilities — the governing capability owns the WAL context, per Section 6.4. |
| Layer 3 — Structural Privacy (cloud path) | Implemented | Full Spec 3/Spec 4 pipeline: `context_packager.py` + `egress_proxy.py` + egress gateway. |
| Layer 3 — Structural Privacy (worker egress path) | Aspirational | The request sanitizer described in Section 3.4 is not implemented in v0.2. Worker containers default to `network_mode: "none"`, which removes the path the sanitizer would inspect. v0.2 mitigates the risk by closing the path, not by inspecting it. |

### 11A.2 Silo Network Architecture (Section 3)

| Segment | Spec 6 Status | v0.2 Reality |
| --- | --- | --- |
| `drnt-internal` | Implemented | `docker-compose.yml`. Carries orchestrator ↔ audit ↔ worker-proxy ↔ ollama ↔ egress-gateway. |
| `drnt-external` | Implemented (Spec 4) | Egress gateway and ollama only. Orchestrator is not on this segment. |
| `drnt-worker` | Aspirational | Not separated in v0.2. Worker containers run with `network_mode: "none"` rather than on a dedicated segment. |
| `drnt-bus` | Aspirational | L1↔worker IPC is file-based (inbox/outbox JSON via shared sandbox volume), not a bus. No message-bus container exists. |
| `drnt-tailscale` | Aspirational | Not in v0.2. |
| `drnt-gateway` | Aspirational as a separate segment | Egress gateway sits on `drnt-external` directly. |

The worker egress proxy described in Section 3.3 is also aspirational. The `worker-proxy` service in v0.2 is a Docker-socket sidecar that brokers container lifecycle calls — it is not the request-level outbound HTTP proxy described by Spec 6. The two share a name but solve different problems. Section 3.4's request sanitizer, which depends on the worker egress proxy, is also not implemented.

### 11A.3 Agent Runtime Manifests (Section 4)

| Element | Status | Evidence |
| --- | --- | --- |
| Manifest dataclass | Implemented | `orchestrator/runtime_manifest.py` — `RuntimeManifest`, `VolumeMount`, `NetworkPolicy`, `ResourceLimits`, `SecurityPolicy`. |
| Manifest validator | Implemented | `orchestrator/manifest_validator.py`. |
| Manifest enforcement at sandbox creation | Implemented | `orchestrator/blueprint_engine.py` translates manifest → `SandboxBlueprint`. v0.2 Phase 2B (commit `72fb249`) wires `mem_limit`, `pids_limit`, and `tmpfs` from blueprint into the executor request body so the values reach Docker rather than silently defaulting at the executor. |
| Manifest immutability during execution | Implemented | Blueprint is constructed before container creation; container is one-shot and recreated per job. |
| Schema field naming | Diverges from Spec 6 | The v0.2 manifest is keyed on `capability_id` and `worker_type` rather than `agent_type_id`. `network.egress_allow` plays the role of `network_egress.allowed_endpoints`. The `cloud_dispatch_dependencies` field is not present — cloud routing is governed by the capability registry. |
| `binaries.allowed/blocked` | Aspirational | No binary allowlist enforcement in v0.2. The worker image entrypoint is fixed and `read_only: true` prevents installation. |
| `skills.required/optional/blocked_categories` | Aspirational | No skill subsystem in v0.2 (see 11A.5). |

### 11A.4 Sandbox Blueprints (Section 5)

| Element | Status | Notes |
| --- | --- | --- |
| Blueprint dataclass + engine | Implemented | `orchestrator/sandbox_blueprint.py`, `orchestrator/blueprint_engine.py`. |
| Single v1 blueprint | Implemented | Conceptually `drnt-worker:latest` only. There is no on-disk blueprint catalog under `/var/drnt/config/blueprints/`; the blueprint is constructed in-process from manifest + defaults. |
| Resource ceilings | Implemented (v0.2 Phase 2B, commit `578adde`) | `config/worker-proxy-registry.json` declares `caps.mem_limit_max`, `caps.pids_limit_max`, `caps.wall_timeout_max`. Worker-proxy rejects requests exceeding caps with HTTP 422 before the Docker socket is touched. |
| `isolation.landlock` | Aspirational | Landlock not configured in v0.2. |
| `isolation.seccomp_profile` | **Partial — broken** | `config/seccomp-default.json` exists. The orchestrator loads its full content at startup and threads the JSON string through to Docker's `security_opt=["seccomp=<content>"]`. Docker expects a *path* in that argument, not the profile body. Net effect: the seccomp filter is not applied to worker containers in v0.2. Tracked as a v0.2 known issue (see project `STATUS.md`). |
| `isolation.dropped_capabilities: ["ALL"]` | Implemented | Enforced by worker-proxy validator (`cap_drop` must contain `"ALL"`). |
| `isolation.added_capabilities: []` | Implemented | `cap_add` is in the deferred-fields set on the worker-proxy HTTP model and is rejected by `extra="forbid"`. |
| `network_template.proxy` | Aspirational | No worker egress proxy in v0.2. |
| `network_template.sanitizer` | Aspirational | No request sanitizer in v0.2. |

### 11A.5 Skill Lifecycle (Section 6)

Aspirational across the board for v0.2. There is no skill registry, no skill permission manifest, no sandbox upload tool, and no operator verification checklist enforcement. Worker images are built and shipped by the operator out-of-band; no third-party code path enters the silo in v0.2. The trust ownership model (Section 6.4) is honored implicitly because no skills exist — worker quality failures already attribute to the governing capability through the existing audit-event capability_id.

### 11A.6 Worker Egress Audit Events (Section 7)

| Spec 6 Event | v0.2 Implementation |
| --- | --- |
| `worker.egress_request` | Not emitted. Closest analog is `egress.authorized` (Spec 4 path), emitted by `orchestrator/egress_events.py` for cloud dispatch only. |
| `worker.egress_blocked` | Not emitted. Closest analog is `egress.denied` for cloud dispatch. |
| `worker.sandbox_violation` | Not emitted. The worker-proxy rejects malformed requests with HTTP 422 before the container is created; v0.2 has no runtime sandbox-violation detector inside the container. |
| Schema version 1.5 → 1.6 bump | Not applicable. The v0.2 audit envelope uses `schema_version: "2.0"` (`audit-log-writer/src/event_validator.py`). DRNT moved to its own schema versioning track and is not aligned with the Spec 6 document version numbers. |
| `source` enum extension (`worker_egress_proxy`, `sandbox_runtime`) | Not applicable in v0.2. The v0.2 audit envelope does not enforce a closed `source` enum at the writer; sources used today include `egress_proxy`, `audit_log_writer`, `orchestrator`, etc. |

### 11A.7 Operator TUI (Section 8)

Aspirational. v0.2 has no operator TUI. Approval flow does not exist because no surface generates `worker.egress_blocked` events to approve. Override authority (Spec 5) is exercised via the orchestrator's HTTP API.

### 11A.8 L2 Exit Audit Interaction (Section 10)

Aspirational. There is no L2 service in v0.2. Spec 1's audit log is the integrity surface; downstream classification of integrity-significant vs. advisory events is not implemented.

### 11A.9 Startup Validation (Section 11)

| Check | Status | Evidence |
| --- | --- | --- |
| Manifest validity | Implemented | `manifest_validator.py` invoked during `worker_lifecycle.prepare_worker()`. |
| Sandbox environment posture | Implemented | `orchestrator/startup_validator.py` — sandbox base directory, seccomp profile readable, worker-proxy reachable. Fail-closed on critical checks. |
| Egress posture | Implemented | `startup_validator.py` — `egress.json` validity, secrets readable. |
| Audit integrity | Implemented | `startup_validator.py` — audit socket reachable, today's chain readable, hash chain verifies from genesis. |
| Blueprint catalog validity | Not applicable | Blueprint is in-process, not file-backed in v0.2. |
| Skill permission consistency | Not applicable | No skills in v0.2. |
| Worker egress proxy operational | Not applicable | No worker egress proxy in v0.2. |
| `cloud_dispatch_dependencies` registered | Not applicable | Field not present in v0.2 manifest. |
| Bus operational on `drnt-bus` | Not applicable | No bus in v0.2. |

### 11A.10 v0.2 Posture Summary

The v0.2 implementation realizes the spec's Layer 1 (runtime isolation) end-to-end on the worker-execution path, with default-deny enforced on a dedicated HTTP boundary (`worker-proxy`) backed by a registry file. The orchestrator no longer holds the Docker socket. Layer 2 (behavioral governance) is fully realized through Specs 2 and 5. Layer 3 is realized for the cloud-dispatch path (Specs 3 and 4); the worker-execution structural-privacy path described in Sections 3.3 and 3.4 is intentionally deferred — v0.2 closes the path with `network_mode: "none"` rather than inspecting it with a sanitizer.

The worker egress proxy, request sanitizer, dedicated `drnt-worker` and `drnt-bus` segments, skill lifecycle, operator TUI, L2 exit audit, and the `worker.*` event family are out of scope for v0.2 and remain on the v0.3+ track. The seccomp path-vs-content bug is the one known regression against the as-claimed Layer 1 surface and is tracked in `STATUS.md`.

---

## 12. V1 Scope

### 12.1 In V1

Runtime isolation via Docker with dropped capabilities, restricted volumes, network namespace. Worker egress proxy with request sanitizer (block-only in V1, no clean-and-forward) and default-deny endpoint policy. Operator TUI with TTL-bounded session approvals (default 4 hours). Termination requests routed through L1. Runtime manifests per agent type with separated `network_egress` and `cloud_dispatch_dependencies`. One sandbox blueprint (`drnt-worker-base-v1`) with `resource_ceiling`. Skill installation from outside sandbox only with concrete verification checklist (hash capture, lockfile review, binary inventory, manifest diff). Self-authored and operator-verified skills only. Sandbox violation sentinel (`"worker_sandbox"`) excluded from WAL counters; worker quality failures attribute to governing capability. Runtime reliability records per agent type (informational, not WAL). Audit events: `worker.egress_request`, `worker.egress_blocked`, `worker.sandbox_violation` (Spec 1 schema 1.6). Dedicated `drnt-bus` segment for L1-to-worker IPC. Spec 5 override interaction for in-flight worker termination. L2 distinguishes integrity-significant worker events from advisory context.

### 12.2 Not in V1

MicroVM isolation (Firecracker/gVisor) for third-party skills. Automated skill verification (VirusTotal, reputation scoring, registry integration). Formal safety proofs / sliding mode control integration for behavioral governance. Skill marketplace integration. Multi-blueprint environments (different blueprints for different agent types). Cross-agent communication policies (inter-agent identity and access control).

### 12.3 Designed For But Deferred

**SMC integration point:** The worker egress proxy's sanitizer and policy decisions are currently binary (pass/block). A future continuous safety signal (sliding mode control surface) could provide proportional intervention — throttle before block, modify before deny. The proxy's architecture supports this: replace the binary check with a normalized safety score, map the score to discrete decisions (pass/throttle/modify/block). The audit event schema captures the decision type regardless of whether the underlying mechanism was binary or proportional.

**Solid Protocol integration point:** Worker agents currently have no cross-boundary identity. A future integration with W3C Solid Protocol would provide standards-based identity, access control, and data sharing between agents from different platforms. The runtime manifest's `cloud_dispatch_dependencies` field could extend to carry Solid WebID credentials for inter-agent coordination. The manifest schema accommodates this without structural change.

## 13. Scope Boundaries and Cross-References

- Completion contracts and self-attestation mechanics (agent registry operational design)
- Verification agent check catalog (agent registry operational design)
- WAL promotion/demotion mechanics (Spec 2)
- Context packaging pipeline (Spec 3)
- Cloud dispatch egress checks (Spec 4)
- Override handling for jobs with worker agent involvement (Spec 5, this spec Section 9.5)
- Evaluation feedback loop from worker outcomes (Evaluation Loop document)

---

*End of Spec 6 — FINAL. Part of the DRNT Interface Specification series: Spec 1 (Audit/Event Schema), Spec 2 (Capability Model), Spec 3 (Context Boundary), Spec 4 (Egress Policy Binding), Spec 5 (Override Semantics), Spec 6 (Silo Runtime Security), Spec 7 (Signal Chain Resilience).*
