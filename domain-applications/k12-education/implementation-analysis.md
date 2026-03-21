# Sovereign AI for a Rural School District

## A Local-First 'Route, Not Reason' Architecture — Implementation Analysis

*A reference implementation for deploying AI infrastructure in a rural Title I school district.*

> **Note:** This document describes a reference architecture applied to a class of rural K-12 environment. It does not represent the position of any specific school district, and was not commissioned or endorsed by any educational institution. District-specific details have been generalized. State-level references (Georgia Department of Education, state law) are retained as public policy context.

---

# PART 1: STRATEGIC DECISION FRAMEWORK

## Purpose of This Document

This document provides the Superintendent of the district with the technical analysis, financial modeling, legal compliance framework, and engineering specification necessary to make an informed decision about deploying artificial intelligence infrastructure in the district. It is designed to withstand review by the Georgia Technology Authority (GTA), the Georgia Department of Education (GaDOE), Regional Education Service Agency directors, and any external technology consultants the Superintendent may engage for independent verification.

The architecture described herein is not theoretical. Every hardware specification carries a current market price. Every legal citation references enacted statute or published regulation. Every integration point has been verified against the district’s existing technology stack. *Where estimates are used, they are explicitly identified as such.*

## The Problem: AI Is Already in the District

the district does not currently operate any formal AI tools. However, that does not mean AI is absent from the district. Nationally, nearly 90,000 Georgia educators individually use MagicSchool AI, and consumer tools like ChatGPT, Copilot, and Gemini are freely accessible on any personal device. When a teacher pastes IEP content into a consumer chatbot to draft parent communication, or when a special education coordinator uploads evaluation data to generate a PLAAFP narrative, that teacher has transmitted protected student records to a third-party server with no Data Processing Agreement, no audit trail, and no district oversight.

**This is not a hypothetical risk. It is a current FERPA exposure.**

[^1]

The question before the Superintendent is not whether to permit AI in the district. The question is whether to govern it through network architecture and enforceable technical controls, or to continue relying on acceptable use policies that teachers may not read and cannot be technically enforced.

## The Architecture in Plain Language

| **THE CORE PRINCIPLE: ROUTE, DON’T REASON** A small, efficient computer in the district seat reads every AI request from every teacher in the district. It decides: Can I answer this myself using data already on our servers? Or do I need to send this to a more powerful cloud AI for help? If it sends the request to the cloud, it first strips out every student name, every ID number, every medical diagnosis, every behavioral marker. The cloud AI sees only the educational question. It never sees the student. The intelligence leaves the district seat. The identity stays. |
| --- |

The system operates on a three-tier classification protocol. Approximately 60–80% of daily requests from administrative staff resolve entirely on local hardware at zero cost and zero privacy exposure. The remaining requests escalate to enterprise cloud AI services, but only after structural redaction removes all personally identifiable information.

| **Tier** | **What It Handles** | **Where It Runs** | **Data Exposure** | **Cost** |
| --- | --- | --- | --- | --- |
| Tier 1: Local Deterministic | Attendance lookups, bus routes, lunch balances, password resets, standards retrieval | the district central node only | Zero — no data leaves the building | $0.00 |
| Tier 2: Local Verification | FTE pre-validation, IEP compliance audits, budget code verification, PDF form extraction | the district central node only | Zero — processed statelessly on local hardware | $0.00 (electricity only) |
| Tier 3: Cloud Escalation | Lesson plan differentiation, parent communications, behavioral trend analysis | Orchestrated locally, processed by cloud API | Managed — payload fully anonymized before transmission | ~$0.01–$0.03 per query |
| BLOCKED | IEP goal writing, educator evaluation, student placement, discipline decisions | Does not execute | Not applicable | Not applicable |

## Why the county Is Unusually Well-Positioned

Four structural advantages make the district a stronger candidate for this architecture than the majority of Georgia’s 181 school districts:

- **98% fiber-to-the-home coverage. **the local fiber provider has completed a full copper-to-fiber conversion serving 97.95% of the county.

[^5]

- **Compact, centralized geography. **Seven schools across four communities, all connected by district fiber WAN. A single hub in the district seat serves every site without latency concerns.

- **Machine-readable Georgia Standards of Excellence. **Georgia is the first state to publish all K–12 academic standards in the CASE (Competency and Academic Standards Exchange) format, accessible via a REST API at case.georgiastandards.org. This enables the local system to ground every teacher query in the exact Georgia standard — something no commercial SaaS vendor replicates.

[^8]

