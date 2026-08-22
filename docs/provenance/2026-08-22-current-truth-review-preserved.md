# Preserved Review Artifact — DRNT Current-Truth Review and Atomic Reconciliation Matrix — 2026-08-22

**Record type:** Dated, non-controlling review artifact, preserved 2026-08-22 as part of the corpus-provenance closure.

**Provenance:** The matrix below is machine-assisted repository analysis tied to named source artifacts and SHAs. It was produced on 2026-08-22 against these sources, each identified in its Section A by local clone and HEAD commit: `local-first-ai-orchestration` @ `2359ba6`, `drnt-federal-track-frozen` @ `fc6135a`, `local-first-ai-gateway` (`drnt-project`) @ `0b6603e`, `drnt_domain_fixtures_private` @ `cdf3f66`, and `Local-AI-Orchestrator` @ `6223e35`. Test executions reported in Section C were performed the same day without modifying any source. The text below is preserved from the review's final report. Local-environment details (absolute local paths and similar machine-specific references) have been generalized for publication; no finding, classification, quotation, or recommendation has been edited, reclassified, or updated for this preservation.

**Review status:** This matrix is a machine-assisted reconciliation performed against the identified repository sources and SHAs on August 22, 2026. Source quotations and repository facts are independently reproducible from those artifacts; classifications reflect a single analytical pass and have not been independently adjudicated. An IMPLEMENTS, CLARIFIES, PROPOSES EXTENSION, CONFLICTS, or QUESTION NOT REACHED classification should therefore be treated as a review finding, not as a governing determination.

**Supplemental note (added 2026-08-22, same day, after the review):** the recovered Volume 1 Engineering Design Basis, Volume 1 FINAL Controlled Structural Outline, Volume 3 Corrected Engineering Design Basis, and Volume 3 FINAL Controlled Structural Outline (see [`publications/recovered-source-authorities/`](../../publications/recovered-source-authorities/README.md)) were subsequently located and searched for the five relevant open questions — operation-to-operation governed handoff (B-18/D-03), independent receiving-operation acceptance (B-20/D-04), generalized governing-team constitution (B-31/D-05), selection of the governing body by interested parties (B-32/D-05), and recusal (B-34/D-05) — and were **silent on all five**. This strengthens the search basis for those findings; it does not convert QUESTION NOT REACHED findings into governing determinations. Note also that the review's D-01 and D-02 rows reflect repository state at review time; both were addressed later the same day (see the [Track A closure register](2026-08-22-track-a-closure-register.md)) — the rows below are preserved as written.

---

# DRNT Current-Truth Review — 2026‑08‑22

**Scope discipline:** Everything below is sourced from the actual repository files at the SHAs listed, and from test suites executed today without modifying any source. No repository was modified; all test outputs were written to a temporary location outside the repositories.

---

## A. Current‑Truth Inventory

**Repositories examined** (all local clones, working trees inspected, git state recorded):

| Repo (origin) | HEAD |
|---|---|
| `local-first-ai-orchestration` | `2359ba6` |
| `drnt-federal-track-frozen` | `fc6135a` |
| `local-first-ai-gateway` (= `drnt-project`) | `0b6603e` |
| `drnt_domain_fixtures_private` | `cdf3f66` |
| `Local-AI-Orchestrator` (historical precursor) | `6223e35` |

**Controlling manuscripts and status-claiming artifacts:**

| Item | Path | Status language (verbatim) | File commit | Date | Supersession/conflict |
|---|---|---|---|---|---|
| Project Plan v0.2 | `local-first-ai-orchestration/publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md` | "FROZEN ARCHITECTURAL BASELINE — August 2026"; "This document is the controlling corpus-level architecture"; "It supersedes the earlier July 2026 three-paper roadmap" | `47543cc` (2026‑08‑12) | Aug 2026 | None found. Note: source DOCX binary and its SHA‑256 were **not available** to the repo (MANIFEST: "not available from library transcription") — the Plan exists in-repo as transcription only. |
| **Volume 1 — Define Reality** | `…/controlled-architecture/Volume_1_Define_Reality_Final_Controlled_Manuscript.md` | "Final Controlled Manuscript — August 2026" | `47543cc` | Aug 2026 | None. Source DOCX SHA‑256 `90870d46…` recorded in MANIFEST; DOCX remains layout authority. |
| **Volume 2 — Evaluate Reality** | `…/controlled-architecture/Volume_2_Evaluate_Reality_Final_Controlled_Manuscript.md` | "Final Controlled Manuscript — August 2026" | `47543cc` | Aug 2026 | **Recorded identity ambiguity:** supplied DOCX filename said "Public_Edition" while the title page says Final Controlled Manuscript; MANIFEST resolves in favor of internal identity and discloses the discrepancy. |
| **Volume 3 — Connect Reality** | `…/controlled-architecture/Volume_3_Connect_Reality_Final_Controlled_Manuscript.md` | "Final Controlled Manuscript — August 2026" | `47543cc` | Aug 2026 | None. DOCX SHA‑256 `5148d369…`. |
| **Brownfield-transition paper** | — | — | — | — | **NOT FOUND.** The string "brownfield" (case-insensitive, with variants) appears in **zero files** across all five repositories and in the sweep of local out-of-repository document locations. No file is structured as a brownfield-transition paper. The nearest non-controlling material is *The Enterprise Under Test* doctrine v3, which never uses the term. Per task rules, I stop here for this item and classify the Part 3 mechanisms directly against Volumes 1–3. |
| Volume Design Bases & Controlled Structural Outlines | — | Cited as controlling *within* the volumes (Vol 1 Source Note: "The FINAL Volume 1 Controlled Structural Outline controls this manuscript's structure… The completed Volume 1 Engineering Design Basis controls the meaning and engineering content"; Vol 3 Source Note similar) | — | — | **NOT FOUND in any repository.** They exist only in the off-repo project library. The volumes cite controlling documents that are not committed anywhere I can verify. |
| Federal fixture — frozen canonical | `drnt-federal-track-frozen/docs/Federal_Scale_Orchestration_Test_Fixture_v1_2_1.docx` | "**v1.2.1 is the frozen Phase A baseline.**" (§0.2); LEDGER Entry 002: "CANONICAL" | `35a8f03` (2026‑07‑03) | Jul 2026 | Canonical. Blob SHA‑256 re-verified today = `d342eb87…` = ledger value. Derived MD (`797d55b3…`) also verified. |
| Federal fixture — publications copy | `local-first-ai-orchestration/publications/fixtures/Federal_Scale_Orchestration_Test_Fixture_v1.2.md` | "Version 1.2 — July 2026" | `47543cc` | Jul 2026 | **Superseded representation**: MANIFEST explicitly notes the v1.2 vs v1.2.1 difference and "does not silently reconcile" it. The frozen v1.2.1 docx governs the federal track. |
| Harness (frozen) | `drnt-federal-track-frozen/artifacts/drnt-harness-v1.1.zip` (+ derived `harness/` tree) | LEDGER Entry 001: "accepted / committed as Tier 3 synthetic harness deliverable"; README: "Tier 3 until reviewed and explicitly committed" (reviewed 2026‑07‑03) | `a49adc5` | Jul 2026 | Zip SHA‑256 re-verified today = `974ed410…` = ledger. |
| Tier 3 design-input note | `drnt-federal-track-frozen/design-inputs/tier3_design_input_note.md` | "Frozen as the design-input companion"; "does not reopen or amend the accepted… artifact" | `a49adc5` | Jul 2026 | Blob SHA‑256 re-verified = `c597d2f8…` = ledger. |
| Implementation testbed | `drnt-project` (origin `local-first-ai-gateway`), v0.2.2 | README: "If there is ever a conflict between a spec claim and what this repo contains, `STATUS.md` is the source of truth for what is actually built." | HEAD `0b6603e` | STATUS reconciled to `ebee130`, Jun 2026 | Untracked working-tree items: `orchestrator/test_evidence_predicate_harness.py`, `AGENTS.md`, `DRNT_Project_State_Snapshot_2026-06-04.md`, and **three zero-byte files `v2.md`/`v3.md`/`v4.md`** (stray, empty, Jul 3) plus a stray `config;C` directory. |
| Domain fixture | `drnt_domain_fixtures_private` | README: "synthetic operational test fixture"; not controlling | HEAD `cdf3f66` | Jun 2026 | `trace_001.json` is **untracked**. |
| Hierarchy declarations | `publications/README.md` ("Controlling order" 1→5), `publications/MANIFEST.md`, `docs/architecture/DRNT_Authority_Map.md`, `docs/adr/ADR-002` ("ACCEPTED … Supersedes earlier corpus-level framing where inconsistent"), `docs/legacy/README.md` | All consistent: Plan → Vol 1 → Vol 2 → Vol 3 → everything else non-controlling | `47543cc`+ | Aug 2026 | Internally consistent; no competing hierarchy found. |

