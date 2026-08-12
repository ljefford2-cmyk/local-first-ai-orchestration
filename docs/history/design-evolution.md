# Design Evolution Reading Guide

This guide explains how the repository's major ideas developed into the August 2026 controlled three-volume architecture. It is a reading path, not an alternate authority hierarchy and not an implementation-status record.

For controlling architecture, use the order in the [Publication Manifest](../../publications/MANIFEST.md) and [Publications README](../../publications/README.md). For claims about running software, use the code, tests, receipts, and current `STATUS.md` in the separate [`local-first-ai-gateway`](https://github.com/ljefford2-cmyk/local-first-ai-gateway) repository.

## 1. Earlier local-orchestrator framing

The repository began with a narrower problem: why a single AI model should not be trusted as the whole operating environment and how a local orchestrator could govern routing, context, privacy, capability, execution, and audit.

Useful earlier materials include:

- [Why You Cannot Depend on a Single AI Model](../why-you-cannot-depend-on-a-single-ai-model.md);
- [DRNT Specification Technical Overview](../drnt-specification-technical-overview.md); and
- [Lightweight Evaluation Loop](../lightweight-evaluation-loop.md).

These documents established important implementation-facing mechanisms. Their framing is historical where it conflicts with the controlled corpus. See the [legacy reading guidance](../legacy/README.md).

## 2. From defects to governed organizational learning

[From Defect Detection to Governed Enterprise Learning](../../publications/supporting-papers/From_Defect_Detection_to_Governed_Enterprise_Learning_Revised.md) expands the problem from detecting an isolated defect to understanding the relationships around it. It develops the idea that the organizational value lies in routing evidence to legitimate decision-makers and learning from outcomes without transferring authority to a model.

The paper contributes several bridges from the earlier architecture:

- the defect is an artifact, while the knowledge lies in relationships;
- exploration and execution require different governance;
- human authority must be structural rather than ceremonial; and
- enterprise learning must remain governed rather than becoming autonomous system mutation.

## 3. The enterprise becomes the item under test

[The Enterprise Under Test](../../publications/enterprise-doctrine/The_Enterprise_Under_Test_Final_Doctrine_v3.md) moves the assurance boundary beyond the model and introduces enterprise-level doctrine. It develops seat-layer separation, reachability rather than universal access, audit inversion, consequence-aware human authority, and organizational learning.

This is where DRNT is most clearly reframed from a local routing architecture into one possible technical inheritance for a larger governed organizational system.

## 4. The controller enters the assurance boundary

[The Controller Is Part of the System Under Test](../../publications/supporting-papers/The_Controller_Is_Part_of_the_System_Under_Test_Public_Edition.md) formalizes the relationship between the claim, the item boundary, and the measurand. It explains why a controller, evaluator, evidence channel, or other dependency belongs inside the governed boundary whenever its failure could change the truth or defensibility of the claim.

Its major contributions include:

- item definition before measurement;
- claim-relative boundary selection;
- separation of design-time assumptions from runtime reality;
- epistemic independence; and
- reconstruction of the as-operated system.

## 5. The model is separated from the organizational claim

[The Model Is Not the Claim](../../publications/supporting-papers/The_Model_Is_Not_the_Claim_Discussion_Paper.md) applies the emerging architecture to model and agent assurance. It distinguishes necessary technical controls from sufficient evidence for an organizational claim and connects model behavior to authority, context, control paths, human participation, external effects, and continuing conformance.

The central transition is from asking whether a model performed correctly to asking what exact claim the organization seeks to support and what complete socio-technical system that claim depends on.

## 6. Public synthesis: the agent is not the system

[AI Orchestration Governance: The Agent Is Not the System](../../publications/foundation/AI_Orchestration_Governance_The_Agent_Is_Not_The_System.md) provides the public synthesis. It makes the broader thesis accessible without requiring readers to begin with the controlled manuscripts:

- automation is supporting machinery, not the mission;
- professional augmentation is the central value;
- whole-of-organization awareness does not imply universal access or centralized authority;
- testing and validation are sensors rather than the endpoint; and
- neither the agent nor the controller may become final authority over organizational correctness.

## 7. Controlled consolidation: Define, Evaluate, Connect

The August 2026 controlled architecture consolidates these developments into three functions:

1. [Volume 1 — Define Reality](../../publications/controlled-architecture/Volume_1_Define_Reality_Final_Controlled_Manuscript.md) defines the governed claim, boundary, purpose, authority, consequence, measurands, intended effects, and declared operating model.
2. [Volume 2 — Evaluate Reality](../../publications/controlled-architecture/Volume_2_Evaluate_Reality_Final_Controlled_Manuscript.md) defines independent operational assurance, truthful claim states, effect verification, findings, closure, and governed learning.
3. [Volume 3 — Connect Reality](../../publications/controlled-architecture/Volume_3_Connect_Reality_Final_Controlled_Manuscript.md) connects those requirements to complete capability paths across the heterogeneous as-built and as-operated enterprise.

The [Project Plan v0.2 — Frozen Architectural Baseline](../../publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md) controls the corpus-level terminology, allocation, baseline machinery, and change process.

## How to interpret this evolution

The sequence shows conceptual expansion and consolidation. It does not establish that every described mechanism was implemented in the same order—or implemented at all.

Keep three propositions separate:

- **Architecture authority:** what the controlled corpus requires.
- **Demonstrated implementation:** what current code, tests, receipts, and observed behavior establish at a stated scale.
- **Historical contribution:** how an earlier document helped produce the current architecture.

Publication chronology, conceptual maturity, architectural authority, and implementation status are related but never interchangeable.

## Source-document provenance

The original DOCX editions of the five publications above are the visual source authorities. Their filenames, identities, SHA-256 hashes, sizes, and status notes are recorded in the [Publication Manifest](../../publications/MANIFEST.md). The repository carries Markdown transcriptions for reading, search, linking, and architecture control; it does not duplicate the DOCX binaries.