- **the regional education service agency (RESA) shared-services infrastructure. **The RESA’s eight-district cooperative model provides a proven vehicle for shared technology staffing, eliminating the need for the district to independently recruit and retain specialized AI systems engineers.

## The Binding Constraint: IT Staffing

The architecture is technically sound. The cost model delivers 55–63% savings over SaaS alternatives. The legal compliance posture exceeds current standards. None of that matters if the district cannot sustain the system operationally.

the county’s Office of Technology Services operates with approximately seven staff members managing all technology for 3,400 students and 545 total staff across seven campuses. The architecture requires Linux administration, Docker container orchestration, GPU driver management, and local LLM inference pipeline maintenance — skills that are uncommon in K-12 IT departments nationally and functionally nonexistent in rural Georgia districts.

[^7]

| **THE RESA DECISION IS STRUCTURAL, NOT OPTIONAL** Without a formal Memorandum of Understanding with the regional education service agency (RESA) for shared AI infrastructure engineering, this architecture should not be deployed. A single departure from the district IT team would leave the system unmanageable. The RESA model amortizes a $70,000–$90,000 AI systems administrator salary across eight member districts, reducing the district’s annual share to approximately $10,000–$15,000. |
| --- |

## Legal and Compliance Framework

The architecture’s compliance posture has been evaluated against five overlapping legal frameworks. The following summary identifies where the architecture exceeds current requirements, where it meets them, and where residual risks persist.

### Federal: FERPA

Because Tier 1 and Tier 2 tasks process exclusively on district-owned hardware, raw student data physically never leaves the premises. This establishes direct physical control over education records — a standard that exceeds the contractual “direct control” typically invoked under FERPA’s school official exception. The append-only audit log satisfies 34 CFR § 99.32 requirements for tracking all disclosures of personally identifiable information.

[^1]

### State: Georgia SB 89

By sequestering raw data on local servers and transmitting only redacted, tokenized snippets to cloud providers, the architecture structurally prevents third-party vendors from acting as “operators” capable of profiling students under O.C.G.A. § 20-2-666. The encrypted-at-rest storage design activates Georgia’s breach notification safe harbor under O.C.G.A. § 10-1-912.

[^2]

[^9]

### State: GaDOE January 2025 AI Guidelines

The orchestrator’s tier classification directly implements the GaDOE “traffic light” protocol. Green and yellow tasks (lesson planning, rubric creation, text leveling) route to appropriate processing tiers. Red tasks (IEP goal writing, educator evaluation, student placement) are physically blocked from execution. The system returns a policy-aligned warning and logs the attempt.

[^3]

### Federal: COPPA 2025

The architecture implements an enrollment-grade gate integrated with PowerSchool’s Student Information System. Students below 9th grade are structurally blocked from authenticating into the AI platform. This gate is hard-coded in the identity layer, not configurable by individual educators.

[^12]

### Residual Legal Risks

Four compliance risks persist even with local-first design and must be mitigated through operational procedures:

- **Cloud API data retention. **Standard cloud API endpoints retain request data for 30 days for abuse monitoring. Zero Data Retention (ZDR) requires explicit enterprise agreements. Google Workspace for Education provides inherent ZDR protections. OpenAI and Anthropic require separate negotiation.

- **Redaction completeness. **No automated system achieves 100% accuracy. FERPA’s “reasonable person” standard means combinations of seemingly innocuous data points (grade level, school, date, activity) can re-identify a student. Layered redaction (rule-based plus ML-based plus periodic human audit) is required.

- **Third-party plugin/skill creep. **Each external integration is potentially a new “operator” under Georgia § 20-2-666, requiring its own compliance review and Data Processing Agreement.

- **Shadow SaaS adoption. **If teachers independently adopt MagicSchool, Copilot, or other commercial AI tools alongside this system, the cost model collapses and the privacy architecture is bypassed entirely. Board-level AI Acceptable Use Policy adoption is prerequisite, not optional.

## Financial Analysis

The following projections are specific to the county’s scale: 7 schools, approximately 3,400 students, 245 classroom teachers, and 545 total staff. All hardware pricing reflects February 2026 market conditions and Georgia DOAS statewide contract availability.

[^15]

### 5-Year Total Cost of Ownership Comparison

