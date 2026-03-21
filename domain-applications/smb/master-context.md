# AI Orchestration Framework for Small Business — Master Context

*Load this document at the start of every document-specific chat session.*

---

**Master Context Document**

AI Orchestration Framework for Small Business

*Load this document at the start of every document-specific chat session.*

| **Instructions for Use** Upload this file as the first message in each new chat. Follow it immediately with the document-specific prompt provided in Section 6. Do not mix document work across chats. Each chat produces one deliverable. |
| --- |

# **1.  What This Document Is**

This is the shared foundation for a five-document series on practical AI orchestration for small and mid-sized businesses. It establishes canonical definitions, audience parameters, architectural principles, and scope boundaries that every document in the series must honor.

Any AI model working from this context should treat these definitions as authoritative. Do not reinterpret, soften, or reframe them unless explicitly instructed to do so in a document-specific prompt.

# **2.  Primary Audience Profile**

All documents in this series are written for the following reader. Every authoring decision — language, examples, depth, tone — should be optimized for this person.

- **Role: **Small or mid-sized business owner, practice manager, or senior non-technical decision maker.

- **Technical background: **Little to none. Does not have a dedicated IT staff. May have one generalist IT person or rely on a managed service provider.

- **AI experience: **Has used ChatGPT, Copilot, or similar tools. Has encountered at least some of the six end-user friction points described in Section 4. Is skeptical but curious.

- **Primary concern: **Liability, data privacy, cost control, and not becoming dependent on a vendor they cannot leave.

- **Industry context: **May operate in a regulated industry (healthcare, legal, financial, construction, insurance). Compliance language resonates. Jargon does not.

- **Decision style: **Needs to understand the concept before evaluating the cost. Responds to concrete examples and plain analogies over technical diagrams.

| **Tone Directive** Write as a trusted advisor who respects the reader's intelligence and their time. No hype. No fear-mongering. No technical condescension. The reader has real problems and needs practical answers, not a sales pitch. |
| --- |

# **3.  Canonical Definitions**

These terms have specific meanings in this framework. Use them consistently across all documents. Do not substitute synonyms unless quoting an external standard.

| **Term** | Canonical Definition |
| --- | --- |
| **AI Orchestrator** | A lightweight local AI model (7–13 billion parameters) running on hardware the business controls. Its sole function is to receive requests, classify them, route them to the appropriate tool or model, and log the result. It does not perform deep reasoning. |
| **Route, Don’t Reason** | The governing design principle. The Orchestrator’s job is traffic direction, not problem solving. Heavy reasoning is delegated to specialized models only when needed and only with curated context. |
| **Context Packager** | The component that prepares every outbound request. It strips or masks sensitive data, attaches only the relevant business context, and enforces data minimization rules before anything leaves the local environment. |
| **Local Environment** | Hardware and software under the direct control of the business. This includes on-premises servers, dedicated workstations, and private cloud instances owned or leased by the business. It does not include shared SaaS platforms. |
| **Cloud Model** | A third-party AI service accessed via API (e.g., OpenAI, Anthropic, Google, Microsoft). All cloud model calls are mediated by the Orchestrator and filtered by the Context Packager. |
| **Specialized Model** | A cloud or local AI model scoped to a specific domain or task type (compliance, drafting, data analysis). Preferred over general-purpose models for domain-specific work. |
| **Trust Tier (WAL)** | Workflow Autonomy Level. A four-level scale (0–3) governing how much autonomous action an AI workflow is permitted to take. All workflows begin at Level 0. Promotion requires demonstrated accuracy over time. |
| **WAL-0 — Recommend** | AI produces a recommendation. A qualified human reviews and approves before any action is taken. This is the mandatory starting point for all new workflows. |
| **WAL-1 — Assist** | AI completes routine, low-risk tasks with light human review. Suitable for lookups, formatting, scheduling, and standard document pre-population. |
| **WAL-2 — Execute** | AI acts autonomously on well-defined, fully audited workflows. A human reviews the log periodically rather than each individual action. |
| **WAL-3 — Automate** | AI manages an end-to-end workflow including exception handling. Reserved for mature, thoroughly validated processes only. |
| **Overreliance** | A condition where staff bypass or rubber-stamp AI recommendations without meaningful review. An operational risk that must be actively monitored, not assumed away. |
| **Vendor Lock-in** | The condition of being unable to switch AI providers without rebuilding business workflows. This architecture is specifically designed to prevent it through model-agnostic routing. |
| **Data Minimization** | The practice of sending only the data required to complete a task to any external service. A default behavior enforced by the Context Packager, not left to staff discretion. |

