# Sovereign AI for a Rural School District

## Consolidated Architecture & Implementation Report

*Synthesized from multi-model AI research: Claude (Anthropic) + Gemini (Google)*

> **Note:** This document describes a reference architecture applied to a class of rural K-12 environment. District-specific details have been generalized.

---

# **Executive Summary**

the district (the district) stands at a strategic inflection point. The uncontrolled adoption of commercial AI tools — teachers pasting IEP content into consumer chatbots, administrators querying behavioral data through cloud interfaces — creates direct and immediate exposure under Georgia's Student Data Privacy Act (O.C.G.A. §§ 20-2-660 through 20-2-668) and the federal Family Educational Rights and Privacy Act (FERPA). The question before the Board is not whether to allow AI in schools; it is whether to allow it without guardrails.

This consolidated report synthesizes two independent AI research analyses — one from Claude (Anthropic), focused on Georgia legal compliance and cost modeling, and one from Gemini (Google), focused on K-12 operational scenarios and technical specification — into a single, actionable blueprint. The result is a Local-First AI Orchestration System designed specifically for the county's Title I, rural context: one that keeps student data on district-controlled hardware in the district seat while leveraging frontier cloud models only for anonymized, privacy-safe instructional tasks.

| **The Core Principle: Bounded Local Reasoning** The district central node does not compete with cloud AI models. It acts as a Chief of Staff — handling deterministic lookups and structured data verification locally, and packaging only anonymized context for cloud escalation. AI handles assembly. Humans make decisions. Student data stays in Georgia. |
| --- |

## **What the Board Needs to Know**

**The privacy risk is real and current. **The architecture provides structural guardrails — compliance enforced by code and network design, not by vendor contracts or policy alone.

**The cost case is compelling. **A Local-First approach projects a five-year Total Cost of Ownership (TCO) of approximately $200,000 versus $540,000+ for comparable SaaS licensing for staff alone — a savings exceeding 63%.

**The staffing gap is the real barrier. **This architecture requires Linux, Docker, and LLM management skills that the district's four-person IT team cannot sustain alone. The recommended deployment path runs through the the regional education service agency (RESA) shared-services model, amortizing specialized AI engineering across member districts.

**A single, centralized hub in the district seat is the right architecture for the district. **Distributing servers to each of the district's seven school sites would be unmanageable for the current IT team. One node in the district seat — serving all sites over the district's existing fiber WAN — maximizes uptime, minimizes maintenance burden, and centralizes security control.

# **Section 1: Strategic Context ****&**** Situational Analysis**

## **1.1 Demographic ****&**** Socioeconomic Imperatives**

the district serves between 3,375 and 3,454 students across seven schools: the county High School, the county Middle School, Hoboken Elementary, the district seat Elementary, the district seat Primary, Atkinson Elementary, and Waynesville Primary. As a Title I district, nearly 70% of students qualify for free or reduced-price lunch programs. The child poverty rate stands at 17.1%.

In this context, the district is the primary provider of high-quality digital access for most students. A cloud-exclusive AI strategy inherently favors students with robust home internet and personal devices, creating a two-tiered system. The Local-First architecture is equity-native: by centralizing AI processing on district hardware in the district seat, every student on the district network accesses identical, privacy-preserved AI tools regardless of economic status.

## **1.2 The Connectivity Landscape: The Hard Ceiling**

Approximately 76% of locations in the county are considered served with high-speed internet, leaving 24% — nearly one in four households — unserved or underserved. This creates a hard ceiling for instructional design. Teachers cannot assign homework requiring cloud AI tools if a quarter of their class cannot reliably access them.

The the district central node directly addresses this constraint. By handling Tier 1 (deterministic) and Tier 2 (local verification) tasks entirely within the district's fiber WAN, the architecture eliminates the need for students or teachers to rely on external internet for the majority of AI interactions. When the district's ISP experiences an outage, the the district seat node continues serving all schools over the internal network — a resilience posture that cloud-only tools cannot match.

## **1.3 The IT Staffing Reality**