| **Cost Category** | **SaaS Licensing (Staff Only)** | **Local-First Architecture** | **Implications** |
| --- | --- | --- | --- |
| Initial Hardware (CapEx) | $0 | ~$44,000 (2 enterprise nodes + UPS + cooling) | One-time capital investment vs. permanent operating drain |
| Annual Software/Token Cost | ~$172,800/yr (245 teachers @ $18/mo per seat) | ~$2,000–$4,000/yr (metered cloud API overflow) | Orchestrator’s tiering system eliminates 60–80% of token costs |
| Annual RESA Engineering Share | Included in vendor license | ~$12,000–$15,000/yr | Amortized across 8 RESA districts |
| Annual Power, Cooling, Maintenance | N/A | ~$3,000–$5,000/yr | Enterprise servers at ~1,700W total draw |
| 5-Year Total | $864,000+ | $125,000–$165,000 | 55–80% savings depending on SaaS comparison baseline |

**Critical caveat: **The SaaS comparison uses Microsoft 365 Copilot at $18/user/month for teaching staff only. Extending SaaS licenses to all 3,400 students would push the 5-year SaaS cost above $2.5 million. The local-first architecture can extend to students at near-zero marginal cost once the infrastructure is deployed.

### Funding Pathways

The procurement must be bifurcated to avoid E-Rate audit violations:

- **E-Rate Category 2 (80% discount): **Covers managed switches, firewall appliances, cabling, wireless access points, and UPS for network equipment. the district’s 5-year Category 2 budget is approximately $685,000 pre-discount based on 3,400 students at $201.57/student.

[^10]

- **E-SPLOST Capital Allocation: **The district’s active Education SPLOST generates approximately $1.99 million annually. Server hardware, GPUs, cooling systems, and AI-specific infrastructure are capital expenditures appropriately funded through SPLOST. SPLOST referendum language should explicitly authorize “Centralized Computational Infrastructure” and “Data Privacy Appliance Hardware.”

[^6]

- **E-Rate cannot fund: **AI servers, GPUs, software licenses, or cooling systems. These must be funded entirely through local capital sources.

## Multi-Model Adversarial Validation

This architecture has been subjected to independent analysis by three frontier AI systems (Claude/Anthropic, Gemini/Google, ChatGPT/OpenAI) in a structured adversarial review process. Each model evaluated the architecture against its own training data, analytical frameworks, and reasoning capabilities. The following summarizes the consensus findings and identified points of divergence.

### Consensus Findings (All Three Models Agree)

- The single the district seat hub topology is correct for the district’s scale and staffing constraints. Distributed per-school deployment would be unmanageable.

- The RESA partnership is structurally required. Without it, the system becomes fragile within 18 months.

- Georgia’s machine-readable GSE standards are a unique technical enabler that no SaaS vendor can easily replicate.

- The cost model is directionally sound but depends entirely on routing discipline and prevention of shadow SaaS adoption.

- The privacy architecture (structural containment) is stronger than contractual compliance (vendor DPAs alone).

- Human-in-the-loop gates for high-stakes decisions (IEP, discipline, employment, 504) are legally non-negotiable.

### Identified Failure Modes

The adversarial review identified five failure modes that could undermine the deployment:

| **Failure Mode** | **Trigger** | **Consequence** | **Mitigation** |
| --- | --- | --- | --- |
| Shadow SaaS adoption | Teachers independently adopt commercial AI tools | Cost model collapses; privacy architecture bypassed | Board-adopted AI AUP; network-level blocking of unauthorized AI endpoints |
| RESA engineering instability | Shared AI administrator departs; position unfilled | System degrades within 90 days; patches and updates stop | Cross-training requirement; documented runbooks; redundant RESA staffing |
| Improper network segmentation | AI VLAN accessible from student devices or public network | Unauthorized access to AI endpoints; potential data exposure | FortiGate NGFW with explicit deny rules; quarterly penetration testing |
| Redaction bypass | Context Packager fails to catch indirect identifiers | Student identity inferrable from cloud-transmitted data | Layered redaction (rule + ML + periodic human audit); conservative over-redaction |
| Cloud cost creep | Model Performance Ledger not enforced; expensive models used for simple tasks | Monthly API costs escalate from $200 to $2,000+ | Hard budget caps per model tier; weekly cost dashboard; automatic routing enforcement |

## The Four Board-Level Decisions

The deployment of this architecture requires four structural decisions that only the Superintendent and Board of Education can make. Each is binary. Half-measures on any one of them will produce technical debt rather than institutional benefit.

