# ADR-002: Adopt the Three-Volume Governance Architecture as the Controlling Corpus

**ACCEPTED** · August 2026 · Supersedes earlier corpus-level framing where inconsistent

## Context

The repository began as a local-first, multi-model orchestration reference architecture and later matured into DRNT — Don't Reason, Navigate & Task — with detailed specifications for routing, capability-scoped trust, context boundaries, egress, overrides, isolation, resilience, and audit evidence.

That framing established valuable implementation primitives, but it did not fully organize the larger organizational problem exposed by later work:

- the model or agent is not necessarily the system relevant to the claim;
- the organizational claim determines the evaluation boundary;
- authority, technical capability, evidence, execution, and external effect are separate propositions;
- assurance must evaluate the claim-relevant organizational operation rather than only the model or task;
- the as-built and as-operated enterprise requires its own continuing integration and qualification discipline; and
- organizational learning must remain separate from authority to change the system.

Project Plan v0.2 and the Final Controlled Manuscripts for Volumes 1, 2, and 3 now provide a complete corpus-level allocation.

## Decision

The repository adopts the following as its controlling architectural structure:

1. **Volume 1 — Define Reality** controls claim, boundary, assumptions, purpose, authority, consequence, measurands, intended effects, declared organizational operating model, source lineage, versioning, lifecycle, and governed-target handoff.
2. **Volume 2 — Evaluate Reality** controls independent observation, runtime-control relationships, independent evaluation, evidence sufficiency, consequence windows, claim states, effects, findings, closure, baselines, evaluator governance, organizational learning, and governed change.
3. **Volume 3 — Connect Reality** controls discovery, complete claim-relevant capability paths, requirement-relative qualification, capability results, Graduated Integration, runtime envelopes, continuing qualification, degradation, requalification, retirement, and heterogeneous enterprise interaction.

Project Plan v0.2 controls architecture and cross-volume allocation. The Final Controlled Manuscripts control their respective functions and interfaces.

DRNT remains the originating implementation architecture, first intended realization, and principal technical inheritance. Its specifications and governance artifacts remain active where consistent with the controlled architecture.

Claims about the running personal testbed remain controlled by the separate `local-first-ai-gateway` repository, not by architectural prose in this repository.

## Relationship to ADR-001

ADR-001 remains valid for project identity:

- DRNT is a governed AI-agent gateway and orchestration architecture;
- the architecture is technology- and domain-agnostic;
- the current reference implementation is personal-scale; and
- planner/mobile work remains an implementation/domain history rather than the whole project identity.

ADR-002 expands the corpus-level governance model and controls wherever the earlier local-model-centric or implementation-centric framing is incomplete or inconsistent with the three-volume architecture.

## Consequences

- Root documentation now begins with the organizational claim and three-volume architecture rather than single-model limitations.
- The controlled publications are committed as a distinct `publications/` corpus with repository transcriptions, source identities, and source hashes; the supplied DOCX artifacts remain in the project files.
- The Authority Map now separates source authority from implementation-claim status.
- Project volume numbers are explicitly distinguished from DRNT implementation-layer labels.
- Older reference-architecture documents remain historically useful but no longer control corpus-level architecture.
- Technical specifications remain valid for their bounded subjects and must be interpreted under the controlled hierarchy.
- No document may imply that enterprise architecture, independent evaluation, full organizational learning, or enterprise-scale L3 capability is implemented without current gateway evidence.

## Non-decisions

This ADR does not:

- claim that the three-volume architecture is fully implemented;
- authorize a deployment, pilot, certification, or regulatory claim;
- require one data lake, ontology, identity system, agent framework, cloud provider, or integration platform;
- require maximum automation or human approval of every low-consequence action;
- retire the existing DRNT specification suite; or
- amend the Project Plan or Final Controlled Manuscripts.