The district's technology department consists of Director of Technology Alan Morgan, SIS/PowerSchool Coordinator David Moody, and three technicians: Pete Santiago, Luke Sinclair, and Josh Smith. This yields approximately one technician per 1,125 students. Managing a self-hosted AI environment — Linux servers, Docker containers, GPU drivers, API gateways — sits well outside typical K-12 IT scope.

Attempting this deployment in isolation would create extreme technical debt risk. The the regional education service agency (RESA) shared-services model is therefore not optional — it is the mechanism that makes this architecture sustainable. By pooling resources with neighboring Ware, Pierce, and other member districts, the county gains access to AI systems engineering talent without bearing the full cost of a specialized hire.

# **Section 2: Technical Architecture — The the district central node**

## **2.1 Architecture Philosophy: Bounded Local Reasoning**

The Local Orchestrator is not a chatbot. It is a traffic controller for intelligence. Its primary directive is to prevent expensive, privacy-risky cloud processing for tasks that can be handled cheaply and safely on the the district seat server. Most current AI interactions in schools involve context dumping — pasting raw student records into cloud interfaces. The Orchestrator intercepts this pattern entirely.

When a user submits a query, the the district seat node first classifies intent. Deterministic tasks (lookups, roster queries, schedule checks) are resolved instantly from local databases. Structured processing tasks (form extraction, compliance checks, basic summarization) are handled by a locally-running open-source Small Language Model (7B to 13B parameters). Only when a task requires genuine creativity or complex synthesis does the system engage the cloud — and even then, only after the Context Packager has stripped all personally identifiable information.

| **Centralization Rationale** Both research sources independently recommend against distributing servers to each of the district's seven school sites. A single the district central node is the correct architecture for a four-person IT team. The district's existing fiber WAN delivers sub-100ms latency to all sites, making distributed compute unnecessary and centralized management vastly superior. |
| --- |

## **2.2 Three-Tier Task Classification**

Every user interaction is classified into one of three execution tiers before any processing occurs:

| **Tier** | **Name** | **Examples (the district Context)** | **Routing** | **Privacy Risk** | **Cost** |
| --- | --- | --- | --- | --- | --- |
| **1** | The School Clerk (Deterministic) | Attendance lookup, bus status, GSE standards list, ClassLink password reset, lunch menu | the district seat Node — SQL/Vector Search only | None | $0.00 |
| **2** | The Compliance Officer (Verify Locally) | IEP compliance audit, budget code validation, FTE pre-validation, PLAAFP baseline check, behavioral pattern analysis | the district seat Node — Local LLM (Llama 3 8B / Mistral 7B) | Low (On-premise) | Electricity only |
| **3** | The Instructional Coach (Cloud Escalation) | Differentiated lesson plans, parent comm drafts, complex data synthesis, essay rhetorical analysis, CCRPI narrative | Cloud API (Claude Sonnet / GPT-4) via Context Packager | Managed — PII redacted | ~$0.01–$0.03/query |
| **BLOCKED** | High-Stakes Prohibited | Write IEP goals, predict GMAS scores, recommend discipline, educator evaluations | Rejected / Flagged — Human Required | N/A | N/A |

## **2.3 Privacy Preservation: The Context Packager**

No student data reaches the cloud without passing through the Context Packager — a multi-stage redaction and sanitization process running on the the district seat node before any API call is dispatched:

- Entity Recognition: Scans prompts for student names, GTIDs, dates of birth, and other PII patterns.

- Tokenization: Replaces identifiers with opaque tokens. 'John Smith' becomes 'Student_Alpha'; a specific IEP date becomes 'Meeting_Date.'

- Keyword Filtering: Checks against a local Data Loss Prevention (DLP) rule set for sensitive terms related to medical conditions, disciplinary actions, or abuse allegations. Matches are blocked or flagged for human review.

- Re-identification (Local Only): When the cloud model returns a response, the the district seat node swaps tokens back to real identifiers before displaying text to the teacher. The cloud provider never sees real names.

This architectural guarantee is materially stronger than the contractual promises of SaaS vendors. Compliance with FERPA and Georgia SB 89 is enforced by code and network topology, not by lawyer-drafted Terms of Service. The keys to student identity never leave the district seat.

## **2.4 Key Architectural Components**