**Hierarchy note for Part 2:** the repos place the frozen Plan *above* the volumes; per this task's instruction, classification below is against **Volumes 1–3 only**, with Plan/doctrine/fixture coverage recorded in a separate column.

---

## B. Atomic Reconciliation Matrix

Legend: **I** = IMPLEMENTS, **C** = CLARIFIES, **PE** = PROPOSES EXTENSION, **X** = CONFLICTS, **QNR** = QUESTION NOT REACHED. All quotes verbatim from the Final Controlled Manuscripts (paths in Section A). "Outside coverage" cites non-controlling material only (Plan / Doctrine v3 / Fixture v1.2.1); those never affect the classification.

### Boundary Sufficiency

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑01 | Boundary Sufficiency Determination exists as a governing artifact | "Review must test clarity, scope, logical structure, authority, consequence, **boundary adequacy**, assumptions, measurand, intended effect, and downstream evaluability." / Record C: "Boundary, included components, exclusions, and **materiality rationale**." | Vol 1 §4; Vol 1 §16 (Artifact C) | **C** | The determination function (adjudicating and recording boundary adequacy) is already required via §4 review + Record C. A *named* standalone artifact makes that obligation explicit without changing the architecture — the eleven records are "information obligations, not eleven mandatory systems" (§16). Action: none required; if a named artifact is desired, express it as a view of Record C. |
| B‑02 | Requirement to retain the determination | Record C lifecycle: "Versioned with boundary." Record J: "Historical state and effective-period integrity for material governed objects." | Vol 1 §16 | **I** | Retention of the boundary definition and rationale is an explicit record obligation. No action. |
| B‑03 | Record *why* the boundary is sufficient | Record C: "materiality rationale"; §8: Assumption Register "Exclusion rationale — Why the dependency remains outside the item." | Vol 1 §§7–8, 16 | **I** | Rationale on both the inclusion and exclusion sides is required. No action. |
| B‑04 | Reconsider the determination when assumptions materially change | "If that basis no longer holds, the exclusion and the claim-relative boundary must be reconsidered." / "Boundary changes materially → Version the item definition and dependent assumptions." | Vol 1 §19 (explicit-exclusion rule); §15 change-trigger table; §16 Record C trigger "Material dependency/boundary change" | **I** | Explicit. No action. |
| B‑05 | Versioning / effective-period treatment | Record C "Versioned with boundary"; "Assurance is time-bound… A valid conclusion about one version and period does not silently transfer to another." | Vol 1 §16; Vol 2 §2.4 | **I** | No action. |
| B‑06 | Assurance not exceeding characterized reality | "The evidence places an upper bound on what the organization can defensibly claim…" / "If the enterprise cannot supply required evidence, the resulting claim and authority must reflect that limitation rather than manufacture assurance." | Vol 2 §8.3; §1.4 | **I** | Core rule of the architecture. No action. |

