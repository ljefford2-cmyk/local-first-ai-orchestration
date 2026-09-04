# AR-1.0 — Track B Tier 3 Declared-Architecture Applicability Audit Rule

**Status at preparation:** Non-controlling Track B method record. **DRAFT / NOT AUTHORIZED / NOT EXECUTED.** Not part of the controlling architecture and does not supersede the Project Plan or Volumes 1–3.

**Date:** 2026-09-04

**Method version:** `AR-1.0`

**Repository reference snapshot:** `1ef1a5a44a45ed16232ecf9a5d073ae779133165`

**Sole applicability reference:** `publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md`

**Applicability-reference Git blob:** `047121d9605e687880488e877dc1c71723d93599`

AR-1.0 is a method rule, not architecture. It creates no new requirement force.

This record defines the bounded method that may govern B1.5 only after a
separate owner-authorization record identifies and authorizes the exact
committed bytes of AR-1.0. This draft does not authorize population
enumeration, classification, adjudication, review, acceptance, evidence
mapping, or any change to the repository architecture or implementation.

## 1. Purpose

B1.5 determines, for each frozen Track B Tier 3 source row, whether its
recorded triggering condition is recognized or affirmatively excluded by the
declared architecture represented by the pinned Project Plan.

B1.5 answers a declared-architecture applicability question. It does not
decide whether a condition currently exists in an organization or operation,
whether a requirement is implemented or satisfied, or whether evidence is
sufficient.

## 2. Method standing and namespace safeguard

The unit and vocabulary of this method are specific to Track B.

In this record, **Tier 3** means a conditional requirement designated Tier 3
under ER-1.0 when the frozen Track B source population was established. It is
unrelated to the Authority Map expression **Tier 3 — staging truth**. No
relationship, equivalence, hierarchy, or inference between those uses is
permitted.

AR-1.0 may organize and classify existing frozen source rows only. It may not:

- amend or interpret the controlling architecture beyond the text of the
  pinned applicability reference;
- create, strengthen, weaken, repair, or retire a requirement;
- reopen or modify B1.1–B1.4;
- create a fourth normative architecture; or
- give a canonical ID normative standing.

## 3. Fixed reference and allowed inputs

### 3.1 Reference identity

All B1.5 architectural results are bounded jointly to:

- repository snapshot
  `1ef1a5a44a45ed16232ecf9a5d073ae779133165`; and
- Project Plan Git blob
  `047121d9605e687880488e877dc1c71723d93599` at
  `publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md`.

The Project Plan blob is the sole result-bearing applicability reference. A
later `HEAD`, working-tree copy, branch, release, or Project Plan revision may
not be substituted.

A citation or incorporation reference inside the Project Plan is not
permission to follow that reference into a Volume, manuscript, design basis,
paper, implementation artifact, or other file. If the pinned Project Plan does
not itself establish the relation needed by AR-1.0, the result is not supplied
from another source.

### 3.2 Allowed artifact set and permitted use

Only the artifacts in this table are permitted substantive repository inputs
to population establishment and audit execution. The Git blob identities are
part of the input boundary.

| Artifact | Git blob | Permitted use |
|---|---|---|
| `docs/track-b/B1.1-volume-1-requirement-register.md` | `6c9078ac08fb07c9322172b8a3ed9f4650c86845` | Select frozen Volume 1 Tier 3 source rows; read their recorded fields. |
| `docs/track-b/B1.2-volume-2-requirement-register.md` | `753824a8f75af0e8790bc8efe64f0eb0593dec33` | Select frozen Volume 2 Tier 3 source rows; read their recorded fields. |
| `docs/track-b/B1.3-volume-3-requirement-register.md` | `c7bd2ea30507d51c72cc353eedcaf530a1c7b328` | Select frozen Volume 3 Tier 3 source rows; read their recorded fields. |
| `docs/track-b/B1.4-source-to-canonical-crosswalk.md` | `2c33703dcf87e992867112423dba9cffb3898dc4` | Supply the non-controlling canonical cross-reference for each source row. |
| `docs/track-b/B1.4-canonical-requirement-register.md` | `0489a9ab635a04ff730bc05b8f2ccf83a3dd71bb` | Verify a canonical cross-reference only; no canonical-core wording may ground an audit result. |
| `docs/track-b/B1.1-adjudications-A1-A3.md` | `def288fe490dc28c8cadf9105d20df59fd9da538` | Establish the accepted extraction adjudications and bundled-proposition application note; no audit result may be grounded here. |
| `docs/track-b/B1.4-input-manifest.md` | `2a00a7ab9b91cc882681d893cadca4e12694fc7a` | Verify frozen source identity, provenance, and counts. |
| `docs/track-b/B1.4-canonicalization-owner-acceptance.md` | `a8562cd76756112360e6d21d4a6ca6bcbcb4fab2` | Establish that B1.4 is owner accepted and frozen and that B1.5 remained reserved. |
| `publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md` | `047121d9605e687880488e877dc1c71723d93599` | Sole source permitted to ground an architectural `PRESENT` or `ABSENT` result. |

