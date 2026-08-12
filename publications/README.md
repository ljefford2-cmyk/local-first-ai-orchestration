# Publications and Controlled Architecture

This directory contains the current public and controlled writing corpus for the DRNT program. It separates the three-volume organizational architecture from implementation-facing technical specifications and from the running personal testbed.

## Controlling order

1. [`Project Plan v0.2 — Frozen Architectural Baseline`](controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md) controls corpus-level architecture, terminology, volume allocation, claim states, baseline machinery, and change classification.
2. [`Volume 1 — Define Reality`](controlled-architecture/Volume_1_Define_Reality_Final_Controlled_Manuscript.md) controls claim, boundary, authority, consequence, measurand, intended-effect, declared-model, and governed-target definition.
3. [`Volume 2 — Evaluate Reality`](controlled-architecture/Volume_2_Evaluate_Reality_Final_Controlled_Manuscript.md) controls independent observation, runtime-control relationships, independent evaluation, evidence sufficiency, claim states, baselines, effects, findings, closure, evaluator governance, and governed organizational learning.
4. [`Volume 3 — Connect Reality`](controlled-architecture/Volume_3_Connect_Reality_Final_Controlled_Manuscript.md) controls complete claim-relevant capability paths, requirement-relative qualification, capability results, Graduated Integration, runtime envelopes, continuing qualification, and the heterogeneous as-built/as-operated enterprise interface.
5. Foundation, doctrine, supporting papers, fixtures, specifications, and governance artifacts provide inheritance or testing material only where they map cleanly into that hierarchy.

## Directory map

| Directory | Role |
|---|---|
| [`controlled-architecture/`](controlled-architecture/) | Frozen Plan and the three Final Controlled Manuscripts. |
| [`foundation/`](foundation/) | Public conceptual entry point: the agent is not the system. |
| [`supporting-papers/`](supporting-papers/) | Engineering foundations and discussion papers supporting the controlled volumes. |
| [`enterprise-doctrine/`](enterprise-doctrine/) | Enterprise DRNT and seat-layer doctrine. |
| [`fixtures/`](fixtures/) | Synthetic stress fixtures. These are not deployment proposals or proof of implementation. |

The [`Publication Manifest`](MANIFEST.md) records source identities, statuses, hashes, and known naming/version notes.

For a conceptual reading path from the earlier local-orchestrator framing through the supporting papers and into the controlled volumes, see the [Design Evolution Reading Guide](../docs/history/design-evolution.md).

## Reading discipline

The corpus distinguishes three questions that must not collapse:

- **What does the architecture require?** Controlled here by the Project Plan, volumes, specifications, and doctrine.
- **What has been demonstrated as a primitive?** Established only at the stated scale by code, tests, receipts, and operating evidence.
- **What is hypothesized for a fixture or future realization?** Useful for testing architecture, but not an implementation claim.

The publication-level `[P] / [S] / [F]` labels express demonstrated primitive, specified mechanism, and fixture or implementation hypothesis. They do not replace source authority or promote prose into runtime fact.