### Operating and Effect Boundaries

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑07 | Characterized operating boundary | "A component belongs inside the claim-relevant system whenever its credible failure, omission, unmodeled behavior, or authority could change the truth, scope, or defensibility of the claim." + qualification bound to "Configuration + Scope + **Operating Conditions** + Effective Period." | Vol 1 §7; Vol 3 §4 | **I** | Claim-relative boundary + Vol 3 operating-envelope binding jointly require a characterized operating boundary. No action. |
| B‑08 | Characterized downstream/effect boundary | Intended effect "identifies the affected person, object, environment, or organizational state; required timing; acceptable tolerance; persistence or duration…" Boundary includes "execution and effect-observation paths." | Vol 1 §13; §7 | **C** | The volumes require declaring the downstream effect and including effect-observation paths, but do not define a distinct "effect boundary" object. Framing one clarifies existing obligations. No action required. |
| B‑09 | Historical reliability limited to demonstrated envelope | "Historical recurrence also has no authority-generating effect." / "A favorable result is therefore not a permanent attribute of an interface. It is an engineering conclusion bounded to a path, configuration, scope, condition, and time." | Vol 3 §6; §10; Vol 2 §2.4 | **I** | Explicit. No action. |
| B‑10 | Pre-existing legacy downstream operation does not auto-authorize a new AI-mediated effect | "Technical capability, repeated use, and model inference do not create purpose." / "Repeated operation does not create authority." / "technical availability does not become authority." | Vol 1 §§10–11; Vol 3 Abstract, §6 | **I** | Direct instance of the non-conversion rules plus Graduated Integration (movement "evidence-based, governed," Vol 3 §13). No action. |
| B‑11 | Effect path may proceed when sufficiently understood | Capability result "Available as required: The qualified path satisfies the defined requirement within the stated configuration, scope, operating conditions, and effective period" + activation under §12. | Vol 3 §4, §12 | **I** | No action. |
| B‑12 | Effect may instead be contained | Runtime Control "may pause, restrict, revoke, **isolate**, redirect, escalate, require authorization, or terminate"; reconciliation covers "**containment boundaries**"; envelope preserves "rollback or containment." | Vol 2 §6.1, §5.1; Vol 3 §14 | **C** | Containment exists as a control action and declared condition; enumerating it as a formal disposition rung clarifies but does not extend. No action. |
| B‑13 | Effect may pass to another governed receiving operation | "If an alternate path is used, its own qualification and limitations apply; the organization may not inherit the properties of the failed path by substitution." / "Alternative paths require a rule stating when substitution is valid." | Vol 3 §22, §10 | **C** | Passing an effect to a separately qualified path/operation is covered by alternate-path substitution rules; but see B‑18/B‑20 for what is *not* covered. |
| B‑14 | Otherwise, consequential AI-mediated effect stops | "If the independence required by the applicable consequence cannot be provided… The resulting claim, authority, assurance mode, and **permitted consequence class** must reflect the limitation rather than silently waive the requirement." | Vol 2 §7.2; §8.3; Vol 3 §1 ("the correct output is a limitation, not a weaker substitute") | **I** | The stop-outcome follows: an insufficiently understood consequential effect falls outside the permittable consequence class. Note the volumes route the *operational* decision through Accountable Authorization constrained by the evidentiary ceiling (§6.3), not an automatic halt mechanism. No action. |

### Containment

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑15 | Containment as a governing mechanism | "The required sequence is simple: define the governed reality; define the assurance burden; then design bounded execution and enterprise interaction. **This is containment-before-capability.**" | Vol 3 §2; Vol 2 §5.1, §6.1 | **C** | Containment appears as design principle, declared condition, and control action — not as a named standalone governing mechanism with its own artifact. Treating it as one clarifies. No action. |
| B‑16 | Containment as a continuing claim, not one-time validation | "Runtime-maintained assumptions therefore require observation and reconciliation during the period in which they matter." (containment boundaries are listed reconciliation subjects, §5.1) | Vol 2 §2.3, §5.1 | **I** | A relied-upon containment boundary is a runtime-maintained condition; the continuing-claim treatment is required. No action. |
| B‑17 | Loss of containment evidence triggers reconsideration/discrepancy | "Detection creates a discrepancy record…" / "Any interval during which preventive capability continued to be asserted without adequate support is itself a discrepancy and may become a finding." | Vol 2 §5.2, §6.3; §17.3 | **I** | The loss-of-evidentiary-support machinery covers containment claims. No action. |

### Handoff

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑18 | Governed handoff (operation → receiving operation) as a distinct mechanism | (none — "handoff" in the volumes means the Vol 1→Vol 2/3 governed-target handoff, Record K) | Vol 1 §16 (different sense); Vol 3 §§10, 15–16 (adjacent machinery) | **QNR** | The volumes govern composite paths, federated custody, and external/hybrid paths, but define no operation-to-operation handoff mechanism that transfers effect responsibility across a boundary. Adjacent machinery (complete-path principle, Vol 3 §3) implies any receiving operation is simply *inside* the sending claim's path unless the claim structure is recomposed under Vol 1 §6. Action: record as an architectural question (→ D‑03). |
| B‑19 | Receiving boundary understood as actually operated, not merely documented | "As-Operated describes how the implemented system actually participates and functions over time." / "A path that exists only because an experienced employee knows which office to call is still part of the as-operated enterprise if the organization relies on it." | Vol 3 §7, §5, §10 (qualification tests the actual path) | **I** | Whatever receives the effect is part of the complete path and must be qualified as operated. No action. |
| B‑20 | Independent acceptance by the receiving operation | (none) — nearest: hybrid intervention path "may require evidence of correct recipient, successful delivery, comprehensibility, valid authority, **acknowledgment**…" | Vol 3 §16 | **PE** | Acknowledgment is required as *evidence*; an independent *acceptance authority act* by the receiver is a requirement beyond what the volumes establish. Action: dispose as proposed extension via governed change or drop (→ D‑04). |
| B‑21 | Common-mode dependencies defeating nominal independence | "Two groups can be organizationally separate while depending on the same telemetry, identity service, comparison engine, model, schema, time source, or evidence store. If those shared dependencies can defeat the claim… organizational separation alone is insufficient." | Vol 2 §9.1, §16; Vol 3 §11 | **I** | Explicit. No action. |
| B‑22 | Chains of handoffs do not manufacture independence | Same §9.1 text; "A new path does not inherit the old path's qualification merely because it serves the same business function." | Vol 2 §9.1; Vol 3 §24 | **C** | The claim-relative independence rule reaches chained arrangements; stating it for handoff chains clarifies. No action. |