The authorized AR-1.0 bytes and their separate owner-authorization record are
governance prerequisites. The frozen population record, working audit
records, review records, and AR-A records are governed process records as they
are created. Those records may be read only to administer, review, adjudicate,
and preserve the pass in accordance with this method. They do not supply
facts about a row's trigger or the declared architecture and may not ground a
`PRESENT` or `ABSENT` result.

Before any population work, the executor shall verify every allowed path and
Git blob against the pinned repository snapshot. The input and execution
provenance records shall name every substantive, governance, and process
artifact actually read; state its permitted purpose; report its path or record
identity; and report a SHA-256 digest of the bytes read where a byte artifact
exists. For repository inputs, they shall distinguish that digest from the Git
blob identity. An integrity mismatch, missing path, or perceived need for an
unlisted substantive artifact stops the pass and is referred; no substitute
is permitted.

### 3.3 Prohibited inputs

No substantive artifact outside section 3.2 may be used to establish the
population, interpret or repair a trigger, or determine its relation to the
declared architecture. Governed process records described in section 3.2 may
be used only for their stated administrative purposes. In particular, B1.5
shall not read or use:

- the source manuscripts or uploaded DOCX or PDF copies;
- fixture, harness, gateway, test, implementation, deployment, operational,
  telemetry, or evidence artifacts;
- current organizational practice, oral declarations, unstated owner
  knowledge, or as-operated facts;
- source-row exclusion ledgers;
- canonical decision logs, canonical-core text as a substitute for a source
  trigger, related canonical rows, or cross-volume analogies;
- later versions of the Project Plan or the repository; or
- the measurement-model document or other explanatory material supplied
  during method development.

Absence of permission to read an artifact is not evidence that a trigger is
absent.

## 4. Hard audit boundaries

B1.5 shall not:

- map evidence;
- judge satisfaction, conformance, implementation, effectiveness,
  reasonableness, or evidence sufficiency;
- inspect or recommend a fixture, harness, gateway, test, implementation, or
  architecture change;
- retier, merge, unmerge, split, rewrite, or remove any B1.1–B1.4 row;
- create a new requirement or requirement ID;
- compare requirements across volumes except to carry the frozen B1.4
  canonical cross-reference;
- evaluate the adequacy, completeness, correctness, or governance of the
  Project Plan;
- decide a current organizational claim, scope, authority assignment, or
  as-operated fact; or
- begin the downstream evidence-mapping pass.

Statements such as `not implemented`, `no agent holds that authority`, and
`the fixture does not exercise that path` describe implementation extent.
They never establish absence of an architectural trigger and are prohibited
grounds for an `ABSENT` result.

## 5. Population establishment and freeze

### 5.1 Population unit

The population unit is one original source requirement row whose frozen Tier
field is `3`, identified by its original `V1-Rnnn`, `V2-Rnnn`, or `V3-Rnnn`
ID.

The expected frozen distribution at the pinned inputs is:

| Source register | Tier 3 rows |
|---|---:|
| Volume 1 | 6 |
| Volume 2 | 21 |
| Volume 3 | 17 |
| **Total** | **44** |

The B1.5 population is not a set of canonical rows. Each of the 44 source IDs
is a separate audit member exactly once. More than one source member may carry
the same canonical cross-reference, and a canonical row may contain members
from different extraction tiers. Neither fact changes population membership.

### 5.2 Enumeration and pre-classification freeze

After AR-1.0 is committed and separately authorized, the first act of B1.5 is
to enumerate all population IDs from the three pinned source registers in
fixed source order. The population record shall include:

- every original source ID, grouped by volume;
- per-volume and total counts;
- the pinned repository snapshot;
- the path, Git blob identity, and SHA-256 of each register read;
- the canonical cross-reference for each member as an administrative field;
- confirmation that every selected ID occurs exactly once; and
- its own version and SHA-256.

That explicit population record must be reviewed, frozen, and recorded before
the first row or branch is classified. A count other than `6 / 21 / 17 = 44`,
a duplicate, a missing source ID, or a cross-reference conflict stops
classification and is referred for human disposition.

### 5.3 Identity and tier preservation

The Tier designation in force when the population record is established
controls membership for that B1.5 run. A later reclassification is a recorded
population change requiring separate authorization and a new population
version; it is not a substitution inside the frozen run.

A missing, malformed, ambiguous, or non-conditional trigger does not remove a
row from the population and does not authorize retiering. It creates a visible
finding and referral under sections 11 and 12.

## 6. Source-row and referent rule

The recorded Tier 3 trigger field defines the predicate tested by B1.5. The
full frozen source requirement row may be used only to resolve grammar,
pronouns, and back-references already present in that trigger, including forms
such as `they`, `that basis`, and `those conditions`.

Full-row use may not:

- add a predicate;
- strengthen, narrow, generalize, or repair the recorded trigger;
- import an unstated actor, object, scope, qualification, exception,
  dependency, temporal condition, or consequence;
- use the quoted controlling passage as proof that the trigger is present in
  the Project Plan; or
- authorize opening the manuscript.

The row supplies the predicate being tested, not proof of its own presence in
the declared architecture. If the referent remains unresolved from the frozen
row, record a trigger-field finding, assign `INDETERMINATE` as required by
section 9.3, and refer it. Do not consult the manuscript.

## 7. Governing audit question

For each population member, ask:

> Does the pinned Project Plan affirmatively recognize or affirmatively
> exclude the recorded triggering condition, within the material bounds of
> that source row?

The answer is an architectural relation between a frozen predicate and a
fixed declared-architecture text. It is not an observation that the predicate
has occurred in a present operation.

Each determination shall identify the exact Project Plan anchor and state the
material bound of the result. A shared topic, word, purpose, analogy, or broad
architectural theme is not enough.

## 8. Material branches and compound triggers

One original source ID remains one population member. Where its recorded
trigger contains materially different predicates or branches, B1.5 shall
analyze those branches separately without creating new requirement IDs or new
population members.

The analysis shall preserve, as written:

- `AND` and `OR` relations;
- complementary conditions;
- exceptions and exclusions;
- dependency relations;
- temporal order and lifecycle conditions; and
- actor, object, scope, qualification, and consequence differences material
  to the trigger.

No branch may be selected as the representative branch. Branches may not be
averaged, collapsed, silently discarded, or forced into a single parent truth
value.

Each material branch receives its own analysis-only branch key, Project Plan
anchor, architectural result, result bound, activation locus, grounds, and
referral status. The primary audit register still contains one parent record
for the source ID; it may contain a keyed multi-branch record or point to a
linked branch continuation. An administrative `SEE BRANCHES` notation is not
an additional architectural result. If activation loci differ, the parent
activation-locus field may use `MIXED — SEE BRANCHES`.

## 9. Closed architectural-result vocabulary

Every whole-row or material-branch determination shall use exactly one of the
following results. There is no fourth truth value.

### 9.1 `TRIGGER PRESENT IN DECLARED ARCHITECTURE`

Use `PRESENT` only where the pinned Project Plan affirmatively states:

1. the recorded triggering condition; or
2. an explicitly encompassing declared class that includes that condition
   without losing a material actor, object, scope, qualification, exception,
   dependency, temporal relation, or consequence.

The grounds shall cite the exact Project Plan location and explain the bounded
match. Topic similarity, common vocabulary, inferred purpose, analogy,
converse, contrapositive, or general architectural theme is insufficient.

A Plan allocation of a function, volume, or topic is not, by itself,
`PRESENT` of a volume-extracted triggering condition.

Every proposed `PRESENT` grounded on section 9.1(2) rather than section 9.1(1)
shall be referred for qualified human disposition.

