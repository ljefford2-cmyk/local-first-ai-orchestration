# Regulated Industry Supplement

*Mapping the architecture to HIPAA, attorney-client privilege, and GLBA.*

---

**AI Orchestration Framework**

**for Small Business**

**DOCUMENT 5**

**Regulated Industry Supplement**

*HIPAA  •  Attorney-Client Privilege **&** State Bar Rules  •  GLBA*

| This document maps the four-layer architecture and WAL trust tiers to the specific compliance obligations of healthcare practices, law firms, and financial services firms. It is designed to be read by the business owner and shared directly with their compliance advisor, attorney, or outside counsel. |
| --- |

# **How to Use This Document**

This document has three parallel sections — one for healthcare, one for legal practices, and one for financial services. You only need to read the section that applies to your business, though reading all three costs nothing and may surface risks you had not considered.

Each section is organized around four questions:

- Which parts of the architecture satisfy specific compliance requirements?

- Which compliance requirements does the architecture not satisfy on its own?

- What residual risks remain regardless of how well the system is built?

- What should you bring to your compliance advisor?

| This document is not legal advice. It is a plain-language orientation tool. The regulatory frameworks described here are complex, jurisdiction-sensitive, and subject to change. Nothing in this document substitutes for a conversation with a qualified attorney, compliance officer, or regulatory specialist who knows your specific practice and state. |
| --- |

A note on architecture terminology: this document uses the canonical layer names from the framework throughout. If you have not read Document 1, the summary below will orient you.

| **Architecture Layer** | **What It Does** |
| --- | --- |
| Layer 1 — Local Orchestrator | Runs on hardware your business controls. Receives every request, classifies it, and routes it. Logs all activity. Never leaves your local environment. |
| Layer 2 — Context Packager | Reviews every outbound request before it goes anywhere. Strips or masks sensitive data. Enforces your data minimization rules. Acts as the checkpoint between your environment and external services. |
| Layer 3 — Specialized Cloud Models | Third-party AI services (e.g., drafting, analysis). They receive only the curated, de-identified context the Context Packager allows through. The architecture is model-agnostic — no single vendor required. |
| Layer 4 — Trust Tier Governance (WAL) | Enforces how much autonomous action each workflow is permitted to take. WAL-0 requires human approval of every AI output. WAL-3 is full automation, reserved only for thoroughly validated workflows. |

**  PART A — HEALTHCARE PRACTICES**

  HIPAA Privacy Rule, Security Rule, and Breach Notification Rule

## **The Compliance Landscape in Plain Language**

HIPAA — the Health Insurance Portability and Accountability Act — establishes federal minimum standards for protecting individually identifiable health information. For a small practice, the three rules that matter most are the Privacy Rule (who can access what and under what conditions), the Security Rule (how electronic Protected Health Information must be stored, transmitted, and controlled), and the Breach Notification Rule (what you must do if PHI is improperly disclosed).

The central concept is Protected Health Information, or PHI. PHI is any information that relates to a patient's health condition, treatment, or payment for treatment — and that could reasonably be used to identify that patient. PHI includes names, dates, geographic identifiers, phone numbers, Social Security numbers, medical record numbers, and many other fields even when they appear without a clinical narrative.

When AI systems interact with PHI — even indirectly, even in anonymized summaries — HIPAA obligations follow the data.

## **A. What the Architecture Satisfies**

### **Security Rule: Access Controls and Minimum Necessary**

The Security Rule requires covered entities to implement access controls limiting access to PHI to those who need it to perform their job. The Local Orchestrator (Layer 1) addresses this by design: it sits on hardware your practice controls, meaning no third party can access the system or its logs without your permission. All requests are logged, creating an audit trail that directly satisfies the Security Rule's audit control requirements.

The Security Rule's 'minimum necessary' standard — requiring that only the PHI actually needed for a task be disclosed or accessed — is addressed by the Context Packager (Layer 2). Before any request leaves your local environment, the Context Packager applies data minimization rules that strip or mask identifying fields. This is not optional behavior the system can bypass; it is enforced at the architecture level.

### **Security Rule: Transmission Security**

The Security Rule requires that any electronic PHI transmitted over a network be protected against unauthorized interception. Because all calls to external AI services (Layer 3) are mediated by the Context Packager, identifiable PHI should never reach a cloud model under correct operation. The architecture satisfies transmission security not primarily through encryption (which is a vendor-level control) but through elimination: if PHI is stripped before transmission, it cannot be intercepted.