### Authority

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑23 | Progressive AI participation (observe → propose → consequential execution) | Graduated Integration modes 1–6: "Observe Only … Execute Higher-Consequence Actions Within Demonstrated Authority." "Movement between modes is evidence-based, governed, reversible…" | Vol 3 §13 | **I** | No action. |
| B‑24 | Qualified vs nominal human approval | "…authorization is meaningful only when the authorized person has adequate and comprehensible information, valid authority, sufficient time, and a practical ability to reject, modify, redirect, delay, cancel, or escalate… **A recorded approval event alone is insufficient.**" / "An Approve button is not a sufficient human-control model." | Vol 2 §11.1; Vol 1 §9 | **I** | No action. |
| B‑25 | Approval laundering | "This distinction prevents two forms of **authority laundering**. Engineering cannot convert technical capability into organizational permission. Management cannot authorize a capability beyond what the evidence demonstrates." | Vol 3 §12; Vol 2 §11.2 (authorization may not promote claim states) | **C** | The volumes name authority laundering and forbid its two forms; "approval laundering" (nominal approvals legitimizing action) is a named instance of §11.1 + §12. The *reviewer-shopping* variant exists only in non-controlling doctrine ("Rejection is a receipt… cannot be shopped among reviewers," Doctrine §9) — not in the volumes. No action required at volume level. |
| B‑26 | Authority narrows when evidence weakens | "Evidence Quality and Sufficiency → Maximum Defensible Claim and Authority Envelope." / "Material loss of independence, failed effect verification… requires restriction or requalification." | Vol 2 §8.3, §15.4; Vol 3 §13, §22 | **I** | No action. |
| B‑27 | Restoration of reduced authority requires affirmative evidence | "Restoration is not an administrative reversal. It requires adequate qualification evidence and accountable authorization." / "Administrative declaration alone is insufficient." | Vol 3 §24; Vol 2 §15.4, §6.3 | **I** | No action. |
| B‑28 | Recorded vs currently exercisable authority | Authority = "**Current** bounded warrant with… revocation, expiration, exhaustion, supersession…" Episodes bind "authority state"; activation binds "authority state… before use." | Vol 1 §3, §11; Vol 2 §2.1; Vol 3 §12 | **I** | No action. |
| B‑29 | Permanent human-operated / no-AI outcome as legitimate end state | "[Graduated Integration] does not assume that greater autonomy is the desired endpoint." / "Retirement is a legitimate engineering outcome." / "Integration is not the universal objective." | Vol 3 §13, §24, §16, §29 | **I** | No action. |

### Governing Body

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑30 | A governing body oversees the assurance machinery | "A governing body or equivalent accountable mechanism must oversee whether evaluation remains sufficiently independent, trustworthy, technically competent, professionally competent, properly bounded, adequately resourced, and aligned with organizational mission." | Vol 2 §15.1 | **I** | Established — but only for governance *of evaluators/assurance machinery*. No action. |
| B‑31 | A designated governing **team** as the maker of institutional determinations generally (boundary sufficiency, handoff acceptance, etc.) | (none — determinations are assigned to "accountable authority" functions; no body is constituted) | Vol 2 §11 (Accountable Authorization as an *interface*, not a body); Vol 1 §11 ("Seats are not mandatory departments") | **QNR** | The volumes deliberately avoid mandating organizational structures. Whether institutional determinations require a constituted team is not addressed. Outside coverage: Doctrine seat topology; Fixture "NSIA AI Governance Board" (§10.7) — both non-controlling. → D‑05. |
| B‑32 | Constitution of the body per operation; may the party seeking a favorable determination select it? | (none) — nearest: "A beneficiary of a narrow claim may not unilaterally exclude material failure paths." / "The function benefiting from a measure should not automatically become the sole authority over what the measure means." | Vol 1 §4, §13 | **QNR** | Anti-self-serving rules exist for claim scope and measure meaning, but panel/body selection is not addressed. → D‑05. |
| B‑33 | Conflict disclosure within the body | Role obligation: "Conflict / independence — Known limitations affecting the role." Evaluator governance considers "evaluator incentives." | Vol 1 §9; Vol 2 §15.3 | **C** | Disclosure of conflicts is a declared-role obligation; extending it to a governing body clarifies. |
| B‑34 | Recusal within the body | (none) | — | **QNR** | No recusal mechanism exists in the volumes. → D‑05. |

### Control Timing

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑35 | Preventive vs post-consequence distinction | "Preventive Assurance means… detection, routing, decision, and effective intervention can reliably complete before the applicable consequence becomes irreversible. Post-Consequence Assurance means prevention is no longer possible…" | Vol 2 §6.3 | **I** | No action. |
| B‑36 | Detective control must not be represented as a preventive boundary | "Post-Consequence Assurance remains valuable for reconstruction, accountability, remediation, and learning, **but it must not be represented as prevention.**" / "Preventive Assurance is itself a claim. Nominal design values are insufficient…" | Vol 2 §19, §6.3; Vol 3 §4 ("Available too late for preventive control") | **I** | No action. |
| B‑37 | Consequence determines which is required | Classification influences "consequence-window, human-review, independence, effect-verification, and evaluation effort" (Vol 2 column) — and "Some operations may have no practical preventive window at all." | Vol 1 §12; Vol 2 §6.2 | **C** | Consequence classification *scales* the requirement; the volumes stop short of a rule that a given consequence class *mandates* preventive control (they acknowledge operations with no preventive window). Stating "consequence determines which is required" clarifies the classification linkage; a hard mandate would extend. No action. |

### Evaluation

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑38 | Continuous independent evaluation | "Declared-model fidelity is continuing rather than a one-time administrative gate." Two loops; per-episode Independent Evaluation. | Vol 2 §3.2, §1.2, §9 | **I** | No action. |
| B‑39 | Level 1: actual operation vs declared model | "Loop 1—Operational Assurance—asks: Did this operation occur as authorized and intended…" (evaluation compares declaration with as-operated evidence) | Vol 2 §1.2; Vol 1 §17 | **I** | No action. |
| B‑40 | Level 2: appropriateness of declared model itself | "Loop 2 evaluates the operating model itself… whether accumulated operational evidence shows that the declared operating model remains appropriate." | Vol 2 §13.1 | **I** | No action. |
| B‑41 | Evaluator informs but does not replace governing judgment | "Independent Evaluation determines that evidentiary limit. Accountable Authorization decides what the organization permits within it." / Loop 2 "may not authorize its own recommendation." | Vol 2 §8.3, §13.4 | **I** | No action. |
| B‑42 | Governing judgment does not exempt subsequent independent evaluation | "Where the authorizing function materially contributes to the organizational claim, the authorizing function is itself part of the evaluated system." / Authorization "may not… grant technical closure." | Vol 2 §11.2, §10.2 | **I** | No action. |
| B‑43 | Human compensation/workarounds as part of operational performance | "A workaround, bypass, local practice, or recurring exception can become part of the Observed Baseline without becoming authorized." Loop 2 inputs include "differences between documented and actual practice." | Vol 2 §12.2, §13.2; Vol 3 §5 | **I** | No action. |
| B‑44 | Credible latent exposure as an evaluation/prioritization input | "Loop 2 must consider exposure and denominators, recurrence, correlation, severity, reversibility, consequence, operational history, novelty, and uncertainty… A rare event with catastrophic consequence may matter more than a frequent nuisance." (inputs include "near misses") | Vol 2 §13.3, §13.2 | **C** | Latent-exposure weighting is consistent with and largely contained in the §13.3 factor set; naming it as a distinct input clarifies. No action. |

