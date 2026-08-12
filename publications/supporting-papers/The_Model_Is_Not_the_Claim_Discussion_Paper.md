# The Model Is Not the Claim

*The Missing Assurance Rule for Critical AI Operations*

**Lawrence Jeffords — End-User and Operator**

**Status:** Public Discussion Paper — 10 August 2026

> **Controlled-text note:** This Markdown file is a repository transcription derived from the controlled DOCX supplied for the August 2026 update. The source DOCX remains authoritative for exact layout, tables, figures, and publication identity; its SHA-256 is recorded in the [Publication Manifest](../MANIFEST.md).

---

## 1. Executive summary

Academic, government-supported, and industry publications are converging on an important point: reliable AI operations are not secured by model performance alone. The STRAICS review places AI-enabled clinical decision support inside a socio-technical ecosystem and emphasizes resilience, workflow, monitoring, adaptation, and organizational learning. The MITRE report prepared for NIH defines a system broadly enough to include people, processes, hardware, software, and facilities, and reports that three exploratory pilot teams had limited ability to substantiate responsible-AI claims. EPC Group supplies concrete lifecycle controls: approval roles, staged validation, registries, event logs, monitoring, incident records, retirement, and rollback. IQVIA emphasizes augmentation of skilled organizations, institutional knowledge, competency, manual fallback, patient benefit, safety signals, overrides, near misses, and review of samples accepted by the AI—not only cases it flags.

These are valuable contributions to different parts of a larger assurance problem. They should not be treated as competing attempts to solve an identical problem, and this paper does not suggest that any author or vendor copied or adopted my work. Taken together, the reviewed public documents support a bounded inference: they do not supply a general rule for deciding when such components collectively support the particular organizational claim being asserted. This paper does not infer that no proprietary, confidential, sector-specific, or other public method exists.

The proposed rule is claim-relative. The evaluation boundary must include every human, model, agent, controller, data source, authorization, tool, workflow, dependency, and external effect necessary to establish the claim. A narrow claim—“model version M achieved sensitivity S on dataset D”—may justify a narrow boundary. A consequential claim—“the hospital validly identified a patient’s elevated sepsis risk and caused an authorized, timely, clinically appropriate intervention”—requires evidence from the patient record through the model, routing, clinician, order, pharmacy, nursing, administration, patient response, and independent reconstruction.

Four propositions must therefore remain separate:

1.  the organization possesses governance controls;
2.  those controls operated effectively in the relevant operation;
3.  the complete organization acted within valid authority; and
4.  independent evidence establishes that the claimed external effect occurred.

The proposal is an assurance method, not a product, licensing scheme, or guarantee. Its rigor should scale with consequence, reversibility, uncertainty, and the breadth of the claim—not with whether the technology is called AI, machine learning, an agent, automation, or software.

## 2. Standing, purpose, and evidence limitations

I write as an experienced end-user and organizational operator, in my personal capacity—not for a vendor, regulator, academic institution, healthcare organization, or standards body. The operational question is: what evidence would justify reliance on an organization’s consequential AI claim?