### **Privacy Rule: Workforce Controls and Oversight**

The Privacy Rule requires covered entities to train workforce members on HIPAA requirements and to implement reasonable safeguards. The WAL trust tier system (Layer 4) directly supports workforce oversight by controlling what AI workflows staff can invoke and with what degree of autonomy. Practices can configure WAL-0 for all workflows touching PHI, ensuring a qualified human reviews every AI output before it affects a patient record, a billing transaction, or a referral decision.

| Architecture Alignment Summary: Layer 1 satisfies audit control requirements. Layer 2 addresses the minimum necessary standard and transmission security. Layer 4 enforces workforce oversight controls by requiring human review at WAL-0 for sensitive workflows. |
| --- |

## **B. What the Architecture Does Not Automatically Solve**

### **Business Associate Agreements (BAAs)**

HIPAA requires covered entities to enter into a Business Associate Agreement with any third party that creates, receives, maintains, or transmits PHI on their behalf. Cloud AI providers are almost certainly Business Associates if they receive any PHI. The architecture reduces the likelihood that PHI reaches cloud models — but it does not eliminate the obligation to have BAAs in place.

Many major AI providers will sign BAAs under their enterprise tiers. Some will not. Some will sign BAAs that are inadequate under HIPAA. The architecture does not evaluate, negotiate, or execute BAAs. That is a legal and procurement task the owner must complete independently.

| Do not assume that 'the AI never sees real patient names' is sufficient. Courts and regulators have found that aggregated or contextual information can constitute PHI even without explicit identifiers. Consult your HIPAA counsel before operating without a BAA. |
| --- |

### **De-identification Standards**

HIPAA defines two methods for de-identifying PHI: the Expert Determination method and the Safe Harbor method. Safe Harbor requires the removal of 18 specific identifier types. The Context Packager can be configured to strip many of these fields, but the architecture does not automatically apply a HIPAA-compliant de-identification standard. The rules governing what constitutes adequate de-identification must be defined by someone with HIPAA expertise and then implemented as configuration.

### **Breach Notification**

The Breach Notification Rule requires covered entities to notify affected individuals, HHS, and in some cases media outlets when a breach of unsecured PHI occurs. The architecture's audit logs provide forensic evidence to support breach investigation. However, the architecture does not detect breaches automatically, does not classify security events, and does not trigger notifications. Those processes require a breach response plan that exists outside the AI system.

### **State Law Preemption**

Where state privacy laws are more protective than HIPAA, the stricter standard applies. Several states — including California, New York, Texas, and others — impose requirements that go beyond the federal floor. The architecture is neutral on this point; it does not know your state's law and cannot be configured to comply with it without that legal analysis being done first.

## **C. What to Bring to Your Compliance Advisor**

When you meet with your HIPAA compliance officer or healthcare attorney, bring the following:

| **Question to Raise** | **Why It Matters** |
| --- | --- |
| Which cloud AI providers receive any information from our system, and do we have BAAs with each of them? | No BAA = potential HIPAA violation regardless of architecture. |
| Has our Context Packager configuration been reviewed against HIPAA's 18 Safe Harbor identifiers? | The Packager strips what you configure it to strip — not necessarily what HIPAA requires. |
| Are our audit logs sufficient to support a breach investigation under the Breach Notification Rule? | Logs are only useful if they capture the right events at the right level of detail. |
| Do any of our AI workflows touch PHI at WAL-1 or above, and have we documented the business justification? | WAL-1+ means reduced human review. You need a documented rationale for each exception. |
| Does our state impose requirements stricter than HIPAA that affect how we configure this system? | State preemption can override federal minimums. |
| Does our EHR vendor's acceptable use policy restrict AI integration in ways that affect our architecture? | EHR vendors often have terms governing third-party access to patient data. |

**  PART B — LEGAL PRACTICES**

  Attorney-Client Privilege, Confidentiality, and State Bar Rules

## **The Compliance Landscape in Plain Language**