`PRESENT` means only that the trigger or its encompassing class is recognized
by the pinned declared architecture. It does not mean that the trigger is
active in a particular organization, claim, system, or run. It does not mean
that the requirement is implemented, satisfied, evidenced, or universally
applicable.

### 9.2 `TRIGGER ABSENT FROM DECLARED ARCHITECTURE`

Use `ABSENT` only where the pinned Project Plan affirmatively excludes the
recorded triggering condition or its applicable class within the same
material bounds used for a `PRESENT` comparison.

Every proposed `ABSENT` result shall name all three of the following:

1. the controlling conditional from the frozen source row;
2. the specific trigger asserted to be absent; and
3. the exact Project Plan text and location that affirmatively excludes it.

Silence, omission, ambiguity, insufficient detail, an unlisted example, a
missing declaration, missing evidence, lack of implementation, lack of an
assigned authority, or a fixture that does not exercise a path is never an
affirmative exclusion.

A Plan statement that the architecture does not guarantee a property is not
an exclusion of the triggering conditions under which rules about that
property apply.

`ABSENT` supports only this bounded statement: the trigger is affirmatively
excluded by the pinned declared-architecture reference. The audit shall not
emit an unqualified `NOT APPLICABLE` label. If a later record describes the
bounded consequence as not applicable, it must preserve the controlling
conditional, absent trigger, exact affirmative exclusion, reference pins, and
result bound.

Every proposed `ABSENT` result requires qualified human adjudication and
remains proposed until that disposition is recorded.

### 9.3 `TRIGGER RELATION INDETERMINATE FROM ALLOWED INPUTS`

Use `INDETERMINATE` where the relation cannot be established under sections
9.1 or 9.2 from the allowed inputs. This includes:

- Project Plan silence or insufficient detail;
- material ambiguity or conflict;
- an unresolved referent;
- a missing, malformed, or non-conditional trigger field; or
- a match that depends on inference prohibited by this method.

`INDETERMINATE` is a valid, visible B1.5 result. It is not a provisional
`ABSENT`, permission to add information, or a defect determination about the
Project Plan. It requires qualified human disposition, which may preserve the
result as unresolved.

## 10. Closed activation-locus vocabulary

Architectural result and activation locus are separate fields. Activation
locus is not a third or fourth architectural truth value and may not change
the result assigned under section 9.

The activation-locus field permits exactly one of these four values:

- `SETTLED BY DECLARED TEXT` — the pinned Project Plan itself settles the
  relevant activation question at the declared-architecture level;
- `DECLARED CLAIM/SCOPE DECISION REQUIRED` — application to a particular
  case depends on a claim or scope declaration not established by the allowed
  inputs;
- `RUN-TIME OR EVIDENCE FACT REQUIRED` — application to a particular case
  depends on an operational, event, state, or evidence fact not established
  by the allowed inputs; or
- `MIXED — SEE BRANCHES` — material branches have different activation loci,
  which are stated branch by branch.

As the only alternative to those four values, where an `INDETERMINATE`
architectural result prevents any activation locus from being supported, the
field shall contain the administrative null `NOT ASSIGNED — ARCHITECTURAL
RELATION INDETERMINATE` and the referral status or adjudication ID. That
notation is not an activation-locus value and does not create an additional
truth value.

A `PRESENT` result may properly carry `DECLARED CLAIM/SCOPE DECISION REQUIRED`
or `RUN-TIME OR EVIDENCE FACT REQUIRED`: the architecture can recognize a
trigger class without establishing that a particular case activates it.

These activation-locus entries are terminal for B1.5. They state only the
class of unresolved fact. B1.5 shall not name or design a future artifact,
test, fixture, evidence source, implementation, sufficiency standard, or
remedy to resolve it.

No preparer, reviewer, authorizing authority, accepting authority, or AR-A
adjudicator may introduce an oral scope declaration, organizational knowledge,
evidence, implementation fact, or as-operated fact to settle an activation
locus inside B1.5.

## 11. Findings, uncertainty, and stop conditions

The audit shall maintain a findings ledger separate from the architectural
result field. At minimum, it shall record:

- missing, malformed, ambiguous, or non-conditional trigger fields;
- unresolved trigger referents;
- conflicts between a frozen source row and its canonical cross-reference or
  canonical wording;
