# DRNT Architecture Overview — Current System Map

**Status:** Implementation-facing architecture overview under the August 2026 controlled corpus.

Read the [`DRNT Authority Map`](DRNT_Authority_Map.md) first. The Project Plan and Final Controlled Manuscripts control organizational architecture. The separate gateway repository controls current implementation claims.

## 1. Two coordinate systems

The repository uses two different coordinate systems that must not be collapsed.

### Controlled project volumes

- **Volume 1 — Define Reality**: claim, boundary, authority, consequence, measurand, effect, declared model.
- **Volume 2 — Evaluate Reality**: observation, control relationships, independent evaluation, claim state, findings, baselines, learning.
- **Volume 3 — Connect Reality**: complete capability path, qualification, integration mode, runtime envelope, continuing qualification.

### DRNT implementation layers and mechanisms

- **Gateway/router**: route, gate, dispatch, and record without turning open-ended reasoning into authority.
- **Bounded execution**: perform a defined task under constrained context, tools, credentials, network, time, and side effects.
- **Receipt/audit spine**: preserve a tamper-evident, replayable history that the producer can append to but not quietly rewrite.
- **Independent evaluation realization**: separately evaluate evidence and discrepancies without becoming the actor that produced the record.
- **Enterprise-interaction realization**: connect governed requirements to APIs, databases, event streams, UI/computer-use paths, people, physical processes, offline systems, and external organizations.

The layer names are implementation mechanisms. They do not redefine volume ownership.

## 2. System map

```text
                  VOLUME 1 GOVERNED TARGET
       claim · purpose · boundary · authority · consequence
      assumptions · measurands · effects · declared model
                              │
                              ▼
                  VOLUME 2 ASSURANCE CONTRACT
       evidence · timing · independence · consequence window
             intervention requirement · effect requirement
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     DRNT / VOLUME 3 REALIZATION                  │
│                                                                 │
│  Intent / event                                                 │
│       │                                                         │
│       ▼                                                         │
│  GATEWAY / ROUTER                                               │
│  classify work · bind purpose/authority · select capability     │
│  apply context and permission gates · record decision           │
│       │                                                         │
│       ▼                                                         │
│  BOUNDED EXECUTION PATH                                         │
│  worker/model/tool · credentials · target binding · interface   │
│  retries · override · recovery · acknowledgment                 │
│       │                                                         │
│       ▼                                                         │
│  EFFECT AND EVIDENCE PATH                                       │
│  downstream state · physical/organizational effect observation  │
│  provenance · custody · independent sentinels · limitations     │
│       │                                                         │
│       ▼                                                         │
│  APPEND-ONLY RECEIPT / RUNTIME INTEGRATION ENVELOPE              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
                 VOLUME 2 INDEPENDENT EVALUATION
        reconstruct · challenge · evaluate quality/sufficiency
          assign claim state · create findings · preserve history
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
             RUNTIME RESPONSE      LOOP 2 LEARNING
             under valid           evaluate whether the
             authority             declared model remains fit
                    │                   │
                    └─────────┬─────────┘
                              ▼
                  ACCOUNTABLE, VERSIONED CHANGE
                              │
                              └──── returns to Volume 1 target
```

## 3. Route, don't reason as authority

DRNT does not prohibit reasoning. It prohibits reasoning from silently becoming authority, policy, containment, or verification.

The gateway may use deterministic classification and authored rules to answer routing questions:

- What work class is this: exploration or execution?
- Which registered capability is eligible?
- What purpose and authority apply?
- What context may cross the boundary?
- What credential and target are permitted?
- What review or approval gate applies?
- What must be recorded before dispatch?

Open-ended reasoning can assist inside a bounded task. Its output remains evidence, analysis, recommendation, draft, or proposed work until a valid authority path gives it operational effect.

## 4. Exploration and execution

The same model can assist with both classes, but the control objectives differ.

| Work class | Objective | Governance emphasis |
|---|---|---|
| **Exploration** | Widen the field of view, compare alternatives, generate hypotheses, draft, and investigate uncertainty. | Source provenance, uncertainty, controlled variance, no presentation as verified finding, professional synthesis before consequence. |
| **Execution** | Perform a defined thing consistently, correctly, and reconstructably. | Deterministic routing, bounded authority, schema and target binding, controlled side effects, reproducible replay, effect verification, and failure handling. |

When a workflow cannot be cleanly classified, the architecture treats the ambiguity as a governed condition rather than letting fluency decide the control plane.

## 5. Complete path, not component

The engineering unit is the complete claim-relevant capability path. A correct component result can still fail the organizational requirement because of wrong binding, stale source, invalid authority, failed credential release, interface drift, missing acknowledgment, unobserved effect, corrupted evidence, or common-mode dependence.

A path is qualified only for a specific requirement, configuration, scope, operating condition, and effective period. Material change breaks silent inheritance.

## 6. Evidence and non-self-certification

The execution path can produce evidence. It cannot become final authority over the evidence used to establish its own correctness.

The receipt layer must preserve enough information to reconstruct the actual operation, including as applicable:

- governing claim, purpose, authority, consequence, and effective versions;
- source-event identity and intent equivalence;
- context included, withheld, unavailable, transformed, or restricted;
- capability, model, tool, prompt, schema, policy, and configuration identity;
- routing, gating, credential release, target binding, dispatch, retries, overrides, and recovery;
- execution and downstream acknowledgment;
- external-effect observation and evidence custody; and
- limitations, degradation, alternate path, and active-mode consequences.

A receipt proves what the recording mechanism captured. It does not, by itself, prove completeness, correctness, authority, effect, or organizational claim state.

## 7. Current demonstrated and specified status

The current personal testbed's implementation status changes independently from this document. The authoritative status source is the gateway repository's [`STATUS.md`](https://github.com/ljefford2-cmyk/local-first-ai-gateway/blob/main/STATUS.md).

At a high level, the corpus distinguishes:

- **Demonstrated primitives `[P]`**: selected routing, capability, context, audit, bounded-dispatch, replay, override, and related controls at personal-testbed scale where current gateway evidence supports them.
- **Specified mechanisms `[S]`**: independent evaluation, independence sensing/gating, complete organizational learning, and other mechanisms not established as live runtime components unless the gateway evidence says otherwise.
- **Fixture or realization hypotheses `[F]`**: enterprise/federal topology, scale, concurrency, organizational structures, and synthetic operating behavior.

No architecture diagram promotes `[S]` or `[F]` to `[P]`.

## 8. Governing outcome

The system succeeds when it helps the organization:

- see a claim-relevant condition;
- route the right evidence to the legitimate function;
- act within valid bounded authority;
- preserve professional judgment where consequence requires it;
- verify execution and external effect separately;
- reconstruct and challenge the actual path;
- detect degradation, recurrence, and organizational seams; and
- change the operating model only through accountable, versioned governance.

The chatbot is an interface. The agent is a worker. The controller is part of the system under test. The organizational claim governs the boundary.