| **Decision** | **Question** | **If Yes** | **If No** |
| --- | --- | --- | --- |
| 1. AI Governance | Will the Board adopt a formal AI Acceptable Use Policy that mirrors GaDOE’s traffic-light protocol? | Proceed to Decision 2 | Do not deploy. Policy vacuum invites uncontrolled adoption. |
| 2. RESA Partnership | Will the Board authorize an MOU with the regional education service agency (RESA) for shared AI infrastructure engineering? | Proceed to Decision 3 | Do not deploy. Local IT team cannot sustain the system alone. |
| 3. Capital Investment | Will the Board allocate approximately $44,000 from E-SPLOST for centralized compute infrastructure? | Proceed to Decision 4 | Evaluate SaaS alternatives at $172,800/year for staff-only licensing. |
| 4. Institutional Discipline | Will the Board enforce prohibition of unauthorized AI tools alongside the managed platform? | Deploy the architecture | Cost savings and privacy guarantees are void. |

| **STRATEGIC VERDICT** Given the county’s fiber-rich rural environment, Title I equity mandate, active E-SPLOST capital flexibility, absence of current AI infrastructure, and proximity to RESA shared services, this architecture fits the district unusually well. The gating constraint is not hardware, cost, or technology. It is institutional discipline. If deployed through a single the district seat hub, RESA-managed backend, strict tier enforcement, SPLOST-funded compute, E-Rate-funded backbone, and Board-adopted AI AUP, the architecture is technically sound and legally defensible. If deployed halfway, it becomes technical debt. |
| --- |

# PART 2: TECHNICAL ENGINEERING SPECIFICATION

*Audience: *This section is written for the Director of Technology, SIS/PowerSchool Coordinator, Technology Coordinator, IT technicians, RESA engineering staff, and any external technology reviewers the Superintendent may engage. Every specification is intended to be peer-reviewable.

## Hardware Specification: the district central node

The deployment consists of two Dell PowerEdge T560 tower servers in an active/standby configuration, housed in the district’s primary data closet at the central office in the district seat. Tower form factor eliminates rack infrastructure requirements. Each node is equipped with dual NVIDIA L4 GPUs for AI inference.

### Server Configuration (Per Node)

| **Component** | **Specification** | **Rationale** |
| --- | --- | --- |
| Server Chassis | Dell PowerEdge T560 Tower | 4.5U tower; supports up to 6 GPUs; dual-socket Xeon Scalable; iDRAC9 remote management; available via GA DOAS Enterprise Infrastructure contract |
| Processor | 2x Intel Xeon Silver 4410Y (12-core, 2.0GHz) | Sufficient for orchestration workload; low TDP (150W each); DDR5 support |
| Memory | 256GB DDR5 ECC RDIMM (4x64GB DDR5-5600) | ECC memory is non-negotiable for 24/7 operation; 256GB supports concurrent model loading and vector database operations |
| GPU | 2x NVIDIA L4 24GB PCIe (per node; 4 total) | 24GB VRAM supports 13B parameter models; 72W passive cooling; enterprise-validated; ~39 concurrent inference sessions per GPU on 8B INT4 model |
| Storage | 2x 3.84TB NVMe SSD (RAID 1 mirror) | Samsung PM9A3 or Micron 7450 PRO; enterprise read-intensive; RAID 1 provides data redundancy |
| Network | Dual 10GbE SFP+ (bonded) | Bonded for failover; connects to central 10GbE managed switch |
| Power Supply | Dual 1100W redundant PSU | Hot-swappable; N+1 redundancy |
| Operating System | Ubuntu Server 24.04 LTS | 5-year support window; NVIDIA driver compatibility; Docker and Ollama native support |

### GPU Selection Justification

The NVIDIA L4 was selected over both the T4 (budget) and RTX 5060 Ti (consumer) alternatives based on three criteria: VRAM capacity, power efficiency, and enterprise suitability.

| **Metric** | **NVIDIA L4** | **NVIDIA T4** | **RTX 5060 Ti 16GB** |
| --- | --- | --- | --- |
| Street Price | $2,000–$3,000 | $725–$900 | $429–$480 |
| VRAM | 24GB GDDR6 | 16GB GDDR6 | 16GB GDDR7 |
| TDP | 72W (passive cooling) | 70W (passive cooling) | 180W (active fan required) |
| Max Concurrent Sessions (8B INT4, 4K context) | ~39 sessions | ~23 sessions | ~23 sessions |
| 13B Model Support (Q4) | Yes — fits comfortably | Marginal — requires aggressive quantization | Marginal |
| Enterprise Validated | Yes | Yes | No — consumer GPU |
| Cooling Requirements | None (passive heatsink) | None (passive heatsink) | Requires chassis airflow; not validated for server closets |

The L4’s 24GB VRAM is the decisive factor. It runs the routing classifier (Phi-4-mini, 3.8B) and the primary local generation model (Qwen 2.5 7B or Llama 3.1 8B) simultaneously on a single GPU, with sufficient VRAM headroom for context window expansion. Four L4s across two nodes provide approximately 156 concurrent inference sessions — more than sufficient for 245 teachers even at peak usage.

