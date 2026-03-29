# DRNT → NemoClaw Governance Overlay

**Version 1.0** · March 2026

Depends on: [Event Schema v2.0](DRNT_Event_Schema.md), [Capability Trust Profile v1.0](DRNT_Capability_Trust_Profile.md)

---

## 1. What This Document Is

NemoClaw (NVIDIA's open-source agent stack, announced GTC 2026) provides **execution security** — sandboxed containers, a YAML policy engine, and a privacy router. It answers the question: *what can an agent physically do?*

DRNT provides **governance** — earned trust, graduated autonomy, append-only audit, human override authority, and structural privacy enforcement. It answers the question: *what should an agent be allowed to do, and who decides?*

These are complementary layers. NemoClaw provides the cage; DRNT provides the rules for how the cage opens. This document maps DRNT's governance specifications onto NemoClaw's execution infrastructure, identifies where they align, where DRNT extends OpenShell, and where OpenShell fills gaps DRNT left to implementation.

This is not a product integration guide. NemoClaw is alpha software (March 2026). This is an architectural compatibility analysis that positions DRNT's governance specs as a concrete overlay for any sandbox-execution framework that shares OpenShell's design pattern.

---

## 2. Architectural Alignment

### 2.1 Shared Design Principles

Both architectures converge on the same structural commitments:

**Out-of-process enforcement.** OpenShell's core claim is that policy enforcement lives outside the agent process — the agent cannot override its own guardrails. DRNT's Spec 6 (Silo Runtime Security) makes the identical commitment: container configuration, Landlock, seccomp, and network namespace isolation are all out-of-process. The agent cannot modify, disable, or escalate runtime constraints from inside the sandbox.

**Default-deny posture.** OpenShell blocks everything unless explicitly allowed — filesystem paths, network endpoints, process execution. DRNT's Context Packager (Spec 3) defaults to `local_only` for all memory objects and blocks unclassifiable content. DRNT's egress gateway (Spec 4) blocks all endpoints not in the egress registry. The principle is the same: the burden of proof is on the request, not the restriction.

**Credential isolation.** OpenShell injects API keys as runtime environment variables — credentials never touch the sandbox filesystem. DRNT's Spec 3 treats `credential` as a hardcoded sensitivity class with mandatory strip action. DRNT's Spec 4 resolves `secret_ref` values at the gateway — secrets never appear in the context package or audit log.

**Immutable execution environment.** OpenShell's sandbox blueprint is digest-verified and recreated identically on each run. DRNT's sandbox blueprints (Spec 6) are locked at creation time — filesystem and process restrictions cannot change without sandbox recreation.

**Audit trail.** OpenShell logs every policy decision. DRNT logs every policy decision, every routing decision, every model response, and every human decision in a cryptographically chained append-only journal.

### 2.2 The Three-Layer Correspondence

| DRNT Layer (Spec 6) | OpenShell Component | Alignment |
|---------------------|---------------------|-----------|
| **Layer 1: Runtime Isolation** (environment-level) | **Sandbox** + **Policy Engine** | Direct correspondence. Both use Landlock, seccomp, network namespaces, dropped capabilities. OpenShell adds OPA Rego evaluation at L7; DRNT uses a simpler allow/deny proxy with request sanitizer. |
| **Layer 2: Behavioral Governance** (application-level) | *Not present in OpenShell* | **DRNT extension.** WAL state machine, capability registry, promotion/demotion rules, gate model — none of this exists in OpenShell. OpenShell's policies are static or human-hot-reloaded; DRNT's trust levels change automatically based on measured performance. |
| **Layer 3: Structural Privacy** (data-level) | **Privacy Router** | Partial correspondence. Both intercept outbound inference calls and route based on organizational policy. DRNT's Context Packager provides field-level sensitivity classification, dual independent scans (Packager + gateway), and sub-field precision stripping. OpenShell's Privacy Router uses Gretel-derived differential privacy for PII masking — a different technique for the same goal. |

---

## 3. Where DRNT Extends OpenShell

### 3.1 Graduated Trust (WAL State Machine)

OpenShell's policies are binary: allowed or denied. An agent either can or cannot perform an action. There is no concept of *earning* expanded permissions through demonstrated reliability.

DRNT's WAL state machine provides graduated trust:

- WAL-0: recommend only (human reviews everything)
- WAL-1: draft and assist (auto-retry, memory writes on accept)
- WAL-2: execute pre-approved (direct delivery, post-action review)
- WAL-3: limited autonomous (auxiliary capabilities only in v1)

**Proposed integration point:** DRNT's WAL level would determine which YAML policy set OpenShell enforces. A capability at WAL-0 operates under the most restrictive policy set. Promotion to WAL-1 would trigger a policy reload that expands specific permissions. The proposed mapping:

```
WAL level → OpenShell policy set (requires hot-reload support)

WAL-0 → drnt-wal0.yaml  (all results held, no autonomous actions)
WAL-1 → drnt-wal1.yaml  (auto-retry enabled, format_result unlocked)
WAL-2 → drnt-wal2.yaml  (direct delivery, expanded retry, auto memory write)
WAL-3 → drnt-wal3.yaml  (auxiliary only: unattended notifications, queue management)
```

OpenShell's hot-reloadable dynamic policies (network, inference) could make this feasible without sandbox recreation, assuming hot-reload remains available at that boundary. DRNT's startup reconciliation emits `wal.promoted` or `wal.demoted` events; the orchestrator would apply the corresponding policy set via `openshell policy set` or equivalent.

**What this would solve:** OpenShell gives you a cage with a fixed door. DRNT gives you a door that opens wider as the agent proves it won't escape — and slams shut the moment it tries.

### 3.2 Automatic Demotion

OpenShell's policies change when a human edits YAML. There is no automatic policy tightening in response to agent behavior.

DRNT demotes automatically:
- 3 failures in a rolling 24h window → demote one WAL level
- Strip failure caught by egress gateway → suspend `context.package`
- Human override (cancel/redirect) → conditional demotion
- Model change → reset all affected capabilities to WAL-0

Each demotion would trigger the corresponding OpenShell policy reload, assuming the runtime supports the `policy set` mechanism at that boundary. The agent's permissions contract in real time without human YAML editing.

### 3.3 Per-Capability Trust Granularity

OpenShell policies apply per sandbox (per agent instance). All actions within that sandbox operate under the same policy set.

DRNT's trust is per-capability, not per-agent. A single agent may operate at WAL-2 for `route.cloud.claude` while `context.package` runs at WAL-0. This means within one OpenShell sandbox, different actions have different permission levels — enforced by DRNT's permission checker, not by OpenShell's policy engine.

**Architecture implication:** DRNT's behavioral governance layer operates as a **control-plane service** that is logically above and partially outside the sandbox. The orchestrator, capability registry, permission checker, and log writer are control-plane components — they govern what happens inside the sandbox but are not themselves sandbox-resident code. OpenShell provides the enforcement floor (what is physically possible). DRNT provides the decisioning ceiling (what is permitted given current trust state). The enforcement floor is out-of-process and structurally unbypassable. The decisioning ceiling is in-band at the application layer — it can further restrict but never relax what the floor permits.

### 3.4 Cryptographic Audit Chain

OpenShell logs policy decisions. DRNT chains every event with SHA-256 hashes, creating a tamper-evident record where modifying any event invalidates all subsequent hashes. An independent verification script (`drnt-audit-verify`) walks the chain nightly.

OpenShell could consume DRNT's audit log as a governance data source. DRNT's `event_id` (UUIDv7) and `committed_at` timestamp provide the join keys.

### 3.5 Human Override Authority

OpenShell's human intervention model is policy editing: update a YAML file, hot-reload or recreate the sandbox. This is an infrastructure operation.

DRNT's override model is operational: cancel, redirect, modify, or escalate a specific job from watch or phone. Overrides take effect immediately, trigger worker termination (Spec 6 Section 9.5), spawn successor jobs, and conditionally adjust trust levels — all without touching YAML.

**Irreversibility boundary:** `job.dispatched` is the point where data has left the local environment. Before this event, overrides prevent egress. After this event, overrides control response handling but cannot recall data already transmitted. This boundary is stated identically in the Event Schema and Capability Trust Profile.

**Proposed integration point:** A DRNT override that demotes a capability from WAL-2 to WAL-1 would drive an OpenShell `policy set` call that loads the WAL-1 policy. The human taps "redirect" on their watch; the sandbox tightens within seconds. This requires OpenShell's hot-reload mechanism to accept programmatic policy changes from an external control plane — a capability that exists in the current alpha but whose API stability is not yet guaranteed.

### 3.6 Job-Based UX (Submit → Process → Deliver)

OpenShell operates at the agent session level — an agent runs inside a sandbox for an indefinite session. There is no concept of discrete jobs with submission, processing, and delivery phases.

DRNT's job-based model means every interaction has a clear lifecycle (`job.submitted` → terminal state), a governing capability, a declared pipeline, and a review gate determined by the WAL level. This structure enables the replay and accountability guarantees that OpenShell's session model does not provide.

---

## 4. Where OpenShell Fills DRNT Gaps

### 4.1 L7 Policy Evaluation (OPA Rego)

DRNT's Spec 6 describes a worker egress proxy with allow/deny decisions at the endpoint level. OpenShell's policy engine evaluates at four levels: binary, destination, method, and path. This means OpenShell can allow GET to a GitHub repo but block POST — granularity DRNT's spec left to implementation.

**Adoption:** Replace DRNT's simple allow/deny proxy with OpenShell's OPA-based policy engine. DRNT's `worker.egress_request` and `worker.egress_blocked` events still fire — the evaluation logic changes, not the audit schema.

### 4.2 K3s Orchestration

DRNT's Spec 6 describes Docker containers with manual composition. OpenShell runs a full K3s cluster inside a single Docker container, providing pod lifecycle management, service discovery, and rolling updates without external Kubernetes infrastructure.

For DRNT's v1 (single-user personal system), K3s inside Docker is a clean implementation path for the multi-container topology: orchestrator, log writer, egress gateway, and worker sandboxes all as pods within one K3s cluster.

### 4.3 Credential Provider Model

DRNT's Spec 4 uses `secret_ref` values resolved at the gateway. OpenShell provides a credential provider abstraction (`openshell provider create`) with auto-discovery from the shell environment and runtime injection without filesystem exposure.

**Adoption:** Map DRNT's `secret_ref` resolution to OpenShell providers. The gateway resolves `secret_ref` by calling OpenShell's provider API instead of reading a local secrets file. Credentials remain isolated from the agent and absent from the audit log.

### 4.4 Agent-Proposed Policy Changes

OpenShell has a unique feature: when an agent hits a constraint, it can propose a policy update for human approval. This closes the feedback loop between runtime enforcement and policy evolution.

DRNT's closest analogue is the Lightweight Evaluation Loop, where feedback signals drive promotion recommendations. OpenShell's agent-proposed policy updates could feed into DRNT's promotion evidence — if the agent consistently proposes reasonable policy expansions that the human approves, that's evidence supporting WAL promotion.

### 4.5 GPU Passthrough for Local Inference

DRNT's technology stack specifies an RTX 5060 Ti 16GB with CUDA acceleration for Ollama. OpenShell's experimental GPU passthrough passes host GPUs into sandboxes for local inference. This aligns directly — DRNT's local model runs inside an OpenShell sandbox with GPU access, governed by DRNT's `route.local` capability.

---

## 5. Integration Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    OpenShell Runtime                     │
│  ┌──────────┐  ┌──────────────┐  ┌───────────────────┐ │
│  │ Sandbox  │  │ Policy Engine│  │  Privacy Router   │ │
│  │(Landlock,│  │  (OPA Rego,  │  │ (inference.local, │ │
│  │ seccomp, │  │  YAML rules, │  │  credential strip,│ │
│  │ netns)   │  │  L7 filter)  │  │  PII masking)     │ │
│  └────┬─────┘  └──────┬───────┘  └────────┬──────────┘ │
│       │               │                    │            │
│  ═════╪═══════════════╪════════════════════╪════════    │
│       │    DRNT GOVERNANCE LAYER           │            │
│  ┌────┴──────────────────────────────────────────────┐  │
│  │                                                    │  │
│  │  ┌──────────────┐  ┌────────────┐  ┌───────────┐ │  │
│  │  │ Capability   │  │  Context   │  │  Egress   │ │  │
│  │  │ Registry +   │  │  Packager  │  │  Gateway  │ │  │
│  │  │ WAL State    │  │  (Spec 3)  │  │  (Spec 4) │ │  │
│  │  │ Machine      │  │            │  │           │ │  │
│  │  │ (Spec 2)     │  │ Sensitivity│  │ Route     │ │  │
│  │  │              │  │ scan, strip│  │ registry, │ │  │
│  │  │ Permission   │  │ default-   │  │ hash      │ │  │
│  │  │ check on     │  │ deny       │  │ verify,   │ │  │
│  │  │ every action │  │            │  │ rate limit│ │  │
│  │  └──────┬───────┘  └─────┬──────┘  └─────┬─────┘ │  │
│  │         │                │                │       │  │
│  │  ┌──────┴────────────────┴────────────────┴────┐  │  │
│  │  │         Append-Only Audit Log               │  │  │
│  │  │  (SHA-256 chained, JSONL, Event Schema 2.0) │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  │                                                    │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
└──────────────────────────────────────────────────────────┘
         │                              │
    ┌────┴────┐                   ┌─────┴──────┐
    │  Watch  │                   │   Phone    │
    │(Pip-Boy)│                   │(Primary UI)│
    └─────────┘                   └────────────┘
```

**Key relationships:**
- OpenShell's Sandbox is the enforcement floor. DRNT's control-plane services (orchestrator, capability registry, permission checker, log writer) operate above it.
- OpenShell's Policy Engine enforces the WAL-level policy set selected by DRNT's control plane.
- OpenShell's Privacy Router and DRNT's Context Packager are parallel privacy controls — one for worker execution egress, one for cloud dispatch egress.
- DRNT's audit log captures everything — including OpenShell policy decisions, which surface as `worker.egress_request` and `worker.egress_blocked` events (canonically defined in Event Schema v2.0 Section 3.8).

---

## 6. Event Schema Mapping

DRNT's Event Schema v2.0 covers all governance and worker execution events. The worker events (`worker.egress_request`, `worker.egress_blocked`, `worker.sandbox_violation`) are canonically defined in Event Schema Section 3.8, consolidated from Spec 6. The mapping to OpenShell operations:

| OpenShell Operation | DRNT Event(s) | Notes |
|--------------------|---------------|-------|
| Sandbox creation | `system.startup` | DRNT logs component inventory including sandbox blueprint version |
| Policy evaluation: allow | `worker.egress_request` | Logged with endpoint, binary, and outcome |
| Policy evaluation: deny | `worker.egress_blocked` | Includes `block_reason` and `skill_id` if attributable |
| Policy hot-reload (WAL change) | `wal.promoted` or `wal.demoted` | WAL event is the trigger; policy reload would be the side effect, assuming `openshell policy set` or equivalent is callable from DRNT's control plane |
| Privacy Router: route to local | `job.dispatched` with `route_id` pointing to local route | DRNT's routing classification already determines local vs. cloud |
| Privacy Router: route to cloud | `context.packaged` → `job.dispatched` with cloud `route_id` | Full Context Packager pipeline runs before cloud dispatch |
| Privacy Router: PII strip | `context.strip_detail` | DRNT's per-field strip detail is more granular than OpenShell's differential privacy approach |
| Credential injection | Not logged (by design) | Credentials never appear in DRNT's audit log. `secret_ref` resolution is at the gateway. |
| Sandbox violation | `worker.sandbox_violation` | Sentinel `"worker_sandbox"` — excluded from WAL counters |
| Agent-proposed policy change | `human.feedback` (level 2/3) | Maps to DRNT's feedback loop; feeds promotion evidence |

---

## 7. What This Means for NemoClaw Adopters

If you are evaluating NemoClaw/OpenShell for agent deployment and want governance beyond YAML policy files, DRNT's specs provide:

**A trust lifecycle.** OpenShell tells you how to lock down an agent. DRNT tells you how to gradually unlock it as it proves reliable, and how to lock it back down when it doesn't. The WAL state machine, promotion criteria, and automatic demotion rules are directly implementable against OpenShell's hot-reloadable policy system.

**Per-capability granularity.** OpenShell policies apply per sandbox. DRNT's capability model lets you assign different trust levels to different actions within the same sandbox. The permission checker runs inside the sandbox; the sandbox provides the enforcement floor.

**An audit chain.** OpenShell logs decisions. DRNT chains them cryptographically and ties every decision to a specific job, capability, WAL level, and human review outcome. The append-only JSONL format is queryable with `jq` today and indexable with SQLite tomorrow.

**A human override protocol.** OpenShell's human intervention is YAML editing. DRNT's is a tap on a watch. The override semantics (Spec 5) define cancel, redirect, modify, and escalate with conditional demotion logic, successor job spawning, and post-delivery revocation — all operational actions, not infrastructure changes.

**A privacy pipeline with dual scans.** OpenShell's Privacy Router is a single-pass system. DRNT's Context Packager (primary scan) plus egress gateway (secondary scan) provides defense in depth — if the Packager misses a credential, the gateway catches it, and the `context.package` capability is suspended.

---

## 8. What This Means for DRNT

NemoClaw/OpenShell validates the architectural pattern DRNT specified independently. The convergence on out-of-process enforcement, Landlock/seccomp isolation, default-deny networking, and credential isolation is not coincidental — it reflects the constraints any serious agent security architecture must satisfy.

Concretely, OpenShell provides implementation components DRNT can adopt:

- **OPA Rego policy engine** replaces DRNT's simpler allow/deny proxy
- **K3s-in-Docker** provides the container orchestration DRNT's multi-container topology needs
- **Credential provider model** replaces DRNT's `secret_ref` file-based resolution
- **GPU passthrough** enables DRNT's local Ollama inference inside sandboxed containers
- **YAML policy hot-reload** provides the mechanism for WAL-level policy transitions

DRNT adds the governance intelligence that OpenShell explicitly does not provide: earned trust, automatic demotion, per-capability permissions, job-based accountability, and human override authority from a wrist-mounted interface.

---

## 9. Scope Boundaries

This overlay covers the architectural correspondence between DRNT (Specs 1–6) and NemoClaw/OpenShell as documented at GTC 2026 launch. It does not cover:

- NemoClaw's Nemotron model integration (DRNT is model-agnostic)
- OpenShell's agent-first development workflow (DRNT is not a development tool)
- NemoClaw's ClawHub skill marketplace (DRNT's v1 permits only self-authored and operator-verified skills)
- Performance benchmarks (NemoClaw has not published any as of March 2026)
- Multi-tenant deployment (both DRNT and OpenShell are single-user/single-player in v1)

---

## 10. The Governance Gap

NemoClaw's launch at GTC 2026 confirms that the industry has converged on execution security: sandboxes, policy engines, privacy routers. Many current agent frameworks primarily emphasize execution security — controlling what an agent can physically access, reach, and execute.

What is largely absent from these frameworks is dynamic trust governance: the trust lifecycle, the earned autonomy model, the job-based accountability chain, the human override protocol. Execution security gives you a cage. It does not give you a systematic way to decide when to widen the cage, or a mechanism to narrow it automatically when things go wrong.

That governance layer is what DRNT's specs define. It is framework-agnostic. It composes with any sandbox-execution environment that provides out-of-process enforcement and policy hot-reload. NemoClaw is one such environment. It is not the only one.

The three artifacts in this series — [Event Schema](DRNT_Event_Schema.md), [Capability Trust Profile](DRNT_Capability_Trust_Profile.md), and this overlay — provide a concrete governance vocabulary, trust lifecycle, and integration architecture for any team that has solved the cage problem and now needs to solve the trust problem.

---

*End of DRNT → NemoClaw Governance Overlay v1.0. Complete governance artifact series: Event Schema (Spec 1 consolidation), Capability Trust Profile (Spec 2 consolidation), Governance Overlay (framework integration).*