For law firms, the dominant legal and ethical obligations around AI use derive from three overlapping sources: the attorney-client privilege (a common law evidentiary rule protecting client communications from disclosure), the duty of confidentiality under the Rules of Professional Conduct (an ongoing ethical obligation that is broader than privilege), and competence obligations under Rule 1.1 of the Model Rules of Professional Conduct, which courts and state bars are beginning to apply directly to AI use.

Attorney-client privilege protects communications between attorneys and clients made for the purpose of obtaining legal advice, provided they are kept confidential. Disclosure of privileged communications to third parties — including AI systems — can waive the privilege, depending on the circumstances and jurisdiction. This is not theoretical risk. Several courts have already grappled with questions of privilege waiver in the context of AI use.

The duty of confidentiality under Rule 1.6 is broader: it prohibits disclosure of any information relating to the representation of a client, regardless of privilege status, unless the client consents or an exception applies. Any AI system that receives client information is potentially implicated.

## **A. What the Architecture Satisfies**

### **Confidentiality: Local Processing as a Control Boundary**

The most significant confidentiality risk with AI in legal practice is disclosure to third parties — specifically, the cloud AI providers that process input data and may retain it, use it for model training, or expose it through security incidents. The Local Orchestrator (Layer 1) addresses this risk at the structural level: the Orchestrator runs on hardware the firm controls, processes requests internally, and only routes to cloud models what the Context Packager permits through.

This architecture is meaningfully different from sending client communications directly to a commercial AI chatbot, which is the high-risk behavior most bar ethics opinions are warning against. The Context Packager (Layer 2), when properly configured, can strip client-identifying information, matter references, and confidential substantive content before anything is transmitted to a third-party model.

### **Competence: Oversight and Human Review**

Rule 1.1 requires lawyers to provide competent representation, which several state bar ethics opinions have now interpreted to include understanding the capabilities and limitations of AI tools used in legal work. The WAL trust tier system directly supports competence compliance by ensuring that AI outputs — especially at WAL-0 — are reviewed by a qualified attorney before they affect any client matter. An AI that drafts, summarizes, or analyzes in a supervised WAL-0 workflow is a tool the attorney uses. An AI that acts autonomously on a client file is a delegation question with significant ethical dimensions.

### **Supervision: Audit Logs as Oversight Evidence**

Model Rules 5.1 and 5.3 require supervising attorneys to make reasonable efforts to ensure that the work of subordinates and non-attorneys conforms to the professional obligations of the firm. The audit logs generated by Layer 1 create a record of every AI interaction — what was requested, what was routed where, and what was returned. This log infrastructure supports the kind of supervisory oversight the Model Rules contemplate, and provides documentation if a firm ever needs to demonstrate to a bar disciplinary body that it exercised appropriate oversight.

| Architecture Alignment Summary: Layer 1 keeps processing on firm-controlled hardware and generates supervisory audit records. Layer 2 reduces client data exposure to cloud services. Layer 4 enforces attorney review at WAL-0, directly supporting competence and supervision obligations. |
| --- |

## **B. What the Architecture Does Not Automatically Solve**

### **Privilege Waiver Analysis**

Whether routing client communications through an AI system — even one that de-identifies them before cloud transmission — constitutes a waiver of attorney-client privilege is an unsettled legal question. The analysis depends on whether disclosure is 'voluntary,' whether the third party is an agent of the attorney, and the specific privilege law of the relevant jurisdiction. The architecture reduces the risk by limiting what reaches third parties, but it does not perform a privilege waiver analysis. That is a legal judgment the firm must make.

| Privilege waiver law varies significantly by state and federal circuit. Do not assume that de-identification eliminates privilege risk in all jurisdictions. Some courts have applied waiver even when the content was partially obscured. |
| --- |

### **State Bar Ethics Opinions on AI**

As of 2025-2026, state bars across the country are issuing guidance on AI use in legal practice at a rapid pace. The obligations vary significantly. Some states require client disclosure of AI use in certain contexts. Some prohibit AI-generated work product from being submitted to courts without disclosure. Some require specific technical safeguards as a condition of compliant use. This architecture is designed to be consistent with common bar guidance themes — particularly around oversight and confidentiality — but it cannot monitor or adapt to your state's specific and evolving rules.

### **Client Consent and Engagement Letters**

Several ethics opinions suggest that firms using AI in client matters should address the practice in their engagement letters and, in some cases, obtain informed client consent. The architecture does not generate engagement letters, does not communicate with clients, and does not manage consent. Updating intake documents and client agreements is a practice management task outside the scope of this system.