- Model Performance Ledger: Tracks cost, latency, and human approval rates per model per task type. Routes cheap tasks to Gemini Flash-Lite ($0.10/M tokens) and premium tasks to Claude Sonnet ($3.00/M tokens) based on measured performance.

- Append-Only Audit Log: Every action, cloud call, and execution decision is permanently recorded. Cannot be modified by the agent. Satisfies FERPA 34 CFR § 99.32 and supports parent Data Receipts per O.C.G.A. § 20-2-667.

- Memory Governance Layer: Immutable raw memory with provenance tracking. Derived summaries expire on schedule and regenerate from source. Political, religious, and psychometric data is tagged and shredded per O.C.G.A. § 20-2-660.

- OneRoster / ClassLink Gate: Dynamic identity validation against district rostering. A teacher accesses only data for students in their current sections. Access revokes automatically when a section assignment changes.

- CIPA Safety Classifier: A dedicated Tier 2 model scans both inputs and outputs for CIPA-prohibited content, operating independently of cloud provider safety filters. Required double-check before any student-facing deployment.

- GSE CASE Integration: The Georgia Standards of Excellence are ingested directly from the GaDOE OpenSALT API and stored in a local vector database. Every teacher prompt is grounded in accurate, current Georgia standards — a precision advantage no national SaaS vendor can replicate.

# **Section 3: Infrastructure — The the district seat Central Hub**

## **3.1 Centralized Hub Strategy**

Both independent research analyses explicitly recommended against a distributed server deployment across the district's seven school sites. Maintaining seven distinct server closets with varying power, cooling, and physical security conditions is unmanageable for a team of four technicians. The correct architecture for the county is a single, enterprise-grade the district seat Central Hub.

| **Siting Recommendation** The the district central node should be located at the the county District Office or the county High School in the district seat — the facilities with the most robust power infrastructure, physical security, and proximity to the district's fiber WAN aggregation point. All seven school sites connect to this hub over existing district fiber. |
| --- |

The district's internal fiber WAN links all outlying schools to the district seat. A teacher at Waynesville Primary sends a request; it travels over district fiber to the the district seat node, is processed, and the answer is returned — typically in under two seconds for Tier 1 tasks. This leverages internal bandwidth that is both faster and more reliable than the district's external internet connection, and critically, it means the AI infrastructure continues functioning during ISP outages.

## **3.2 Recommended Hardware Specification**

| **Component** | **Specification** | **Rationale** |
| --- | --- | --- |
| **Compute** | Dell PowerEdge T560 Tower (x4 nodes) | iDRAC9 remote management; hot-swap drives; 5-yr ProSupport; DOAS contract eligible |
| **GPU** | NVIDIA L4 24GB PCIe (x2 per node) | 70W TDP; purpose-built inference; runs 13B models; upgrade path to L40S for RESA scale |
| **System RAM** | 256–512GB DDR5 ECC RDIMM per node | ECC required for 24/7 operation; headroom for vector DB and concurrent model loading |
| **OS/Model Storage** | 2x 1.92TB NVMe SSD (RAID 1) | Fast model loading; RAID 1 for OS redundancy; 13B model ~8GB with room for multiple models |
| **Data/Audit Storage** | 4x 4TB SAS HDD (RAID 10) | Audit logs, memory store, vector DB; RAID 10 for write durability and read speed |
| **Network** | Dual 25Gbps SFP28 bonded | High-bandwidth backhaul for concurrent inference; failover bonding prevents single link failure |
| **Power** | Redundant 1100W PSU + UPS 1500VA | Redundant power is non-negotiable for 24/7 operations; single hub is single point of failure without this |
| **Isolation** | Docker CE + Firecracker MicroVM (third-party skills) | MicroVM prevents kernel-level escapes; required before any external skill marketplace integration |

## **3.3 Software Stack**

- Container Runtime: Docker CE + Firecracker (MicroVM for third-party skills)

- LLM Inference: Ollama (primary) | vLLM for higher-throughput RESA deployments

- Local LLM Model: Mistral 7B or Llama 3.1 8B (routing/orchestration) | Phi-3 Medium (CIPA classifier)

- Vector Database: Qdrant (self-hosted) for GSE standards, board policies, and curriculum documents

- Orchestration: n8n or custom Python agent (Phase 1–3) | MCP adapters (Phase 4)