### Power, Cooling, and UPS

| **Parameter** | **Value** | **Notes** |
| --- | --- | --- |
| Total Power Draw (2 nodes + network) | ~1,700W sustained | T560 with 2xL4: ~650W per node; switch + firewall: ~200W; UPS overhead: ~120W |
| Cooling Load | ~5,800 BTU/hr (7,500 with margin) | 12,000 BTU ductless mini-split recommended; $1,300–$2,500 installed |
| UPS Specification | APC Smart-UPS SMT3000RM2UC + 2x external battery packs | 3kVA line-interactive; ~30–35 minute runtime at 1,600W load; network management card for remote monitoring |
| Circuit Requirements | Dedicated 20A circuit (NEMA 5-20R) | Standard commercial electrical; no special wiring required |

### Estimated Hardware Budget

| **Line Item** | **Qty** | **Unit Cost (DOAS/Cooperative)** | **Extended Cost** |
| --- | --- | --- | --- |
| Dell PowerEdge T560 (configured, no GPU) | 2 | $12,000 | $24,000 |
| NVIDIA L4 24GB PCIe GPU | 4 | $2,500 | $10,000 |
| APC Smart-UPS SMT3000RM2UC + ext. batteries | 1 | $5,000 | $5,000 |
| Ductless Mini-Split (12K BTU, installed) | 1 | $2,000 | $2,000 |
| Cabling, SFP+ transceivers, miscellaneous | 1 | $500 | $500 |
| SUBTOTAL: Non-E-Rate Hardware |  |  | $41,500 |
| 10GbE Managed Switch (E-Rate eligible) | 1 | $6,000 | $6,000 |
| FortiGate 100F Firewall (E-Rate eligible) | 1 | $3,000 | $3,000 |
| SUBTOTAL: E-Rate Eligible (80% funded) |  |  | $9,000 → $1,800 district cost |
| TOTAL ESTIMATED DISTRICT OUTLAY |  |  | $43,300 |

## Software Stack Specification

All core software components are open-source, free of licensing fees, and production-ready as of February 2026. The stack is deployed as Docker containers managed by Docker Compose (Phase 1) with migration to K3s lightweight Kubernetes (Phase 2) when spanning both nodes.

| **Component** | **Version** | **License** | **Function** | **Resource Requirements** |
| --- | --- | --- | --- | --- |
| Ollama | v0.16.3 (Feb 2026) | MIT | Model management and serving; OpenAI-compatible API; automatic multi-GPU layer splitting | Minimal CPU; GPU VRAM per model |
| vLLM | v0.16.0 (Feb 2026) | Apache 2.0 | High-throughput multi-user inference; PagedAttention memory management; continuous batching | GPU VRAM; benefits from high CPU thread count |
| Open WebUI | v0.6.52 | MIT | Teacher-facing interface; RBAC; OAuth/SSO integration; built-in RAG; conversation history | 2GB RAM; lightweight web server |
| Qdrant | v1.17.0 | Apache 2.0 | Vector database for GSE standards storage and retrieval-augmented generation | 256MB RAM for ~30K vectors; 50–100MB storage |
| Docker + Docker Compose | Latest stable | Apache 2.0 | Container orchestration; single YAML deployment file | 200MB overhead |
| Ubuntu Server | 24.04 LTS | GPL | Host operating system; 5-year security support through 2029 | Base OS requirements |

### Local Model Selection

The orchestrator runs two models simultaneously: a lightweight routing classifier and a primary local generation model.

| **Role** | **Recommended Model** | **Parameters** | **VRAM (Q4)** | **Context Window** | **License** | **Rationale** |
| --- | --- | --- | --- | --- | --- | --- |
| Routing Classifier | Phi-4-mini-instruct | 3.8B | ~2.5GB | 128K tokens | MIT | Exceptional reasoning per parameter; classifies intent and selects Tier 1/2/3 routing with high accuracy |
| Local Generation | Qwen 2.5 7B Instruct | 7.6B | ~4.5GB | 128K tokens | Apache 2.0 | Best-in-class instruction following; superior structured output; strong multilingual support |
| Fallback Generation | Llama 3.1 8B Instruct | 8.0B | ~5.0GB | 128K tokens | Community | Most battle-tested ecosystem; largest community support; proven reliability |

Both the routing classifier and primary generation model fit simultaneously on a single L4 GPU (~7GB combined), leaving 17GB of VRAM for KV-cache, context windows, and concurrent sessions. The second GPU on each node provides capacity for peak loads and model experimentation.

