# Operations & Governance Guide

*Post-deployment guide for the business owner or office manager running the system day-to-day.*

---

AI Orchestration Framework for Small Business

**Document 6**

**Operations ****&**** Governance Guide**

*For the business owner or office manager running the system after deployment*

| **What this document covers** Section 1 — Reading and acting on audit logs Section 2 — WAL promotion and demotion criteria Section 3 — Detecting and responding to overreliance Section 4 — Incident response for a bad AI output Section 5 — Quarterly governance review checklist | **How to use this document** Keep it accessible. This is a reference, not a manual you read once and file. Return to Section 2 before any WAL promotion decision. Run Section 5 every quarter without exception. Go to Section 4 immediately when something goes wrong. |
| --- | --- |

# **Section 1 — Reading and Acting on Audit Logs**

The audit log is the most important operational tool you have. It is the record of everything the AI system did, what it was asked to do, what it produced, and what a human did with that output. If something goes wrong, the log tells you when, where, and why. If the system is working well, the log proves it.

This section explains what a well-configured log contains, how to read it without technical expertise, and what action each entry type requires from you.

## **1.1  What the Audit Log Contains**

Every interaction routed through the Orchestrator should produce a log entry. A complete entry includes the following fields. Your implementation may use different labels, but every field below should be present in some form.

| **Field** | **What It Records** | **Why It Matters** |
| --- | --- | --- |
| Timestamp | Date and time the request was received | Establishes sequence; essential for incident reconstruction |
| Request ID | A unique identifier for the transaction | Links the request to its output and any follow-on actions |
| Originating User | Which staff member or system triggered the request | Identifies patterns; flags high-frequency individual use |
| Workflow / Task Type | The category of task (e.g., draft, summarize, classify) | Tells you which WAL rules apply to this entry |
| WAL Level at Time of Request | The autonomy level in effect when the task ran | Confirms the governance rules that applied |
| Destination Model | Which AI model handled the request | Tracks cloud dependencies; essential for vendor review |
| Data Sensitivity Flag | Whether the Context Packager flagged sensitive data | Indicates compliance risk; requires closer review |
| Output Disposition | What the human did: approved, rejected, modified, bypassed | The single most important field for overreliance detection |
| Processing Time | How long the request took | Useful for performance review; extreme outliers may indicate errors |
| Error / Exception Flag | Whether the system encountered any fault | Immediate action trigger; see Section 4 |

## **1.2  The Three Log Entry Types You Will See Most**

Not every log entry requires the same response. Learn to distinguish these three categories quickly.

| **ROUTINE** | **What it looks like: **Normal task type, correct WAL level, output marked Approved or Modified, no error flags, no sensitive data flag. **What it means: **The system worked as designed. **What you do: **Nothing immediate. Count these in your quarterly review to confirm baseline volume. |
| --- | --- |

| **REVIEW** | **What it looks like: **Sensitive data flag triggered. Output marked Approved without modification on a WAL-0 workflow. Same task repeated many times by one user in a short period. **What it means: **Something warrants your attention, though it may not be a problem yet. **What you do: **Read the entry in detail. Pull the associated output if stored. Speak with the relevant staff member before the next shift. Do not wait until the quarterly review. |
| --- | --- |

| **IMMEDIATE ACTION** | **What it looks like: **Error or exception flag. Output disposition shows Bypassed. Sensitive data flag combined with an external model call. A WAL level mismatch (task ran at a higher level than authorized). **What it means: **Something in the system did not work as intended, or a person circumvented a control. **What you do: **Stop the workflow. Document the entry. Go to Section 4. Do not attempt to fix the underlying system issue before logging what happened. |
| --- | --- |

## **1.3  Building a Log Review Habit**

A log you never read is not a control — it is a false sense of security. The following schedule is the minimum:

| **Frequency** | **What to Review** | **Time Required** |
| --- | --- | --- |
| Daily (1–2 min) | Scan for any IMMEDIATE ACTION flags from the prior day. If none, you are done. | 2 minutes |
| Weekly (10–15 min) | Review all REVIEW-flagged entries. Check output disposition rates — are approvals running suspiciously high? Any single user dominating volume? | 15 minutes |
| Quarterly (60–90 min) | Full structured review. Use the checklist in Section 5. | 90 minutes |