### Materiality and Discrepancy

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑45 | Predefined materiality thresholds | "Threshold / decision rule — Prospective boundary where applicable." vs "Universal statistical or numerical thresholds are not prescribed here." | Vol 1 §13; Vol 2 §9.6, §5.1 | **C** | Prospective threshold discipline is required per measurand and for aggregation rules; a universal predefined-materiality-threshold regime for discrepancies is deliberately *not* prescribed. Clarifies within that split. No action. |
| B‑46 | Novel observations bearing on a governing assumption must be surfaced | Baseline Operational Observation exists to reveal "conditions not adequately represented by the current declaration"; "material divergence be detectable and represented before it is silently absorbed into normal practice." | Vol 2 §4.3, §5.1 | **I** | No action. |
| B‑47 | Negative-materiality determinations are recorded | "Detection creates a discrepancy record… Independent Evaluation later determines the discrepancy's evidentiary significance and whether it becomes an assurance finding." | Vol 2 §5.2–5.3, §10.1 | **C** | For detected/routed discrepancies, the evaluated disposition (including "not material") is part of the record machinery. A standing register of negative determinations for observations that never became discrepancies is not required — but the recorded-disposition core exists. No action at volume level. |
| B‑48 | Negative-materiality determinations reviewed in aggregate | Loop 2 consumes "false positives, false negatives…" and evaluator governance considers "false-positive and false-negative performance." | Vol 2 §13.2, §15.3 | **I** | Aggregate review of dismissals is an established input. No action. |
| B‑49 | Material changes trigger reconsideration | Change-trigger table (§15); "Material change breaks silent inheritance." | Vol 1 §15; Vol 3 §23; Vol 2 §2.4 | **I** | No action. |
| B‑50 | Discrepancies do not automatically rewrite declared reality | "Evidence may motivate change; it does not enact change." / "Repetition does not create legitimacy." | Vol 2 §14.1, §12.2; Vol 1 §15 | **I** | No action. |
| B‑51 | Open discrepancy requires a governed response | "[A material difference] must resolve through either an accountable, authorized, versioned change to the Declared Baseline or an explicit, owned, justified, and time-bounded exception." | Vol 2 §12.4, §10.2–10.3 | **I** | No action. |
| B‑52 | Unresolved conditions constrain or narrow what can continue | "Depending on the claim and consequence, the supported claim may be narrowed, become Indeterminate or Unsupported; authority may need restriction; assurance may become Post-Consequence only; or the underlying system may need to change." | Vol 2 §17.3, §8.3, §15.4 | **I** | No action. |
| B‑53 | Unresolved conditions must not normalize through delay/repetition | "An Assured Baseline may not persist as a tolerated shadow operating model." / "Administrative inaction does not convert an expired exception into continuing authority." / "repetition does not create legitimacy" (prohibited paths). | Vol 2 §12.4; Vol 1 §15 | **I** | No action. |

### Attribution

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑54 | Distinct agent attribution where the legacy system supports it | Evidence properties include "attribution"; "Identity is first-class evidence where needed to establish authorship, authority, decision, override, or material action." | Vol 2 §8.1; Vol 1 §9; Vol 3 §9 (identity binding) | **I** | Operationalizes the required attribution property. No action. |
| B‑55 | Compensating attribution around a legacy system that cannot natively identify the agent | "A UI agent may be the only practical interface to an old application and therefore require stronger target binding, credential limitation, visual-state checks, and separate effect observation." | Vol 3 §8, §16 | **I** | Compensating path design to satisfy required evidence properties is exactly Vol 3's job. No action. |
| B‑56 | Assurance claim must accurately state what the legacy application itself proves | "The design records that the legacy application's completion status cannot independently establish physical correction." / Acknowledgment: "not automatically evidence that execution or the intended external effect occurred." | Vol 3 §31; Vol 1 §3; Vol 2 §7.1 | **I** | No action. |
| B‑57 | Compensating attribution evidence independent of the execution path it attests | "[Independence] requires that the validity of the evidence not ultimately depend upon the unilateral authority of the component whose behavior the evidence is intended to establish." (Explicit execution/effect-path independence exists for *effect* evidence: Vol 2 §7.2.) | Vol 2 §4.1, §7.2 | **C** | The general independence rule reaches attribution evidence on a claim/consequence-relative basis; a categorical independence requirement for attribution specifically would extend. No action. |

### Remediation

| ID | Mechanism | Controlling text | Location | Class | Reason / Action |
|---|---|---|---|---|---|
| B‑58 | Evidence locates where the actual problem lives before intervention | "A recurring discrepancy may indicate a defective control, an obsolete rule, an incorrect consequence classification, a missing dependency, a bad schema, a training or workload problem, or an operation that has evolved beyond its declaration." + change cycle requires evaluation and hazard/impact analysis before deployment. | Vol 2 §13.2, §14.1 | **I** | No action. |
| B‑59 | Minimum necessary intervention rather than wholesale modernization | "The architecture therefore accepts heterogeneous reality rather than requiring the enterprise to rebuild itself around AI." / "Volume 3 explicitly rejects universal data-lake, universal identity, universal ontology, universal cloud, and universal connectivity requirements." | Vol 3 §32, §28, §2 | **I** | No action. |
| B‑60 | Re-evaluation after remediation | "Technical closure requires evidence demonstrating that the defined closure condition has actually been satisfied" (determined by Independent Evaluation); change cycle ends in "Effectiveness Evaluation." | Vol 2 §10.2, §14.1; Vol 3 §23 | **I** | No action. |
| B‑61 | Failed remediation does not justify maintaining expanded AI authority | "A finding may not be treated as resolved merely because corrective action was assigned…" + authority envelope bound by current evidence (§8.3). | Vol 2 §10.2, §8.3; Vol 3 §22 | **I** | No action. |
| B‑62 | Observed operational burden as a prioritization input | Loop 2 inputs include "evaluator-capacity limitations, and effects produced by the assurance system itself"; evaluator health includes "workload, backlog…" | Vol 2 §13.2, §15.3–15.4 | **C** | Burden on the *assurance* side is explicit; general operational burden as a remediation-prioritization input is consistent but not enumerated. Clarifies. No action. |
| B‑63 | Credible latent exposure defined by present evidence, not speculation | Non-claims: no "quantified risk reduction without evidence"; Loop 2 factors are evidence-based (§13.3). | Vol 2 §13.3, §19 | **C** | Consistent with the evidence discipline; not established as a defined mechanism. No action. |