- Identity: ClassLink / Clever OneRoster API (SSO validation and roster gating)

- Audit Log: Append-only filesystem (chattr +a) + Elasticsearch for query and reporting

- Monitoring: Grafana + Prometheus for GPU utilization, inference latency, and API cost tracking

- Encryption: LUKS full-disk encryption for /memory and /audit partitions

## **3.4 Network Upgrades ****&**** E-Rate Strategy**

The Bounded Local Reasoning model saves external internet bandwidth but increases internal WAN traffic. When a class of 30 students at Hoboken Elementary interacts with the AI, that data flows to the the district seat hub over district fiber. Fiber links connecting outlying schools must be verified for capacity — links currently running at 1Gbps may require upgrading to 10Gbps to prevent bottlenecks during peak instructional periods.

E-Rate Category 2 funding explicitly covers switches, routers, cabling, and wireless access points — the network infrastructure supporting the AI deployment. AI compute hardware itself is not E-Rate eligible. Districts that missed the FY2026 E-Rate window should prepare for FY2027 filing (opening January 2027), using the intervening period to design network architecture specifications. By maximizing E-Rate subsidies on the network, the district frees SPLOST capital to fund the the district seat server cluster.

## **3.5 Offline Continuity**

If the district's ISP experiences an outage — not uncommon in rural areas due to fiber cuts or severe weather — the the district central node remains fully operational for Tier 1 and Tier 2 tasks over the internal WAN. Tier 3 cloud escalations queue silently (do not fail with an error) and teachers receive a clear Offline Mode notification. The system continues serving all seven schools for the vast majority of AI use cases. This resilience converts the AI system from a discretionary tool into critical infrastructure comparable to HVAC or network uptime.

# **Section 4: Student Data Privacy ****&**** Georgia Legal Framework**

## **4.1 Privacy by Architecture, Not by Contract**

Where SaaS tools like Google Workspace for Education achieve FERPA compliance through vendor Data Processing Agreements (DPAs), this architecture achieves compliance through structural design: student PII physically cannot reach cloud endpoints without explicit code-enforced redaction. This is a materially stronger posture than contractual compliance.

| **Law / Regulation** | **Key Requirements** | **Architecture Response** |
| --- | --- | --- |
| **FERPA 20 U.S.C. § 1232g** | Education records under district control; minimum necessary disclosure; complete audit trail | Raw records never leave the district seat; Context Packager enforces minimum-necessary; append-only log records every disclosure event |
| **Georgia SB 89 O.C.G.A. §§ 20-2-660–668** | No behavioral targeting; no psychographic profiling; parental access within 3 business days; privacy officer required | Toxic data shredder removes political/religious/psychometric data nightly; audit log generates parent Data Receipts; opt-out flag blocks ALL Tier 3 escalation |
| **COPPA 2025 ****&**** CIPA** | Opt-in consent for under-13; expanded PII includes biometrics; content filtering required; penalties up to $51,744/child | CIPA classifier scans inputs and outputs; student access limited to grades 9-12 initially; enrollment-grade gate prohibits under-9th access regardless of teacher config |
| **IDEA 2004 SBOE Rule 160-4-7** | All IEP eligibility, placement, and service decisions must be made by the IEP Team — qualified humans | GO-IEP write access prohibited at the firewall level. Agent has no network path to GO-IEP write endpoints. Sidecar-only pattern for IEP document assistance. |
| **O.C.G.A. § 10-1-912 GA Breach Notification** | If compromised data was encrypted and the key was not compromised, individual breach notification is not required | LUKS full-disk encryption on the district seat node directly activates this safe harbor provision — significant liability protection |

## **4.2 Non-Negotiable Human Gates Under Georgia Law**

The architecture enforces the following as hard approval gates — no administrator override permitted:

- Special Education eligibility and IEP placement (IDEA + SBOE Rule 160-4-7)

- Discipline decisions exceeding 10 days (O.C.G.A. § 20-2-751)

- All employment actions — AI may not generate evaluations, tenure recommendations, or employment decisions (Georgia Fair Dismissal Act, § 20-2-940–947)

- Student records amendments (FERPA)

- Section 504 plan determinations

