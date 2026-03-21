# Vendor Evaluation Guide

*A buyer's guide for evaluating AI vendors and consultants.*

---

AI ORCHESTRATION FRAMEWORK

FOR SMALL BUSINESS

Document 4

**Vendor Evaluation Guide**

*What to Ask Before You Sign Anything*

March 2026  |  Pre-Decisional Working Draft

*Load Master Context Document at start of each document session*

# **Introduction**

**Vendors are in the business of selling. **That is not a criticism — it is a fact you must keep front of mind during every meeting, every demo, and every proposal review. AI vendors, in particular, operate in a market where few buyers understand what they are actually buying. That information gap works in the vendor's favor.

This guide is designed to close that gap. It gives you a specific set of questions to ask, a list of behaviors that should end the conversation, a scoring tool to compare competing proposals on an equal footing, and examples of what honest answers sound like versus what deflection sounds like.

You do not need technical expertise to use this guide. You need patience, a willingness to ask the same question twice when the first answer does not answer it, and the confidence to walk away when the numbers or the behavior do not add up.

| **How This Guide Fits the Framework** |
| --- |
| The AI Orchestration Framework this series describes is built on a specific principle: your business controls the routing layer, your data is minimized before it leaves your environment, and no single vendor owns your workflow. |
| A vendor who cannot support that architecture — or who actively discourages it — is not a partner. They are a risk. |
| Use the questions and checklists in this guide to verify alignment with that principle before any contract is signed. |

# **Section 1: The Ten Questions**

Ask every one of these questions in every vendor meeting. Record the answers. Compare them across vendors. A vendor who cannot answer them clearly, or who becomes evasive, is showing you something important.

## **Question 1: Where Does My Data Go, and Who Can Access It?**

This is the first question for a reason. Before you evaluate features or pricing, you need to understand data flow.

**What you are asking: **Does the vendor's system receive, store, or transmit my business data? To what servers? In what country? Who at the vendor company can access it, and under what circumstances?

**Why it matters: **AI systems that process your data on vendor-controlled infrastructure can expose customer records, financial information, or regulated data to third parties — including subcontractors and law enforcement in foreign jurisdictions — without your awareness. The framework this series describes minimizes external data exposure by design. A vendor whose system requires sending all your data to their servers is structurally incompatible with that approach.

**What a complete answer includes: **Specific server locations (not just 'the cloud'), named subprocessors, retention periods, and a clear statement of who has human access to your data and under what policy.

## **Question 2: Can I Export All My Data and Configurations at Any Time?**

**What you are asking: **If I decide to leave tomorrow, can I take everything with me — my data, my workflow configurations, my prompt templates, my logs — in a standard format I can actually use?

**Why it matters: **Vendor lock-in is built in layers. One of the most common is making your data or configurations proprietary, so that switching vendors requires rebuilding from scratch. The framework uses model-agnostic routing precisely to avoid this. A vendor who cannot provide clean, complete, portable exports is betting that the switching cost will keep you from leaving.

**What a complete answer includes: **Specific export formats (CSV, JSON, standard API schemas), a written commitment to export availability, and a clear statement that there is no export fee.

## **Question 3: What Happens to My Data If I Cancel?**

**What you are asking: **When my contract ends, what happens to every copy of my data the vendor holds — including backups, training datasets, and logs?

**Why it matters: **Cancellation clauses that permit indefinite data retention, or that use your data to improve vendor models, create ongoing exposure after the business relationship ends. In regulated industries, this is a compliance problem, not just a preference.

**What a complete answer includes: **A specific data deletion timeline, a written deletion confirmation process, and an explicit statement that your data was not and will not be used to train the vendor's shared models without your documented consent.

## **Question 4: Does This System Work With AI Models Other Than Your Own?**

**What you are asking: **If I want to replace the underlying AI model — or route certain tasks to a different provider — can I do that without rebuilding the entire system?

**Why it matters: **The framework this series describes is model-agnostic by design. The local orchestrator routes tasks; the cloud models that perform the work are interchangeable. A vendor whose platform only works with their proprietary model is selling you a dependency, not a solution.

**What a complete answer includes: **A technical explanation of how the system connects to AI models, confirmation of support for standard APIs (OpenAI-compatible, Anthropic, Google, open-source), and a written statement that model substitution does not void the service agreement.

## **Question 5: What Are the Total Costs Over 24 Months?**

**What you are asking: **Give me a complete cost model — implementation, licensing, per-seat fees, usage-based fees, support tiers, and upgrade costs — for the first two years. In writing.