### Added (discovered overlap)

| ID | Mechanism | Controlling text | Location | Class | Reason |
|---|---|---|---|---|---|
| B‑64 | Non-self-certification (no component is final authority over evidence of its own correctness) | "No single operational component becomes final authority over evidence used to establish its own correctness." | Vol 1 §21; Vol 2 §9.1; Vol 3 §29 | **I** | This is the spine the brownfield-style mechanisms hang on; it is explicit in all three volumes. |

**Coverage-outside-the-volumes summary** (non-controlling; never used for classification): the Plan (§§4–6, 11, 15, 21–24, 31, 34) restates most **I** rows at lower resolution. Doctrine v3 uniquely carries: rejection-shopping prohibition, seat topology, reconciling-seat-outside-measured-chain, adversarial-validation-as-routine. Fixture v1.2.1 uniquely instantiates: numeric WAL promotion/decay, independence-receipt schema, two-seat (meaning/action) authority, AI Governance Board as terminus. Where a QNR row above has doctrine or fixture coverage, that coverage is *not* coverage in the controlling architecture.

---

## C. Federal Fixture Current-State Report

**Versions (verified, not recalled):**
- Fixture: **v1.2.1**, "frozen Phase A baseline," canonical DOCX SHA‑256 `d342eb8766a7aae6e5f6e9d3ec8ee5fbceaf61b223ac0c4f0e154156da859878` — recomputed today from the committed blob; matches Ledger Entry 002. The publications repo carries a v1.2 transcription, with the discrepancy explicitly disclosed in MANIFEST.
- Harness: **drnt-harness-v1.1**, zip SHA‑256 `974ed41062f0…` recomputed today; matches Ledger Entry 001. Extracted tree is the derived form.
- Design-input note: blob SHA‑256 `c597d2f8…` matches ledger (working-tree hash also matches — the entry‑003 CRLF hardening is doing its job).

**Test execution (2026‑08‑22, Python 3.14.3, no sources modified, outputs written outside the repositories):**
- **Unit tests: 21/21 pass** (`python -m unittest discover -s tests`).
- **Scenario suite: 8/8 pass** — `ALL PASS: True`, run twice, all four output files **byte-identical** across runs, seed 20260703.
- **Chain verification:** the reproduced harness log head is `f72fbbb6d755a68a37e9b6371641ef3fdba7a3fb66a01616bf0d34adb3dc1955` — **exactly equal to the "Verified harness log head" in Ledger Entry 001**. Seven weeks after freeze, on a different machine run, the frozen harness reproduces its accepted state bit-for-bit. This is the strongest single piece of evidence in the federal track.
- I replicated `scripts/check.sh` behavior manually (runner ×2 + byte compare + independent chain verification) rather than running it in-place, because it writes `.check_run*` directories inside the repo working tree.

**Scenario verdicts and evidence quality** (what each would have to observe to fail / what it does not determine):

| Scenario (FI) | Verdict | Would fail if… | Satisfiable by construction? | Does not determine |
|---|---|---|---|---|
| Consensus capture (FI‑02) | gate `rejected` | gate returned accepted/downgraded for a shared-retrieval receipt with movement > 0 | No — exercises a real rule against generated adversarial input; the honest control (accepted) proves the predicate is conditional | Content-aware correlation: unanimous answers with distinct `basis` fields score 0.0 (documented proxy limitation) |
| Omitted egress event (FI‑09) | `omission` + incident | reconciler failed to flag a sentinel-only event beyond the window | No | Real sentinel independence — sentinels are synthesized by the same seeded generator (see structural limit below) |
| Clock-skew (FI‑20) | `resolved-false-positive`, no incident | window logic misclassified benign skew as omission | No | Behavior at real clock semantics; synthetic logical clock only |
| Missing external digest (FI‑10) | `unauthenticated-history` | verifier accepted a self-consistent chain without an external anchor | No | Anchor-store independence — the "external store" is an in-memory dict written by the runner |
| Sensor unavailable (FI‑11) | `downgraded`, work not blocked | gate blocked work or granted credit with no receipt | Partially — it tests the explicit `receipt is None` branch; valuable mainly as a regression pin | Whether downgrade semantics propagate to any promotion/consumer (no consumer exists) |
| Unsigned config change (FI‑19) | `refused` + dependent receipt `downgraded` | mutation drill accepted a change missing an artifact, or the quarantine path failed | No — composition of two components is genuinely exercised | Real signing (mock SHA-256 signature scheme, documented) |
| Honest consensus (control) | `accepted` | — positive control | — | — |
| Signed change (drill control) | `accepted` | — positive control | — | — |

**Structural evidence limit (applies to every scenario):** the producer stream, the sentinel streams, and the anchor store are all minted by `scenarios/generators.py` in one process. The suite demonstrates the *logic* of independence machinery; it cannot demonstrate independence *in operation*. The harness README states this itself ("this skeleton reads synthetic data it generated for itself") and correctly caps the claim at "demonstrated as standalone synthetic primitives."

**Replay/invariance claims demonstrated:** deterministic byte-identical replay across runs; hash-chain verification from genesis seed; head equality with the frozen ledger value; gate determinism (50-iteration test); no wall-clock reads.

**Independence requirements currently tested:** externally authored thresholds (`max_judgment_movement 0.34`, `max_movement_with_shared_retrieval 0.0`, `max_residual_error_correlation 0.5`); gate has no write path to its thresholds; receipt generator emits `gate_result: null` (only the gate fills it); reconciler/verifier evaluate only material they did not produce; runner-only file I/O verified by the check-script grep discipline.

**Implementation features relevant to the fixture (drnt-project v0.2.2, HEAD `0b6603e`):** hash-chained append-only audit writer, WAL capability registry/state/permission checker, context boundary, override/successor semantics, source-event replay guard, Phase 4A.2 proposal/review surface, and the completed egress remediation (app-level Bearer gate + network-topology isolation on `drnt-sandbox`, with `TestEgressBoundaryProbe` as regression guard). These are the [P] primitives the fixture's claim classes lean on.

**New, fixture-relevant, and uncommitted:** `orchestrator/test_evidence_predicate_harness.py` (untracked) compiles external fixture descriptors into the real CapabilityRegistry → PermissionChecker path. Run today: **7/7 pass** (demo descriptor); **14/14 pass** with `DRNT_FIXTURE_DIR` pointed at the domain fixture's `trace_001.json` (itself untracked in that repo). Its evidence encoding is self-labeled "TEMPORARY HARNESS ENCODING — NOT DOCTRINE" (WAL level stands in for evidence presence): it genuinely demonstrates deterministic conditional gating, default-deny for unregistered actions, and audit-event emission; it does not demonstrate evidence-predicate semantics as such.