## **4.3 Residual Privacy Risks**

- Cloud API Data Retention: OpenAI and Anthropic maintain 30-day retention for abuse monitoring. Execute Zero Data Retention (ZDR) agreements through the SDPC framework before Phase 4 activation.

- Redaction Completeness: No automated system achieves 100% PII removal. Combinations of grade + school + date + activity can re-identify students. Implement layered redaction: rule-based + ML + human review for edge cases.

- Parental Opt-Out Enforcement: Directory information opt-outs must block even anonymized Tier 3 escalation. This must be hard-coded — no administrator override permitted.

- Third-Party Skill Compliance: Each plugin added to the agent is potentially a new operator under § 20-2-666, requiring its own DPA. Use 1EdTech TrustEd Apps vetting score as a prerequisite for installation.

# **Section 5: Instructional Integration — The Georgia Standards Advantage**

## **5.1 Machine-Readable GSE Standards (CASE) Integration**

Georgia publishes the Georgia Standards of Excellence (GSE) in machine-readable CASE format via the GaDOE OpenSALT platform (case.georgiastandards.org). The the district seat node ingests these standards directly via REST API and stores them in the local Qdrant vector database. Loading the full GSE library is the single highest-leverage deployment action available to the county — and it is available today at no cost.

When a teacher asks for a lesson plan, the the district seat Orchestrator automatically injects the specific, current GSE standard code into the request before cloud escalation. The result is instructional content precisely aligned to what the district students will be assessed on via Georgia Milestones — an accuracy advantage no national SaaS vendor replicates.

## **5.2 High-Stakes vs. Low-Stakes Instruction Guardrails**

GaDOE's January 2025 AI guidance establishes a Traffic Light Protocol distinguishing high-stakes from low-stakes AI decisions. The the district seat Orchestrator enforces this distinction mechanically:

- Blocked (High-Stakes): Write an IEP goal, predict GMAS scores, recommend student discipline. The system returns: This task involves high-stakes decision making and requires human judgment per District Policy. Please consult the appropriate department.

- Allowed (Low-Stakes): Create a rubric, draft a quiz from a text passage, simplify a reading passage to Lexile 600, generate a differentiated lesson plan. Routed to Tier 2 or Tier 3 as appropriate.

## **5.3 Special Education ****&**** RTI/MTSS Workflow Support**

The Department of Student Services — led by Director Wendi Lee and Coordinator Teri Hendrix — manages complex compliance workflows for Special Education, Section 504, and RTI/MTSS. The architecture creates a specialized Student Services Skill with read-only access to procedural manuals and compliance calendars, firewalled from any write capability to GO-IEP.

The AI can summarize lengthy psychological evaluation reports, extract key compliance dates (flagging eligibility expirations within 14 or 60 days), and draft parent notification letters. It cannot write IEP goals, assign service minutes, or determine eligibility. The human IEP team remains the final authority on all legal documents.

## **5.4 Administrative Workflow Automation**

| **Workflow** | **Tier** | **AI Role** | **Human Gate** |
| --- | --- | --- | --- |
| FTE Count Pre-Validation (200+ GaDOE edit checks) | 1/2 | Cross-reference enrollment, flag anomalies, validate against GaDOE edit rules before submission | YES — Certification & submission |
| IEP Compliance Audit (GO-IEP / IDEA timelines) | 2 | Scan PLAAFP for required baseline data, check service minutes vs. bell schedule, flag 60-day evaluation deadlines | YES — ALWAYS (All IEP decisions) |
| Title I Budget Code Validation | 2 | Check funding source vs. allowable expense list, validate object codes, flag mismatches before requisition approval | YES — Final approval |
| CPI Staff Data (3x annually) | 1 | Aggregate HR data, format for GaDOE submission, flag missing required fields and certification mismatches | YES — HR Director sign-off |
| CCRPI Data Preparation | 1/3 | Compile assessment and attendance data (T1); generate anonymized trend narrative for Board (T3) | YES — Superintendent review |

# **Section 6: Financial Modeling — The Case for Local-First**

## **6.1 Five-Year Total Cost of Ownership**

The financial case for the the district seat Central Hub is compelling. Comparing costs over five years reveals the fundamental difference between renting intelligence from a SaaS vendor and owning it:

| **Cost Category** | **SaaS Model (Commercial License)** | **Local-First Model (Recommended)** | **Strategic Implications** |
| --- | --- | --- | --- |
| Licensing / Hardware | $540,000 (500 staff @ $18/mo) | $40,000 (Hardware Purchase) | SaaS fees are a permanent General Fund drain; hardware is a one-time SPLOST expense |
| Cloud API Tokens | Included | $60,000 ($1k/mo avg, 5yr) | Local model handles Tier 1/2 at no cloud cost; cloud only for high-value Tier 3 |
| RESA Support (Shared Engineer) | Included | $75,000 ($15k/yr RESA share) | Shared engineer via the regional education service agency (RESA) prevents need for $90k full-time hire |
| Power & Cooling | N/A | $25,000 ($5k/yr est.) | Enterprise servers run 24/7; cost absorbed by existing facilities budget |
| **TOTAL 5-YEAR TCO** | **$540,000+** | **~$200,000** | **~63% Savings** |
| Funding Source | General Fund (Operating) | SPLOST (Capital) + General Fund | Shifts cost from OPEX to CAPEX — aligns with rural school budgeting |

This comparison covers staff access only. Extending commercial SaaS to all 3,400 students would add approximately $730,000 per year — completely untenable on a Title I budget. With the the district seat Local-First model, student access adds only marginal electricity cost, making equitable district-wide deployment financially possible.

## **6.2 Funding Mechanics: SPLOST VII ****&**** E-Rate**

Hardware Procurement (SPLOST VII): The district should explicitly list Centralized Computational Infrastructure and Data Privacy Appliance Hardware in the upcoming SPLOST VII referendum project list. The following language is recommended:

| **Recommended SPLOST VII Language** "Acquisition, installation, and maintenance of instructional technology infrastructure, including but not limited to: centralized computational servers, data privacy appliance hardware, network security upgrades, and associated software licensing to support advanced learning initiatives, administrative efficiency, and student data protection." |
| --- |

Network Infrastructure (E-Rate): E-Rate Category 2 funds cover switches, fiber maintenance, and wireless access points — the roads for AI traffic — at 80-90% discount. AI compute hardware is not E-Rate eligible. Budget segmentation between E-Rate and SPLOST is critical; no commingling of funding sources under any circumstances. Districts that missed the FY2026 E-Rate window should prepare specifications now for FY2027 filing.

# **Section 7: Implementation Roadmap — FY2026–2027**

## **7.1 RESA Shared-Services Model**

Dr. Kim Morgan, Superintendent, should propose the creation of a Regional AI Infrastructure Consortium to the the regional education service agency (RESA) Board of Control. Instead of the district attempting to hire a $90,000/year AI Systems Engineer at a salary out of reach for the district's pay scale, the RESA hires this specialist and amortizes the cost across member districts. the county's estimated share: $15,000 per year.

The RESA engineer manages the backend of the the district seat infrastructure remotely — Docker updates, GPU driver patches, API configurations. Mr. Morgan's team focuses on the frontend — teacher support, device connectivity, and user account management via ClassLink. This division of labor makes the advanced architecture sustainable for a lean rural IT team.

## **7.2 Phased Deployment Timeline**

| **Phase** | **Timeline** | **Key Actions** | **Success Metric** |
| --- | --- | --- | --- |
| **Phase 1 Foundation ****&**** Procurement** | Feb–May 2026 | Board AI Acceptable Use Policy adopted; SPLOST/General Fund allocation secured; DOAS contract hardware ordered for the district seat Hub; E-Rate FY2027 planning begun; RESA MOU signed; 1 HS + 1 MS pilot sites selected; ClassLink OAuth2 integration configured | Policy adopted; hardware on order; RESA partner signed; the district seat node operational for Tier 1 |
| **Phase 2 Clerk Deployment** | Jun–Aug 2026 | Edge nodes installed in the district seat MDF; ClassLink/OneRoster integration tested and validated; Tier 1 deterministic skills activated; GSE CASE library fully loaded into Qdrant; AI Champions trained; staff-only Back-to-School rollout | Tier 1 uptime >99.5%; 50+ active staff users; zero privacy incidents |
| **Phase 3 Compliance Layer** | Sep–Dec 2026 | Tier 2 activated: IEP Compliance Checker, Budget Code Verifier, FTE Pre-Validator, Form Ingestion; CIPA Safety Classifier deployed; Red Team adversarial audit; Parental Transparency Dashboard launched; district-wide procurement plan finalized from pilot data | IEP audit accuracy >95%; Red Team finds zero jailbreak paths; parent dashboard live |
| **Phase 4 Cloud Escalation** | Jan–May 2027 | Tier 3 activated for instructional planning; Model Performance Ledger live; ZDR agreements executed with cloud vendors; controlled student access grades 9-12 (COPPA-compliant enrollment gate); RESA expansion planning; district-wide rollout authorized by Board | API cost within 20% of projection; teacher satisfaction >75%; zero FERPA/SB 89 incidents |