| **A NOTE ON LOG STORAGE** |
| --- |
| Your audit logs are a legal and operational asset. Store them with the same care you apply to financial records. |
| Minimum retention: 12 months of accessible logs; 3 years of archived logs. |
| Logs must be stored in a location the AI system cannot modify. Read-only access for the AI; read/write for designated administrators only. |
| If your business operates in a regulated industry, confirm retention requirements with your compliance advisor — your obligation may exceed these minimums. |

# **Section 2 — WAL Promotion and Demotion Criteria**

Autonomy is earned, not assumed. Every workflow starts at WAL-0. Promotion to a higher level is a deliberate business decision based on demonstrated performance, not a default that happens after time passes. Demotion is equally deliberate and must happen the moment a workflow fails to justify its current level.

This section defines the criteria for each transition and the conditions that require demotion.

| **THE CARDINAL RULE OF WAL MANAGEMENT** |
| --- |
| When in doubt, stay lower. A workflow running at WAL-0 that could safely run at WAL-1 costs you some staff time. |
| A workflow running at WAL-2 that should have stayed at WAL-1 can cost you a client, a lawsuit, or a regulatory action. |
| The asymmetry is not subtle. Default to caution. |

## **2.1  The Four WAL Levels — Quick Reference**

| **Level** | **Name** | **What AI Is Permitted To Do** | **Human Role** |
| --- | --- | --- | --- |
| WAL-0 | Recommend | Produce a recommendation, draft, or analysis | Reviews every output before any action is taken |
| WAL-1 | Assist | Complete routine low-risk tasks with light review | Spot-checks outputs; reviews exceptions |
| WAL-2 | Execute | Act autonomously on well-defined, fully audited workflows | Reviews the log periodically, not each action |
| WAL-3 | Automate | Manage an end-to-end workflow including exception handling | Sets policy; reviews outcomes; intervenes on escalations |

## **2.2  Promotion Criteria by Transition**

The table below defines what must be true before you promote a workflow. These are minimum requirements, not aspirational targets. All criteria must be met — there are no partial promotions.

| **Transition** | **Promotion Criteria (all required)** | **Additional Conditions** |
| --- | --- | --- |
| **WAL-0** ↓ **WAL-1** *Recommend → Assist* | **Promotion Criteria** Minimum 30 completed transactions in the workflow 95% or higher human approval rate with no modifications Zero instances of sensitive data mishandling Zero overreliance flags in the review period Workflow is fully documented in plain language | **Additional Conditions** Suitable only for tasks with no direct client or financial impact on individual transactions Output format must be standardized and verifiable at a glance Spot-check rate defined in writing before promotion takes effect |
| **WAL-1** ↓ **WAL-2** *Assist → Execute* | **Promotion Criteria** Minimum 90 days at WAL-1 without demotion Spot-check error rate below 2% over the full period All exceptions escalated correctly — none handled silently by the AI Staff overreliance rate at or below baseline for the business Workflow has survived at least one edge-case or exception without error | **Additional Conditions** Requires written authorization from the business owner — not delegatable Must define the log review cadence that replaces per-action review Not permitted for any workflow touching regulated data without compliance sign-off |
| **WAL-2** ↓ **WAL-3** *Execute → Automate* | **Promotion Criteria** Minimum 180 days at WAL-2 without demotion End-to-end error rate below 1% over the full period All exception types encountered have known handling paths The workflow has been independently reviewed by a second qualified party The business can demonstrate full reversal of any AI action within 24 hours | **Additional Conditions** Reserved for thoroughly validated, low-sensitivity, high-volume tasks only Requires documented rollback procedure Quarterly audit is mandatory — not optional — at this level Any regulatory or compliance implication requires professional sign-off |

## **2.3  Demotion — When to Move a Workflow Down**

Demotion is not a failure. It is the governance system working correctly. The following conditions require immediate demotion to the next lower WAL level. Do not wait for the quarterly review.

| **Demotion Trigger** | **Required Action** |
| --- | --- |
| Any output causes real-world harm (financial loss, client complaint, compliance exposure) | Immediate demotion to WAL-0. Initiate incident response per Section 4. |
| Error rate exceeds the threshold defined at promotion | Demote one level. Do not re-promote until root cause is identified and resolved. |
| Staff overreliance detected at this workflow's level | Demote one level. Address overreliance per Section 3 before re-promoting. |
| Sensitive data mishandling detected (any instance) | Immediate demotion to WAL-0. Do not re-promote without compliance review. |
| The underlying AI model changes (version, provider, or configuration) | Demote to WAL-0. Treat the new configuration as a new workflow. Restart the promotion clock. |
| A new regulatory requirement applies to this workflow | Demote to WAL-0. Do not re-promote without professional guidance. |

