# Brownfield Transition Adversarial Development Record — Non-Controlling Working Material

> **Cover warning.** This is a conversation-derived development-history record. It is non-controlling, is not a governing specification, and does not supersede Volumes 1–3. It does not have the same provenance as the repository-derived reconciliation matrix. Candidate mechanisms are preserved as questions exposed during adversarial development, not as adopted architectural rules.

## Part A — Purpose and provenance

This record preserves the development questions exposed during adversarial review of *Govern From Known Reality Outward — A Brownfield Transition Method for AI-Mediated Enterprise Operations* (the registered frozen PDF; see its [registration record](2026-08-22-brownfield-transition-paper-registration.md)).

- **Why the adversarial development occurred:** while developing the Brownfield Transition Method, its control mechanics were pressure-tested adversarially — candidate control mechanisms, boundary determinations, handoff semantics, and authority rules were proposed, attacked, and refined before the paper was finalized. The paper itself records the outcome discipline in §3: detailed control questions exposed during development "are maintained separately as working material" and "should not acquire normative force unless reconciled against" the controlling architecture.
- **Where it occurred:** the underlying discussion occurred in conversation, outside the repository. No repository artifact captured it contemporaneously.
- **What this record is:** a retrospective preservation (recorded 2026-08-22) of the development *questions*, their origins as far as the surviving records identify them, and how each was disposed of by the repository-derived reconciliation performed on 2026-08-22 (the [preserved Current-Truth Review](2026-08-22-current-truth-review-preserved.md), Atomic Reconciliation Matrix rows B-01…B-64). It does not reconstruct conversational turns and does not restate candidate mechanisms as rules.
- **Origin-code preservation limit:** the adversarial review used lettered finding codes (e.g., C1, D1, D2, H5, J4). Only the codes explicitly carried into later records survive; where an entry's original codes were not preserved, the entry says so and the durable cross-reference is the matrix B-row ID.

## Part B — Questions exposed

### Boundary sufficiency determination