# **Section 8: Research Reconciliation — Discrepancies ****&**** Gaps**

## **8.1 Where the Two Research Sources Diverge**

### **Discrepancy: Tier 3 Traffic Volume Estimate**

Gemini's analysis claims 70% of AI traffic will be handled locally, delivering 60-80% cost savings. The Claude analysis implies that teacher workflows — lesson differentiation, feedback drafting, parent communication — are inherently creative, and Tier 3 cloud escalation may represent 60-70% of educational use cases in Year 1.

**Reconciliation: **Gemini's 70% local estimate is likely accurate for administrative workflows (FTE validation, compliance checks). It is optimistic for instructional use cases. Budget for 40-60% Tier 3 traffic in Year 1 for teacher-facing tools. The Model Performance Ledger will produce actual data within 90 days of deployment. Budget conservatively for API costs in Year 1.

### **Discrepancy: Hardware Specification Scale**

Gemini recommends NVIDIA L40S or partitioned A100 ($50K-$100K/node) for running multiple 70B models concurrently. The Claude analysis recommends Dell PowerEdge T560 with NVIDIA L4 ($8K-$12K) for a 7-13B routing model.

**Reconciliation: **Both are correct for different deployment scales. For the district's single-district the district seat deployment running a 7-13B orchestration model, the L4 is correct and far more cost-effective. For a RESA shared-services hub eventually serving 10+ districts with concurrent heavy workloads, the L40S becomes appropriate. Do not over-specify hardware for a Year 1 pilot.

### **Discrepancy: 1EdTech TrustEd Apps Real-Time Enforcement**

Gemini describes a real-time automated Green/Yellow/Red blocking system that queries TrustEd Apps before every connection. The TrustEd Apps API does not currently support real-time automated enforcement in this manner.

**Reconciliation: **The concept is architecturally sound but requires custom implementation. the district IT must build a scheduled nightly sync of TrustEd scores into a local allowlist database. Blocking is enforced by local network egress rules updated from that database, not by a live API call per connection.

## **8.2 Gaps Identified in Both Sources**

### **Gap: GO-IEP Write Access Not Explicitly Prohibited**

Neither original research source explicitly addressed a hard prohibition on AI write capability to GO-IEP at the network level. This is a legal requirement under IDEA and SBOE Rule 160-4-7, not merely best practice. The safest implementation: the the district seat node has no network path to GO-IEP write endpoints at the firewall level, with audit log documentation confirming the agent never attempted to write.

### **Gap: Offline Fallback Protocol Not Defined**

Both analyses acknowledge Georgia's digital divide but neither specifies a clear operational protocol for complete internet loss. The the district seat architecture requires explicit guidance: Tier 3 tasks queue (do not fail silently) when cloud APIs are unreachable; teachers receive a clear Offline Mode notification; Tier 1/2 local capability continues uninterrupted over the district WAN.

### **Gap: Student-Facing Age Verification (COPPA 2025)**

Both analyses recommend limiting student access to grades 9-12 initially, but neither specifies a technical enrollment-grade gate mechanism. Given COPPA 2025 penalties of $51,744 per affected child, the the district seat architecture needs an enrollment-based access control that automatically restricts AI access for students below 9th grade — regardless of teacher configuration.

### **Both Sources Independently Agree: Georgia GSE CASE Advantage**

