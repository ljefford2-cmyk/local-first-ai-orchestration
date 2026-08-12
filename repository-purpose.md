# Repository Purpose

This repository develops and preserves a governance architecture for organizations whose consequential work is increasingly mediated by AI models, agents, controllers, software, human decision-makers, legacy systems, external services, and physical processes.

Its purpose is:

> **Create a governance framework that enables diverse AI systems to operate together in a more useful, trustworthy, and accountable manner.**

It is not a chatbot project, an autonomous-agent platform, a claim that one product governs an enterprise, or a replacement for professional judgment. It is the architecture for defining what the organization claims, determining what authority applies, connecting those requirements to the enterprise that actually exists, independently evaluating what the evidence supports, and changing the organization only through accountable, versioned governance.

## The governed object

The governed object is not the model or agent by default. It is the **claim-relevant organizational system**.

A consequential claim may depend on:

- people and professional roles;
- authority sources, delegations, limits, expiration, and revocation;
- information sources, context, transformations, identity, and time;
- models, agents, deterministic logic, controllers, routing, and gates;
- credentials, interfaces, legacy systems, vendors, and external organizations;
- execution paths, physical processes, acknowledgments, and external effects;
- evidence custody, reconstruction, independent observation, and evaluation; and
- findings, measures, incentives, oversight, and governed change.

A component belongs inside the governed boundary whenever its credible failure, omission, unmodeled behavior, or authority could change the truth, scope, or defensibility of the claim. A material dependency left outside the boundary becomes an explicit assumption; it does not disappear.

## The repository's four functions

### 1. Define reality

The repository specifies how an organization governs the exact claim for which it seeks justified reliance. That includes the claim's purpose, boundary, assumptions, authority, consequence classification, measures and measurands, intended external effects, declared operating model, effective period, versions, and lifecycle.

### 2. Evaluate reality

It specifies how the organization independently determines what happened, whether runtime-maintained assumptions remained valid, whether intervention remained possible before consequence, whether execution and external effect occurred, what the evidence permits the organization to claim, and whether the declared operating model remains appropriate.

### 3. Connect reality

It specifies how those assurance requirements reach the heterogeneous as-built and as-operated enterprise through complete claim-relevant capability paths. APIs, databases, event streams, UI/computer-use mechanisms, professional judgment, human reporting, physical observation, offline systems, external organizations, and hybrid paths can all participate when they are appropriately bounded, qualified, observable, reconstructable, and challengeable.

### 4. Govern learning

It specifies how discrepancies, findings, overrides, effect failures, evidence limitations, recurring seams, evaluator-health signals, and operational history can produce organizational learning without becoming autonomous self-modification. Evidence may motivate change. It does not enact change. A proposed change must be evaluated, validated where appropriate, authorized, versioned, deployed, observed, and tested for actual effectiveness.

## The operating discipline

The architecture keeps distinctions that are often collapsed:

- mission is not claim;
- purpose is not authority;
- authority is not evidence;
- model output is not organizational action;
- action is not external effect;
- execution evidence is not effect evidence;
- technical qualification is not organizational authorization;
- repeated practice is not legitimacy;
- observation is not evaluation;
- evaluation is not authorization;
- a finding is not remediation;
- remediation is not technical closure; and
- learning is not permission to rewrite the system.

These distinctions are not semantic refinements. They prevent capability, confidence, workflow completion, management approval, or accumulated evidence from being promoted into a stronger claim than the evidence supports.

## Professional augmentation

The architecture is designed to improve professional judgment rather than quietly displace it. A governed system should deliver the right authorized information, applicable rule, current condition, operating history, uncertainty, limitations, and available options to the function legitimately responsible for the decision.

Meaningful human involvement requires adequate and comprehensible information, valid authority, sufficient time, and a practical ability to reject, alter, stop, redirect, delay, or escalate before the relevant consequence becomes irreversible. Where a low-consequence, reversible capability has been validly preauthorized and demonstrated, bounded machine-speed operation may be appropriate. A ceremonial approval click is not a control.

## DRNT's role

DRNT — Don't Reason, Navigate & Task — is the originating implementation architecture and first intended realization. It places routing, policy, capability, context, credential, containment, receipt, and override controls before or around execution. Open-ended reasoning may assist inside a bounded task; it does not substitute for authority, policy, containment, or verification.

The repository contains both the broader three-volume organizational architecture and the DRNT technical specifications that can instantiate parts of it. These are related but not interchangeable.

## Evidence discipline

No person or machine is authorized to certify its own correctness merely by producing or controlling the evidence. Evidence may originate inside the evaluated system, but its validity must remain independently defensible relative to the claim.

Claims about the running personal testbed are controlled by the separate `local-first-ai-gateway` repository, its code, tests, receipts, and current `STATUS.md`. This repository controls what the architecture requires. It does not convert architectural prose into implementation fact.

## Intended outcome

Success is not the number of agents deployed, the percentage of work automated, or the confidence of a model response.

Success is that the organization:

- sees relevant conditions sooner;
- routes evidence to the right function;
- acts inside valid, bounded authority;
- preserves meaningful professional judgment;
- reconstructs what actually occurred;
- verifies whether the intended effect happened;
- detects recurrence, drift, common-mode failure, and organizational seams;
- learns from accumulated operational reality; and
- improves deliberately without surrendering control over its own correctness.
