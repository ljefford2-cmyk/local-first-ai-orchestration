# ADR-001: Project Identity

**ACCEPTED** · April 2026 · Establishes orchestration repo identity

## Decision

DRNT — Don't Reason, Navigate & Task — is a governed AI-agent gateway and
orchestration architecture. It places a governing layer between AI agents and
execution: bounded capabilities, context boundaries, policy-controlled egress,
human-approved execution, runtime isolation, failure recovery, and
tamper-evident auditability.

The architecture is technology-stack-agnostic and domain-agnostic. Existing
cross-domain materials apply the reference pattern across regulated, clinical,
small-to-medium business, education, and personal-scale contexts. The current
reference implementation is the personal-scale Desktop Hub: a single-operator
governed execution layer running on commodity hardware.

DRNT originated in earlier work on a personal daily-planner interface. As
AI-agent capabilities advanced, it became clear that useful agent systems
require governance before execution, not autonomous action with safety
retrofitted afterward. DRNT is that governing layer.

The personal-scale mobile interface carries forward from the planner work.
In that interface model, the iPhone serves as the command surface, Agent Inbox,
approval surface, daily-focus surface, and mobile cache. The Apple Watch serves
as the instant capture and lightweight status surface. These mobile surfaces
are first-class DRNT interfaces, but their full native implementation is
defined and validated through separate mobile planning and build artifacts.

## Scope

This ADR establishes the current project identity and consolidates existing
repo language about the architecture-vs-implementation distinction.

It does not define the full mobile contract, proposal schema, Agent Inbox
schema, implementation plan, enterprise profile, or domain-module map. Those
belong in separate specifications and gateway-side planning artifacts.

## Consequences

- The top-level project identity is DRNT as a governed AI-agent gateway and
  orchestration architecture.
- The reference architecture is domain-agnostic; the current reference
  implementation is the personal-scale Desktop Hub.
- The Daily Planner work is treated as origin and as reusable mobile/domain
  structure.
- The iPhone and Apple Watch surfaces are first-class DRNT interfaces for the
  personal-scale implementation model.
- The Desktop Hub remains the governed execution layer.
- Future non-personal deployments, including workforce, enterprise, and
  regulated-domain systems, are architecturally consistent with the governing
  pattern but are out of scope for the current reference implementation unless
  separately specified and evidenced.
- Future documentation should describe planner-shaped concepts as
  domain/interface components inside DRNT.
- Proposal/approval semantics, Agent Inbox behavior, and mobile command-surface
  details require separate Phase 4A planning and implementation evidence.
