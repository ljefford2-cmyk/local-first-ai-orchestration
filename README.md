# Governed AI Orchestration

**Define reality. Evaluate reality. Connect reality.**

This repository is the architecture, governance, and publication corpus for DRNT — **Don't Reason, Navigate & Task** — and the broader three-volume architecture for governed AI-mediated organizations.

The central premise is simple:

> **The AI agent is not the organizational system.**

A model or agent can complete its assigned task correctly while the organization still fails because the wrong information was used, authority was invalid, a control was bypassed, the action reached the wrong target, the intended external effect did not occur, or the available evidence cannot independently establish what happened.

The governed object is therefore the **claim-relevant organizational system**: the people, authority, information, controls, workflows, models, agents, software, legacy systems, credentials, physical processes, external dependencies, effects, evidence, evaluators, and change mechanisms needed to support the exact claim being made.

## Current controlled architecture

The August 2026 corpus is governed in this order:

1. **[Project Plan v0.2 — Frozen Architectural Baseline](publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md)** controls corpus-level architecture, terminology, volume allocation, claim states, baseline machinery, and change classification.
2. **[Volume 1 — Define Reality](publications/controlled-architecture/Volume_1_Define_Reality_Final_Controlled_Manuscript.md)** defines the governed target: claim, purpose, boundary, assumptions, authority, consequence, measures, intended effects, and the Declared Organizational Operating Model.
3. **[Volume 2 — Evaluate Reality](publications/controlled-architecture/Volume_2_Evaluate_Reality_Final_Controlled_Manuscript.md)** defines independent operational assurance and governed organizational learning.
4. **[Volume 3 — Connect Reality](publications/controlled-architecture/Volume_3_Connect_Reality_Final_Controlled_Manuscript.md)** defines governed integration and continuing qualification of complete claim-relevant capability paths across the as-built and as-operated enterprise.

The complete publication set, source identities, hashes, and version notes are recorded in the **[Publication Manifest](publications/MANIFEST.md)**.

## The three-volume architecture at a glance

| Volume | Governing question | Owned function |
|---|---|---|
| **Volume 1 — Define Reality** | What exactly is the governed organizational reality? | Claims, boundary, assumptions, purpose, authority, consequence, measurands, intended effects, declared model, versions, and handoff. |
| **Volume 2 — Evaluate Reality** | What happened, what does the evidence support, and does the operating model remain appropriate? | Independent Observation, Runtime Control relationships, Independent Evaluation, effect verification, claim states, findings, baselines, evaluator governance, Loop 2 learning, and governed change. |
| **Volume 3 — Connect Reality** | How can the assurance burden reach the enterprise that actually exists? | Discovery, complete capability paths, requirement-relative qualification, capability results, Graduated Integration, runtime envelopes, continuing qualification, degradation, requalification, and heterogeneous integration. |

These volume numbers are **not** the same thing as DRNT's implementation-layer labels. Volume 1 is not the L1 router; Volume 2 is not merely an L2 model; and Volume 3 is not a roaming-agent layer. See the **[Three-Volume Architecture Overview](docs/architecture/Three_Volume_Architecture_Overview.md)**.

## Public entry point

**[AI Orchestration Governance: The Agent Is Not the System](publications/foundation/AI_Orchestration_Governance_The_Agent_Is_Not_The_System.md)** is the public conceptual entry point. It explains the practical proposition behind the controlled volumes:

- automation is supporting machinery, not the mission;
- exploration and execution require different governance;
- professional augmentation is the central value;
- whole-of-organization awareness does not mean universal access or centralized authority;
- control, observation, evaluation, and authorization must not collapse;
- the controller is part of the system under test when the claim depends on it; and
- evidence may propose change, but accountable authority must authorize, version, and record it.

## Core engineering invariants