### **Output Accuracy and Hallucination Risk**

Legal AI outputs — case citations, statutory references, contract language — can contain factual errors, fabricated citations, and incorrect legal conclusions. Several attorneys have faced sanctions for filing AI-generated briefs containing fictitious case citations. The WAL-0 requirement for human review addresses this risk operationally, but it does not eliminate it. Overreliance — the tendency of reviewers to rubber-stamp AI outputs without genuine verification — remains a real risk that the architecture flags but cannot prevent. Attorney vigilance is irreplaceable.

## **C. What to Bring to Your Ethics Counsel or Bar Liaison**

| **Question to Raise** | **Why It Matters** |
| --- | --- |
| Has our state bar issued guidance on AI use in legal practice, and does our architecture satisfy its requirements? | Bar ethics opinions are jurisdiction-specific and evolving rapidly. |
| Does routing client information through our Context Packager — even in anonymized form — risk privilege waiver under our jurisdiction's standards? | Waiver analysis is fact- and jurisdiction-specific. It requires a legal opinion, not an architectural assumption. |
| Should we disclose AI use in our engagement letters? Do any of our practice areas require client consent? | Some states require disclosure. Others recommend it. Some clients will ask regardless. |
| Which of our workflows involve work product or client communications, and are those workflows configured to WAL-0? | WAL-1 or above for client-matter work requires a documented justification and supervision plan. |
| How do we document AI-assisted work product to satisfy court disclosure requirements in jurisdictions where we practice? | Courts in several circuits are requiring disclosure of AI-generated or AI-assisted filings. |
| What is our process for verifying AI-generated case citations, statutory references, and legal conclusions? | Sanctions have already been imposed for unverified AI-generated filings. |

**  PART C — FINANCIAL SERVICES FIRMS**

  Gramm-Leach-Bliley Act (GLBA): Safeguards Rule and Privacy Rule

## **The Compliance Landscape in Plain Language**

The Gramm-Leach-Bliley Act governs how financial institutions — including insurance agencies, mortgage brokers, securities dealers, tax preparers, and many other businesses that provide financial products or services to consumers — collect, protect, and share Nonpublic Personal Information (NPI). The two rules most directly relevant to AI use are the Safeguards Rule (technical and administrative controls for protecting NPI) and the Privacy Rule (obligations to inform customers about information sharing and to honor opt-outs).

In 2023, the FTC updated the Safeguards Rule significantly, adding requirements for multi-factor authentication, encryption, access controls, and security event logging that are more specific and demanding than prior versions. These updated requirements are in effect and apply to financial institutions with more than 5,000 customer records (with some exemptions for smaller firms). Even firms below the threshold should treat the Safeguards Rule as a baseline.

Nonpublic Personal Information under GLBA includes financial account numbers, Social Security numbers, income information, credit history, transaction data, and any other personally identifiable financial information that is not publicly available. When AI systems process customer data — even in a support, compliance, or operational capacity — GLBA obligations apply.

## **A. What the Architecture Satisfies**

### **Safeguards Rule: Access Controls and Audit Logging**

The updated Safeguards Rule requires financial institutions to implement access controls to prevent unauthorized access to customer information and to maintain audit trails sufficient to detect and investigate security events. The Local Orchestrator (Layer 1) satisfies both requirements structurally. Because it runs on hardware the firm controls, access to the system and its logs is controlled by the firm. Every interaction is logged, creating an audit trail that supports both internal oversight and regulatory examination.

### **Safeguards Rule: Data Minimization and Third-Party Oversight**

The Safeguards Rule requires firms to oversee their service providers through appropriate contractual arrangements and to ensure that providers implement appropriate safeguards for customer information. The Context Packager (Layer 2) supports this obligation by limiting what NPI reaches any cloud AI provider. Sending a minimized, de-identified request to a cloud model is a materially different risk posture than sending raw customer records. While vendor contracts and due diligence are still required, the architecture reduces the sensitivity of data those contracts need to cover.

### **Safeguards Rule: Human Oversight of Automated Processes**