**Why it matters: **AI vendor pricing is designed to look affordable at entry and scale aggressively as usage grows. The monthly seat fee is rarely the full cost. Vendors routinely charge separately for support, for data storage, for API calls above a threshold, and for access to features that were demonstrated as standard during the sales process.

**What a complete answer includes: **A written cost schedule, a clear description of what triggers overage charges, and a cap or ceiling on annual cost increases during the contract term.

## **Question 6: Who Owns the Outputs This System Produces?**

**What you are asking: **When your system produces a document, recommendation, or decision output for my business, who owns that output — me or you?

**Why it matters: **Some vendor agreements claim rights over AI-generated outputs, particularly if those outputs are used to improve the vendor's model. In professional service contexts, this can create liability problems. In regulated industries, it may conflict with recordkeeping requirements.

**What a complete answer includes: **An unambiguous statement that all outputs generated using your business data are owned by your business, with no vendor license or reuse rights, confirmed in the service agreement.

## **Question 7: What Is Your Uptime Guarantee, and What Happens When You Go Down?**

**What you are asking: **What is the contractual uptime SLA, how is downtime calculated, and what happens to my workflows — and my business — when your system is unavailable?

**Why it matters: **The framework this series describes is designed with local operation as a fallback. Cloud model vendors will have outages. The question is not whether downtime will happen — it will — but whether your business can continue operating when it does. A vendor who presents 99.9% uptime as a complete answer has not thought seriously about business continuity.

**What a complete answer includes: **Specific SLA terms with defined penalties, a documented incident notification process, and a clear explanation of how to operate core workflows during an outage.

## **Question 8: How Do You Handle a Data Breach Involving My Information?**

**What you are asking: **If your systems are compromised and my business data is involved, what are your notification obligations, what do you do, and what do I need to do?

**Why it matters: **A vendor who holds your data is a security exposure. You need contractual certainty about notification timelines, response procedures, and liability allocation before an incident occurs — not after.

**What a complete answer includes: **Contractual notification timelines (72 hours is a reasonable floor), a described incident response process, a statement of the vendor's liability in a breach scenario, and confirmation of their own cyber insurance coverage.

## **Question 9: Can I See the Audit Log for Everything This System Does?**

**What you are asking: **For every action this system takes — every AI call, every output, every automated workflow step — is there a log I own, can access at any time, and can export?

**Why it matters: **The framework this series describes requires full auditability by design. AI systems that produce outputs without accessible logs prevent you from detecting errors, investigating problems, or demonstrating compliance. A vendor who restricts log access is making it impossible for you to govern your own AI usage.

**What a complete answer includes: **Confirmation that logs are complete, accessible without additional charges, exportable in a readable format, and retained for a defined period that meets your compliance obligations.

## **Question 10: What Does It Cost and How Long Does It Take to Leave?**

**What you are asking: **Walk me through the exit process: How do I notify you? What is the timeline? Are there early termination fees? What will I lose in terms of functionality or data if I switch to a competitor?

**Why it matters: **The cost of leaving a vendor is the hidden price of the contract. Vendors who make exit expensive or complicated are relying on that friction to retain customers who would otherwise leave. Asking this question before signing forces the vendor to put their exit terms on the table — and tells you a great deal about how they view the business relationship.

**What a complete answer includes: **A written description of the termination process, specific exit timelines, a clear statement of early termination fees if any, and a confirmation that data portability is available through the transition period.

# **Section 2: Red Flag Checklist**

The following behaviors, statements, and contract terms are warning signs. One or two of these may be explainable. A pattern of them should end the conversation.

| **Category** | **Red Flag** | **Why It Matters** |
| --- | --- | --- |
| **Lock-In Risk** | **Proprietary data format with no export path** | Your data, configurations, and workflow logic cannot leave their system in a usable format. Switching vendors means starting over from scratch. |
| **Lock-In Risk** | **Per-seat or usage pricing with no cap** | Costs scale unpredictably. A vendor who cannot provide a cost ceiling for normal business usage is transferring financial risk to you. |
| **Lock-In Risk** | **The system only works with their AI model** | Model-agnostic routing — a core feature of this framework — is impossible. You are buying a permanent dependency on their model's availability, pricing, and decisions. |
| **Lock-In Risk** | **Multi-year contract required to access key features** | Features demonstrated in the sales process are held behind a long-term commitment. The commitment is how they recover the sales cost, not how they deliver value. |
| **Data Risk** | **Vague or absent data residency disclosure** | You do not know where your data is stored, what jurisdiction it falls under, or who can access it. This is a compliance problem in any regulated industry. |
| **Data Risk** | **Contract includes rights to use your data for model training** | Your business data — including client information — is being used to improve a product sold to your competitors. |
| **Data Risk** | **No deletion guarantee on contract termination** | The vendor retains your data indefinitely after the relationship ends. You have no way to verify or enforce deletion. |
| **Data Risk** | **Breach notification window exceeds 72 hours** | Industry standard and most regulatory frameworks require prompt notification. A longer window protects the vendor, not you. |
| **Misalignment** | **Vendor discourages local orchestration or local control** | This framework's core value is business-controlled routing. A vendor who argues against it is arguing against your ability to govern your own AI usage. |
| **Misalignment** | **Cannot explain how their system handles sensitive data differently from routine data** | Data minimization — sending only what is required — is a non-negotiable framework principle. A vendor who cannot describe their approach to it does not have one. |
| **Misalignment** | **Audit logs are unavailable, partial, or behind a paid tier** | You cannot govern what you cannot see. A vendor who restricts log access is preventing you from managing your own system. |
| **Misalignment** | **Resistance to answering questions in writing** | Verbal assurances are not enforceable. A vendor who will not confirm data practices, exit terms, or SLAs in the contract is not committing to them. |