| **MODEL CHANGES RESET THE CLOCK** |
| --- |
| If your AI vendor updates the model — even a minor version change — the workflow's demonstrated performance history no longer applies. |
| The new model is a different system. It requires its own validation period. |
| This is not a vendor policy. It is a basic principle of risk management. |
| Document the model version in effect at the time of every promotion decision. You will need it. |

# **Section 3 — Detecting and Responding to Overreliance**

Overreliance is the condition where staff approve AI outputs without meaningful review. It is the most common governance failure in deployed AI systems, and it is almost invisible until it causes a problem. The audit log is your early warning system. This section explains how to read it for overreliance signals and what to do when you find them.

## **3.1  What Overreliance Looks Like**

Overreliance rarely announces itself. Staff do not usually intend to rubber-stamp outputs — they develop the habit gradually as the system appears to be working. Watch for these patterns in the log:

| **Signal** | **What It Looks Like in the Log** | **What It May Indicate** |
| --- | --- | --- |
| Near-zero rejection rate | Output disposition shows Approved on 98–100% of entries over an extended period | Staff are not critically evaluating outputs; they are confirming them |
| Approval speed anomaly | If your system logs timestamps, approval happens within seconds of output generation | Outputs are being approved faster than a meaningful review could occur |
| Absence of modifications | No entries show Modified disposition despite the workflow involving drafts or recommendations | Staff are accepting outputs verbatim rather than treating them as drafts |
| Volume concentration | One staff member accounts for a disproportionate share of approvals | Overreliance may be concentrated in one role or shift |
| Consistent bypassing of review steps | Log entries show tasks processed without the required WAL-0 human review step | A process control has been informally disabled |

## **3.2  The Overreliance Detection Threshold**

No single number defines overreliance for every business. The following thresholds should be treated as investigation triggers — not proof of a problem, but evidence that warrants a conversation.

| **Metric** | **Investigation Threshold** | **Action** |
| --- | --- | --- |
| Approval rate (WAL-0 workflows) | Above 97% over 30 or more transactions | Review a sample of the approved outputs. Pull five at random and evaluate them yourself. |
| Modification rate (drafting workflows) | Below 5% over 30 or more transactions | Pull several samples. If AI drafts are going out unedited, the workflow needs a required review step. |
| Approval speed (if logged) | Consistent approvals in under 30 seconds for outputs requiring substantive review | Speak with the staff member. Observe the review process directly. |
| Single-user volume share | One user responsible for over 60% of approvals | Review that user's approval history. Assess whether review responsibilities are appropriately distributed. |

## **3.3  How to Respond to Overreliance**

When you identify overreliance, the response has three phases: immediate, short-term, and structural. Work through them in order.

**Phase 1 — Immediate (same day)**

- Stop treating the affected workflow as validated. If it was being considered for WAL promotion, freeze that decision.

- Do not confront staff as if overreliance is a disciplinary matter — in most cases it is not. It is a process problem.

- Document what you observed in the log and when you observed it.

**Phase 2 — Short-Term (within one week)**

- Have a direct conversation with the staff member(s) involved. Ask them to walk you through how they review an output. Listen. You will learn whether the review process was unclear, the tool was seen as authoritative, or the review step was genuinely seen as unnecessary overhead.

- Revisit the workflow design. If the review step is easy to skip, it will be skipped. Make the review step unavoidable — require a written justification for approvals on high-risk workflows, or require the reviewer to answer a specific question about the output before marking it approved.

- Pull a random sample of previously approved outputs from this workflow. Evaluate them independently. If errors are present, initiate Section 4.

**Phase 3 — Structural (before re-establishing baseline)**

- Reset the WAL level for the affected workflow to one level below current, regardless of prior performance. The demonstrated performance was not real — it was unreviewed approvals.

- Add an explicit check to the quarterly governance review for this workflow until three consecutive quarters show a healthy review pattern.

- Consider whether the same dynamic may exist in other workflows. Overreliance in one area is often a sign of a broader culture issue, not an isolated incident.

| **THE AUTOMATION BIAS PROBLEM** |
| --- |
| Overreliance is not carelessness. It is a documented cognitive phenomenon called automation bias — the tendency to trust automated outputs more than manual ones, especially when the system appears to be accurate most of the time. |
| Your staff are not making an error in judgment. They are responding normally to a system that has built credibility through repeated correct outputs. |
| This is precisely why governance cannot rely on human vigilance alone. The audit log, the modification-required steps, and the review checkpoints are not obstacles to efficiency — they are the countermeasure to automation bias. |