Several regulatory bodies — including the FTC, the SEC, and FINRA — have expressed concern about financial firms deploying automated processes without adequate human oversight. The WAL trust tier system is directly aligned with this concern. By requiring WAL-0 for new workflows and defining explicit criteria for autonomy expansion, the framework creates an auditable rationale for any level of AI automation the firm employs. If an examiner asks how you supervise your AI workflows, the WAL governance records provide a documented answer.

| Architecture Alignment Summary: Layer 1 satisfies access control and audit logging requirements. Layer 2 reduces third-party NPI exposure and supports service provider oversight. Layer 4 provides documented human oversight controls that satisfy examiner expectations for automated process governance. |
| --- |

## **B. What the Architecture Does Not Automatically Solve**

### **Written Information Security Program (WISP)**

The updated Safeguards Rule requires covered institutions to develop, implement, and maintain a comprehensive Written Information Security Program. The WISP must be approved by the firm's board or senior leadership, include risk assessments, designate a qualified individual responsible for the program, and be reviewed annually. The architecture described in this framework is a component that belongs inside a WISP — it does not substitute for one. Firms that do not have a current, written WISP are not in compliance regardless of how well their AI system is configured.

### **GLBA Privacy Rule: Notice and Opt-Out Requirements**

The Privacy Rule requires financial institutions to provide customers with a clear and conspicuous privacy notice describing what information is collected, how it is used, and with whom it is shared. Customers must be given the opportunity to opt out of certain information sharing. If your AI system processes customer data in a way that is not covered by your existing privacy notice — for example, routing financial queries through a cloud AI model not previously disclosed — you may need to update your notice and obtain fresh opt-outs. The architecture does not generate privacy notices or manage opt-out records.

### **Sector-Specific Regulations Beyond GLBA**

GLBA is the federal floor, not the ceiling. Registered investment advisers are also subject to SEC rules on recordkeeping and cybersecurity. Broker-dealers face FINRA oversight. Insurance firms face state insurance department requirements that vary by state. Mortgage originators face CFPB regulations. Firms in these categories should treat this section as a starting point, not a complete compliance map.

### **AI-Specific Regulatory Attention in Financial Services**

The FTC, SEC, CFPB, and FINRA have all issued guidance and taken enforcement actions related to AI use in financial services. Areas of particular focus include: AI-generated financial advice or recommendations that may trigger investment adviser registration; fair lending and disparate impact concerns when AI assists in credit or underwriting decisions; and deceptive practices claims when AI-generated communications mislead consumers. These are not abstract future risks. The architecture's WAL tier requirements for human review at WAL-0 provide structural protection against autonomous AI decision-making in these sensitive areas — but the substantive regulatory analysis must still be done.

## **C. What to Bring to Your Compliance Advisor or Regulator Liaison**

| **Question to Raise** | **Why It Matters** |
| --- | --- |
| Do we have a current, board-approved Written Information Security Program, and does it address AI systems? | The updated Safeguards Rule requires a WISP. AI systems should be explicitly covered. |
| Have we identified all cloud AI providers that receive any customer NPI, directly or indirectly, through our system? | You need written agreements with all service providers who handle NPI. |
| Does our privacy notice cover the use of AI systems that process or transmit customer financial information? | Undisclosed data practices can trigger FTC enforcement and state AG actions. |
| Are any of our AI workflows involved in credit decisions, investment recommendations, or insurance underwriting? | These areas carry heightened fair lending, suitability, and consumer protection risk. |
| Are we subject to SEC, FINRA, CFPB, or state insurance department rules in addition to GLBA? | Sector-specific rules layer on top of GLBA. You may have additional AI governance obligations. |
| Have we documented our WAL-tier decisions for any automated workflow involving customer data? | Examiners are increasingly asking for documentation of AI oversight frameworks. WAL logs provide that evidence. |

# **Cross-Industry Considerations**

Regardless of which regulatory regime applies to your business, the following principles hold across all three sectors. Think of this section as the short list of things that are true everywhere.

## **The Architecture Reduces Risk. It Does Not Eliminate It.**

This framework was designed to make regulated-industry AI use safer, more governable, and more defensible. It does that by keeping sensitive data local, enforcing human review at WAL-0, creating audit records, and preventing single-vendor dependency. None of that is the same as being in compliance. Compliance requires legal analysis, written policies, vendor agreements, staff training, and ongoing monitoring. The architecture provides a foundation that makes those compliance efforts more tractable. It is not a substitute for them.