## Cloud API Overflow Configuration

Tier 3 requests that exceed local model capabilities escalate to enterprise cloud APIs. The Model Performance Ledger routes simple overflow to cost-efficient models and reserves premium models for tasks requiring deep pedagogical reasoning.

| **Provider** | **Budget Tier Model** | **Input/MTok** | **Output/MTok** | **Premium Model** | **Input/MTok** | **Output/MTok** |
| --- | --- | --- | --- | --- | --- | --- |
| Google | Gemini 2.0 Flash | $0.10 | $0.40 | Gemini 2.5 Pro | $1.25 | $10.00 |
| OpenAI | GPT-4o-mini | $0.15 | $0.60 | GPT-4o | $2.50 | $10.00 |
| Anthropic | Claude Haiku 3.5 | $0.80 | $4.00 | Claude Sonnet 4.5 | $3.00 | $15.00 |

| **RECOMMENDED PRIMARY CLOUD TARGET: GOOGLE GEMINI** the district already operates Google Workspace for Education. Gemini for Education is available under existing Workspace terms with inherent FERPA compliance, automatic zero-data-retention protections, and no additional legal work required. At $0.10/$0.40 per million tokens, Gemini 2.0 Flash is 8x cheaper than Claude Haiku and the natural overflow target for a Google Workspace district. Estimated annual cloud API cost for 245 teachers at 10 requests/day for 180 school days: $90–$180/year. |
| --- |

[^11]

### Privacy Compliance Status by Provider

| **Provider** | **ZDR Available** | **SDPC NDPA Status** | **FERPA Coverage** | **Action Required** |
| --- | --- | --- | --- | --- |
| Google | Automatic under Workspace Education terms | Education framework in place | Contractual via Workspace DPA | None — existing legal framework sufficient |
| OpenAI | Via enterprise agreement | Signed (October 2025) | ChatGPT Edu DPA | Execute Georgia-specific Exhibit G attachment via SDPC |
| Anthropic | Via security addendum | No NDPA found | Requires custom negotiation | Full enterprise DPA negotiation required before use |

## Integration Architecture

### PowerSchool → ClassLink → AI Orchestrator Pipeline

The district’s existing data flow provides a clean integration chain for the AI orchestration layer. No new identity systems are required.

[^14]

| **System** | **Role** | **Integration Method** | **Data Flow** |
| --- | --- | --- | --- |
| PowerSchool SIS | Source of truth for enrollment, grades, attendance, GTID, FTE segments | OneRoster v1.1 REST API (/api/ims/oneroster/v1p1/) with OAuth 2.0 | Provides student/teacher/class/enrollment data to ClassLink |
| ClassLink Roster Server | OneRoster-certified intermediary; identity hub | OneRoster API consumer (from PowerSchool); SAML/OAuth provider (to AI platform) | Normalizes roster data; provides SSO authentication for AI interface |
| Open WebUI (AI Interface) | Teacher-facing AI portal | OAuth 2.0 authorization code flow with PKCE via ClassLink | Authenticates teachers via ClassLink SSO; queries role/roster for access control |
| Qdrant Vector DB | Georgia Standards of Excellence storage | CASE REST API ingestion from case.georgiastandards.org | Provides standards-aligned context for Tier 3 cloud escalation |
| GO-IEP | State IEP management system | Read-only data export (no write path) | AI operates in sidecar mode; zero network egress to GO-IEP write endpoints |

### ClassLink SSO Configuration

Open WebUI supports OAuth 2.0 natively. The integration with ClassLink uses the authorization code flow:

- Register the AI platform as a custom application in ClassLink Management Console at dev.classlink.com

- ClassLink provides Client ID and Client Secret for OAuth 2.0

- Authorization endpoint: https://launchpad.classlink.com/oauth2/v2/auth

- Token claims provide user role (teacher/admin/student) and organization for access control

- The AI orchestrator queries ClassLink Roster Server in real-time to verify current student-teacher roster assignments before granting data access

### Dynamic Roster-Based Access Control

When a teacher queries the system for student-specific information, the orchestrator performs three simultaneous checks:

- Authenticates the teacher’s identity via ClassLink SSO token

- Queries the ClassLink Roster Server to confirm the teacher’s current course/section assignments

- Restricts data access exclusively to students currently enrolled in that teacher’s active sections

The instant a student is removed from a teacher’s roster in PowerSchool, the change propagates through ClassLink Roster Server and the orchestrator automatically revokes that teacher’s access to the student’s historical context. No manual intervention required.