- population, identity, count, path, digest, or blob discrepancies;
- method deviations;
- reviewer divergences;
- any authorized post-extraction adjudication or application note that appears
  consequential to a Tier 3 row; and
- any visible audit pattern associated with the already recorded low Volume 1
  Tier 3 count.

The last two categories are recorded for accountable review only. They do not
authorize reopening extraction, comparing substantive requirements across
volumes, changing a tier, changing the population, or evaluating the Project
Plan.

A finding does not by itself change an architectural result. Where the result
cannot be supported under section 9, assign `INDETERMINATE` and refer it.

A trigger defect stops classification of the affected row, which remains in
the population with `INDETERMINATE` and a referral. It does not halt
classification of the remaining population unless the defect also creates an
input- or population-integrity failure.

Classification shall stop for the affected row or pass, as appropriate, on:

- an input-integrity or population-integrity failure;
- a perceived need to use a prohibited input;
- a trigger defect that prevents bounded analysis;
- a proposed change to the population or method;
- a material conflict; or
- any other condition for which AR-1.0 does not supply a closed rule.

Stopping preserves uncertainty; it does not turn missing information into
absence.

Exposure to a prohibited substantive input cannot be cured by disclosure or
human adjudication. Every affected proposed determination shall be discarded
and re-performed in a clean execution context that has not received the
prohibited input. If clean re-performance cannot be established, the affected
row or pass is not eligible for acceptance. A deviation that changes the
method itself requires the versioning and reauthorization rule in section 13.

This exposure rule governs execution contexts. Method development — authoring,
reviewing, and comparing AR-1.0 drafts, including work performed in contexts
that received the measurement-model document or the preauthorization sample
reasoning — is not execution and is not contaminated by this rule. Those
contexts shall not serve as the B1.5 preparer or re-performance reviewer.
Execution requires fresh contexts. The preparer receives only the authorized
AR-1.0 bytes, the owner-authorization record, the frozen population record,
and the section 3.2 inputs. The re-performance reviewer receives only the
authorized AR-1.0 bytes, the owner-authorization record, and the section 3.2
inputs; it does not receive the frozen population record or any prepared
determination until its own population reproduction and determinations are
fixed under section 16.

## 12. Human adjudication and non-self-certification

### 12.1 Functional roles

AR-1.0 uses functional, vendor-neutral roles:

- **preparer** — produces the initial population and proposed row or branch
  determinations;
- **re-performance reviewer** — independently reproduces the population and
  row or branch determinations under section 16;
- **accountable human authorizing authority** — authorizes use of the exact
  committed AR-1.0 bytes and fixed reference before population work begins;
- **qualified human adjudicator** — disposes of anomalies and referred
  questions within the authority of this method; and
- **accountable human accepting authority** — decides whether the completed
  B1.5 record is accepted and frozen.

The organization designates the people, qualifications, authority scopes, and
escalation route appropriate to its circumstances. AR-1.0 does not prescribe a
job title, committee, reporting chain, vendor, model, or product.

Method authorization is a held human decision. It may not be issued by the
preparer acting as an automated system, by a re-performance tool, or by any
other automated process. The organization may assign the human authorizing,
adjudicating, and accepting functions according to its own governance,
provided every boundary and separation expressly required by AR-1.0 remains
intact.

### 12.2 Automated analysis is proposal only

Automated tools may assist with enumeration, matching, classification, and
review. Their outputs remain proposals. No automated preparer or reviewer may
accept, certify, or finally adjudicate its own correctness.

At minimum, all of the following require disposition by a qualified human
adjudicator:

- every proposed `ABSENT` result;
- every `INDETERMINATE` result;
- every trigger-field or referent defect;
- every population or input discrepancy;
- every conflict or method deviation;
- every change-sensitive anomaly; and
- every divergence between the preparer and the re-performance reviewer.

A change-sensitive anomaly is a difference plausibly associated with a
software, tool, model, process, feature, dependency, or operating-context
change that could affect the reliability of the execution record. Automated
analysis may surface that anomaly but may not determine whether it is
legitimate or what disposition it receives. That judgment belongs to the
qualified human adjudicator within the bounds of this method.

The human adjudicator may affirm or reject a proposal, narrow its stated
bound, require correction under the existing method, or preserve the matter
as unresolved. Human involvement does not relax the allowed-input boundary or
the burdens in section 9.