The four public sources form a convergence record, not a settled framework. STRAICS is a peer-reviewed conceptual Review; peer review does not prove effectiveness, and its authors call for validation ([Al-Horani & Tadesse, 2026, pp. 11–12](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2026.1870819/pdf#page=11)). CORAL is a MITRE report prepared for NIH under contract; its notice disclaims official-government status absent separate designation ([Kapusta et al., 2026, PDF pp. 2–4](https://datascience.nih.gov/sites/g/files/mnhszr336/files/2026-04/impact_report_March2026_final_posting.pdf#page=2)). EPC and IQVIA are commercial publications; useful guidance does not convert their outcome or capability assertions into independently established evidence.

The supplied Jeffords corpus controls the proposed method. The requested *Performance-Based Assurance for AI-Mediated Operations: A Framework for Technology-Neutral Governance* was not located; the closest source, *Integrated Draft 0.4*, is cited only by its actual title. Simulations and artifacts can instantiate propositions or controls but do not establish sector-wide effectiveness, compliance, or superiority. A supplied conformance audit expressly excludes theoretical validity; its title alone does not establish examiner independence. *A Book of Rules for Autonomous Systems* intentionally names no author and is cited by title.

Evidentiary terms are deliberate: “the paper states” attributes; “the vendor reports” identifies a commercial assertion; “the documents suggest” marks inference; “the proposed method would require” identifies methodology; and “independently established” requires examination outside the evaluated correctness claim.

Absence from a public document is not evidence that a control does not exist internally. No conclusion is drawn here about confidential systems, evaluations, or evidence that were not reviewed. Conversely, an assertion that evidence exists privately does not, by itself, substantiate a claim made to a critical organization, regulator, insurer, patient, or other relying party.

### Regulatory status note

Legal status was verified on 10 August 2026. In the United States, the HIPAA Security Rule governs covered entities and business associates handling ePHI under 45 C.F.R. Parts 160 and 164, Subparts A and C. HHS describes it as scalable and technology-neutral; the regulatory text controls ([eCFR](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C); [HHS](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)). FDA’s January 2025 AI-device lifecycle document remains draft; its August 2025 PCCP document is final guidance for specified device-software submissions. Neither guidance itself creates legally enforceable responsibilities ([FDA draft](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/artificial-intelligence-enabled-device-software-functions-lifecycle-management-and-marketing); [FDA PCCP guidance](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/marketing-submission-recommendations-predetermined-change-control-plan-artificial-intelligence)). In the EU, Regulation 2024/1689 became generally applicable 2 August 2026, subject to exceptions and amendment; Annex III high-risk requirements apply 2 December 2027 and Annex I product-embedded requirements 2 August 2028 ([consolidated Act](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX%3A02024R1689-20260727)). Actor, intended purpose, use, jurisdiction, classification, and date control. NIST’s AI RMF is voluntary, not law or certification ([NIST](https://www.nist.gov/itl/ai-risk-management-framework)).

## 3. Evidence of convergence

### STRAICS: the clinical ecosystem, not an isolated model

STRAICS places adaptive clinical decision support inside workflow, human-machine collaboration, organizational learning, and clinical conditions. Its continuous frame includes drift, calibration, versioning, cognitive burden, response latency, overrides, incident learning, graceful degradation, recovery, and adaptation. It distinguishes governance structures from functional risk mitigation and recommends simulation, direct observation, and near-miss/adverse-event analysis ([Al-Horani & Tadesse, 2026, pp. 5–8](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2026.1870819/pdf#page=5)).

The article illustrates sepsis monitoring and reframes audit around the AI-enabled healthcare system ([pp. 10–12](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2026.1870819/pdf#page=10)). But this narrative/conceptual Review reports no search strategy, evidence grading, systematic appraisal, STRAICS trial, comparator, or outcome dataset; it calls for validation. STRAICS evidences conceptual convergence, not improved outcomes.

### MITRE CORAL report prepared for NIH: shared assurance capacity

CORAL defines a system to include processes, hardware, software, facilities, and people ([Kapusta et al., 2026, report p. 6, PDF p. 11](https://datascience.nih.gov/sites/g/files/mnhszr336/files/2026-04/impact_report_March2026_final_posting.pdf#page=11)). From stakeholder engagement and three selected exploratory pilots on explainability, human-AI interaction, and calibration, it describes fragmented practices and scarce shared resources. All three pilots reportedly had limited ability to substantiate responsible-AI claims ([report pp. 17–24, PDF pp. 22–29](https://datascience.nih.gov/sites/g/files/mnhszr336/files/2026-04/impact_report_March2026_final_posting.pdf#page=22)).

The proposed CORAL resource would supply playbooks, benchmarks, methods, tools, training, and real-world investigations. The pilots remain exploratory and practices preliminary; the report requires more real-world evidence before claim substantiation and playbook validation ([report p. 24, PDF p. 29](https://datascience.nih.gov/sites/g/files/mnhszr336/files/2026-04/impact_report_March2026_final_posting.pdf#page=29)).

### EPC: concrete lifecycle mechanics

EPC translates governance into committees and owners; technical, clinical, and shadow-deployment validation; registries and version history; detailed event classes; drift thresholds; incident records; retirement; and rollback ([O’Connor, 2026, cited sections](https://www.epcgroup.net/blog/ai-governance-healthcare-hipaa-guide)). These can supply raw material for approval, monitoring, investigation, and reconstruction.

The commercial page reports 50-plus implementations, audit/compliance results, and 90 percent fewer AI-related patient-safety incidents, but publishes no cohort, baseline, denominator, incident definition, period, comparator, assessor, or analysis. These are vendor reports—not fraud allegations or established effects. Its specified cryptography, AI-event schemas, and annual review cadence are EPC practices, not technologies, records, or intervals prescribed by the technology-neutral Security Rule ([eCFR](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C)). This says nothing about confidential support; only that the public page does not independently substantiate the results.

### IQVIA: the human and outcome edge

IQVIA presents AI as an amplifier, not a substitute for skilled judgment. Its “competency cliff” warns that automating routine work while experts retire may remove the experience that forms future judgment. It recommends mentorship, rationale capture, graduated responsibility, complex-case exposure, and manual fallback ([Flanagan, King & Reed, 2026, printed pp. 3, 9–12; PDF pp. 4, 10–13](https://www.iqvia.com/-/media/iqvia/pdfs/library/white-papers/2026/building-trust-safety-and-value-in-regulated-healthcare-systems.pdf#page=4)).

It makes patient benefit the objective and recommends monitoring errors, adverse events, near misses, overrides, safety-signal response, corrective action, and results. Its manufacturing vignette tests samples the AI accepted, not only flagged items ([printed pp. 17–20; PDF pp. 18–21](https://www.iqvia.com/-/media/iqvia/pdfs/library/white-papers/2026/building-trust-safety-and-value-in-regulated-healthcare-systems.pdf#page=18)). No method, dataset, comparator, references, or independent validation is reported. Recommendations and vignettes are not demonstrated outcomes; that conclusion does not deny possible confidential evidence.

### What the convergence does—and does not—show

Across the four reviewed sources, documented convergence includes socio-technical scope, lifecycle controls, human competence, monitoring, auditability, incident learning, adaptation, and outcome measurement. The sources vary in purpose and maturity; no claim is made that they share terminology or a lineage. The cross-document inference is narrower: none provides a general, claim-relative rule that (a) selects every material component according to the exact organizational claim and (b) determines whether the combined evidence supports that claim. That is not a defect if the problem falls outside a source’s scope. It is the integration problem identified in this reviewed public record.

## 4. The unresolved claim-to-evidence problem

A claim creates its own evidentiary burden. The claim “this model predicted elevated sepsis risk” concerns a model, inputs, intended use, threshold, and performance context. “The hospital responded appropriately” concerns an organization: identity, admissible data, routing, authority, human capability, policy, orders, pharmacy, nursing, devices, time, execution, patient response, adverse effects, and recovery. Evidence adequate for the first claim cannot be silently promoted into evidence for the second.

The proposed boundary rule is:

> > *Include every element whose credible failure could change the truth, scope, or defensibility of the claim.*

The boundary therefore expands or contracts with the claim. It is not fixed around the model, agent, lifecycle, vendor platform, department, or legal entity. Excluded dependencies must be expressed as bounded assumptions and tested sufficiently to justify the exclusion. If a material assumption cannot be observed, the result is not automatically a narrower claim. The final claim finding may be “Narrowed” only when affirmative evidence supports a precise smaller proposition; otherwise it is “Unsupported.” “Pending” is temporary while a defined evidence window remains open; “Indeterminate” describes an effect state, not a final claim verdict (Jeffords, *Attachment A: What We Are Actually Proposing*, 2026, pp. 14–18, 28–29).

Key terms are used as follows:

- **Model:** a mathematical or computational component that produces an inference, prediction, representation, or output.
- **Agent:** a software actor that can retain state, select steps or tools, and pursue an assigned objective.
- **System:** the set of components inside the chosen evaluation boundary.
- **Operation:** one as-performed sequence undertaken for a defined purpose under a specific context, authority, policy, staffing, and configuration.
- **Claim:** the exact proposition for which a claimant seeks justified reliance.
- **Authority:** a current, bounded operational permission binding a source and holder to the particular operation and consequence. It may arise from law, regulation, contract, clinical privilege, organizational policy, or valid delegation. The method does not create authority; it tests the asserted source and its applicability.
- **Control path:** the as-operated route through decision points and mechanisms able to permit, constrain, redirect, stop, contain, or recover the operation.
- **Effect:** the consequence outside the executing operation after proposal, authorization, dispatch, and execution. A verified effect is an effect established through the declared evidence method and observation window. Event-chain binding is distinct from causal attribution of a population outcome.
- **Independent evaluation:** a competent verdict whose validity is not controlled by the actor or mechanism whose correctness the verdict asserts.

In this paper, **admissible context** means information permitted and fit for the stated operation under applicable authority, policy, consent, contract, and law; it does not necessarily mean evidence admissible in litigation.

This vocabulary prevents four different demonstrations from collapsing into the word “governed.” A control inventory shows possession. Event evidence can show operating effectiveness. Authority evidence can show the organization was permitted to act. External observation can show whether the claimed effect occurred. Each may fail while another succeeds.

## 5. Necessary controls versus sufficient assurance

Common governance mechanisms are necessary, valuable, and non-self-proving:

- A model registry provides evidence of what was registered, not that the registered model remained appropriate in the actual operation.
- A signature provides evidence that someone signed, not that the signer possessed current authority or sufficient evidence.
- A log provides evidence of what the logging system recorded, not that the record is complete, correctly bound, or true.
- A functioning immutability mechanism can prevent or make later alteration detectable within its threat model; it does not show that required events were captured before action.
- An alert provides evidence that a threshold was crossed, not that containment occurred.
- A clinician click provides evidence of an interface event, not meaningful human control.
- A committee provides evidence that governance structure exists, not that its controls operated effectively.
- A validated model does not prove the downstream organization acted safely.
- Compliance mapping does not prove lawful operation in the configured environment.
- Monitoring does not prove prevention, containment, recovery, or effect.

This is not an argument against registries, signatures, logs, immutability, alerts, clinicians, committees, validation, compliance analysis, or monitoring. It is a rule against asking a control artifact to establish more than it can. Assurance is the disciplined assembly of such artifacts around an exact claim, with explicit limits and an independent verdict.

## 6. Claim-relative whole-system assurance

The eleven headings used here synthesize the supplied Jeffords materials into an integrated method. They are this paper’s organization, not a numbered list quoted from one source. The distinctive contribution is the integration, not a claim to have invented every component.

### 1. Claim-relative boundary selection

State the claim before choosing the item. Include dependencies whose supportable failure modes could materially defeat it under the consequence class—not every connected component. Excluded material dependencies become explicit, proportionately tested assumptions. The claim cannot exceed the boundary or evidence (Jeffords, *Attachment A*, 2026, pp. 5, 14–15; *Controller*, Public Edition, pp. 3–4).

### 2. Purpose integrity

A consequential operation needs a valid, current, bounded purpose. “Benefit patients” is too broad. The proposed record identifies population, operation, comparator, threshold, time horizon, intended and prohibited effects, and accountable claimant. Purpose change may require new authority and assurance.

### 3. Authority integrity

Authority is operational, not a title, signature, role, or human presence. Evidence identifies source, holder, delegation, scope, limits, validity period, expiration, revocation, exhaustion, supersession, configuration binding, and revalidation points. Revalidation may be automated, not fresh human approval. A retry after indeterminate effect is a new event; consumed authority does not regenerate automatically (Jeffords, *Attachment A*, pp. 10–12).

### 4. Context-bound operation

Information must be admissible, sufficient, current, sourced, authorized, and bound to the correct person, purpose, policy, operation, and configuration. Missing, stale, restricted, conflicting, or withheld context is an operating condition—not a hidden data-quality score.

### 5. Control-path integrity

Evaluate the actual path through agent/model, controller, tools, credentials, interfaces, people, downstream systems, and environment. A claim-dependent controller is inside the test. **Control** constrains before consequence; **observation** preserves state; **evaluation** judges the claim; **decision/disposition** governs the response. Evaluation does not authorize action, nor disposition enlarge the verdict. Detection without timely prevention, redirection, containment, or stop is insufficient.

### 6. Separate recommendation, decision, action, and effect

A correct prediction does not establish delivery, informed authority, decision, execution, intended effect, or containment. Each transition needs identity, sequence, configuration, and evidence binding; causal wording needs evidence suited to its asserted scope.

### 7. Meaningful human control

Human approval must not launder responsibility. When a claim relies on human review, authorization, or intervention, the synthesized test is:

1.  adequate and comprehensible information;
2.  valid authority; and
3.  a practical opportunity to alter, reject, stop, or escalate the operation.

Competence, workload, time, interface, access, and support evidence whether those conditions held. Bounded automation may instead rely on valid preauthorization and enforced machine control; a ceremonial click adds nothing.

### 8. Effect verification and failure performance

Distinguish proposal, authorization, dispatch, execution, external effect, and verified effect; then examine unintended effect, containment, recovery, and recurrence. A failed operation can show good recovery; a successful effect can hide failed controls. Performance, control effectiveness, resilience, and assurance quality remain separate. Reconstruction does not prove population causation.

### 9. Reconstruct the as-operated operation

Reconstruct the actual model, controller, policy, data, authority, staffing, tools, credentials, dependencies, configuration, discrepancies, overrides, retries, escalation, and unresolved effects—not the intended workflow alone.

### 10. Independent evaluation

A supplied response reference states the governing shorthand, supported substantively by the principal papers: **No person or machine is authorized to certify their own correctness.** Every actor may produce evidence; none establishes its own final correctness merely by producing or controlling it. Independence is claim-relative and requires competence, access, authority, and separation sufficient to challenge the claim.

### 11. Continuing conformance

Predeployment approval is not permanent. Material change to authority, model, data, policy, configuration, population, environment, workflow, personnel, dependency, or regulation can suspend or expire a conclusion and trigger re-evaluation.

## 7. Authority, context, and the as-operated control path

Purpose states the legitimate objective and intended effect; it does not confer permission. Authority identifies who or what may act, within what scope, configuration, consequence, and period. Context states the information on which the action may rely. The control path determines whether those conditions remain enforceable before consequence. These are linked but not interchangeable.

Consider a credential. Access control may prove that a clinician or agent possessed it. Authority integrity asks more: which source granted its use for this patient and operation; whether delegation was valid; whether the credential’s scope included the tool, order, and consequence; whether authority had expired, been revoked, exhausted, or superseded; and whether the current model, controller, policy, and configuration still matched the authorization. Revalidation must occur before credential release or consequence-producing action when any of those facts could have changed.

Context integrity likewise exceeds provenance. A laboratory value may be authentic yet stale for the decision, correctly timed yet bound to the wrong encounter, permitted for care yet inadmissible for a research operation, or complete in its source system while missing a contraindication held elsewhere. The record must state minimum context, conflicts, freshness limits, permitted sources, and the consequence of uncertainty.

The as-operated control path then connects recommendation, decision, action, and effect. It must show not only who could observe an operation but who or what could interrupt it, in what window, through which mechanism, and with what fallback. Emergency, degraded, bypass, retry, rollback, alternate-tool, and alternate-credential paths matter as much as the nominal path. If an alert arrives after the intervention window, a nominal stop button is not meaningful control. If a controller can suppress evidence, broaden tools, release credentials, or accept its own success signal, its correctness is part of the claim and must be challenged independently.

## 8. Agentic execution in critical organizations

The four reviewed healthcare sources largely emphasize AI analysis, a signal, and human decision. Agents may instead retain state, combine information, choose tools, use credentials, delegate, modify records, transact, and create effects before every intermediate step is reviewed.

This does not require individual approval for every action. Consequential action must remain within bounded authority and an enforced path. Low-consequence, reversible, observable work may be tightly preauthorized; greater consequence, novelty, uncertainty, or irreversibility demands stronger pre-action controls, narrower authority, effect observation, and evaluation.

Claim-relative assurance can operate at machine speed. A versioned operating definition may preauthorize a registered work type within standing, session, or bounded-use authority, capability, and effect envelopes. A runtime controller checks purpose, authority, context, configuration, action, tool, credential, target, and effect bounds. In-envelope action proceeds with bound receipts; mismatch defaults to denial, a separately authorized narrower path, or escalation. Evaluation later finalizes event claims, samples recurring operations where appropriate, and reviews on schedule or trigger. Human judgment applies where the design relies on it or an exception leaves the envelope.

Failure of the AI-mediated assurance path suspends that automated authority; it does not erase a clinician’s separately delegated emergency or standard-care authority. Action under a separate authority path must be recorded as such, and the organization may not claim that the failed AI-controlled path validly produced the consequence.

Agent policy and controller guardrails are not external guarantees. Claim-dependent routing, credential release, tool restriction, stopping, and evidence capture enter the test. The agent may be clinically correct while the organization lacks authority or evidence.

## 9. Confidential dependencies and the assurance ceiling

Proprietary source code, model weights, trade secrets, security-sensitive test details, protected health information, and unrelated internal artifacts need not be published or disclosed broadly. Public transparency is not identical to assurance access. The relying organization must receive evidence sufficient to understand the supported claim, scope, assumptions, limitations, version, and period of validity. Where underlying material cannot appropriately be disclosed to the organization, a regulator, conformity-assessment body, or other qualified, independent examiner may review it under protection and report claim-scoped findings and limitations.

Applied to an opaque third-party dependency, the proposed method yields three defensible alternatives:

1.  the provider supplies sufficient claim-scoped evidence, or permits protected underlying evidence to be examined, by an appropriately qualified independent examiner;
2.  the organization constrains the dependency so its opacity cannot materially defeat the bounded claim; or
3.  the organization reduces the claim, restricts the permitted operation, or denies the consequential use.

A prior audit, regulatory review, certification, or examiner report may be reused when its claim, boundary, methods, independence, version, configuration, intended use, limitations, and current validity match the present reliance. Its label alone is not sufficient.

The principle is concise: **Confidential is acceptable. Unexamined is not.** “Examined” does not mean publicly disclosed or exhaustively exposed. It means material evidence received access, competence, independence, and scope sufficient for the bounded claim. If neither the organization nor a suitable examiner can evaluate a material dependency, there is an assurance ceiling. Commercial sensitivity governs protection and sharing; it cannot enlarge what the evidence supports.

## 10. Worked sepsis-response example

Assume the hospital asserts:

> > ***For Patient X at 14:03, the organization validly identified elevated sepsis risk and caused an authorized, timely, clinically appropriate intervention.***

This is not a model claim. It combines correct identification, organizational validity, authorization, timeliness, appropriateness, execution, and effect. Each word changes the boundary.

### Claim ladder

The following boundaries and evidence fields are requirements of the proposed Jeffords method, not established clinical or regulatory requirements. The ladder expands evidentiary scope; it is not a chain of logical entailment. Population accuracy supports intended-use capability but does not establish that one patient-level prediction was correct.

| **Claim**                                                   | **Required evaluation boundary**                                                                                                                                                                                                                     | **Minimum evidence needed**                                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1\. “The model predicts sepsis accurately.”                 | Intended use; target population; training, validation, and test data; model identity/version; threshold; comparator; calibration; subgroup performance; known limitations.                                                                           | Locked model/configuration; dataset provenance and separation; endpoint definition; sensitivity/specificity or other suitable metrics; calibration and subgroup results; uncertainty; independent test or defensible external evaluation.                                                                                                                                        |
| 2\. “The alert reaches the appropriate clinician reliably.” | Claim 1 plus patient/EHR identity; data freshness and encounter binding; controller, threshold, routing rules, directory/on-call state, network, delivery channel, interface, timestamps, acknowledgments and escalation.                            | Bound inference and routing record; correct recipient/role; delivery time; presentation; queue state; failed-delivery and escalation tests; versioned controller/routing configuration; completeness check.                                                                                                                                                                      |
| 3\. “The clinician can make a meaningful decision.”         | Claims 1–2 plus clinician identity, competence, current authority, workload, time, information available, limitations shown, access to relevant records, intervention mechanisms and backup support.                                                 | Three-part test: comprehensible and adequate information; valid authority; practical opportunity to alter/reject/stop/escalate. Include training/competency, duty/privilege state, workload, interface capture, review time, conflicting data, override/escalation and system availability.                                                                                      |
| 4\. “The hospital responds appropriately.”                  | Claims 1–3 plus predeclared clinical criteria, applicable protocol or qualified review, governing policy, order entry, pharmacy, laboratory, nursing, staffing, devices, timing, contraindications, execution, escalation, containment and recovery. | Current criteria, protocol and configuration approval; contraindication assessment; authority chain; signed/transmitted order; pharmacy verification; administration record; lab/staffing events; timing; discrepancies and overrides; effect evidence whose provenance, integrity, observation boundary, and ability to contradict the controller are independently defensible. |
| 5\. “The operation improves patient outcomes safely.”       | Claims 1–4 plus patient response, intended and unintended outcomes, comparator/counterfactual design, time horizon, equity/subgroups, downstream care, adverse effects, recurrence and independent evaluation.                                       | Patient outcome and adverse-event evidence; justified comparator; attribution limits; safety signals; accepted-case sampling; containment/recovery; subgroup analysis; independent reconstruction; scoped verdict with limitations and expiration.                                                                                                                               |

### The complete boundary for the 14:03 claim

The record would bind Patient X and the active encounter to the EHR sources used; show provenance, admissibility, completeness, freshness, and conflicts; identify the model version, intended use, calibration, subgroup performance, threshold, and limitations; identify the controller and routing configuration; establish delivery time and presentation; establish the clinician’s identity, competence, information, current authority, workload, and intervention opportunity; identify the policy and clinical criteria valid at 14:03; connect the recommendation to the decision, order, pharmacy, nursing, laboratory, staffing, and device execution; confirm administration through an independently defensible effect-evidence path; observe patient response and adverse effects; record discrepancies, overrides, escalation, containment, and recovery; and enable reconstruction under the actual configuration.

The word “caused” has its own burden: evidence must show that the alert materially contributed to the decision and intervention. If the evidence establishes only sequence, the claim must say “was followed by” or “the intervention was executed after the alert.” The hospital need not prove population-level outcome improvement to support that narrower event claim. Conversely, one successful event does not prove population benefit. Claim 5 requires a suitable comparator or counterfactual design and time horizon.

### Model-correct, organization-failed cases

The model can correctly identify elevated risk while the organizational claim fails if:

1.  routing sends the alert to the wrong clinician or an unattended queue;
2.  the clinician’s relevant authority expired, was revoked, or did not cover the order;
3.  stale, inadmissible, incomplete, or wrong-encounter information drove the operation;
4.  the interface concealed a material limitation or omitted a contraindication;
5.  a correct order was approved and transmitted but never administered;
6.  an agent used an unauthorized credential, tool, or downstream system;
7.  the audit record cannot bind recommendation, decision, order, execution, and effect—or cannot support the causal wording asserted; or
8.  the intervention occurred but caused an unmonitored adverse consequence.

None of these cases proves the model defective. Each shows that model correctness and organizational correctness are different claims.

### If an agent can prepare, enter, or execute orders

When AI only emits an alert, order preparation and execution may remain outside the model boundary but inside the organizational claim. If an agent drafts an order, the boundary adds its state, prompt/policy, retrieved context, drafting rules, tool selection, and handoff. If it enters the order, credential release, order-entry interface, duplicate prevention, contraindication checks, confirmation, and cancellation enter the pre-action control path. If it can execute or trigger administration, authority must bind the exact action and consequence; intervention windows, stop and rollback mechanisms, downstream acknowledgments, indeterminate-effect reconciliation, and independent effect observation become stronger. The need for a human decision depends on the authorized design and consequence, but no execution path may escape bounded authority.

## 11. Independent evaluation and continuing conformance

Evidence may originate inside the operation without making it the final judge. Model confidence, controller denial, operator attestation, vendor testing, and hospital compilation each carry provenance and limits. The verdict must remain independently defensible.

Independence is claim-relative; organizational separation alone is inconclusive. A team using only controller observations cannot establish controller correctness unless evidence-path integrity and completeness are tested. The evaluator needs relevant competence, protected access, freedom to report, and controls making suppression, rewriting, omission, or post-consequence narrowing preventable or detectable. Legal-compliance claims require qualified legal expertise; an assurance verdict is not a legal determination without applicable authority and competence.

A verdict states claim, boundary, evidence, assumptions, exceptions, basis, limits, evaluator, validity, and review triggers. Cadence scales with consequence: qualify configurations before use; control and receipt each event; finalize event claims when made; sample or trigger review of recurring work; and review the operating definition periodically or after material change. Every consequential action needs authority and an enforced path—not manual examination. Changed authority, data, policy, controller, tool, configuration, population, workflow, personnel, dependency, incident, evidence, or law may invalidate the conclusion.

## 12. Implications for institutions

### NIST and assurance-method developers

NIST could add a voluntary claim-to-boundary discipline under which an assurance profile asks the claimant to state the exact proposition, define the item by materiality, license evidence only for that scope, and specify independent challenge. Unsupported breadth would be rejected; narrowing would occur only where affirmative evidence supports an exact smaller claim. Existing Govern, Map, Measure, and Manage work remains useful.

### Model and platform providers

Providers need not certify customer organizations. They can make integration more supportable by supplying versioned intended-use and limitation evidence, configuration identity, change notices, scoped evaluations, provider-side receipts, tool and credential events, and interfaces that allow independent reconciliation. Where evidence is confidential, controlled examiner access is appropriate. Provider evidence should state what it does not establish—especially customer authority, downstream execution, and external effect.

### Critical organizations

Organizations should register consequential claims and work types, bind each operation to purpose, authority, context, configuration, and effect, and require a record capable of joining recommendation through outcome. Consequence classification should determine friction: low-impact reversible tasks need not inherit controls designed for irreversible clinical action. Procurement should treat a material dependency that neither the organization nor a suitable authorized examiner can examine—and that cannot be adequately constrained—as a claim limit.

### Regulators

Regulators should distinguish binding law from guidance, means of compliance, enhanced controls, and vendor practice. A claim-relative schedule can make submissions more precise without prescribing a model or architecture. It also avoids equating an FDA marketing authorization, a committee signature, or a compliance map with lawful operation by a particular actor in a particular configuration. Regulatory evaluation should remain tied to jurisdiction, regulated actor, product or function, intended use, and effective date.

### Insurers and other relying parties

Insurers can ask what exact loss-control or safety claim is being made, what boundary was evaluated, whether authority and effect evidence are included, who examined it, and when it expires. This supports differentiated reliance without pretending assurance is a guarantee. Patients and other affected parties could receive more precisely scoped assurance.

## 13. Conclusion

The four reviewed publications converge on necessary components of whole-system governance. They reflect movement beyond model accuracy toward socio-technical conditions, lifecycle controls, competence, monitoring, auditability, resilience, and outcomes. Their different scopes and evidence bases should be respected.

The gap identified in this reviewed public record is a general, claim-relative method for assembling those components into an independently supportable organizational assurance claim. The model is not the claim. The agent is not the organization. The log is not the event, and the action is not the effect. Justified reliance begins by stating the claim, drawing a material boundary, reconstructing what occurred, and issuing a narrower conclusion only when affirmative evidence supports it; otherwise the claim is unsupported.

## 14. Comparative source matrix

| **Source**                                   | **Strongest contribution**                                                                                                                                                      | **Primary unit of evaluation**                                                                                       | **Evidence produced**                                                                                                                             | **Authority treatment**                                                                                                     | **Effect verification**                                                                                                              | **Reconstruction capability**                                                                                               | **Independent evaluation**                                                                                | **Public evidence status**                                                                                                                                                                   | **What the source cannot establish by itself**                                                                                                                                    |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **STRAICS**                                  | Socio-technical resilience across technical, clinical, organizational, and regulatory-economic conditions                                                                       | AI-CDSS embedded in a clinical ecosystem                                                                             | Narrative literature synthesis; proposed domains, metrics, monitoring, stress testing and response concepts                                       | Recognizes responsibility, escalation and override; the article does not specify an operational authority lifecycle         | Calls for outcome-linked real-world evaluation; no demonstrated effect chain                                                         | Discusses logging, traceability, near-miss analysis and observation; no complete claim-to-effect rule                       | Audit is central; examiner independence and a self-certification rule are not specified                   | Peer-reviewed conceptual Review; future validation expressly required                                                                                                                        | Empirical effectiveness; exact claim boundary; valid organizational authority; complete action-to-effect linkage                                                                  |
| **MITRE CORAL report prepared for NIH**      | Broad system definition and proposed shared playbooks, benchmarks, tools and evaluation support                                                                                 | AI use case/research workflow across lifecycle; system includes people, processes, hardware, software and facilities | Three exploratory pilots; engagements; documentation review; nine approaches; preliminary practices and roadmap                                   | Addresses accountability in principle; the report does not specify source-holder-scope-validity-revocation semantics        | Recommends clinical utility and patient-outcome measures; reports no independently verified outcome                                  | Captures data, architecture, workflow, decisions and user needs; not event-level reconstruction                             | Advisory input described; no independent verdict method specified                                         | MITRE technical report for NIH; preliminary practices need further real-world evidence                                                                                                       | Validated playbook; causal outcome; representative NIH-wide result; exact claim sufficiency or independently established effect                                                   |
| **EPC**                                      | Practical roles, staged validation, registry/versioning, detailed event logs, monitoring, incident records, retirement and rollback                                             | Healthcare AI model/system through organizational lifecycle                                                          | Records the guide recommends or reports implementations generate: approvals, versions, inferences, users, clinical/admin events and incidents     | Named roles and signatures; the reviewed page does not specify an authority lifecycle                                       | Shadow comparison and clinical-response records; the page does not define a complete intervention-to-patient-effect chain            | Rich event categories; completeness, causal-linkage, authority-state and external-effect rules are not specified            | The reviewed page does not specify independent evaluation; auditor access alone is insufficient           | Framework public; outcome and capability results are vendor-reported                                                                                                                         | That a specific organization used the approved configuration, acted under valid authority, executed the intervention or caused the claimed effect                                 |
| **IQVIA**                                    | Human augmentation, competency cliff, knowledge preservation, manual fallback, patient benefit, near misses, overrides, safety signals and accepted-sample review               | Regulated organization’s human-machine partnership                                                                   | Records the paper recommends: competency, validation, lineage, version, decision, monitoring, corrective action, safety and outcomes              | Calls for genuine authority and human judgment; the paper does not specify an authority lifecycle                           | Strong recommendation to measure outcomes and adverse effects; no demonstrated dataset                                               | Versioning, lineage and action logs support partial reconstruction; no minimum purpose-to-effect record specified           | Accepted-sample testing is called independent; evaluator competence/separation are undefined              | Strategic white paper and illustrative/vendor vignettes; no methods, references or validated outcomes                                                                                        | Meaningful human control in an event, valid authority, completed downstream action, causal patient benefit or independently supportable claim                                     |
| **Jeffords claim-relative assurance method** | Integrates claim boundary, purpose, authority, context, control path, recommendation/decision/action/effect separation, reconstruction, independence and continuing conformance | Exact organizational claim and every material dependency whose credible failure could defeat it                      | Claim package; work-order/authority state; context/configuration identity; runtime decisions; execution/effect records; discrepancies and verdict | Source, holder, delegation, scope, limits, validity, expiry, revocation, exhaustion, supersession, binding and revalidation | Requires independently defensible evidence of execution, delivery, intended/unintended effects, containment, recovery and recurrence | Requires reconstruction of actual model, controller, policy, data, authority, people, tools, dependencies and configuration | Claim-relative, competent challenge; no party establishes its own correctness by controlling the evidence | Testable assurance proposition with defined rules and schemas; some supplied artifacts instantiate records or control primitives; no complete-deployment or sector-wide empirical validation | Cannot by itself prove effectiveness, safety, legality or an outcome; those require implementation, operation-specific evidence, independent evaluation and real-world validation |

## 15. Minimum Claim Evidence Schedule

Use one schedule per material claim; “not applicable” requires a reason. Stable fields may be inherited by version, but each operation binds current authority, context, configuration, runtime events, exceptions, and effect evidence. Pending is temporary while the evidence window is open; final verdicts are Supported, Narrowed, or Unsupported. Narrowed requires affirmative evidence for an exact smaller claim; indeterminate is an effect state, not a final verdict.

| **Field**                                                                                         | **Required entry**                                                                                                                                   |
|---------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Exact organizational claim**                                                                    | One falsifiable proposition; separate recommendation, decision, action and effect.                                                                   |
| **Claim state and evidence deadline**                                                             | Declared/Pending while the window is open; finalization deadline and responsible evaluator.                                                          |
| **Claimant and accountable owner**                                                                | Legal/organizational claimant; named accountable role and contact.                                                                                   |
| **Intended purpose and effect**                                                                   | Population, operation, comparator/baseline, threshold, time horizon, intended and prohibited effects.                                                |
| **Applicable population and operating context**                                                   | Setting, jurisdiction, workflow, users, exclusions, environmental assumptions.                                                                       |
| **Consequence classification**                                                                    | Severity, reversibility, scale, time sensitivity, uncertainty and affected parties.                                                                  |
| **System boundary**                                                                               | Every element whose credible failure could defeat or change the claim; justified exclusions/assumptions.                                             |
| **Evidence inheritance**                                                                          | Claim package/registered work type, approved operating definition, inherited versions and change state.                                              |
| **Models, agents, controllers, tools, people, data sources, vendors and downstream dependencies** | Identity, role, ownership, interface and materiality of each.                                                                                        |
| **Authority source, holder, scope, limits, effective period and current validity**                | Delegation chain; expiry, revocation, exhaustion and supersession state; configuration binding.                                                      |
| **Required authority revalidation points**                                                        | Before credential release, dispatch, irreversible action, retry, escalation or other consequence window.                                             |
| **Context provenance, admissibility, freshness, completeness and conflicts**                      | Minimum context; sources; permissions; timestamps; missing/stale/restricted/conflicting facts and disposition.                                       |
| **Model, controller, policy and configuration identity**                                          | Versions, hashes/identifiers, parameters, thresholds, routing and effective dates.                                                                   |
| **Pre-action control and decision evidence**                                                      | Preconditions, safeguards, meaningful-human-control evidence, denials, escalation and stop opportunity.                                              |
| **Runtime control decision**                                                                      | Allowed, denied, held, redirected or escalated; controller basis and separately authorized path, if used.                                            |
| **Tools and credentials used**                                                                    | Tool calls, targets, scopes, credential source/release/use, denied attempts and delegation.                                                          |
| **Actual execution evidence**                                                                     | Dispatch, acceptance, downstream action, delivery/administration, timestamps and reconciliation of indeterminate states.                             |
| **External-effect evidence**                                                                      | Independent observation of intended effect, comparator/attribution limits and observation window.                                                    |
| **External-effect state and evidence deadline**                                                   | Confirmed effect, confirmed absence or indeterminate effect; deadline, reconciliation and retry consequence.                                         |
| **Unintended effects**                                                                            | Adverse, collateral, delayed, equity, privacy, security and operational effects.                                                                     |
| **Exceptions, discrepancies, overrides, containment and recovery**                                | What departed from plan; who responded; stop/contain/recover time; residual risk and recurrence action.                                              |
| **Continuing-conformance triggers**                                                               | Model, data, authority, policy, controller, configuration, population, environment, workflow, staff, dependency, incident, evidence or legal change. |
| **Evidence custodian**                                                                            | Custodian, location, retention, integrity, access controls and protected-evidence procedure.                                                         |
| **Independent evaluator**                                                                         | Person/entity, role, scope, access and reporting line.                                                                                               |
| **Evaluator competency and independence**                                                         | Domain, technical, operational and evidentiary competence; conflicts; dependence on evaluated components.                                            |
| **Decision/disposition**                                                                          | Operational response to findings; kept distinct from action authorization and the evaluation verdict.                                                |
| **Verdict, scope, limitations and expiration**                                                    | Final Supported, Narrowed or Unsupported; reasons, assumptions, effective period and re-evaluation date.                                             |
| **Verdict history**                                                                               | Expiration, supersession or later invalidation preserved without rewriting the historical verdict.                                                   |

## Editor’s note on evidence status

**Established by the reviewed sources:** the four publications reflect movement beyond isolated model metrics toward socio-technical scope, lifecycle governance, human competence, monitoring, auditability, incident learning and outcome relevance. They also report or describe difficulty substantiating broad responsible-AI claims.

**Inferred here:** comparing their evaluation units and evidence structures suggests that the four reviewed publications do not supply a general claim-relative sufficiency rule. This is a cross-document inference, not a statement made by all four sources.

**Proposed or synthesized here from the supplied Jeffords corpus:** the claim-relative boundary rule and integrated requirements for purpose, authority, context, control path, separation of recommendation/decision/action/effect, meaningful human control, effect verification, reconstruction, independent evaluation and continuing conformance.

**Unvalidated:** the reviewed public record does not establish that the Jeffords method reduces incidents across critical sectors, that STRAICS has been operationally validated, that CORAL’s preliminary practices produce validated organizational outcomes, or that EPC’s and IQVIA’s reported or illustrative outcomes have been independently established.

**Unresolved source/terminology issues:** *A Book of Rules for Autonomous Systems* intentionally has no named author and is cited by title. The conformance audit likewise has no identified personal author in the reviewed text and is limited to controlled editorial conformance. The requested *Performance-Based Assurance for AI-Mediated Operations: A Framework for Technology-Neutral Governance* was not located; *Integrated Draft 0.4* is the closest supplied source and is not silently renamed. A later *Attachment A* tightens Draft 0.3: “Narrowed” requires affirmative support for the smaller claim; otherwise the result is unsupported. The three-part human-control wording, opaque-dependency alternatives and full purpose-field list are operational syntheses; the exact non-self-certification sentence appears in a supplied response reference with equivalent principal-paper rules. Proposed “shall” or “must” is not current law. STRAICS’s three dimensions and four resilience domains are not treated as identical. EPC’s update dates conflict internally. IQVIA has no bibliography or study method and predates July 2026 EU timing amendments. No material contradiction was found in the core boundary rule.

## 16. References

Al-Horani, R. A., & Tadesse, A. F. (2026). [“Continuous assurance for AI-driven clinical decision support systems.”](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2026.1870819/pdf) *Frontiers in Artificial Intelligence*, 9, Article 1870819. DOI 10.3389/frai.2026.1870819.

Electronic Code of Federal Regulations. [45 C.F.R. Part 164, Subpart C—Security Standards for the Protection of Electronic Protected Health Information](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C). United States; covered entities and business associates handling ePHI; current text verified 10 August 2026.

European Commission. (2026). [AI Act: regulatory framework and application timeline](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai). Verified 10 August 2026. European Union; providers, deployers and other actors as allocated by the Act; applicability depends on classification, intended purpose and use.

European Union. [Regulation (EU) 2024/1689, consolidated version of 27 July 2026](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX%3A02024R1689-20260727). Providers, deployers, importers, distributors, product manufacturers and other actors as allocated by the Regulation; verified 10 August 2026.

Flanagan, M., King, M., & Reed, J. (2026). [*The Human Edge of AI: Building Trust, Safety and Value in Regulated Healthcare Systems.*](https://www.iqvia.com/-/media/iqvia/pdfs/library/white-papers/2026/building-trust-safety-and-value-in-regulated-healthcare-systems.pdf) IQVIA.

Food and Drug Administration. (2025). [*Artificial Intelligence-Enabled Device Software Functions: Lifecycle Management and Marketing Submission Recommendations*](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/artificial-intelligence-enabled-device-software-functions-lifecycle-management-and-marketing) (Draft Guidance; nonbinding; not for implementation). United States; manufacturers of device software functions within FDA jurisdiction; verified 10 August 2026.

Food and Drug Administration. (2025). [*Marketing Submission Recommendations for a Predetermined Change Control Plan for Artificial Intelligence-Enabled Device Software Functions*](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/marketing-submission-recommendations-predetermined-change-control-plan-artificial-intelligence) (Final Guidance, 18 August 2025). United States; specified medical-device marketing-submission context; verified 10 August 2026.

Health and Human Services, Office for Civil Rights. [Summary of the HIPAA Security Rule](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html), implementing 45 C.F.R. Parts 160 and 164, Subparts A and C. United States; covered entities and business associates handling ePHI; verified 10 August 2026.

Jeffords, L. (2026). *AI Orchestration Governance: The Agent Is Not the System.* Supplied discussion paper.

Jeffords, L. (2026). *The Controller Is Part of the System Under Test: Item Definition, Assumptions of Use, and Claim Licensing in AI Evaluation.* Supplied paper.

Jeffords, L. (2026). *Model Framework for Performance-Based Assurance of AI-Mediated Operations: Integrated Draft 0.4.* Supplied model-framework draft.

Jeffords, L. (2026). *What We Are Actually Proposing: Why the Agent Is Not the System and How AI Models and Organizations Can Work Together in a Governed Way.* Discussion Draft 0.3. Supplied methodology paper.

Jeffords, L. (2026). *Attachment A: What We Are Actually Proposing.* Supplied methodology paper; later integrated version.

Jeffords, L. (2026). *The Enterprise Under Test: AI, DRNT, and the Seat-Layer Audit of Organizational Coherence.* Final doctrine paper, Version 3; supplied signed-off architecture file.

*Independent Conformance Audit—Controlled Mechanical Edit: The Controller Is Part of the System Under Test.* (2026). Supplied document-conformance audit; scope limited to controlled editorial conformance.

*A Book of Rules for Autonomous Systems.* (2026). Supplied text; deliberately issued without a named author.

Kapusta, A., et al. (2026). [*NIH Collaborative AI Assurance Research Laboratory: Justification and Impact Report.*](https://datascience.nih.gov/sites/g/files/mnhszr336/files/2026-04/impact_report_March2026_final_posting.pdf) MITRE, Public Release Case 26-0299.

National Institute of Standards and Technology. (2023). [*Artificial Intelligence Risk Management Framework (AI RMF 1.0), NIST AI 100-1.*](https://nvlpubs.nist.gov/nistpubs/ai/nist.ai.100-1.pdf) Voluntary guidance; verified 10 August 2026.

O’Connor, E. (2026). [“AI Governance for Healthcare: The HIPAA Compliance Guide for 2026.”](https://www.epcgroup.net/blog/ai-governance-healthcare-hipaa-guide) EPC Group, 23 February 2026; live page updated 10 August 2026; accessed 10 August 2026.