- **The claim determines the boundary.** A model-only claim may justify a narrow item. A consequential organizational claim generally requires a broader socio-technical boundary.
- **Purpose is not authority.** Technical capability, repeated use, organizational preference, or model inference does not create permission.
- **Authority is not evidence.** Authorization to act and evidence that action occurred are separate propositions.
- **Qualification is not authorization.** Engineering establishes what a path has demonstrated; accountable authority may permit less, never more.
- **Action is not effect.** Recommendation, approval, dispatch, execution, acknowledgment, external effect, and verified effect remain distinct.
- **No self-certification.** Evaluated components may produce evidence, but no component becomes final authority over evidence used to establish its own correctness.
- **Missing evidence does not become assumed evidence.** A limitation constrains the claim, authority, assurance mode, or system design.
- **Learning does not authorize its own conclusions.** Evidence and evaluation may recommend change; governed, versioned authority enacts it.
- **The as-operated enterprise governs engineering reality.** Architecture diagrams and policies are hypotheses until current evidence shows how work actually occurs.

## DRNT's place in the architecture

DRNT is the originating architectural context, first intended realization, and principal technical inheritance. It supplies implementation-facing mechanisms such as route-don't-reason routing, capability-scoped trust, context boundaries, credential and egress gates, bounded execution, durable source-event identity, override semantics, and tamper-evident receipts.

DRNT does **not** replace the three-volume architecture. The volumes define the organizational governance and assurance problem. DRNT provides one concrete realization path for parts of that architecture.

## Architecture truth versus implementation truth

This repository controls architecture and specification. It does not prove that every described mechanism is running.

The separate **[local-first-ai-gateway](https://github.com/ljefford2-cmyk/local-first-ai-gateway)** repository controls implementation claims for the personal-scale testbed. Its **[STATUS.md](https://github.com/ljefford2-cmyk/local-first-ai-gateway/blob/main/STATUS.md)**, code, tests, receipts, and observed behavior determine what is implemented, partial, or not built.

The controlling rule is:

> **Governance language must never outrun implementation evidence.**

The publication corpus uses `[P]`, `[S]`, and `[F]` where useful:

- **[P] Demonstrated primitive** — shown runnable at the stated scale; never automatic proof at enterprise scale.
- **[S] Specified mechanism** — required or designed, but not established as a running component.
- **[F] Fixture or implementation hypothesis** — synthetic behavior, scaling assumption, or proposed realization under test.

See the **[DRNT Authority Map](docs/architecture/DRNT_Authority_Map.md)** before relying on any implementation statement.

## Repository map

| Path | Purpose |
|---|---|
| [`publications/`](publications/) | Controlled volumes, public papers, enterprise doctrine, synthetic fixture, and repository text transcriptions grounded in the supplied source artifacts. |
| [`docs/architecture/`](docs/architecture/) | Authority map, three-volume overview, and implementation-facing system maps. |
| [`specs/`](specs/) | DRNT interface and control specifications. |
| [`governance/`](governance/) | Event, trust, sandbox, and governance overlays. |
| [`validation/`](validation/) | Adversarial review methods and validation material. |
| [`docs/adr/`](docs/adr/) | Architectural decision records. |
| [`docs/legacy/`](docs/legacy/) | Reading guidance for earlier repository materials that remain historically useful but no longer control the corpus. |

## What this repository does not claim

It does not claim a complete commercial product, enterprise deployment, federal deployment, universal visibility, perfect independence, prevention of every failure, universal preventive intervention, perfect reconstruction, zero residual risk, maximum automation, or replacement of professional judgment and accountable institutional authority.

The objective is not an autonomous organization. It is an organization capable of seeing itself more accurately, acting within valid authority, challenging its assumptions, verifying real effects, learning from operational evidence, and improving without surrendering final authority over its own correctness to any single person, department, vendor, model, agent, controller, evaluator, or authorization seat.

## License and publication rights

Repository code and technical specification material are licensed under the [MIT License](LICENSE) except where a file states otherwise. Separately authored manuscripts in `publications/` are governed by the **[Publication Rights Notice](publications/LICENSE-NOTICE.md)** and any terms stated in the individual artifact.

## Author

**Lawrence Jeffords — End-User and Operator**