# **Section 4 — Incident Response: When an AI Output Causes a Real Problem**

At some point, the system will produce an output that causes a real-world consequence — an error in a client communication, a calculation mistake that affected a transaction, a compliance exposure, or something else with tangible impact. This section tells you what to do when that happens.

The goal of incident response is not to assign blame. It is to stop further harm, understand what happened, fix what can be fixed, and prevent recurrence.

| **BEFORE YOU BEGIN: THE FIRST FIVE MINUTES** |
| --- |
| 1. Stop the workflow that produced the problem. Do not run additional transactions through it until the root cause is understood. |
| 2. Preserve the audit log entry. Do not delete, modify, or 'clean up' any records related to the incident. |
| 3. Do not attempt to fix the error by running another AI query. Use human judgment for any immediate remediation. |
| 4. If the incident involves a client, a financial transaction, or a regulated dataset, contact your relevant advisor (attorney, accountant, compliance officer) before taking further action. |

## **4.1  Incident Classification**

Not all incidents are equal. Classify the incident before determining your response path.

| **Tier** | **Description** | **Examples** | **Response Timeframe** |
| --- | --- | --- | --- |
| Tier 1 — Minor | Error with no external impact. Caught before leaving the organization. No client, financial, or regulatory consequence. | Incorrect internal summary. Draft email caught during review. Mis-categorized routing that was corrected. | Resolve within 48 hours. Log and review at next quarterly cycle. |
| Tier 2 — Significant | Error that reached a client, affected a financial transaction, or involved sensitive data. Consequence is real but contained. | Incorrect figure in a client-facing document. Miscommunication to a client based on AI output. Sensitive data included in an output that was shared. | Respond within 4 hours. Notify affected party. Document fully. Review within 7 days. |
| Tier 3 — Critical | Error with material financial, legal, regulatory, or reputational consequence. May require external reporting. | HIPAA or GLBA disclosure incident. Material financial error affecting a client account. AI output used as basis for a significant business or legal decision that caused harm. | Immediate. Contact professional advisors before taking further action. Do not attempt to manage independently. |

## **4.2  The Incident Response Process**

Follow these steps in order for all Tier 1 and Tier 2 incidents. Tier 3 incidents require professional guidance alongside these steps — do not substitute this process for legal or compliance advice.

| **Step 1** **Contain** | Halt the affected workflow at its current WAL level. If the erroneous output was acted upon externally, identify the scope of the action. What was sent? To whom? What decision was made based on it? Flag the audit log entries associated with the incident as preserved. Do not allow routine log rotation or deletion to affect these records. |
| --- | --- |

| **Step 2** **Document** | Create an incident record. A simple document is sufficient. It should capture: the date and time of the incident, the Request ID from the audit log, the nature of the error, the output that caused the problem (preserved verbatim), what action was taken based on the output, and who was involved. Write this while memory is fresh. Do not reconstruct it after the fact. |
| --- | --- |

| **Step 3** **Assess** | Determine whether the error was in the AI output, in the human review process, or in the workflow design. These require different remediation. AI output error: The model produced factually incorrect, inappropriate, or contextually wrong content. Ask: was this a prompt quality issue, a model limitation, or a task outside the scope of this workflow? Review process failure: The output was incorrect but should have been caught. Ask: was the reviewer qualified? Was the review step adequately defined? Was overreliance a factor? Workflow design failure: The task should never have been handled at this WAL level. Ask: was this workflow promoted prematurely? Was the task within the scope the workflow was designed for? |
| --- | --- |

| **Step 4** **Remediate** | Address the real-world consequence first. If a client received incorrect information, correct it — do not delay that conversation to complete the internal review. If a document, communication, or record was affected, issue a corrected version with a clear notation that it supersedes the prior version. Do not attempt to quietly overwrite or replace erroneous outputs without a record that the correction was made and why. |
| --- | --- |

| **Step 5** **Demote** | Demote the affected workflow per the criteria in Section 2.3. If the incident was caused by a model error: demote and quarantine the task type until the root cause is resolved. If the incident was caused by review failure or overreliance: demote and implement the Phase 2 and Phase 3 overreliance response from Section 3.3. If the incident was caused by workflow design failure: demote to WAL-0 and redesign the workflow scope before restarting the validation period. |
| --- | --- |

