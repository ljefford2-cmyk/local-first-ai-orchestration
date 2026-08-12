# Feedback and Contribution Guidance

This repository welcomes criticism, competing examples, missing failure modes, implementation observations, and evidence that exposes unsupported assumptions in the architecture.

The project is maintained by an independent end-user and operator. It is not a product, commercial service, standards activity, employer position, vendor program, or representation of another organization.

## Useful contributions

Useful feedback includes:

- a concrete operation or failure mode the architecture does not cover;
- evidence that a claim boundary, authority model, measurand, or intended effect is incorrectly defined;
- an example where control, observation, evaluation, or authorization cannot be separated as described;
- a practical constraint that makes a proposed assurance or integration mechanism ineffective;
- a common-mode dependency or self-certification path the architecture overlooks;
- evidence that terminology or publication hierarchy is ambiguous or internally inconsistent; and
- implementation results that clarify what a specified mechanism can or cannot demonstrate at a stated scale.

Agreement and general encouragement are appreciated, but specific counterexamples and falsification attempts are especially valuable.

## Where to contribute

- Use [GitHub Discussions](https://github.com/ljefford2-cmyk/local-first-ai-orchestration/discussions) for questions, critique, competing models, operational experience, and early ideas.
- Use an issue for a bounded, actionable repository defect such as a broken link, internal inconsistency, incorrect cross-reference, or clearly identified documentation problem.
- Use a pull request only when the proposed change is already concrete and its authority and implementation implications can be reviewed from the diff.

## Evidence and authority discipline

A comment, issue, or pull request does not automatically change controlled architecture. Proposed changes are evaluated against the repository's authority hierarchy and change classifications.

Keep these propositions separate:

- **Architecture authority:** what the controlled corpus requires.
- **Implementation evidence:** what code, tests, receipts, and observed behavior demonstrate at a stated scale.
- **Synthetic evidence:** what a fixture or modeled exercise helps test without proving deployment or effectiveness.
- **Historical contribution:** how earlier material influenced the current architecture without continuing to control it.

Do not promote a specified, synthetic, proposed, or historically demonstrated mechanism into a current implementation claim by assertion.

Changes to the frozen architectural baseline require a named architectural defect, materially new evidence, or an unsupported load-bearing assumption. Implementation difficulty alone does not reopen the architecture, though it may expose an architecture defect when supported by evidence.

## Sensitive and third-party information

Do not post confidential, proprietary, classified, export-controlled, privileged, personal, security-sensitive, or otherwise restricted information. Do not include credentials, private logs, internal system details, personal records, or an organization's nonpublic incidents.

Operational examples should be public, synthetic, anonymized, or shared with clear authority. Describe only the minimum detail needed to make the architectural point.

## Scope of response

The maintainer may ask for evidence, narrow a claim, preserve an unresolved disagreement, classify a proposal as an implementation constraint, or decline a change that does not fit the repository's controlled purpose. That disposition is repository governance, not a judgment about the contributor or the broader value of the idea.