# **Section 3: Vendor Scoring Rubric**

Use this rubric to evaluate and compare vendor proposals side by side. Score each criterion from 1 to 3. Total the scores. A vendor scoring below 14 out of 24 should not advance in the selection process without written clarification on every low-scoring criterion.

| **How to Use This Rubric** |
| --- |
| Score each vendor independently before comparing. Do not adjust scores based on price alone. |
| A score of 1 on Data Sovereignty or Exit Rights is a disqualifying finding, regardless of the total score. |
| Bring the completed rubric to any reference calls with the vendor's existing customers. |

| **Criterion** | **1 — Concern (proceed with caution or disqualify)** | **2 — Acceptable (meets minimum standard)** | **3 — Strong (exceeds expectations)** |
| --- | --- | --- | --- |
| Data Sovereignty | Data processed on shared infrastructure in unspecified locations. No subprocessor disclosure. | Data stored in named locations. Subprocessors listed. Access policy documented. | Data isolated per customer. Local processing available. No vendor human access to data in normal operations. |
| Model Agnosticism | System only supports vendor's proprietary model. No third-party model support. | Third-party models supported via API with configuration required. Some lock-in remains. | Model-agnostic architecture confirmed. Switching models requires no workflow rebuild. |
| Data Portability & Exit | No export path. Early termination fees exceed two months of contract value. Deletion not guaranteed. | Data exportable in standard formats. Exit timeline defined. Reasonable termination terms. | Full export at any time at no charge. Confirmed deletion with written verification. No termination fee with standard notice. |
| Auditability | Logs unavailable or behind additional payment. No export capability. | Logs available but with limited retention. Export possible with manual steps. | Complete, real-time accessible logs. Exportable in standard formats. Retention meets compliance requirements. |
| Pricing Transparency | Usage-based pricing with no cap. Overages undefined. Key features behind separate tiers. | Pricing documented but complex. Some usage caps available. Feature set clear at contract tier. | Fixed or clearly bounded pricing. Written 24-month cost model provided. No surprise feature gates. |
| Vendor Responsiveness | Questions answered vaguely or redirected to sales collateral. Written commitments refused. | Most questions answered. Some specifics deferred to legal/contract review. | All ten questions answered specifically and in writing. Contract reflects verbal commitments. |
| Security & Breach Terms | No breach notification SLA. Vendor liability for breach unaddressed in contract. No cyber insurance disclosure. | Breach notification within 72 hours. Vendor liability addressed. Basic incident response described. | Breach notification within 24 hours. Clear vendor liability terms. Documented incident response plan. Cyber insurance confirmed. |
| Framework Compatibility | System architecture requires all data to leave the local environment. Local orchestration not supported. | Hybrid architecture possible with additional configuration. Local control partially supported. | Explicit support for local orchestration layer. Context packager / data minimization approach accommodated. |

| **Score Range** | **Interpretation** |
| --- | --- |
| 22–24 | Strong candidate. Verify commitments are reflected in contract language before signing. |
| 17–21 | Acceptable with conditions. Identify every criterion scored below 3 and require written resolution in the contract. |
| 14–16 | Marginal. Proceed only if the low scores are on non-critical criteria and a competing vendor is unavailable. |
| Below 14 | Do not proceed. The risk exposure is disproportionate to the potential benefit. |
| Any criterion scored 1 on Data Sovereignty, Exit Rights, or Auditability | Treat as disqualifying regardless of total score. |

# **Section 4: What a Good Response Looks Like**