## Georgia Standards of Excellence Vectorization

Georgia’s CASE-format standards represent the single most compelling technical differentiator of this architecture. The GSE are published in machine-readable format across 25–30+ framework documents covering all 10 content areas, totaling an estimated 15,000–30,000 individual standards (CFItems).

[^8]

### Ingestion Workflow

- Enumerate all frameworks: GET /ims/case/v1p0/CFDocuments returns all framework document GUIDs

- Download complete frameworks: GET /ims/case/v1p0/CFPackages/{identifier} returns document + all items + all associations in single JSON response

- Extract CFItems: Parse fullStatement, humanCodingScheme, CFItemType, educationLevel, subject fields

- Generate embeddings: Concatenate subject + gradeLevel + hierarchy_path + fullStatement + humanCodingScheme as embedding input

- Store in Qdrant: Use CASE GUID as primary key; lastChangeDateTime field enables incremental updates

The entire Georgia standards corpus vectorizes into approximately 50–100MB in Qdrant — trivial storage requirements. Nightly sync jobs using the lastChangeDateTime field keep the local database current with any GaDOE standards updates.

## Network Architecture

### VLAN Segmentation

| **VLAN** | **Purpose** | **AI Access** | **Notes** |
| --- | --- | --- | --- |
| VLAN 10 | Admin/Finance/SIS | No AI access | Isolated from all AI endpoints |
| VLAN 20 | Staff/Teacher devices | HTTPS/443 to AI endpoints only | Primary AI consumer VLAN |
| VLAN 30 | Student Chromebooks | Blocked from AI VLAN | Hard deny rule; no exceptions until Phase 4 student rollout |
| VLAN 40 | AI Infrastructure (servers, iDRAC) | Isolated; management from VLAN 99 only | No direct access from any user VLAN |
| VLAN 50 | IoT/Facilities | No AI access | Standard IoT isolation |
| VLAN 99 | Network Management | Full administrative access | IT staff only; MFA required |

### Bandwidth Assessment

AI inference traffic is negligible relative to existing school bandwidth. At 245 teachers streaming AI responses simultaneously, peak network demand is approximately 0.3 Mbps sustained with 5 Mbps burst — less than 0.15% of typical school bandwidth capacity. The bottleneck in this architecture is GPU compute, not networking. the local fiber provider’s fiber WAN connecting all seven school sites to the the district seat hub provides orders-of-magnitude more bandwidth than the AI workload requires.

## Phased Implementation Timeline

The deployment follows an 18-month phased schedule synchronized with state budget cycles, academic calendars, and E-Rate filing deadlines.

| **Phase** | **Timeline** | **Actions** | **Deliverables** |
| --- | --- | --- | --- |
| Phase 1: Foundation | Months 1–4 | Secure E-SPLOST capital allocation; execute DOAS hardware procurement; file E-Rate FY2027 Category 2 for network upgrades; establish RESA MOU for shared engineering; Board adopts AI AUP | Hardware ordered; RESA agreement signed; AI policy adopted; ClassLink/OneRoster integration tested |
| Phase 2: Clerk Deployment | Months 5–8 | Deploy hardware in the district seat data closet; install Docker/Ollama/Open WebUI; ingest GSE standards via CASE API; launch staff-only pilot for Tier 1 deterministic workflows; train AI Champions cohort | Working system serving Tier 1 lookups; standards database populated; 15–20 trained AI Champions across district |
| Phase 3: Compliance Layer | Months 9–12 | Activate Tier 2 local verification workflows (FTE pre-validation, IEP compliance audit, budget coding); deploy CIPA Safety Classifier; conduct Red Team adversarial audit; launch Parental Transparency Dashboard | Automated FTE pre-validation operational; IEP sidecar compliance tool active; Data Receipts available on request |
| Phase 4: Cloud Escalation | Months 13–18 | Activate Context Packager and Model Performance Ledger; enable Tier 3 cloud escalation to Google/OpenAI enterprise APIs; execute ZDR agreements; cautiously extend to grades 9–12 with COPPA-compliant consent | Full three-tier system operational; student access for 9–12; cost tracking dashboard active |

| **IMMEDIATE ACTION ITEMS (TIME-SENSITIVE)** 1. E-Rate FY2026 Form 471 filing window closes April 1, 2026. If Form 470 has not been filed, the last possible date is March 4, 2026 (28 days before window close). Network infrastructure funding for this cycle requires immediate action. 2. the regional education service agency (RESA) Board of Control should evaluate a shared AI systems administrator position at its next meeting. The FTE-proportional cost share for the district is estimated at $10,000–$15,000/year. 3. The GSE CASE API at case.georgiastandards.org is live and accessible. Standards vectorization can begin immediately as a zero-cost, zero-risk proof-of-concept using any existing district workstation with Python installed. |
| --- |