The entire completed register, including clear `PRESENT` proposals, remains
unaccepted until the accountable human accepting authority reviews the audit,
the re-performance, the findings, the adjudications, and the unresolved list
and records acceptance or rejection.

## 13. AR-A adjudication series and boundaries

B1.5 adjudications shall use a new, sequential series beginning `AR-A001`.
This series is separate from extraction adjudications A1–A3.

Each AR-A record shall:

- identify the affected source ID and branch, if any;
- state the exact referred question;
- cite only the authorized inputs and applicable AR-1.0 clause;
- record the qualified human disposition and its bounded rationale;
- identify the deciding human authority or role; and
- state whether the result, bound, locus, grounds, finding, or review status
  changes under the existing rule.

An AR-A adjudication may apply or correct application of AR-1.0 to a specific
row or branch. It may not:

- create, revise, supplement, or authoritatively interpret missing
  architecture;
- repair a frozen trigger, source row, canonical row, or crosswalk;
- change population membership or tier designation;
- introduce an unlisted input, current-scope declaration, organizational
  knowledge, evidence, implementation detail, fixture result, or as-operated
  fact;
- convert silence, ambiguity, or missing information into absence;
- decide a claim-specific, scope-specific, or as-operated fact;
- waive a present, absent, citation, or review burden; or
- prescribe a downstream proof path, artifact, test, or remedy.

Each ruling is row- or branch-specific unless its stated scope is narrower. A
proposed generally applicable change to the vocabulary, burden, reference,
input boundary, population rule, or decision rule is not an adjudication. It
requires a new AR version, separate owner authorization, and re-performance of
every affected earlier determination.

The AR-A series closes when the B1.5 acceptance record is made. Later facts
may not enter B1.5 through a supplemental adjudication.

## 14. Required audit record and outputs

### 14.1 Core audit register

In this register, `INDETERMINATE` is an architectural relation, not a Volume 2
claim state.

The audit register shall contain one parent record per population member with
these eleven fields:

| # | Field | Required content |
|---:|---|---|
| 1 | Source V-R ID | Frozen original source-row ID. |
| 2 | Canonical cross-reference | Non-controlling B1.4 canonical ID only. |
| 3 | Recorded trigger verbatim | Exact, unaltered trigger field from the frozen register. |
| 4 | Analysis-only branch key / predicate | `WHOLE ROW`, or stable local branch keys and predicates that preserve the original logic; any bounded referent resolution is recorded here or in field 9 without changing field 3. |
| 5 | Project Plan anchor | Exact section or textual location in the pinned Plan; `NONE LOCATED` where appropriate. |
| 6 | Architectural result | Exactly one section 9 result per whole row or material branch; a keyed multi-branch entry is permitted. |
| 7 | Result bound | The actor, object, scope, qualification, exception, dependency, temporal relation, or consequence that limits the result. The fixed reference identity is inherited from the register header. |
| 8 | Activation locus | Exactly one section 10 value per whole row or branch, or the section 10 administrative null where the architectural relation prevents assignment. |
| 9 | Bounded grounds | Concise reasoning under the applicable burden; for `ABSENT`, all three mandatory elements in section 9.2. |
| 10 | Referral / adjudication ID | `NONE`, literal `PENDING`, or the applicable `AR-A###` record. The source ID and branch key identify a pending item in the unresolved list. |
| 11 | Review status | Status under the closed review states below. |

Review status shall use one of:

- `PREPARED — NOT REPERFORMED`;
- `REPERFORMANCE FIXED — COMPARISON PENDING`;
- `REPERFORMED — AGREES`;
- `REPERFORMED — DIVERGES`; or
- `REPERFORMANCE BLOCKED — REFERRED`.

Human referral and disposition are recorded separately in field 10 so that a
human adjudication does not overwrite the re-performance state.

The preparer and reviewer each fix substantive fields 1–10 before comparison.
The review-status field is an administrative overlay populated or updated
only after those substantive records and their SHA-256 digests are fixed.
Both pre-comparison identities shall remain in the review record.

Where one field cannot faithfully contain material branch detail, a linked
branch continuation may repeat fields 4–11. The continuation is part of the
same source-member record and creates no new population member.

### 14.2 Other required records

B1.5 shall also produce:

1. the frozen population record required by section 5;
2. input and execution provenance records naming every artifact read, its
   permitted use, path or record identity, Git blob identity where
   applicable, SHA-256 where a byte artifact exists, and verification result;
3. the prepared audit register;
4. a findings ledger;
5. an unresolved-questions and referrals list;
6. the separate re-performance register prepared blind to the preparer's
   population and determinations, together with its population reproduction;
7. a divergence record preserving both preparer and reviewer determinations;
8. the AR-A adjudication records, if any;
9. a completion record; and
10. a separate owner-acceptance or rejection record.

The execution provenance shall identify the preparer, reviewer, human
authorizing authority, human adjudicator roles used, accepting authority,
methods or tools used, tool or model versions where applicable, shared
dependencies, limitations, and any deviations. These disclosures describe the
execution; they do not alter the vendor-neutral role requirements in section
12.

## 15. Preauthorization exploration has no result force

Any row-level reading performed before AR-1.0 authorization was exploratory
method development only. It may be cited in the authorization record solely
as part of the basis for selecting the applicability reference.

No exploratory classification, including any result discussed for the
four-row reference-selection sample, is an audit input, precedent, expected
answer, or carried disposition. Every sampled row shall be classified anew
with the other population members only after:

1. the exact AR-1.0 bytes are committed and separately authorized; and
2. the explicit 44-row population is enumerated and frozen.

The re-performance reviewer shall not receive exploratory or preparer
classifications before completing the independent work required by section
16.

## 16. Separate full re-performance

A reviewer distinct from the preparer shall perform a complete, blind
re-performance under the same authorized AR-1.0 bytes and the same pinned
inputs.

Before viewing the preparer's population or classifications, the reviewer
shall:

1. independently reproduce the Tier 3 population from the three frozen source
   registers;
2. verify the expected `6 / 21 / 17 = 44` distribution and source-ID
   uniqueness;
3. assign the canonical cross-references;
4. re-perform every whole-row and material-branch determination;
5. fix a separate register using substantive fields 1–10 and the same burdens;
   and
6. record its SHA-256 before comparison.

Only after both substantive records and their SHA-256 digests are fixed may
they be compared. Field 11 is then populated as a post-comparison
administrative overlay. Both pre-comparison determinations and every
divergence shall be preserved; disagreement may not be overwritten by a
consensus register before qualified human adjudication.

The review record shall identify the reviewer, review method, tools and
versions if used, access to prior results, shared dependencies with the
preparer, limitations, population differences, row or branch divergences, and
human dispositions.

AR-1.0 requires separation of preparer and reviewer and blindness to prepared
results until re-performance is fixed. It does not require or prefer a
particular model or vendor. Use of a different model, vendor, or tool is
neither necessary nor sufficient to establish the required separation, and
does not replace human adjudication or accountable human acceptance.

## 17. Terminality, later facts, and reference change

Every accepted B1.5 result is terminal for B1.5 under the pinned repository
snapshot and Project Plan blob. Later evidence, implementation, fixture,
operational, organizational, or scope facts may not amend, settle, replace, or
overwrite any B1.5 architectural result, activation locus, branch analysis,
grounds, or finding.

A downstream evidence-mapping, claim-scope, or as-operated pass may, under its
own authorized method, inputs, reference, vocabulary, and acceptance record:

- cite an unchanged B1.5 row;
- supply a fact B1.5 identified as outside its allowed inputs; and
- make its own downstream determination.

It may not present that downstream determination as a revised B1.5 result.

A later Project Plan revision or correction likewise leaves the completed
B1.5 snapshot unchanged. Applying B1.5 to changed declared architecture
requires a newly identified reference snapshot, a new run record, and the
applicable authorization. A post-acceptance correction to a B1.5 artifact
requires an explicit reopening or change record; silent modification is
prohibited.

## 18. Separate accountability-structure work

The accountability-structure question prompted by Measurement Model section
14 and directed to Project Plan sections 21–24 and Volume 2 sections 10–16 is
separate work. It is not an input, subquestion, finding category, or output of
B1.5.

This section limits the scope of the separate review; it does not restrict
which pinned Project Plan text may serve as a B1.5 anchor.

B1.5 applies frozen triggers against the Project Plan as pinned. It does not
evaluate or repair the Plan. If separate work later changes the Plan, section
17 controls: this B1.5 snapshot remains bounded to the original Plan blob.