# **4.  The Six End-User Friction Points**

These are the documented failure modes that cause AI adoption to stall in practice. Every document in this series should be written with awareness of these problems. Documents that directly address them should use this language as a reference, not a substitute for plain explanation.

| **#** | Problem Statement |
| --- | --- |
| **1** | Prompt engineering burden. Staff must become AI experts to get usable results. When outputs fail due to poor prompting, trust in the tool collapses. |
| **2** | Context contamination. The model injects prior conversation history or perceived user preferences into responses where they are not relevant, distorting the output. |
| **3** | Generalist drift. Models optimized for broad appeal produce average answers for specialized, high-stakes queries. Factual accuracy degrades in focused professional domains. |
| **4** | Reason drift. Regulatory and policy questions are answered based on prevalent opinion rather than the organization’s authoritative source. AI enforces what is popular, not what is correct. |
| **5** | No persistent memory. Months of project context cannot be efficiently reloaded into a new session. Token budgets are consumed by background material instead of the task at hand. |
| **6** | Manual process burden. Staff must copy, paste, file, and transfer AI outputs themselves. The tool creates administrative work instead of eliminating it. |

# **5.  The Four-Layer Architecture**

This is the reference architecture all documents describe. Maintain this structure and layer naming exactly.

| **Layer** | Function |
| --- | --- |
| **Layer 1 — Local Orchestrator** | Receives all requests. Classifies task type. Routes to the appropriate tool or model. Logs all interactions. Runs on business-controlled hardware. Does not reason; it routes. |
| **Layer 2 — Context Packager** | Reviews all outbound requests. Applies data minimization. Enforces business rules. Strips or masks sensitive data. Attaches only relevant context. |
| **Layer 3 — Specialized Cloud Models** | Performs deep reasoning, drafting, analysis, or domain-specific tasks. Receives only curated, minimized context. Multiple providers supported; routing is model-agnostic. |
| **Layer 4 — Trust Tier Governance** | Enforces WAL levels per workflow. Tracks performance over time. Manages escalation and demotion of autonomy. Owned by the business, not the vendor. |

# **6.  The Five-Document Series**

Each document has a defined purpose, audience focus, and scope. Do not allow a document to absorb the purpose of another.

| **Document** | Purpose & Scope |
| --- | --- |
| **Doc 1 — SMB Overview (COMPLETE)** | Introduces the concept to a non-technical business owner. Covers the four layers, six friction points, trust tiers, three business scenarios, and a phased deployment roadmap. This document exists. Do not reproduce it. |
| **Doc 2 — Business Readiness Self-Assessment** | A structured questionnaire that helps a business owner evaluate their current AI exposure, data risk, and workflow readiness. Output: a scored readiness profile that maps to the phased deployment plan. Audience: the owner, alone, before any vendor conversation. |
| **Doc 3 — Plain-Language Reference Architecture** | A non-code technical guide for the person who will implement the system. Covers hardware requirements, available open-source components (Ollama, llama.cpp, Open WebUI), connection patterns, and what a working Phase 1 setup looks like. Audience: a generalist IT person or managed service provider. |
| **Doc 4 — Vendor Evaluation Guide** | A buyer’s guide for evaluating AI vendors and consultants against this framework. Includes mandatory questions, red flags, and a scoring rubric. Audience: the business owner in a procurement conversation. |
| **Doc 5 — Regulated Industry Supplement** | Maps the architecture’s trust tiers and Context Packager to specific compliance frameworks: HIPAA (healthcare), attorney-client privilege and state bar rules (legal), GLBA (financial services). Audience: owners in regulated industries and their compliance advisors. |
| **Doc 6 — Operations ****&**** Governance Guide** | Post-deployment guide covering audit log review, trust tier promotion/demotion criteria, overreliance detection, incident response for a bad AI output, and quarterly governance cadence. Audience: the owner or office manager running the system day-to-day. |

# **7.  Scope Boundaries — What This Framework Is NOT**

These boundaries must be preserved across all documents. Do not allow framing drift toward any of the following:

| **NOT a federal or government architecture** This framework was adapted from a federal transportation proposal. All government-specific language, agency references, compliance citations (OMB, NIST, FedRAMP), and federal procurement framing have been deliberately removed. Do not reintroduce them. |
| --- |

| **NOT an Intelligence Maximalism approach** This framework explicitly rejects the single-large-model-handles-everything pattern. Do not frame any document in a way that suggests a powerful enough AI could replace the Orchestrator architecture. That framing is the problem this framework solves. |
| --- |