This section shows you how to hear what a vendor is actually saying — not just what they are presenting. Good vendors have already thought through hard questions because they run honest businesses. Vendors with something to hide have standard deflections that sound reasonable until you push on them.

The examples below use five of the ten questions from Section 1. Apply the same evaluation standard to every question you ask.

| **Question: Where does my data go, and who can access it?** |
| --- |
| **✓  A Good Vendor Says:** | **✗  A Deflection Sounds Like:** |
| Your data is processed in US East data centers. We maintain a published subprocessor list updated quarterly. Under our standard tier, no Vendor employee has access to customer data except in a documented support incident, which requires your written authorization. I can send you the full data processing addendum today. | We take data security extremely seriously. Our infrastructure is SOC 2 certified, and we follow industry-best-practice encryption standards. Your data is safe with us. Do you want to see our security whitepaper? |

| **Question: What does it cost and how long does it take to leave?** |
| --- |
| **✓  A Good Vendor Says:** | **✗  A Deflection Sounds Like:** |
| Standard notice period is 60 days. No early termination fee after month six. During the notice period you have full data export access. We will send a deletion confirmation within 30 days of your final billing date. I can show you the exact termination language right now. | We are really focused on making sure customers get full value before they would ever consider leaving. We have a very high retention rate. What concerns do you have about the product that I can address? |

| **Question: Does this system work with AI models other than your own?** |
| --- |
| **✓  A Good Vendor Says:** | **✗  A Deflection Sounds Like:** |
| Yes. The routing layer connects to any OpenAI-compatible API endpoint, Anthropic, Google Vertex, or a local Ollama instance. Switching models is a configuration change, not a rebuild. We do not require you to use our preferred model. | Our proprietary model is actually significantly better for business use cases than generic alternatives. We have benchmarks that show the performance gap. Most customers find they do not need to use anything else once they see the results. |

| **Question: Can I see the audit log for everything this system does?** |
| --- |
| **✓  A Good Vendor Says:** | **✗  A Deflection Sounds Like:** |
| Yes. Every AI call, output, routing decision, and user action is logged in real time. You can query logs directly from the admin panel, export them as JSON or CSV, and we retain them for 12 months at no additional charge. Longer retention is available. | We provide reporting dashboards that show you summary activity and usage trends. For detailed transaction-level logs, that is part of our Enterprise tier, which includes a dedicated customer success manager and additional compliance tooling. |

| **Question: Will you confirm your data practices in writing in the contract?** |
| --- |
| **✓  A Good Vendor Says:** | **✗  A Deflection Sounds Like:** |
| Absolutely. Everything I have described today — data residency, subprocessors, deletion timelines, notification requirements — is in our standard Data Processing Addendum. If there are specific commitments you want to add, we can redline them. Our legal team typically turns those around in three to five business days. | Our standard agreement covers all the basics and is the same agreement we use with all our customers. We are not really in a position to make custom modifications, but I can assure you that we take these obligations seriously. Most companies are comfortable with the standard terms. |

| **The Single Most Important Test** |
| --- |
| Ask any of the ten questions from Section 1, and then ask: 'Can you put that in writing in the contract?' |
| A vendor who answers yes — and follows through — is telling you they stand behind what they said. |
| A vendor who says yes in the meeting and then cannot find the right contract language is telling you something different. |
| A vendor who says no is telling you everything you need to know. |

# **Quick Reference: Before You Sign**

## **The Ten Questions — Summary**

- Where does my data go, and who can access it?

- Can I export all my data and configurations at any time?

- What happens to my data if I cancel?

- Does this system work with AI models other than your own?

- What are the total costs over 24 months?

- Who owns the outputs this system produces?

- What is your uptime guarantee, and what happens when you go down?

- How do you handle a data breach involving my information?

- Can I see the audit log for everything this system does?

- What does it cost and how long does it take to leave?

## **Automatic Disqualifiers**

- Vendor refuses to confirm any of the ten questions in writing

- Contract includes rights to use your data for model training without explicit opt-in

- No data deletion guarantee on termination

- Audit logs unavailable without an additional payment tier

- System cannot support local orchestration or model-agnostic routing

## **Minimum Contract Requirements**

- Data Processing Addendum with named subprocessors and defined retention periods

- Deletion confirmation process with written acknowledgment

- Breach notification within 72 hours (24 is preferred)

- Data portability at no additional charge in standard formats

- Termination terms with defined notice period and no unreasonable early exit fees

- Uptime SLA with defined remedies for breach

*AI Orchestration Framework for Small Business — Document 4: Vendor Evaluation Guide*

March 2026  |  Pre-Decisional Working Draft