| **Step 6** **Review and Close** | Within 14 days of the incident, complete a written root cause review. One page is sufficient. What happened, why it happened, and what changed as a result. Add a standing agenda item to the next quarterly governance review to assess whether the remediation held. If this is the second incident of the same type in 12 months, treat it as a systemic problem, not an isolated event. |
| --- | --- |

| **WHAT NOT TO DO** |
| --- |
| Do not delete, modify, or explain away log entries related to the incident. The unaltered record is your protection, not your liability. |
| Do not make promises to clients about the cause or extent of the error until you have completed Step 3. |
| Do not re-enable the affected workflow until Step 5 is complete and documented. |
| Do not treat a Tier 3 incident as a Tier 2. If you are uncertain of the classification, assume the higher tier until you have more information. |

# **Section 5 — Quarterly Governance Review Checklist**

Run this checklist once per quarter without exception. Block ninety minutes. Close other windows. This is the most important governance activity you perform. The quarterly review is where you confirm that the system is working as designed, catch slow-moving problems before they become incidents, and make deliberate decisions about where the system goes next.

Print or complete this checklist on-screen. Record the date, the reviewer's name, and your conclusions for each section. File it with your operational records.

| **Quarterly Governance Review** Review Date:  ___________________________ Reviewer:  ______________________________ Quarter Covered:  ________________________ | **Overall Assessment** ☐  No significant findings — system operating as designed ☐  Minor findings — action plan documented below ☐  Significant findings — remediation required before next review ☐  Critical finding — immediate action required |
| --- | --- |

## **5.1  Audit Log Review**

| **Area** | **Checklist Item** | **Done** |
| --- | --- | --- |
| **Log Completeness** | ☐  All active workflows have audit log entries for the full quarter ☐  No unexplained gaps in log coverage (days or workflows with no entries) ☐  Log storage location has not been modified or compressed in a way that obscures entries |  |
| **Error Flags** | ☐  All IMMEDIATE ACTION entries from the quarter have been addressed and closed ☐  All REVIEW entries from the quarter have a documented resolution ☐  No unresolved error flags remain open from prior quarters |  |
| **Data Handling** | ☐  All sensitive data flags were followed by appropriate human review ☐  No sensitive data flags resulted in an uncontrolled external transmission ☐  Context Packager data minimization rules are still current and have not been bypassed |  |
| **Output Disposition** | ☐  Approval rates are within expected ranges (see Section 3.2 thresholds) ☐  Modification rates for drafting workflows indicate active human review ☐  No Bypassed dispositions are present without a documented justification |  |

## **5.2  WAL Level Review**

| **Area** | **Checklist Item** | **Done** |
| --- | --- | --- |
| **Current WAL Levels** | ☐  Documented WAL levels for every active workflow are current and on file ☐  Each workflow's current WAL level is still justified by the evidence that supported its promotion ☐  No workflows are operating above their documented WAL level |  |
| **Promotion Eligibility** | ☐  Review each workflow currently at WAL-0 or WAL-1 against the promotion criteria in Section 2.2 ☐  Any workflow meeting promotion criteria has been formally evaluated — decision documented regardless of outcome ☐  No promotion decisions are pending without a documented rationale |  |
| **Demotion Review** | ☐  Any workflow that triggered a demotion trigger (Section 2.3) this quarter has been demoted ☐  Any demoted workflow has a documented remediation plan before re-promotion is considered ☐  Model version in use for each workflow is documented and unchanged from last quarter, or a demotion was executed |  |

## **5.3  Overreliance and Staff Review**

| **Area** | **Checklist Item** | **Done** |
| --- | --- | --- |
| **Overreliance Signals** | ☐  Approval rates reviewed against Section 3.2 thresholds for all active workflows ☐  Modification rates reviewed for all drafting workflows ☐  Any overreliance investigation from this quarter has been closed with documented outcome |  |
| **Staff Practices** | ☐  Spot-check review: pull five random outputs from each workflow and evaluate them independently against the AI output — do results justify the disposition recorded? ☐  Staff who use AI tools regularly have had a direct conversation about AI output quality in the past two quarters ☐  New staff who joined since the last review have been briefed on WAL levels and review responsibilities |  |
| **Training Currency** | ☐  Staff understand what WAL level each workflow they use is operating at ☐  Staff know how to flag a suspected AI error through the correct channel ☐  Staff have not been given the impression that AI outputs are authoritative without review |  |

## **5.4  Vendor and System Health Review**