## 19. Completion meaning and stop rule

B1.5 passes through four states. Each state is marked by its own record; no
state is reached by implication from a later one.

1. EXECUTION COMPLETE — the authorized 44-row population is frozen and fully
   covered; every material branch is preserved and addressed; the prepared
   audit register, findings ledger, unresolved list, and input and execution
   provenance records are complete; no proposed determination was prepared
   using a prohibited substantive input, or every affected proposal was
   discarded and cleanly re-performed under section 11. The completion record
   marks this state and uses the bounded language below.
2. RE-PERFORMANCE COMPLETE — the blind re-performance register is fixed with
   its SHA-256; comparison is performed; every divergence is recorded with
   both determinations preserved.
3. ADJUDICATION COMPLETE — every referral, ABSENT proposal, INDETERMINATE
   result, finding, divergence, and deviation requiring human
   disposition under section 12 has received it, including any decision to
   preserve an item as unresolved; every ABSENT that stands has met section
   9.2; administrative deviations are documented and disposed, and any
   deviation that would change the rule has followed section 20.
4. OWNER ACCEPTED / FROZEN or OWNER REJECTED — the accountable human
   accepting authority reviews the records of states 1–3 and records
   acceptance or rejection. Rejection returns the pass to the state the
   rejection names; the AR-A series remains open until an acceptance record is
   made.

None of the four states requires zero findings, zero divergences, zero
INDETERMINATE results, or universal resolution. They require full coverage,
visible uncertainty, required human disposition, separate re-performance, and
accountable human decision.

The completion record shall use bounded language of this form:

> AR-1.0 classified 44 frozen Tier 3 source rows for applicability to the
> declared architecture represented by Project Plan v0.2 at Git blob
> `047121d9605e687880488e877dc1c71723d93599` and repository snapshot
> `1ef1a5a44a45ed16232ecf9a5d073ae779133165`. Results are bounded to
> architectural trigger recognition, affirmative exclusion, or indeterminacy
> from the permitted inputs. Claim-specific and run-time activation remains
> unresolved where those inputs do not establish it.

Do not report completion as `N requirements are not applicable` or any other
unbounded count of non-applicability.

On completion, stop. Do not begin evidence mapping, satisfaction assessment,
implementation review, fixture work, architecture evaluation, or remediation.

## 20. Record and authorization sequence

The sequence is mandatory:

1. prepare this method record as `DRAFT / NOT AUTHORIZED / NOT EXECUTED`;
2. complete owner text review;
3. normalize and verify the canonical file as UTF-8 with LF line endings and
   no carriage-return bytes;
4. perform disclosure inspection of the complete method text;
5. compute its working-tree SHA-256, stage only this method file, verify the
   staged bytes have the same SHA-256, commit it alone, and verify the
   committed bytes and Git blob identity;
6. create and separately commit a human owner-authorization record naming the
   exact AR-1.0 path, SHA-256, Git blob, commit, input snapshot, and
   applicability-reference blob;
7. only after that committed authorization, enumerate, review, separately
   commit, and freeze the explicit population record before any
   classification;
8. execute and commit the prepared audit as one bounded execution change under
   the frozen population and inputs;
9. perform, record, and separately commit the full re-performance and
   comparison;
10. record and commit required human adjudications and any traceable
    pre-acceptance corrections without overwriting the prepared or reviewer
    determinations; and
11. create and separately commit an accountable human owner-acceptance or
    rejection record.

Do not embed AR-1.0's own SHA-256, Git blob identity, or enclosing commit in
this file. Those identities belong in the separate authorization record so
that the canonical method bytes remain non-self-referential.

Any textual change after authorization creates different method bytes and
requires a new method version or explicit versioned amendment, separate owner
authorization, and re-performance of every affected determination before work
resumes.

Until step 6 is complete, AR-1.0 remains not authorized and no B1.5 population
or result exists.

Disclosure inspection under step 4 shall include a full reading of the status,
rules, exceptions, burdens, adjudication boundaries, and completion language,
plus a search for credentials, private paths, usernames, session or transcript
identifiers, scratch locations, and conversational residue. Any change after
inspection requires new byte verification, hashes, and inspection before
commit or push.
