[the-case-for-the-local-orchestrator.md](https://github.com/user-attachments/files/26159036/the-case-for-the-local-orchestrator.md)
# The Case for the Local Orchestrator

*A Reference Architecture for Local-First AI Agent Orchestration*

**v5.2 — Post-Adversarial Edition — March 2026**

*This document is the public reference for the local-first AI orchestration architecture. v5.0 represents a significant reframe of the core principle based on convergent findings from four independent adversarial reviews (Claude, ChatGPT, Gemini, Copilot) and operational analysis. The primary change: the core principle shifts from “route, don’t reason” to “reason locally about retrieval and packaging; reason in the cloud about everything else.” This resolves a structural tension identified independently by all reviewers and aligns the document’s framing with what the architecture actually does. All other components are confirmed and refined. v5.1 adds a general principle on evaluation scaling (Section 8.3) and references the DRNT Lightweight Evaluation Loop as the personal-scale implementation of quality feedback. v5.1.1 updates Section 8.3 to correctly characterize the Lightweight Evaluation Loop as analogous in purpose to the formal harness rather than functionally equivalent, reflecting the lower diagnostic precision of behaviorally derived signals. v5.2 adds the Execution Safety Layer (Section 5) addressing the structural gap between decision governance and consequence governance. Adds adversarial input handling to the Context Packager (Section 3.6), formal failure domain classification (Section 2.5), L1 Router explicit responsibilities (Section 2.4), WAL temporal dynamics and failure-type weighting (Sections 4.3–4.4), quantitative metrics roadmap (Section 8.4), and terminology table. Based on structured redline review of the v5.1.1 repository.*


## Terminology

| Term | Definition |
| --- | --- |
| **L1 Router** | The local model’s routing function. Classifies intent, scopes context, assesses risk, and selects the target model endpoint. Operates within the recoverable-error boundary. |
| **L2 Reconciliation Layer** | Future component. Compares outputs from multiple cloud models, identifies claim-level discrepancies, scores agreement confidence, and surfaces conflicts for human review. Not implemented in v1. |
| **Context Packager** | Structural privacy gate and prompt engineer. All outbound data passes through it. Retrieves relevant context, applies redaction rules, assembles and frames the cloud payload. Default posture is deny. |
| **Execution Safety Layer** | Governs what happens when a decision becomes an action. Network isolation, tool sandboxing, connector trust levels, and WAL-gated execution. Added in v5.2. |
| **WAL (Workflow Autonomy Level)** | Graduated trust model. Four levels (0–3) governing system autonomy per capability. WAL-0: recommend only. WAL-1: act and notify. WAL-2: execute pre-approved classes. WAL-3: autonomous with full audit. Trust earned through demonstrated reliability; anomalies trigger demotion. |
| **Recoverable error** | An error whose consequences are limited to extra round trips, higher token cost, or degraded output quality. No data loss, no privacy breach, no irreversible action. The local model’s reasoning domain. |
| **Consequential error** | An error whose consequences include privacy breach, wrong decisions based on trusted output, irreversible actions, or silent downstream corruption. The cloud model’s domain or the human’s domain. |
| **Gray zone** | A decision where the error class is ambiguous. Default to conservative action (escalate, include more context, route to cloud). Log with gray-zone flag for future rule creation. |
| **Append-only audit log** | Cryptographically hash-chained journal recording every routing decision, packaging decision, model response, and human decision. Agent cannot modify or delete entries. Non-optional. |

# 1. The Problem

Current AI agent frameworks share default assumptions that produce predictable failure modes. Most route every request to a cloud model with full context, creating privacy exposure as a feature rather than a bug. Most offer binary trust: fully autonomous or fully manual, with no mechanism for earning autonomy through demonstrated reliability. Most treat memory as an append-only dump with no provenance, no lifecycle governance, and no distinction between high-confidence facts and low-confidence inferences.

These defaults are expensive, unsafe, and unnecessary. A user asking for a document they already have locally should not trigger a cloud API call. A user asking a frontier model to analyze a contract should not have to send their entire memory store alongside the request. A system that works well for a week should not have the same permissions as one that has worked well for six months.

The local orchestrator pattern addresses these defaults by placing a local model between the user and the cloud. The local model holds personal context, preferences, and memory. It decides what stays local, what goes to the cloud, and how to package what goes. Cloud models handle the complex work. The user retains decision authority.

# 2. Core Principle: Know What You Have, Package What You Need

*Reframed in v5.0 based on convergent findings from four independent adversarial reviews.*

Previous versions of this document used the principle *“route, don’t reason”* to describe the local model’s role. That framing was useful scaffolding but created a structural tension: the local model obviously reasons. Intent classification is reasoning. Context selection is reasoning. Deciding whether a request can be answered locally or needs cloud escalation is reasoning. Four independent adversarial reviews identified this tension from different angles, and the architecture’s own operational test (“if two runs can differ, it’s reasoning”) confirmed it. The local model reasons every time it processes a request.

The v5.0 reframe makes the real principle explicit: the local model reasons about two things and only two things.

*Do I already have this? And if not, what does the cloud need to see?*

The first question is retrieval. The local model checks whether the user’s request can be satisfied from local context: stored documents, prior results, personal data, cached outputs. If it can, the request never leaves the local perimeter. No cloud call, no token spend, no privacy exposure.

The second question is packaging. When local context is insufficient, the local model assembles the tightest possible payload for a cloud model: the right context, stripped of what’s sensitive, framed to get a good answer on the first pass. This is the architecture’s primary value function. A well-packaged request reduces token spend, improves cloud model output quality, minimizes retries, and limits the surface area for data exposure — all simultaneously.

## 2.1 The Boundary Definition

The boundary is not between reasoning and not-reasoning. It is between reasoning where errors are recoverable and reasoning where errors are consequential. The local model gets a reasoning budget proportional to how cheaply its mistakes can be corrected.

| **Error Class** | **Examples** | **Blast Radius** |
| --- | --- | --- |
| **Recoverable** | Misrouted request (sent to Claude when Gemini would have been better). Suboptimal context selection (included extra context, missed a relevant document). Unnecessary cloud call for a locally answerable request. | Extra round trip. Higher token cost. Slightly degraded output quality. User retries or the system self-corrects. No data loss, no privacy breach, no irreversible action. |
| **Consequential** | Sensitive data included in a cloud payload. Incorrect analysis presented as final to the user. Autonomous action taken on flawed judgment. Two semantically different claims normalized into identical form. | Privacy breach. Wrong decision based on trusted output. Irreversible action. Silent downstream corruption. Regulatory exposure. |
| **Gray Zone** | Context selection for ambiguous task specs. Output summarization for constrained displays (watch, notification). Edge-case request classification where task type is unclear. | Logged with gray-zone flag. Default to conservative action (escalate to human, include more context rather than less, route to cloud rather than answer locally). The gray-zone log is the primary instrument for replacing inference with rules over time. |

Recoverable errors are the local model’s domain. Consequential errors are the cloud’s domain or the human’s domain. Gray-zone decisions default to the safer option and generate data for future rule creation.

## 2.2 The Stealth Reasoning Problem

The most important finding from the L2 adversarial review is that tasks which appear structural can cross into consequential-error territory without the system registering the violation.

Converting a natural language claim into canonical subject-verb-object form appears mechanical. In practice it requires determining the implicit subject, resolving ambiguous scope qualifiers, and mapping hedging language onto discrete confidence categories. A 7–13B model performs this inconsistently across runs. Two semantically identical inputs normalize into different forms. Downstream alignment fails silently. The system reports agreement where disagreement exists.

This is a consequential error masquerading as a recoverable one. The v5.0 boundary definition handles it directly: the question is not whether the task looks simple, but what happens when the local model gets it wrong. If the error propagates silently and corrupts downstream results, the task belongs to a cloud model regardless of how structural it appears.

*The practical test: if the local model gets this wrong, does a human see the mistake before anything irreversible happens? If yes, the task is within the local model’s reasoning budget. If no, escalate.*

## 2.3 What the Local Model Actually Does Well

A 7–13B quantized model running on consumer GPU hardware is not a lesser version of a frontier model. It is a different tool with different strengths. Its advantages in this architecture are speed, locality, and context awareness:

**Speed. **The local model responds without network latency. For retrieval decisions and simple classifications, sub-second response times are achievable. This makes the system feel responsive even when cloud calls take several seconds.

**Locality. **The local model holds the user’s complete context: documents, preferences, history, prior results. It can search and retrieve without sending anything to the cloud. For requests that can be answered from stored context, the entire interaction stays local.

**Context awareness. **The local model knows what the user has, what they’ve asked before, and what their preferences are. This makes it an effective prompt engineer: it can frame cloud requests with relevant context, exclude irrelevant context, and structure prompts to get better answers with fewer tokens.

These strengths are maximized when the local model operates within the recoverable-error boundary. They degrade when the model is asked to do work where its limitations produce consequential errors.


## 2.4 L1 Router Responsibilities

*Added in v5.2.*

The L1 Router performs four operations, each within the recoverable-error boundary:

1. **Intent classification.** Parse the user’s request into a structured task specification: task type, complexity estimate, required context classes. Errors here result in suboptimal routing, not in wrong answers or data exposure.

2. **Context scoping.** Determine which context classes the task requires and pass the specification to the Context Packager. The Router identifies what’s needed; the Packager retrieves and redacts it.

3. **Risk classification.** Classify the request as recoverable, consequential, or gray-zone using the boundary definition in Section 2.1. This determines whether local resolution is attempted, which WAL level governs the response, and whether the request is flagged for human review.

4. **Routing decision.** Select the target model endpoint based on task type, model strengths, and operational constraints (availability, cost, rate limits). Log the routing rationale and context inclusion/exclusion decisions for bias detection and routing optimization.

**Bias mitigation note:** Routing decisions can bias outcomes without violating any constraint. Consistently selecting a weaker model, under-contextualizing complex tasks, or defaulting to local resolution when cloud escalation would produce better results are all within the Router’s authority but degrade system value. Logging routing rationale and context inclusion/exclusion decisions enables detection of these patterns through the metrics defined in Section 8.4.

## 2.5 Failure Domain Classification

*Added in v5.2.*

The boundary definition requires a concrete taxonomy of failure types, the layer responsible for preventing each, and the recovery path when prevention fails.

| **Failure Type** | **Layer Responsible** | **Error Class** | **Recovery Path** |
| --- | --- | --- | --- |
| Intent misclassification | L1 Router | Recoverable | Retry with reclassification. Audit log captures original routing for pattern analysis. |
| Context leakage | Context Packager | Consequential | Block transmission. Redact payload. Emit context.strip_detail event. Log for packager rule refinement. |
| Model hallucination | L2 Reconciliation (future) / Human (v1) | Consequential | In v1: human review at WAL-0 catches before action. With L2: reconcile against competing model output, surface discrepancy, escalate to human. |
| Suboptimal model selection | L1 Router | Recoverable | Lower output quality, higher token cost. Logged for routing optimization. No data loss or privacy breach. |
| Over-redaction | Context Packager | Recoverable | Cloud model receives insufficient context, output quality degrades. User resubmits. Packager rule adjusted. |
| Tool/connector compromise | Execution Safety Layer | Consequential | Sandboxed execution limits blast radius. Network isolation prevents lateral movement. See Section 5. |
| Audit log tampering | Log infrastructure | Consequential | Cryptographic hash chain detects modification. Agent has no write access to log stream. Tamper evidence is structural. |

The tool/connector compromise row is the gap this version fills. Previous versions defined what gets decided and what gets packaged but not what happens at the point of execution. Section 5 addresses this directly.

# 3. The Context Packager

Context curation is the architecture’s highest-value function. It is simultaneously the primary cost optimization mechanism and the primary privacy mechanism. Cloud models never see the complete memory layer. Requests arrive at the cloud pre-scoped, pre-cleaned, and well-framed.

## 3.1 The Pipeline

The Context Packager is a distinct architectural component, separated from the routing model. It executes four operations in sequence, each producing an audit log entry:

**1. Receive task specification. **The router passes a structured task specification: task type, required context classes, target model, and prompt framing template.

**2. Retrieve relevant context. **The Packager pulls from the memory store using the task specification’s context class tags, applying the memory selection policy (Section 3.3). It preferentially selects high-confidence, high-relevance objects.

**3. Apply redaction rules. **Scan structured fields and unstructured content for sensitive data. Strip or mask per the configured sensitivity taxonomy. Redaction is rule-based: pattern matching, keyword lists, tag matching. The Packager does not make semantic judgments about what “seems” sensitive. If content does not match any rule and the Packager cannot classify it, the content does not leave the perimeter. Default is deny, not allow.

**4. Assemble and frame the payload. **Construct the outbound request within the target model’s token budget. Apply the prompt template for the task type. Re-scan the assembled payload for inadvertent sensitive content inclusion before transmission.

## 3.2 Prompt Framing as a First-Class Capability

*New in v5.0.*

The Context Packager is not just a privacy gate. It is the system’s prompt engineer. Each task type has an associated prompt template that structures the cloud request for optimal output quality. Templates specify: what context to include, how to frame the task, what output format to request, and what constraints to communicate.

A well-framed request produces better output, fewer retries, and lower total token spend than dumping raw context into a frontier model’s context window. This is where the cost story lives. The local model’s context awareness — knowing what the user has, what they’ve asked before, what matters and what doesn’t — enables packaging that a stateless cloud-first architecture cannot match.

Template versioning is governed by the evaluation harness. When a template change degrades output quality (measured by human override rate, retry frequency, or downstream task success), the change is rolled back. Templates that improve quality are promoted. This makes prompt engineering a measurable, iterative practice rather than an ad hoc art.

## 3.3 Sensitivity Taxonomy and Redaction

The specific categories of sensitive data vary by deployment context. Healthcare deployments define categories from HIPAA’s 18 Safe Harbor identifiers. Legal deployments define categories from privilege and confidentiality obligations. Federal deployments define categories from CUI/SSI classification. Personal deployments define categories from the user’s own privacy preferences.

The method is consistent: define categories, assign detection rules, define the default action per category, and define the escalation path when the Packager encounters content that matches no rule. Default escalation is surface to user at WAL-0. The Packager never makes semantic judgments about content sensitivity. If the rule does not match, the content does not leave the perimeter.

### 3.3.1 The Semantic Ambiguity Edge Case

*Explicitly named in v5.0 based on adversarial review findings.*

Adversarial reviewers correctly identified that some redaction decisions appear to require semantic judgment. Determining whether a string of numbers is a benign product code or a sensitive medical identifier requires understanding context, not just matching patterns. This is a consequential-error task by the v5.0 boundary definition.

The architecture handles this through default-deny. If the Packager’s deterministic rules cannot classify the content, the content stays local. This means some useful context will occasionally be withheld from cloud requests, reducing output quality. That is the correct tradeoff: false negatives (failing to redact sensitive data) have irreversible consequences; false positives (over-redacting) have recoverable consequences. The system is deliberately calibrated to fail conservatively.

## 3.4 Memory Selection Policy

**Confidence scoring. **Each memory object carries a confidence score based on source type. User-stated facts score highest. Inferences from documents score medium. Summaries of summaries score lowest. The Packager preferentially includes high-confidence objects in outbound payloads. This also provides defense against memory poisoning: information injected through external sources inherits lower trust.

**Shareability classification. **Memory objects are classified as either eligible for cloud transmission or local-only. Default is local-only. In regulated environments, classification follows the applicable data governance policy. In personal deployments, the user explicitly tags what can leave the perimeter.

## 3.5 Redaction vs. Tokenization

When the Packager strips sensitive data, it can either remove it entirely (simpler, more private, reduces payload utility) or replace it with reference tokens that preserve payload structure while masking identity. Token substitution requires a secure local mapping vault and careful lifecycle management. Early deployments should start with simple stripping and add tokenization as a maturity enhancement.


## 3.6 Adversarial Input Handling

*Added in v5.2 based on redline review findings.*

The Context Packager’s threat model in previous versions assumed benign input. User-supplied content, connector-retrieved documents, and memory objects were treated as potentially sensitive but not potentially hostile. This assumption is insufficient.

**Prompt injection embedded in user input.** A user request containing instructions intended for the cloud model’s system prompt layer rather than its user message layer. The local model’s intent classification may not detect the injection because the injected content is syntactically valid natural language. The Context Packager must treat instruction-like content in user input as data, not as control flow. Outbound payloads use structured message formats that separate system instructions from user-supplied content. The Packager never interpolates raw user text into system prompt positions.

**Malicious structured data from connectors.** A connector retrieving external content (email, documents, web results) may return content that contains embedded instructions, hidden text, or adversarial formatting designed to influence the cloud model’s behavior. The Packager applies the same data/instruction separation to connector-retrieved content: all external content is placed in user-message or context positions, never in instruction positions. Schema validation rejects connector output that does not match expected structure.

**Memory poisoning through inferred context.** Memory objects derived from prior model outputs inherit the biases and potential errors of those outputs. If a prior cloud response was itself influenced by adversarial input, the resulting memory object carries that influence forward. The Packager’s confidence scoring (Section 3.4) provides partial defense: inferred content scores lower than user-stated facts. The additional defense is that memory objects used as context in cloud requests are never placed in instruction positions, regardless of their source.

The general principle: the Context Packager enforces a hard separation between instructions and data in every outbound payload. Content from any source other than the system’s own prompt templates is treated as data. This is a structural constraint, not a classification judgment.

# 4. Workflow Autonomy Levels

Every new capability starts at WAL-0: human reviews every output before any action. Trust is earned through demonstrated reliability, not granted by default. Anomalies trigger automatic demotion.

| **Level** | **Operational Meaning** | **Safety Contract** | **Governance Requirement** |
| --- | --- | --- | --- |
| **WAL-0 Review** | AI retrieves and presents. Human reviews and decides. | Zero autonomous actions. Every output reviewed. | Default for all new capabilities. |
| **WAL-1 Notify** | AI acts and notifies before execution. Human can cancel. | Timed auto-execute with cancellation window. | Requires documented promotion criteria. |
| **WAL-2 Execute** | AI executes pre-approved routine action classes. | Bounded by parameter limits. Post-audit required. | Written authorization from system owner. |
| **WAL-3 Autonomous** | AI executes independently with full audit logging. | Earned through sustained zero-incident reliability. | Quarterly audit mandatory. |

## 4.1 Promotion Criteria

Promotion to a higher WAL level requires measurable thresholds: minimum transaction count, minimum approval rate, zero sensitive data mishandling, and a documented workflow definition. Specific thresholds are set per deployment context. Multi-factor validation is required — consecutive successes alone do not justify promotion, to prevent conditioning attacks. A burn-in period is mandatory for any new capability: all outputs are escalated to human review for the first N runs before the trust scoring is considered meaningful.

Promotion is per-capability, not per-agent. An agent may operate at WAL-2 for calendar management while remaining at WAL-0 for financial operations. This limits the blast radius of any single capability’s promotion.

## 4.2 Demotion Triggers

Any output causing real-world harm: immediate demotion to WAL-0. Error rate exceeding threshold: demote one level. Sensitive data mishandling: immediate WAL-0. Underlying model changes (version, provider, configuration): demote to WAL-0 and restart the promotion clock. Demotion is not failure — it is the governance system working correctly.


## 4.3 Temporal Dynamics

*Added in v5.2.*

Trust is not static. A capability that operated flawlessly six months ago but has not been exercised since provides weaker evidence than one that operated flawlessly last week. The trust model accounts for temporal decay, recency weighting, and recovery pathways.

**Trust decay.** A capability’s trust score decays over periods of inactivity. The decay rate is configurable per deployment. The mechanism is not automatic demotion but reduction in the trust score used for promotion eligibility. A capability at WAL-2 that has not been exercised for the configured decay window does not automatically demote, but its accumulated evidence weakens, making it ineligible for WAL-3 promotion until fresh evidence accumulates. If the decay window is long enough (also configurable), automatic demotion does occur.

**Recency weighting.** Recent performance counts more than historical performance in trust calculations. A capability with 500 successful executions six months ago and three failures last week should not retain high trust based on volume. The trust calculation weights recent observations more heavily than distant ones. The weighting curve is configurable; the requirement that recency matters is not.

**Recovery pathways.** Demotion is not permanent and not punitive — it is the governance system working correctly. A demoted capability earns its way back through the same promotion criteria that governed its original advancement. The recovery path is identical to the initial promotion path: demonstrate reliability at the current level, accumulate sufficient evidence, meet the promotion threshold. There is no fast-track back to a previous level. The clock resets.

## 4.4 Failure-Type Weighting

*Added in v5.2.*

Not all failures carry equal weight in trust calculations. A routing error that sends a request to a suboptimal model is categorically different from a policy violation that exposes sensitive data. The trust model distinguishes failure types by their consequence:

| **Failure Type** | **Trust Impact** | **Trust Consequence** |
| --- | --- | --- |
| Routing error | Low | Logged. Contributes to pattern analysis. Accumulated routing errors affect promotion eligibility but do not trigger demotion unless frequency exceeds threshold. |
| Quality failure | Medium | Output rejected by human or downstream consumer. Affects trust score meaningfully. Repeated quality failures within a window trigger one-level demotion. |
| Policy violation | High | Sensitive data mishandling, constraint breach, unauthorized action class. Immediate one-level demotion. Requires explicit re-authorization to begin recovery. |
| External damage | Critical | Real-world harm, irreversible action on flawed judgment, data breach. Immediate demotion to WAL-0. Full incident review required before any re-promotion. |

The specific thresholds (how many routing errors before promotion is affected, the time window for quality failure accumulation) are set per deployment context. The principle — that failure type determines trust impact — is universal.

# 5. Execution Safety Layer

*Added in v5.2. Addresses the structural gap identified by redline review: the architecture defined decision governance and packaging governance but not execution governance.*

Previous versions of this document specified what the system decides (L1 routing), how it packages (Context Packager), how it earns trust (WAL), and how it logs (audit journal). What was missing is what happens at the boundary where a decision becomes an action — where a cloud model’s response triggers a tool call, an API request, a file write, or any operation with real-world side effects. The Execution Safety Layer fills this gap.

## 5.1 Execution Boundaries

Any operation that crosses the local perimeter or modifies state outside the orchestrator’s memory requires explicit authorization. The execution boundary is defined by three constraints:

**Network isolation.** The orchestrator process cannot reach the internet directly. Outbound traffic is routed exclusively through a gateway container that enforces the egress policy (endpoint allowlist, token limits, cost caps, rate limits, TLS minimums). This is enforced at the container network topology level, not by firewall rules that can be misconfigured. If the gateway is down, outbound traffic stops. There is no fallback path.

**Tool sandboxing.** Tools that execute operations with side effects (file writes, API calls, system commands) run in isolated environments with constrained permissions. A tool cannot escalate its own permissions, access the orchestrator’s memory store directly, or communicate with other tools except through the orchestrator’s job pipeline. Tool outputs are validated against expected schemas before the orchestrator acts on them.

**WAL-gated execution.** Every tool invocation inherits the WAL level of the capability that triggered it. A capability operating at WAL-0 cannot trigger autonomous tool execution regardless of what the cloud model’s response recommends. The human reviews the proposed action and explicitly approves or rejects. WAL-2 and WAL-3 execution is bounded by pre-approved action classes with parameter limits. Any tool invocation outside the approved class triggers automatic demotion.

## 5.2 Connector Trust Levels

Connectors — integrations that retrieve external data or execute external actions — operate under a trust classification that determines what they can access and what oversight they require:

| **Trust Level** | **Permissions** | **Examples** |
| --- | --- | --- |
| **Read-only** | Retrieve data. No state modification. Output treated as untrusted context subject to Packager rules. | Calendar read, email fetch, document retrieval, weather/news APIs. |
| **Write-guarded** | Can modify external state but every write operation requires WAL-gated human approval. Outputs logged with full parameter capture. | Email send, calendar event creation, file saves to external storage. |
| **Privileged** | System-level operations. Restricted to WAL-2 minimum. Quarterly audit mandatory. Network access controlled by dedicated egress route. | Financial API calls, infrastructure management, external system configuration. |

New connectors default to read-only. Promotion to write-guarded requires the same demonstration of reliability that WAL promotion requires. Privileged connectors require explicit written authorization from the system owner.

## 5.3 Threat Model

The execution layer must defend against threats that the routing and packaging layers cannot address:

**Malicious connector.** A connector that returns crafted data designed to trigger harmful tool invocations downstream. Defense: connector output schema validation, WAL-gated execution preventing autonomous action on untrusted connector data, and sandboxed tool execution limiting blast radius.

**Compromised API endpoint.** A cloud provider endpoint that returns manipulated responses (whether through compromise or through adversarial prompting of the cloud model itself). Defense: the response is data, not instructions. The orchestrator’s response handler parses expected fields; it does not execute arbitrary content from cloud responses. WAL governance ensures human review at low trust levels.

**Tool supply-chain attack.** A tool dependency that introduces malicious behavior through an update. Defense: tool environments are sandboxed with pinned dependency versions. Updates require explicit operator action and trigger WAL reset to WAL-0 for the affected capability.

**Sandbox escape.** A tool that exploits a vulnerability to break out of its isolated environment. Defense: defense-in-depth. Network isolation at the container topology level means even a sandbox escape does not grant internet access. The orchestrator process runs with minimum viable permissions. The audit log runs on a separate write-only channel that the orchestrator cannot modify.

## 5.4 Relationship to DRNT Implementation

The DRNT personal gateway implements this pattern through Docker network topology (drnt-internal bridge with no external access for the orchestrator), a dedicated gateway container that is the sole outbound path, and a ten-step egress check sequence covering route validation, capability binding, token/cost limits, payload hash verification, and sensitivity scanning. See DRNT Spec 4 (Egress Policy Binding) for the concrete implementation. Other deployments implement the same pattern using whatever isolation and enforcement mechanisms are appropriate to their environment.

# 6. Audit Log Design

Every routing decision, packaging decision, model request and response, and user decision is logged in an append-only, cryptographically chained journal. The agent cannot modify or delete its own logs. Tamper evidence is structural, not procedural: hash chaining ensures that any modification to a prior entry is detectable on the next read.

## 6.1 Two-Stream Architecture

The log uses two separate streams to resolve the tension between replayability and privacy:

**Routing metadata stream: **Event type, timestamp, job ID, routing decision, target model, context classes requested, redaction actions taken, packager rule version, gray-zone flags. Enables complete replay of routing decisions without containing sensitive content. Sufficient for WAL governance, anomaly detection, and accountability.

**Content stream (optional, encrypted): **Pre-redaction and post-redaction payloads for debugging. Encrypted at rest. Separate retention and access controls. Enabled only when the operator explicitly opts in.

### 6.1.1 Metadata Inference Risk

*Acknowledged in v5.0 based on adversarial review findings.*

The metadata stream contains no raw sensitive content, but metadata fields can carry inferential risk. If the stream logs that a request used context class “HIPAA_Safe_Harbor” and triggered redaction rule “Oncology_Taxonomy,” an observer can infer health-related information without decrypting the content stream. Sensitive deployments should generalize metadata labels where specificity is not required for routing governance. The metadata stream should receive the same access controls as any other operational telemetry containing inferential risk.

## 6.2 Storage Lifecycle

*Explicitly addressed in v5.0 based on adversarial review findings.*

An append-only log with cryptographic chaining faces a practical constraint: storage is finite. The reference architecture does not prescribe a specific rotation strategy, but implementers must address storage monitoring, capacity alerting, and lifecycle policy. Options include periodic checkpointing with cryptographic rollup to external storage, tiered retention (metadata stream retained longer than content stream), and capacity-triggered archival with chain continuity verification. The system must not silently stop logging when storage is exhausted; that failure mode must be surfaced to the operator and must trigger a halt or degraded-mode transition, not silent continuation without audit coverage.

# 7. Multi-Model Dispatch

The architecture supports routing different task types to different cloud models based on their measured strengths. The model broker pattern — dispatching to Claude for reasoning, ChatGPT for structured output, Gemini for synthesis, or any other configuration the operator defines — is a first-class capability of the orchestrator.

## 7.1 V1 Default: Single-Endpoint Dispatch

The default behavior in V1 is single-model dispatch per request: the orchestrator selects one endpoint based on task classification and routes the request there. This is the correct starting point for three reasons:

**Privacy surface area. **Each additional cloud endpoint that receives context is an additional exposure surface. Single-endpoint dispatch minimizes the number of external systems that see user data per request. Cross-domain analysis during clinical environment research found that parallel dispatch to multiple endpoints increased sensitive data exposure significantly compared to single-endpoint dispatch, due to inter-model context sharing and the compounding effect of multiple redaction surfaces.

**Reconciliation dependency. **Multi-model outputs require reconciliation logic to be useful. Presenting two conflicting answers without a framework for resolving the conflict increases cognitive load without increasing decision quality.

**Implementation sequencing. **The reconciliation layer (the L2 Discrepancy Engine) has been specified and adversarially reviewed but is not yet implemented. Enabling parallel dispatch before the reconciliation layer is ready produces outputs the system cannot responsibly present.

## 7.2 Multi-Model Dispatch as a Future Layer

When the reconciliation layer is operational, multi-model parallel dispatch becomes available as an opt-in capability. The user can request competing answers on high-stakes requests. The system dispatches to multiple endpoints, runs the reconciliation pipeline on the results, and presents a structured comparison with discrepancies flagged and a risk score attached. The human decides which answer to accept, reject, or synthesize.

This is the correct sequencing: single-endpoint reliability first, reconciliation infrastructure second, multi-model dispatch as the layer that depends on both.

# 8. Validation Methodology

This architecture has been stress-tested through multi-model adversarial review across five implementation domains: a federal agency proposal (DOT), a clinical environment submission (Mayo Clinic CAOF), a six-document SMB framework, a personal-scale gateway (DRNT), and the L2 Discrepancy Engine Specification itself.

Adversarial reviews were conducted independently by Claude (Anthropic), Gemini (Google), ChatGPT (OpenAI), and Copilot (Microsoft). Findings from each review were incorporated into successive document versions, with a cross-domain reconciliation pass tracing every structural concern across all implementations to distinguish documentation debt from genuine architectural gaps.

## 8.1 What the Review Process Produced

**The stealth reasoning problem is real and pervasive. **All four independent reviewers independently identified that claim normalization violated the route-don’t-reason principle — despite the spec authors having labeled it a structural task. This convergent finding drove the v5.0 reframe of the core principle from a binary (route vs. reason) to a spectrum (recoverable-error reasoning vs. consequential-error reasoning).

**Model divergence is informative. **Different models reviewing the same document produced meaningfully different critiques. Gemini produced theoretically ambitious but operationally miscalibrated reviews. ChatGPT produced the most structurally useful critiques with actionable experimental designs. Claude produced the most empirically grounded analysis. Copilot produced competent but shallow coverage. These differences are themselves a data point about the architecture’s core premise: different models have different failure modes, and those failure modes are visible in the output.

**The boundary definition needed sharpening, not the components. **The foundational architecture — the Context Packager, WAL governance, append-only audit log, and the core separation of local and cloud concerns — survived every adversarial review intact. What required revision was how the document framed the local model’s role. The architecture was already doing the right thing; the language was claiming something simpler than the reality.

## 8.2 What This Document Is and Is Not

*Explicit genre clarification added in v5.0.*

This is a reference architecture document. It describes a pattern, its components, its design rationale, and the methodology used to validate the design. It is not an academic paper and does not claim experimental validation in the academic sense. It does not include datasets, benchmarks, ablations, or statistical analysis. The validation described in Section 8 refers to structured adversarial review of the design by multiple independent AI models — a design review methodology, not an implementation benchmark.

Claims in this document about system behavior (e.g., that the Context Packager reduces privacy exposure, that WAL governance limits autonomous action) are architectural claims about the properties of the design pattern. They will require empirical measurement when implemented. The document provides the design. Implementation provides the evidence.

## 8.3 Evaluation Scales with Consequence

*Added in v5.1.*

The evaluation mechanism appropriate to a deployment is determined by the deployment’s consequence profile and task repetition pattern — not by a universal standard. A federal regulatory environment where inspectors execute variations of the same 50–200 task types thousands of times per day, and where errors propagate into safety-critical enforcement actions, justifies a formal evaluation harness with golden task suites, SSDF-governed template management, and structured override analytics reported to supervisors. The DOT Departmental Orchestrator uses this approach.

A personal-scale deployment where question diversity is high, repetition is low, most errors are recoverable because the human is the immediate consumer, and users are not IT specialists requires a fundamentally different approach. Formal task suites are unjustified when the same question rarely appears twice. Structured evaluation forms create the exact labor-intensive overhead the architecture is designed to eliminate. But the absence of formal evaluation does not mean the absence of quality feedback. Without a feedback loop, the system cannot improve its routing decisions, context selection, or prompt packaging over time.

The DRNT personal gateway addresses this with a Lightweight Evaluation Loop (see Addendum: DRNT Lightweight Evaluation Loop) that captures quality signals at three levels of decreasing user effort: passive behavioral signals inferred from job outcomes at zero user effort, single-tap failure category identification that maps directly to pipeline components, and optional voice clarification on the phone for actionable specificity. The design constraint is that feedback must be cheaper than the workaround — less effort than rephrasing and resubmitting — so that users provide it as the path of least resistance rather than as an act of diligence. Over time, accumulated feedback produces routing optimization, context selection improvement, and preference learning analogous in purpose to the formal evaluation harness—a lower-cost, lower-certainty quality signal appropriate to a personal-scale environment—built from organic usage patterns rather than curated test cases.

The general principle: every deployment of this architecture needs a mechanism for measuring output quality and feeding that measurement back into routing, packaging, and template decisions. The mechanism itself — its formality, its instrumentation, its reporting structure — should be proportional to the consequence of errors and the repetition of tasks. Over-engineering evaluation for a low-consequence environment wastes user attention. Under-engineering evaluation for a high-consequence environment permits silent quality degradation. Both are architectural failures.


## 8.4 Implementation Metrics

*Added in v5.2.*

This document makes architectural claims about system properties. Those claims require empirical measurement when implemented. The following metrics define the minimum instrumentation required for any deployment to validate that the architecture is performing as designed:

| **Metric** | **What It Validates** | **Source** |
| --- | --- | --- |
| **Local resolution rate** | Percentage of requests satisfied without cloud escalation. Validates that the local model is providing retrieval value, not just acting as a passthrough. | Audit log: count(local-resolved) / count(total requests) |
| **Context reduction ratio** | Ratio of packaged payload size to full-context payload size. Validates that the Context Packager is curating, not dumping. | Audit log: assembled_tokens / total_available_context_tokens |
| **Redaction success rate** | Percentage of outbound payloads that pass sensitivity scanning without post-assembly catches. Validates that upstream rules are catching sensitive content before final assembly. | Audit log: 1 - (count(egress sensitivity catches) / count(dispatched jobs)) |
| **Human override frequency** | Rate at which humans reject or modify system outputs. Proxy for output quality and routing appropriateness. Primary signal for WAL governance. | Audit log: count(override events) / count(delivered results) |
| **Retry rate** | Percentage of jobs that require resubmission. High retry rate indicates poor context selection, wrong model routing, or inadequate prompt framing. | Audit log: count(resubmitted job_ids) / count(unique job_ids) |
| **Trust demotion frequency** | Rate of WAL demotions per capability. Validates that the trust model is responsive to failures and that promoted capabilities are genuinely reliable. | Audit log: count(wal.demoted events) per capability per time window |

All metrics are derived from the append-only audit log. No additional instrumentation infrastructure is required beyond what the architecture already mandates. Deployments that implement the audit log as specified have the data; these metrics define how to read it.

For deployments with multi-model dispatch (post-L2), two additional metrics apply: L2 disagreement rate (percentage of multi-model responses that surface conflicts) and reconciliation-to-human escalation rate (percentage of conflicts that L2 cannot resolve and escalates to human review).

# 9. What This Architecture Is Not

It is not a product. It is a reference pattern that can be implemented with different technology stacks for different contexts.

It is not a replacement for domain-specific compliance work. The architecture provides a foundation that makes compliance more tractable, not automatic.

It is not an argument against cloud AI. The entire architecture depends on cloud models for complex reasoning. It is an argument for local control over what context reaches those models and what actions their outputs can trigger.

It is not a daily planner, productivity suite, or task manager. If a deployment using this pattern helps someone plan their day, that is because they routed a planning request — not because planning is what the architecture is for.

It is not a claim that local models don’t reason. They do. The architecture’s contribution is defining where that reasoning is safe (retrieval, packaging, classification with recoverable errors) and where it is not (substantive analysis, judgment calls, anything where the local model’s output becomes the final answer or where its errors propagate silently).

It is not complete. The L2 reconciliation layer, temporal decay for WAL trust levels, and device-specific signal chain failure modes are specified but not yet implemented. V1 is the foundation. The architecture is designed to grow without requiring the foundation to be rebuilt.

*v5.2 | Post-Adversarial Edition | March 2026 | Validated across Federal, Medical CAOF, SMB Framework, DRNT, and L2 Specification adversarial review. Core principle reframed based on convergent four-model findings. Evaluation scaling principle and DRNT Lightweight Evaluation Loop addendum added in v5.1. Execution Safety Layer, adversarial input handling, failure domain classification, WAL temporal dynamics, implementation metrics, and terminology table added in v5.2 based on structured redline review.*