| **Area** | **Checklist Item** | **Done** |
| --- | --- | --- |
| **Vendor Dependency** | ☐  Review which cloud models received traffic this quarter — has dependency on any single provider increased? ☐  Confirm routing is still model-agnostic — no workflow is hardwired to a single provider ☐  Review any vendor pricing, terms, or service changes communicated this quarter and assess impact |  |
| **Local System** | ☐  Orchestrator hardware is functioning within expected parameters ☐  Local model version is current and documented ☐  Context Packager rules have been reviewed and are still accurate for current workflows ☐  Log storage capacity is sufficient for the next quarter at current volume |  |
| **Cost Control** | ☐  Cloud API spend this quarter is within expected range — no anomalous spikes ☐  High-frequency workflows are being handled locally, not escalated to cloud models unnecessarily ☐  Total AI operating cost per transaction is tracked and has not increased without explanation |  |

## **5.5  Incident Review**

| **Area** | **Checklist Item** | **Done** |
| --- | --- | --- |
| **Open Incidents** | ☐  All incidents from this quarter have been classified (Tier 1, 2, or 3) ☐  All Tier 1 incidents have been closed with root cause documented ☐  All Tier 2 and Tier 3 incidents have been reviewed with a professional advisor if applicable ☐  No incidents remain in open status without an active remediation plan |  |
| **Patterns** | ☐  Review incidents from the past four quarters — any workflow with two or more incidents is flagged for redesign review ☐  Review incident root causes — are errors originating from AI output, review failure, or workflow design? Trend is documented. ☐  Compare incident frequency with transaction volume — is the rate improving, stable, or worsening? |  |
| **Remediation Validation** | ☐  For each incident closed last quarter: confirm the remediation action was implemented and has held ☐  Any workflow that was demoted due to an incident — has it remained demoted and followed the re-promotion process before any level increase? |  |

## **5.6  Quarterly Review Sign-Off**

| **Action Items from This Review** 1.  ________________________________________________________________________________ 2.  ________________________________________________________________________________ 3.  ________________________________________________________________________________ Next Review Date:  _____________________ Reviewer Signature:  ____________________  Date:  ___________ |
| --- |

# **Quick Reference — Decision Trees**

Use these decision trees for rapid triage. When in doubt about which applies, treat the situation as the higher-risk scenario.

## **I found something in the audit log**

| **What I See** | **What I Do** |
| --- | --- |
| Error or exception flag | STOP the workflow. Preserve the log. Go to Section 4. |
| Sensitive data flag + external model call | Review the entry immediately. Confirm the data was minimized before transmission. If not, go to Section 4. |
| Bypassed disposition | Identify the staff member. Document the entry. Speak with them before the next shift. |
| Approval rate above 97% on a WAL-0 workflow | Do not promote. Pull five samples. Read Section 3.2. |
| WAL level mismatch (task ran higher than authorized) | Stop the workflow. Treat as Immediate Action. Investigate how the level was exceeded. |
| Routine entries, no flags | Normal. Note the volume. Continue. |

## **Someone wants to promote a workflow**

| **Question** | **If No** | **If Yes** |
| --- | --- | --- |
| Has the workflow completed the minimum transaction count for this promotion? | Not yet eligible. Wait. | Continue to next question. |
| Does the performance data meet all criteria in Section 2.2? | Not eligible. Identify the gap and set a target date. | Continue to next question. |
| Has any overreliance been detected on this workflow in the past 90 days? | Do not promote. Resolve overreliance first. | Continue to next question. |
| For WAL-2 or WAL-3: has the business owner personally authorized the promotion in writing? | Do not promote. Escalate to owner. | Proceed with promotion. Document the decision date, evidence reviewed, and model version in effect. |

## **Something went wrong with an AI output**

| **First Question** | **Answer** | **Action** |
| --- | --- | --- |
| Was the error caught before any external action? | Yes | Tier 1. Log it. Review at next quarterly cycle. |
| Was the error caught before any external action? | No — reached a client or affected a transaction | Tier 2 at minimum. Begin Section 4 immediately. |
| Does it involve regulated data, a material financial consequence, or potential legal exposure? | Yes | Tier 3. Contact your professional advisor before proceeding. |
| Do you know the root cause? | No | Do not re-enable the workflow. Do not guess. Complete Steps 1–3 of Section 4 before making any changes. |

**AI Orchestration Framework for Small Business — Document 6**

Operations & Governance Guide

*Pre-Decisional Working Draft  |  March 2026*

*This document is part of a five-document series. It does not stand alone as an implementation guide.*