**Later changes not represented in the frozen fixture (expected — the freeze is intentional):** egress remediation commits (`ebee130`→`0b6603e`), Phase 4A.2 review surface, the evidence-predicate harness, and the August 2026 controlled volumes themselves — the fixture (July) predates the Final Controlled Manuscripts (August).

**Requirements found satisfied and adequately evidenced (not gaps):** frozen-baseline integrity (all four ledger hashes re-verified); deterministic replay of the accepted harness state; the no-self-certification separation inside the harness at code level (gate/receipt/reconciler/anchor I/O separation confirmed by reading the modules); sensor-unavailable downgrade semantics; governed-mutation refusal on each missing artifact and on unauthorized signer; ledger append-only discipline (corrections as new entries, e.g., Entry 003 correcting Entry 002's CRLF exposure); the [P]/[S]/[F] discipline consistently carried in fixture, harness README, OVERVIEW, and Volume 3 §27.

**Working-tree hygiene observations (not gaps):** drnt-project root contains three **zero-byte** untracked files `v2.md`, `v3.md`, `v4.md` (Jul 3 — same day as the harness freeze; likely stray shell artifacts) and a stray `config;C` directory; the state snapshot and AGENTS.md are also untracked.

---

## D. Gap Register

One atomic gap per row; exact evidence cited. Satisfied requirements are in Section C, not here.

| ID | Category | Gap | Evidence |
|---|---|---|---|
| D‑01 | EVIDENTIARY GAP | The brownfield-transition paper — the source of the mechanism vocabulary this review was asked to reconcile ("Boundary Sufficiency Determination," "governed handoff," "approval laundering," etc.) — exists in no repository. The mechanisms cannot be traced to any committed text, controlling or otherwise. | Grep for `brownfield` (case-insensitive): zero matches across all five repos; no file with that framing found in the sweep of local out-of-repository document locations. |
| D‑02 | EVIDENTIARY GAP | The Volume Engineering Design Bases and Controlled Structural Outlines are cited *by the volumes themselves* as controlling their structure and meaning, but are not committed to any repository — the controlling chain cannot be independently verified end-to-end. | Vol 1 Source Note ("The FINAL Volume 1 Controlled Structural Outline controls this manuscript's structure… The completed Volume 1 Engineering Design Basis controls the meaning"); Vol 3 Source Note; file inventory of all repos contains no such files. Same class of issue as the Plan's missing source DOCX (MANIFEST: "not available from library transcription"). |
| D‑03 | ARCHITECTURAL QUESTION | Whether an operation-to-operation **governed handoff** exists as a distinct mechanism — or whether a receiving operation is always simply inside the sending claim's complete capability path (or a registered constituent-claim boundary) — is not answered by the volumes. | B‑18: no controlling text; adjacent machinery Vol 3 §3, §10, §22; Vol 1 §6. |
| D‑04 | ARCHITECTURAL QUESTION | Whether **independent acceptance by a receiving operation** is required (as an authority act, not just acknowledgment evidence) is not established; the mechanism as reviewed would add a requirement. | B‑20: Vol 3 §16 requires acknowledgment as *evidence* only. |
| D‑05 | ARCHITECTURAL QUESTION | Constitution of a governing body for institutional determinations beyond evaluator governance — team composition, whether the favor-seeking party may select it, and recusal — is unaddressed. | B‑31, B‑32, B‑34: Vol 2 §15.1 covers only oversight of the assurance machinery; Vol 1 §4/§13 anti-self-serving rules do not reach body selection or recusal. |
| D‑06 | IMPLEMENTATION GAP | The five-state organizational claim-state machinery (Supported/Narrowed/Indeterminate/Unsupported/Contradicted-Failed) has no running component and no fixture/harness evidence anywhere. | Vol 2 §9.3 requires it; Vol 3 §27 lists Independent Evaluation Service as [S]; no harness or gateway component assigns claim states (harness verdicts are gate/reconciler/anchor states, not claim states). |
| D‑07 | IMPLEMENTATION GAP | Effect verification through an independently defensible effect-observation path is demonstrated nowhere. | Vol 2 §7.2 requires it; no [P] primitive, harness scenario, or gateway test observes an external effect independently of an execution path. |
| D‑08 | IMPLEMENTATION GAP | Consequence-window / preventive-latency validation (the `L_detection + … < W_irreversible` condition and its periodic revalidation) has no implementation or measurement anywhere. | Vol 2 §6.2–6.3; fixture latency SLOs are explicitly "[F] until measured under synthetic load" (fixture §4.5); no load harness exists. |
| D‑09 | IMPLEMENTATION GAP | The three-baseline machinery (Declared/Observed/Assured) has no implementation or fixture evidence. | Vol 2 §12; nothing in the harness, gateway, or fixture campaign exercises baseline establishment or exception expiry. |
| D‑10 | IMPLEMENTATION GAP | The six capability results (Available as required … Unavailable) are not implemented in any runtime or exercised by any test. | Vol 3 §4; the vocabulary appears only in prose. |
| D‑11 | EVIDENTIARY GAP | Harness independence evidence is generated by the same process it evaluates: producer stream, sentinel streams, and the external-anchor store are all minted by `scenarios/generators.py` under one seed. Logic is demonstrated; independence-in-operation is not. | `harness/README.md` ("reads synthetic data it generated for itself"); code reading of `generators.py`/`runner.py`; acknowledged, but still the boundary of what the passing suite proves. |
| D‑12 | EVIDENTIARY GAP | Gate verdicts are logged without cryptographic binding to the receipt content evaluated — "changing the control's judgment content did not change the log head." An auditable decision, not yet auditable evidence. | `design-inputs/tier3_design_input_note.md` Observation 1 (frozen, held open for the 6B pass). |
| D‑13 | EVIDENTIARY GAP | The residual-correlation proxy clusters whole judgment objects; unanimous answers with distinct `basis` fields score 0.0 — a documented false-negative class in the independence measurement. | Design-input note Observation 2; `harness/README.md` "Known proxy limitation." |
| D‑14 | EVIDENTIARY GAP | Gate threshold boundary equality is untested: `evaluate` uses strict `>` comparisons, and no test pins behavior at `judgment_movement_score == max_judgment_movement` (without shared retrieval). | `harness/gate.py` (`movement > thresholds[...]`); `tests/test_harness.py::TestGate` covers 0.0, 0.34-with-shared-retrieval, and 0.9-correlation only. |
| D‑15 | EVIDENTIARY GAP | No traceability mapping exists from the frozen fixture's tested invariants (§19.2 matrix) to Volumes 1–3 requirements. The fixture predates the August volumes and traces only to DRNT invariants; passing the fixture/harness is therefore not evidence of conformance to the controlling architecture. | Fixture §19.2 (traces invariant→mechanism→evidence→check→injection, no volume column); volumes dated August 2026 vs fixture frozen July 3, 2026. |
| D‑16 | FIXTURE DEFECT | The frozen fixture's [S] gloss — "no running component anywhere (the independent evaluator, the independence sensor and gate…)" (§0.1) — is stale as a description of the present world: since July 3 the harness runs the sensor/gate/reconciler as standalone synthetic primitives. The claim *tier* was correctly not promoted; the descriptive sentence inside the frozen text no longer matches reality, and the ledger has no entry recording the supersession of that sentence. | Fixture §0.1 vs Ledger Entry 001 + today's re-execution; correction path is a new ledger entry (the ledger is append-only; the frozen doc must not be edited). |
| D‑17 | EVIDENTIARY GAP | The evidence-predicate harness (`orchestrator/test_evidence_predicate_harness.py`, drnt-project) and the domain-fixture descriptor (`trace_001.json`) are **untracked** — 14 passing tests whose evidence exists only in working trees, outside version control and outside STATUS reconciliation. | `git status` in both repos (`??` entries); tests executed and passing today. |
| D‑18 | EVIDENTIARY GAP | That harness encodes evidence presence as a WAL level (present→WAL 1, absent→WAL 0). It demonstrates deterministic WAL gating driven by a fixture descriptor — not evidence-predicate semantics. The limitation is self-declared. | Docstring: "TEMPORARY HARNESS ENCODING — NOT DOCTRINE: the WAL level stands in for evidence presence…" |
| D‑19 | ARCHITECTURAL QUESTION | Where the independence-receipt hash must be bound — producer-side record, evaluator-side record, or both — is an open determination the controlling architecture does not resolve at its altitude (it states functional integrity/independence properties only); explicitly carried as a 6B design question. | Design-input note "Design question for 6B"; Vol 2 §17.1 (functional specification only). Under the frozen change-control taxonomy this would be handled as a Volume 3 Integration Issue, not a baseline reopening. |

---

## E. Recommended Next Actions

All recommendations trace to rows above; nothing else is recommended. Per the task, no new architecture is drafted for the Question-Not-Reached items.

**Corpus corrections**
1. **Resolve the brownfield-paper provenance before any further reconciliation work.** Either commit the current brownfield-transition paper into the corpus (as non-controlling supporting material, per the hierarchy), or record formally that its mechanisms are proposals with no committed source. Until then, every future "reconcile the brownfield review" task rebuilds its subject from memory — the exact failure mode this corpus exists to prevent. (D‑01)
2. **Commit or hash-register the Design Bases and Structural Outlines** the volumes cite as controlling their structure and meaning — or add a MANIFEST note recording their library-only status and SHA‑256s, as was done for the Plan DOCX. (D‑02)

**Fixture corrections**
3. **Append a ledger entry** (never edit the frozen doc) recording that fixture §0.1's "no running component anywhere" gloss is superseded by the harness v1.1 acceptance for the sensor/gate/reconciler/anchor primitives, with claims held at "standalone synthetic primitives." (D‑16)

**Implementation work** (staged, per existing program sequencing — these are the [S] mechanisms the program itself says carry the critical load now)
4. Prioritize, in the 6B lifecycle-feasibility pass, the first non-synthetic step for the IES reconciler: reading a record it did not generate (e.g., the gateway's real audit chain), which converts D‑11's structural limit into demonstrable independence. (D‑11, D‑06)
5. Keep claim-state machinery, effect verification, consequence-window measurement, three-baseline machinery, and capability-result vocabulary on the explicit not-yet-built register rather than allowing prose coverage to stand in for them. No build order is implied by this review beyond what the findings support. (D‑06 … D‑10)

**Evidence / test additions**
6. **Commit the evidence-predicate harness and the domain-fixture `trace_001.json`** (or deliberately discard them) — 14 passing tests should not live untracked; if committed, reconcile STATUS.md. (D‑17)
7. Replace or supplement the WAL-as-evidence encoding with a real evidence-predicate representation before any claim about "evidence-gated execution" is made from that harness. (D‑18)
8. Add a gate boundary-equality test pinning `movement == max_judgment_movement` behavior — as a new test in a future harness version, not an edit to frozen v1.1. (D‑14)
9. Carry the receipt-binding invariant and content-aware correlation into the 6B pass as already planned in the frozen design-input note. (D‑12, D‑13, D‑19)
10. **Author a volumes↔fixture traceability map** (volume requirement → fixture invariant → harness evidence, with explicit "no evidence" cells) so that fixture passes stop being mistakable for architecture conformance. Section B of this report is a starting inventory. (D‑15)

**Unresolved architectural questions** — record for governed disposition; do not resolve by drafting
11. Inter-operation handoff and receiving-operation acceptance (D‑03, D‑04; B‑18, B‑20).
12. Governing-body constitution, selection by interested parties, and recusal (D‑05; B‑31, B‑32, B‑34).
13. Independence-receipt binding locus (D‑19).

---

**Bottom line.** The controlling corpus is real, coherent, and internally consistent: a frozen Plan, three Final Controlled Manuscripts, and an explicit hierarchy, with the brownfield paper and the volume Design Bases as the only NOT FOUND items. Of the 64 atomic mechanisms reconciled, 44 are already IMPLEMENTS, 14 CLARIFIES, 1 PROPOSES EXTENSION, 5 QUESTION NOT REACHED, and **none conflict** with the volumes — the brownfield-review vocabulary is overwhelmingly a restatement of what Volumes 1–3 already establish, with the genuinely open ground concentrated in handoff acceptance and governing-body constitution. The frozen federal harness reproduced its accepted state byte-for-byte today (21/21 unit tests, 8/8 scenarios, log head identical to Ledger Entry 001), and every ledger hash re-verified. The real gaps are evidentiary, not architectural: the passing suites prove component logic on self-generated data, the [S] tier of the architecture remains unbuilt everywhere, and two passing test files sit untracked outside version control.