| **NOT a vendor recommendation** No specific AI vendor, cloud provider, or software product should be recommended or implied as preferred. The architecture is model-agnostic by design. Specific open-source tools (Ollama, llama.cpp) may be named in Doc 3 only, as implementation examples, not endorsements. |
| --- |

| **NOT a replacement for professional judgment** Every document must reinforce that WAL-0 is the default, human review is the baseline, and AI autonomy is earned — not assumed. No document should imply that this system removes the need for qualified professionals. |
| --- |

| **NOT a technical implementation manual (except Doc 3)** Documents 1, 2, 4, 5, and 6 are written for non-technical readers. Code, API references, model weights, and infrastructure configuration details belong only in Doc 3. |
| --- |

# **8.  Document-Specific Prompts**

Use the prompt below when starting the chat for each document. Paste it as the first user message after uploading this file.

## **Doc 2 — Business Readiness Self-Assessment**

| **Prompt to paste:** You are an expert in AI systems architecture and small business operations. I have uploaded the Master Context Document for our AI Orchestration Framework series. Your task is to produce Document 2: the Business Readiness Self-Assessment. This is a structured self-evaluation tool for a non-technical small business owner. It should help them assess: (1) what AI tools they are already using and what data those tools receive today, (2) which of their workflows involve sensitive data, (3) their highest-friction repeated tasks, (4) their current data storage and access practices, (5) their compliance obligations, and (6) their staff’s current AI literacy. The output should include a scored readiness profile that maps directly to the four deployment phases in Doc 1. Tone: plain, direct, non-technical. Format: Word .docx with a clean layout suitable for printing and completing by hand or on-screen. |
| --- |

## **Doc 3 — Plain-Language Reference Architecture**

| **Prompt to paste:** You are an expert in AI systems architecture and small business IT implementation. I have uploaded the Master Context Document for our AI Orchestration Framework series. Your task is to produce Document 3: the Plain-Language Reference Architecture. The audience is a generalist IT person or managed service provider with no AI specialization. It should cover: (1) minimum hardware requirements for a working Phase 1 Orchestrator, (2) available open-source components and what each one does (Ollama, llama.cpp, Open WebUI — as examples, not endorsements), (3) how the four layers connect in a working system, (4) what a Phase 1 deployment looks like end-to-end, and (5) common failure points and how to avoid them. No code. Diagrams may be described in text tables. Format: Word .docx. |
| --- |

## **Doc 4 — Vendor Evaluation Guide**

| **Prompt to paste:** You are an expert in AI procurement, vendor risk, and small business operations. I have uploaded the Master Context Document for our AI Orchestration Framework series. Your task is to produce Document 4: the Vendor Evaluation Guide. The audience is a non-technical small business owner sitting across from an AI vendor or consultant. It should include: (1) the ten questions every owner must ask before signing anything, (2) a red flag checklist (vendor behaviors that indicate lock-in, data risk, or misalignment with this framework), (3) a simple scoring rubric to compare proposals, and (4) a short section on what a good vendor response sounds like vs. a deflection. Tone: direct, protective, skeptical of vendor claims. Format: Word .docx. |
| --- |

## **Doc 5 — Regulated Industry Supplement**

| **Prompt to paste:** You are an expert in AI governance, healthcare compliance, legal ethics, and financial services regulation. I have uploaded the Master Context Document for our AI Orchestration Framework series. Your task is to produce Document 5: the Regulated Industry Supplement. It should map the four-layer architecture and WAL trust tiers to three regulatory environments: (1) HIPAA for healthcare practices, (2) attorney-client privilege and applicable state bar rules for legal practices, and (3) GLBA for financial services firms. For each: explain which architectural components satisfy which compliance requirements, identify residual risks the architecture does not automatically solve, and provide plain-language guidance the owner can take to their compliance advisor. Format: Word .docx. |
| --- |

## **Doc 6 — Operations ****&**** Governance Guide**

| **Prompt to paste:** You are an expert in AI governance, operational risk, and small business management. I have uploaded the Master Context Document for our AI Orchestration Framework series. Your task is to produce Document 6: the Operations and Governance Guide. The audience is the business owner or office manager running the system after deployment. It should cover: (1) how to read and act on audit logs, (2) the criteria for promoting a workflow from WAL-0 to WAL-1 (and the same for each subsequent level), (3) how to detect and respond to overreliance by staff, (4) the incident response process when an AI output causes a real problem, and (5) a quarterly governance review checklist. Tone: operational, practical, no-nonsense. Format: Word .docx. |
| --- |

*AI Orchestration Framework for Small Business — Master Context Document*

March 2026  |  Pre-Decisional Working Draft  |  Load at start of each document chat session