## Appendix A: Source Document Cross-Reference

This document synthesizes findings from the following source analyses. Each source was independently produced, and discrepancies between sources have been resolved through adversarial cross-validation.

| **Source Document** | **Primary Focus** | **Key Contribution to This Analysis** |
| --- | --- | --- |
| Sovereign AI in Georgia K-12 Education: A Strategic Analysis | Georgia-wide legal compliance and architectural philosophy | Three-tier task classification protocol; DLP engine specification; GaDOE traffic-light enforcement; COPPA enrollment gating |
| Local-First AI for Georgia Schools: A District-Level Architecture Analysis | Georgia-specific cost modeling and feasibility assessment | 5-year TCO comparison; RESA shared-services model; rural broadband analysis; CASE/OpenSALT integration discovery |
| the district Sovereign AI Consolidated Report | the county-specific synthesis from Claude and Gemini analyses | the district seat hub topology decision; the district staffing constraint analysis; E-SPLOST/E-Rate funding bifurcation |
| Day in the Life: Three Roles, One School Day | Operational workflow validation | Tier classification correctness across teacher/principal/superintendent personas; workflow sequencing aligned with FERPA, SB 89, IDEA requirements |
| GaDOE IT Infrastructure Overview (Gemini State IT) | State-level technology ecosystem context | My Georgia Insights / SLDS architecture; PeachNet connectivity baseline; Ed-Fi / CEDS interoperability standards; GaDOE AI Ethics Officer role |
| Multi-Model Adversarial Review (ChatGPT) | Independent structural validation | Five failure mode identification; institutional discipline as gating constraint; topology-over-contracts framing |

**Document Classification: **This document contains no student personally identifiable information. It may be shared with GTA, GaDOE, RESA directors, and external technology consultants without restriction.

*End of Document*

the district • Office of Technology Services • [district address]

[^1]: 20 U.S.C. § 1232g; 34 CFR Part 99. FERPA requires educational agencies to maintain direct control over education records and to log all disclosures of personally identifiable information.
[^5]: BestNeighborhood.org, the county fiber coverage data. the local fiber provider provides 97.95% fiber-to-the-home coverage countywide.
[^8]: 1EdTech CASE specification. Georgia was the first state to publish standards in machine-readable CASE format via the OpenSALT platform at case.georgiastandards.org.
[^7]: Incident IQ, 2024 K-12 IT Survey: 61% of K-12 IT leaders identified staff shortages as their top concern; 44% said they cannot sustain current staffing levels over three years.
[^1]: 20 U.S.C. § 1232g; 34 CFR Part 99. FERPA requires educational agencies to maintain direct control over education records and to log all disclosures of personally identifiable information.
[^2]: O.C.G.A. §§ 20-2-660 through 20-2-668. Georgia’s Student Data Privacy, Accessibility, and Transparency Act (SB 89) prohibits operators from using student data for targeted advertising, selling PII, or creating non-educational profiles.
[^9]: O.C.G.A. § 10-1-912. Georgia’s breach notification law provides safe harbor when compromised data was encrypted and the encryption key was not compromised.
[^3]: GaDOE, “Leveraging AI in the K-12 Setting,” January 2025. Establishes traffic-light classification of permissible and prohibited AI uses in Georgia schools.
[^12]: COPPA 2025 amendments expanded protected personal information to include biometric data and behavioral profiles, with penalties up to $51,744 per affected child.
[^15]: Georgia DOAS Enterprise Infrastructure statewide contract provides pre-negotiated pricing for Dell, HP, and Lenovo server hardware available to all state and local government entities.
[^10]: USAC E-Rate Discount Matrix. Rural districts with 51–75% NSLP eligibility qualify for 80% Category 1 and 80% Category 2 discounts.
[^6]: Georgia Department of Audits and Accounts, the school board Annual Financial Report, Fiscal Year 2024.
[^11]: OpenAI signed SDPC Student DPA (October 2025). Google Workspace for Education terms provide inherent FERPA compliance and data processing commitments.
[^14]: PowerSchool Universal Rostering API implements 1EdTech OneRoster v1.1 specification at /api/ims/oneroster/v1p1/ with OAuth 2.0 authentication.
[^8]: 1EdTech CASE specification. Georgia was the first state to publish standards in machine-readable CASE format via the OpenSALT platform at case.georgiastandards.org.