Both research outputs independently identified Georgia's machine-readable GSE standards via GaDOE's OpenSALT platform as the single highest-leverage deployment action available. Loading the full GSE library into the the district seat vector database as the first operational step makes every teacher query more standards-accurate than any national SaaS product. This advantage is free, available today, and exclusive to Georgia districts.

# **Section 9: Risk Register ****&**** Mitigation Strategies**

| **Risk Scenario** | **Likelihood** | **Impact** | **Mitigation Strategy** |
| --- | --- | --- | --- |
| Prompt injection — student jailbreaks the district seat agent | HIGH | MEDIUM | All user input treated as untrusted data, not instructions. Supervisor LLM scans all inputs. CIPA classifier double-checks outputs. Firecracker MicroVM isolation contains any breakthrough. |
| IEP hallucination — agent invents compliance data | MEDIUM | SEVERE | GO-IEP write access prohibited at firewall level. All IEP text generated in sidecar only — human must copy-paste, forcing review. Tier 2 verifier checks against Implementation Manual before display. |
| Cloud vendor data breach (OpenAI, Anthropic, Google) | LOW | HIGH | Context Packager sends only anonymized minimum-necessary payloads — no names or GTIDs. ZDR agreements with all cloud vendors. LUKS encryption activates breach safe harbor under O.C.G.A. § 10-1-912. |
| IT staff turnover — loss of the district seat architecture expertise | HIGH | HIGH | RESA shared-services model distributes risk. Full system documentation required before Phase 3. All setup scripted via Ansible/Terraform (Infrastructure as Code). New engineer can rebuild from scratch without tribal knowledge. |
| the district seat hub outage — single point of infrastructure failure | MEDIUM | MEDIUM | Redundant PSU + UPS at node level. 4-server cluster configured for High Availability. Dell Next Business Day Support agreement mandatory. Client devices detect unavailability and switch to queued mode. |
| Supply chain attack via third-party AI skill/plugin | MEDIUM | SEVERE | All third-party skills execute in Firecracker MicroVM isolation. Default-deny network egress blocks unauthorized outbound calls. 1EdTech TrustEd score check + VirusTotal scan before any installation. |
| Parent/community backlash over AI use | MEDIUM | HIGH | Parental Transparency Dashboard provides plain-English Data Receipts within 3 business days per O.C.G.A. § 20-2-667. Opt-out flag blocks all Tier 3 escalation. Board-adopted AUP creates public accountability. |
| E-Rate audit denial for budget commingling | MEDIUM | LOW | Strict budget segmentation: network infrastructure (E-Rate Category 2) vs. AI compute hardware (SPLOST/General Fund). Separate POs, asset tags, and budget line items. No commingling under any circumstances. |

# **Conclusion**

The Local-First AI architecture anchored at the the district central node is not merely a technology choice — it is a strategic declaration. It asserts that the district need not be passive consumers of Silicon Valley subscription models or accept the privacy risks that come with them.

By leveraging the district's specific strengths — SPLOST VII capital funding mechanisms, the the regional education service agency (RESA) cooperative model, the machine-readable Georgia Standards of Excellence, and a tight-knit administrative structure — the district can build an AI infrastructure that is more private, more resilient, and significantly more cost-effective than commercial alternatives available even to wealthier suburban districts.

A single, enterprise-grade node in the district seat — managed by the RESA, integrated with ClassLink, grounded in Georgia standards, and protected by multiple layers of privacy-by-architecture — is the correct, sustainable, and equitable solution for the county's 3,400 students and 500 staff members.

| **Board Recommendation** Authorize Phase 1 procurement and RESA partnership negotiations. Adopt the AI Acceptable Use Policy. Include Centralized Computational Infrastructure in the SPLOST VII referendum project list. Begin E-Rate FY2027 network planning. This architecture equips the district students to succeed regardless of their zip code or internet connection. |
| --- |

*Document Classification: Internal — District Technology Leadership **&** Board of Education*

*Prepared by: Senior IT Engineering, the district | February 19, 2026 | Version 2.0 Consolidated*

*Sources: Claude (Anthropic) Research Analysis + Gemini (Google) K-12 Architecture Report + Local Sandboxed AI Agent Executive Briefing v2.0*