## **WAL-0 Is Your Regulatory Safe Harbor**

Across all three regulatory regimes, the common theme is that human oversight of consequential AI outputs is either explicitly required or strongly implied. WAL-0 — requiring a qualified person to review and approve every AI output before it affects a patient, client, or customer — is the closest thing to a regulatory safe harbor this architecture offers. You should have a documented, defensible reason for every workflow you promote above WAL-0. When in doubt, stay at WAL-0.

## **Audit Logs Are Regulatory Evidence**

The interaction logs generated by the Local Orchestrator are not just an operational tool — they are potential regulatory evidence. In a HIPAA audit, they demonstrate access controls. In a bar investigation, they document supervisor oversight. In an FTC or SEC examination, they show governance over automated processes. Keep logs. Protect them. Have a documented retention policy that matches or exceeds the retention requirements of your regulatory regime.

## **Context Packager Configuration Is a Compliance Task**

The Context Packager enforces only what you configure it to enforce. Out of the box, it strips what you tell it to strip. Getting that configuration right — defining which fields constitute PHI under HIPAA, which content is covered by confidentiality obligations under Rule 1.6, which data is NPI under GLBA — is not a technical task. It requires someone with domain expertise to translate legal requirements into system rules. Budget for that work, and document it as part of your compliance program.

## **Regulations Are Moving Faster Than Architecture**

The regulatory environment for AI in regulated industries is changing rapidly. State laws, federal guidance, bar opinions, and sector-specific rules are all in active development. This document reflects the landscape as of early 2026. Build your compliance program on current legal advice, not static documents. Review your AI governance posture at least annually, and more frequently if your regulatory environment is active.

## **WAL Tier Reference: Regulatory Risk by Level**

| **WAL Level** | **Healthcare (HIPAA)** | **Legal (Privilege/Bar)** | **Financial Services (GLBA)** |
| --- | --- | --- | --- |
| **WAL-0 Recommend** | Lowest regulatory risk. Clinician reviews every AI output. Consistent with HIPAA workforce oversight requirements. | Consistent with competence and supervision requirements. Attorney reviews all outputs. Recommended default for all client matter work. | Aligns with examiner expectations for human oversight of automated processes. Defensible default under GLBA Safeguards Rule. |
| **WAL-1 Assist** | Acceptable for administrative tasks that do not involve PHI or clinical decisions. BAA and WISP review recommended before expansion. | Appropriate for non-client tasks (scheduling, billing lookups). Requires documented justification for any client-adjacent work. Ethics opinion review recommended. | May be appropriate for routine administrative workflows. Not recommended for tasks touching NPI without documented controls and service provider agreements. |
| **WAL-2 Execute** | High regulatory scrutiny if PHI is involved. Requires thorough documentation, BAA coverage, and legal review before deployment. | Significant ethics and privilege considerations if any client matter content is in scope. Ethics counsel review required. | Regulatory and examiner scrutiny increases significantly at this level. Detailed WISP documentation and legal review required before deployment. |
| **WAL-3 Automate** | Should not be applied to any workflow involving PHI or clinical decisions without extensive legal review and documented regulatory approval. | Should not be applied to client matter workflows without explicit ethics opinion and engagement letter authorization. Potential UPL and malpractice implications. | Generally not appropriate for NPI-adjacent workflows without extensive regulatory clearance. AI-driven financial recommendations or decisions carry heightened consumer protection and suitability risk. |

# **A Final Note on This Document**

This supplement was written to give you a working map — a way to walk into a conversation with your compliance advisor, attorney, or outside counsel and ask the right questions from an informed position. It is not a compliance program. It is not a legal opinion. It is a translation layer between an architectural framework and the regulatory realities of the industries it serves.

The most valuable thing you can do after reading this document is schedule that conversation. Bring the questions from each section's advisory table. Come prepared to explain how your Context Packager is configured, what WAL levels govern your sensitive workflows, and who in your organization reviews AI outputs before they affect a patient, client, or customer.

Regulators, bar examiners, and auditors are not asking whether you use AI. They are asking how you govern it. This framework gives you the foundation to answer that question with confidence.

| AI Orchestration Framework for Small Business — Document 5: Regulated Industry Supplement  │  March 2026  │  Pre-Decisional Working Draft |
| --- |