- **Question:** What artifact must record why the discovered operating and downstream-effect boundary is adequate for the proposed use and consequence — and must it be retained, reasoned, versioned, and reconsidered when assumptions change?
- **Origin finding(s):** codes not preserved; boundary-sufficiency sequence of the adversarial review (paper §9 carries the resulting "Boundary Sufficiency Determination" vocabulary).
- **Why it arose:** brownfield discovery produces partial knowledge of legacy operations; the review pressed on when partial characterization is *enough* to proceed and who must say so on the record.
- **Reconciliation disposition:** matrix rows B-01 (CLARIFIES — the determination function already exists via Volume 1 §4 boundary-adequacy review plus Record C's materiality rationale; a *named* standalone artifact would be a view of Record C), B-02 through B-05 (IMPLEMENTS — retention, rationale, reconsideration on material change, versioning are explicit record obligations), B-06 (IMPLEMENTS).
- **Controlling reference:** Volume 1 §§4, 7–8, 15–16; Volume 2 §2.4, §8.3.
- **Remaining status:** no open question at volume level.

### Effect-boundary characterization and the disposition ladder

- **Question:** Must the downstream/effect boundary be characterized as its own object, and when an AI-mediated effect crosses into enterprise reality, what dispositions exist — proceed when sufficiently understood, contain, pass to another governed receiving operation, or stop?
- **Origin finding(s):** codes not preserved; operating/effect-boundary sequence of the review. The development vocabulary "characterized effect boundary" arose here.
- **Why it arose:** legacy downstream operations already exist and already receive effects; the review pressed on whether pre-existing operation auto-authorizes new AI-mediated effects and what the lawful dispositions are when understanding is partial.
- **Reconciliation disposition:** B-07 IMPLEMENTS; B-08 CLARIFIES (declared intended effect + effect-observation paths are required, but no distinct "effect boundary" object is defined); B-09, B-10, B-11 IMPLEMENTS; B-12 CLARIFIES (containment exists as control action and declared condition, not as a formal disposition rung); B-13 CLARIFIES (alternate-path substitution rules reach it — but see the handoff entries); B-14 IMPLEMENTS (the stop-outcome follows from the permitted-consequence-class ceiling).
- **Controlling reference:** Volume 1 §§7, 10–11, 13; Volume 2 §§5.1, 6.1, 7.2, 8.3; Volume 3 §§4, 6, 10, 12, 14, 22.
- **Remaining status:** no open question at volume level; "characterized effect boundary" remains development vocabulary only.

### Containment as a continuing claim

- **Question:** Is containment a governing mechanism in its own right, and is a relied-upon containment boundary a continuing claim requiring runtime evidence rather than a one-time validation?
- **Origin finding(s):** codes not preserved; containment sequence of the review.
- **Why it arose:** brownfield transition leans on containment while understanding matures; the review pressed on whether containment claims silently decay.
- **Reconciliation disposition:** B-15 CLARIFIES ("containment-before-capability" is a design principle, not a named standalone mechanism with its own artifact); B-16, B-17 IMPLEMENTS (runtime-maintained assumption treatment; loss of containment evidence creates a discrepancy).
- **Controlling reference:** Volume 3 §2; Volume 2 §§2.3, 5.1–5.2, 6.3, 17.3.
- **Remaining status:** no open question at volume level.

### Inter-operation handoff

- **Question:** When an AI-mediated operation passes consequential work into another enterprise operation, is the receiving operation simply part of the originating complete path, or can responsibility terminate at a separately governed receiving boundary?
- **Origin finding(s):** C1 / D1 / D2 review sequence.
- **Why it arose:** brownfield enterprises are chains of operations; the review pressed on where one operation's assurance responsibility ends. The development vocabulary "governed handoff" arose here.
- **Reconciliation disposition:** B-18 **QUESTION NOT REACHED** in the controlling volumes ("handoff" in the volumes is the Volume 1 → Volume 2/3 governed-target handoff, a different sense; the complete-path principle implies the receiver is inside the sending claim's path unless the claim structure is recomposed). Recovered Volume 1/3 Design Bases and FINAL Structural Outlines were also searched (2026-08-22) and did not answer the question. Related: B-19 IMPLEMENTS (the receiving boundary must be understood as actually operated); B-21 IMPLEMENTS and B-22 CLARIFIES (common-mode dependencies and handoff chains do not manufacture independence).
- **Controlling reference (adjacent machinery only):** Volume 3 §§3, 7, 10, 15–16, 22; Volume 1 §6, §16 (Record K); Volume 2 §9.1.
- **Remaining status:** open architectural question (gap register D-03). Not resolved here.

### Independent acceptance by a receiving operation

- **Question:** Must the receiving operation perform an independent acceptance — an authority act of its own — before consequential work passes to it, or is acknowledgment evidence sufficient?
- **Origin finding(s):** C1 / D1 / D2 review sequence.
- **Why it arose:** follows directly from the inter-operation handoff question: if responsibility could terminate at a receiving boundary, something at that boundary would have to accept.
- **Reconciliation disposition:** B-20 **PROPOSES EXTENSION** — Volume 3 §16 requires acknowledgment as *evidence* ("evidence of correct recipient, successful delivery, comprehensibility, valid authority, acknowledgment…"); an independent acceptance authority act by the receiver is a requirement beyond what the volumes establish. The recovered design authorities were also silent (2026-08-22 supplemental search).
- **Controlling reference:** Volume 3 §16 (acknowledgment-as-evidence only).
- **Remaining status:** open (gap register D-04): dispose via governed change or drop. Not adopted here.

### Qualified approval and approval laundering

- **Question:** What separates qualified human approval from nominal approval, and how does the architecture prevent nominal approvals — including approvals passed through intermediaries or re-sought until granted — from converting into legitimizing authority?
- **Origin finding(s):** codes not preserved; authority sequence of the review. The development vocabulary "approval laundering" arose here.
- **Reconciliation disposition:** B-24 IMPLEMENTS ("A recorded approval event alone is insufficient"; "An Approve button is not a sufficient human-control model"); B-25 CLARIFIES — the volumes name **authority laundering** and forbid its two forms (engineering converting capability into permission; management authorizing beyond evidence); the reviewer-shopping variant appears only in non-controlling doctrine ("Rejection is a receipt… cannot be shopped among reviewers," Doctrine §9), not in the volumes.
- **Controlling reference:** Volume 2 §11.1–11.2; Volume 1 §9; Volume 3 §12.
- **Remaining status:** no open question at volume level; "approval laundering" remains development vocabulary, not corpus doctrine.

### Restoration

- **Question:** When AI authority has been temporarily narrowed or suspended, what evidence and authorization are required before the prior authority can be restored?
- **Origin finding(s):** H5 / J4 review sequence.
- **Why it arose:** the review pressed on whether restoration is an administrative reversal or an evidentiary act — whether authority, once reduced, springs back.
- **Reconciliation disposition:** the repository-derived matrix found that Volume 3 §24 and related Volume 2 provisions already require affirmative qualification evidence and accountable authorization for restoration; classified **IMPLEMENTS** (B-27: "Restoration is not an administrative reversal. It requires adequate qualification evidence and accountable authorization"; "Administrative declaration alone is insufficient").
- **Controlling reference:** Volume 3 §24; Volume 2 §§6.3, 15.4.
- **Remaining status:** no open question at volume level.

### Governing team constitution, selection, and recusal

- **Question:** Do institutional determinations (boundary sufficiency, handoff acceptance, and similar) require a designated governing team of qualified personnel; how is such a body constituted for a particular operation; may the party seeking a favorable determination select it; and what conflict-disclosure and recusal obligations bind it?
- **Origin finding(s):** codes not preserved; governing-body sequence of the review (paper §13 carries the resulting governing-team paragraph).
- **Why it arose:** the review pressed on *who decides* — whether the method's determinations can be made by whoever benefits from them.
- **Reconciliation disposition:** B-30 IMPLEMENTS but only for governance of the assurance machinery itself (Volume 2 §15.1); B-31 **QUESTION NOT REACHED** (the volumes deliberately avoid mandating organizational structures; determinations are assigned to accountable-authority *functions*, no body is constituted); B-32 **QUESTION NOT REACHED** (anti-self-serving rules exist for claim scope and measure meaning but do not reach body selection); B-33 CLARIFIES (conflict disclosure is a declared-role obligation); B-34 **QUESTION NOT REACHED** (no recusal mechanism exists in the volumes). The recovered design authorities were also silent on all three QNR rows (2026-08-22 supplemental search).
- **Controlling reference (adjacent machinery only):** Volume 2 §15.1, §15.3, §11; Volume 1 §4, §9, §11, §13.
- **Remaining status:** open architectural questions (gap register D-05). Not resolved here.

### Detective controls represented as preventive boundaries

- **Question:** Must a control that can only detect after consequence be barred from being represented as a preventive boundary, and does the consequence class determine which is required?
- **Origin finding(s):** codes not preserved; control-timing sequence of the review.
- **Reconciliation disposition:** B-35, B-36 IMPLEMENTS (Preventive vs Post-Consequence Assurance; "it must not be represented as prevention"); B-37 CLARIFIES — consequence classification *scales* the requirement, but the volumes stop short of a rule that a consequence class mandates preventive control (some operations have no preventive window).
- **Controlling reference:** Volume 2 §§6.2–6.3, 19; Volume 1 §12; Volume 3 §4.
- **Remaining status:** no open question at volume level.

### Compensating attribution independence

- **Question:** When a legacy system cannot natively identify the acting agent and attribution is compensated around it, must the compensating attribution evidence be independent of the execution path it attests?
- **Origin finding(s):** codes not preserved; attribution sequence of the review.
- **Reconciliation disposition:** B-54, B-55, B-56 IMPLEMENTS; B-57 CLARIFIES — the general evidence-independence rule reaches attribution evidence claim/consequence-relatively; a *categorical* independence requirement for attribution specifically would extend the architecture and was not adopted.
- **Controlling reference:** Volume 2 §§4.1, 7.1–7.2, 8.1; Volume 1 §§3, 9; Volume 3 §§8–9, 16, 31.
- **Remaining status:** no open question at volume level.

## Development-vocabulary preservation

The following terms arose during adversarial development and were **not found verbatim** in the final Brownfield paper or anywhere in the project library (confirmed by full-tree content search, 2026-08-22). They must not be presented as existing corpus doctrine merely because they arose during review; they are preserved here only as development vocabulary attached to the questions that generated them:

- **governed handoff** — attached to the inter-operation handoff question (the paper's own phrasings are "the handoff" / "receiving operation," §11);
- **characterized effect boundary** — attached to the effect-boundary question (the paper's phrasing is "operating and downstream-effect boundary," §9);
- **approval laundering** — attached to the qualified-approval question (the paper's phrasing is "Qualified human approval," §12; the volumes' term is "authority laundering");
- other §41-specific control terminology from the review that is not present in the controlling corpus.

By contrast, **Boundary Sufficiency Determination** does appear in the frozen paper itself (§9) and is registered vocabulary of that non-controlling paper — not of the controlling volumes.
