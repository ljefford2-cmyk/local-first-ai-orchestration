# Plain-Language Reference Architecture

*A non-code technical guide for the person who will implement the system.*

---

**AI Orchestration Framework**

**for Small Business**

**Document 3**

**Plain-Language Reference Architecture**

*For generalist IT staff and managed service providers*

*No AI specialization required*

March 2026  ·  Pre-Decisional Working Draft

# **How to Use This Document**

| **Who this is for: ** This document is written for the person responsible for setting up and maintaining the AI Orchestrator — typically a generalist IT professional or an MSP technician. You do not need prior AI experience. You do need to be comfortable installing software, configuring network access, and troubleshooting basic connectivity issues. |
| --- |

This document covers five topics, in order:

- Minimum hardware requirements for a working Phase 1 setup

- The open-source software components available to you, and what each one does

- How the four layers of the architecture connect in a working system

- What a complete Phase 1 deployment looks like, start to finish

- The most common failure points — and how to avoid them before they happen

| **No code in this document. ** Configuration commands, API references, and model weights are outside the scope of this guide. This document tells you what to install, why it matters, and how the pieces connect. Each component’s own documentation handles the how-to specifics. |
| --- |

## **A Note on Terminology**

This document uses terms defined in the series Master Context Document. The most important ones for your purposes:

| **Term** | **What It Means for You** |
| --- | --- |
| AI Orchestrator | The local software brain of the system. It receives requests, figures out what kind of task it is, and sends it to the right tool or model. It does not do the heavy thinking — it just directs traffic. |
| Route, Don’t Reason | The governing principle. The local model’s job is to classify and route, not to answer complex questions. Complex work goes to cloud models. |
| Context Packager | The data filter. Everything going out to a cloud model passes through here. Sensitive data is stripped before it leaves the building. |
| WAL (Workflow Autonomy Level) | A trust scale from 0 to 3. Every new workflow starts at WAL-0, meaning a human reviews every AI output before any action is taken. WAL levels are promoted only after demonstrated accuracy. |
| Local Environment | Hardware and software under the business’s direct control. This is the home base of the entire system. |
| Cloud Model | An external AI service (like OpenAI, Anthropic, or Google) reached via internet. The Orchestrator decides when to call these — staff do not call them directly. |

# **Section 1: Minimum Hardware Requirements**

The Orchestrator does not need powerful hardware. Its job is routing and classification — lightweight tasks that do not require the kind of processing power associated with running large AI models. What it does require is reliability: consistent availability, local network access, and enough headroom to handle multiple simultaneous requests without queuing delays.

## **1.1  Phase 1 Minimum Specifications**

The following specifications represent the minimum viable hardware for a Phase 1 Orchestrator serving a business of up to approximately 15 staff members. Phase 1 is intentionally scoped for modest, low-risk workflows only — lookups, formatting assistance, and document pre-population. Hardware can be expanded as the system matures.

| **Component** | **Phase 1 Minimum** | **Practical Notes** |
| --- | --- | --- |
| Processor (CPU) | 8-core modern processor (Intel Core i5 or equivalent), released within the past four years | A used workstation-class desktop from a reputable refurbisher will meet this requirement. Server hardware is not required for Phase 1. |
| Memory (RAM) | 32 GB minimum; 64 GB strongly recommended | RAM is the most common bottleneck. The local AI model loads entirely into memory. 32 GB is survivable; 64 GB avoids re-loading delays when the system has been idle. |
| Storage | 500 GB SSD minimum; 1 TB recommended | Model files are large. A 7-billion parameter model typically occupies 4–8 GB after compression. You will want room for multiple models, logs, and local data. |
| GPU (Graphics Card) | Integrated graphics sufficient for Phase 1; a dedicated GPU with 8+ GB VRAM accelerates response times | A GPU is not required to start. Adding one later is straightforward and significantly improves inference speed for larger models. |
| Network | Wired gigabit ethernet connection to the local network; stable broadband internet for cloud model calls | Do not run the Orchestrator on Wi-Fi. Latency spikes on wireless connections degrade the user experience unpredictably. |
| Operating System | Windows 11 Pro, Ubuntu 22.04 LTS, or macOS 13+ | Linux (Ubuntu) provides the smoothest experience with most open-source AI components. Windows works with additional configuration. macOS is viable for small teams, particularly on Apple Silicon hardware. |
| Dedicated hardware | Preferred; the Orchestrator should not share a machine with other production workloads | Running the Orchestrator on a shared workstation is possible but creates maintenance complications. A dedicated low-power mini PC (£300–£500 range) is a practical starting point. |

| **What you are not building: ** Phase 1 does not require a rack-mounted server, a GPU cluster, or enterprise networking equipment. A capable second-hand workstation, a small business router, and a reliable internet connection are sufficient starting points. |
| --- |

## **1.2  What Happens to Hardware as You Grow**

Each WAL level promotion — moving workflows from ‘recommend’ toward ‘execute’ — typically increases the demands placed on the local system. The pattern is predictable:

| **Deployment Phase** | **Typical Hardware Profile** |
| --- | --- |
| Phase 1 (WAL-0 workflows only) | Entry-level workstation; no GPU required; 32 GB RAM sufficient |
| Phase 2 (WAL-0 and WAL-1 workflows) | Dedicated machine; 64 GB RAM; dedicated GPU with 8+ GB VRAM recommended |
| Phase 3 (Multiple concurrent workflows, WAL-2 workflows added) | Workstation-class machine; 64–128 GB RAM; GPU with 12–16 GB VRAM; consider local NAS for log storage |
| Phase 4 (Full deployment, WAL-3 workflows added) | Purpose-built or enterprise-grade hardware; full IT review required; this phase is outside the scope of this document |

# **Section 2: Open-Source Software Components**

The software ecosystem for local AI inference is active and evolving. The tools described in this section are examples of open-source options commonly used in this type of deployment — they are not endorsements. Your specific choices will depend on your operating system, hardware configuration, and the preferences of the business owner. Multiple tools in each category are available; these are named because they are well-documented, widely used, and appropriate for the Phase 1 scope.

| **Open source means the software is free to use and inspect, but not that it requires no maintenance. ** All open-source tools require updates, and responsibility for keeping them current falls on whoever is administering the system. |
| --- |

## **2.1  Model Runtime: What Actually Runs the AI**

The model runtime is the software layer that loads an AI model into memory and responds to requests. Without it, you have a model file but no way to use it. There are two primary options for Phase 1:

**Ollama**

Ollama is a locally-installed application that downloads and runs AI models on your machine. It presents itself to other software as a simple API endpoint — meaning other applications can send it questions and receive answers without needing to know anything about the model itself.

| **Attribute** | **Details** |
| --- | --- |
| What it does | Downloads AI models from a library; loads them into memory; responds to requests from other software on the same machine or local network |
| Best for | Businesses running Windows or macOS, or teams where the administrator wants a simple setup with minimal command-line interaction |
| Strengths | Very easy installation; automatic model downloads; works on all major operating systems; handles GPU acceleration automatically on supported hardware |
| Limitations | Less granular control over memory allocation than some alternatives; newer project with a faster update cadence (stay current) |
| What it is not | It is not a user interface. Users do not interact with Ollama directly. It operates in the background. |

**llama.cpp**

llama.cpp is a lower-level runtime that provides more direct control over how models are loaded and how computing resources are allocated. It requires more familiarity with command-line tools but allows finer-grained optimization for specific hardware.

| **Attribute** | **Details** |
| --- | --- |
| What it does | Runs AI model inference directly on CPU or GPU; supports a wide variety of model formats; allows detailed control over memory and performance settings |
| Best for | Linux environments where an administrator wants precise hardware control; situations where GPU memory must be carefully managed across multiple models |
| Strengths | Highly configurable; runs on virtually any hardware; strong community support; excellent CPU-only performance |
| Limitations | No graphical interface or model library; requires more manual setup; updates are frequent and occasionally break compatibility with specific model formats |
| What it is not | Not a plug-and-play solution. Appropriate for administrators comfortable working in a terminal environment. |

| **Choosing between them: ** For most Phase 1 deployments, Ollama is the simpler starting point. llama.cpp becomes worth the complexity if you have specific hardware constraints or are running on Linux and want maximum performance. Either will do the job. The architecture does not depend on either one specifically. |
| --- |

## **2.2  User Interface: What Staff Actually See**

The model runtime operates silently in the background. Staff need a way to interact with the system. This is where a web-based front-end comes in. Several options exist; Open WebUI is described here as one well-regarded example.

**Open WebUI**

Open WebUI is a self-hosted web application that gives users a browser-based chat interface for interacting with local AI models. It connects to Ollama (or other compatible runtimes) and presents a familiar chat experience, similar to commercial AI products, but running entirely on your own hardware.

| **Attribute** | **Details** |
| --- | --- |
| What it does | Provides a web-based chat interface accessible from any browser on the local network; connects to the local model runtime; supports user accounts and conversation history |
| Best for | Businesses that want staff to have a simple, recognizable interface without each person needing to configure anything on their device |
| Strengths | No software to install on user devices (browser only); supports multiple user accounts with separate conversation histories; can be configured to restrict certain models or features per user |
| Limitations | Requires a stable local network connection; the Phase 1 version does not, by default, enforce the Context Packager rules — that layer requires additional configuration described in Section 3 |
| What it is not | Not a complete Orchestrator by itself. It is the window through which staff communicate with the system. The routing and filtering logic sits behind it. |

## **2.3  Additional Components You Will Encounter**

Beyond the core runtime and interface, several supporting tools are commonly involved in a working deployment. You do not need to install all of these immediately, but you should know what they are.

| **Component** | **What It Does** | **When You Need It** |
| --- | --- | --- |
| API Gateway / Reverse Proxy | Software that manages and controls how requests reach your local models. Can enforce access rules, rate limits, and logging. | Useful in Phase 2+ when you want tighter control over what goes in and out. Nginx and Caddy are common choices. |
| Prompt Management Layer | Configuration files or a lightweight application that stores the routing rules — which task types go to which models. | This is what makes the Orchestrator behave consistently. Without it, routing is manual and unreliable. |
| Log Aggregator | Software that collects and stores interaction logs in a searchable format. | Required for Trust Tier Governance (Layer 4). Simple options like plain-text log files are acceptable for Phase 1; structured databases become necessary at Phase 3+. |
| Local Embedding Store | A specialized database that stores text in a format AI models can search efficiently. Used for retrieval-augmented generation (RAG). | Not required for Phase 1. Becomes relevant when the business wants the AI to reference its own documents and files rather than relying on the model’s general knowledge. |
| Container Runtime (Docker) | Software that packages applications and their dependencies together, making them easier to install and update consistently. | Optional for Phase 1 but strongly recommended for Phase 2+. Reduces the ‘it worked on my machine’ problem significantly. |

# **Section 3: How the Four Layers Connect**

The architecture is defined by four distinct layers. Each has a specific job. None of them does the job of another. Understanding where one ends and the next begins is the most important conceptual foundation for this deployment.

## **3.1  The Four-Layer Architecture at a Glance**

| **Layer 1** | **Local Orchestrator** | Receives every request. Classifies task type. Routes to the right tool or model. Never reasons — it directs. |
| --- | --- | --- |
| **Layer 2** | **Context Packager** | Filters outbound requests. Strips sensitive data. Attaches only relevant context. Acts as a data firewall. |
| **Layer 3** | **Specialized Cloud Models** | Performs actual reasoning, drafting, and analysis. Receives only what Layer 2 approves. Interchangeable by provider. |
| **Layer 4** | **Trust Tier Governance** | Enforces workflow autonomy levels (WAL 0–3). Tracks performance over time. Owned entirely by the business. |

## **3.2  The Data Flow: What Happens When a Staff Member Sends a Request**

The most useful way to understand how the layers connect is to follow a single request from beginning to end.

| **Step** | **Layer Action** | **What Actually Happens** |
| --- | --- | --- |
| Step 1 | Staff member submits a request | A paralegal types a question into the web interface on their desktop browser. The request travels across the local network to the machine running the Orchestrator. |
| Step 2 | Layer 1 receives and classifies | The Local Orchestrator receives the request. It classifies the task type: Is this a document lookup? A drafting task? A compliance question? A routine calculation? Classification takes milliseconds. |
| Step 3 | Layer 1 routes the request | Based on the classification, the Orchestrator decides: Can a local model handle this? Does it need a cloud model? Which one? The routing rule is applied. The request is forwarded accordingly. |
| Step 4 | Layer 2 filters before any cloud call | If the request is routed to a cloud model, it passes through the Context Packager first. The Packager applies pre-configured data minimization rules: client names are masked, account numbers stripped, and only the relevant context is attached. |
| Step 5 | Layer 3 performs the work | The designated model — local or cloud — receives the prepared request and generates a response. The model has no visibility into the raw request that the staff member originally submitted. |
| Step 6 | Response returns and is logged | The response travels back through the Orchestrator to the staff member’s interface. Every step of the transaction is logged: who requested, what classification was applied, which model responded, and when. |
| Step 7 | Layer 4 enforces WAL rules | If the workflow is at WAL-0, the response is presented to the staff member for review before any action is taken. Nothing automated happens. If the workflow has been promoted to WAL-1 or higher, pre-defined lightweight automation may execute — but only within the parameters that Trust Tier Governance permits. |

## **3.3  What Local vs. Cloud Means in Practice**

The Orchestrator decides, for each request, whether to handle it locally or escalate to a cloud model. This decision is based on rules you configure, not on real-time judgment. In Phase 1, the default should be: local model first, cloud model only when local is inadequate for the task type.

| **Category** | **Examples and Notes** |
| --- | --- |
| Tasks appropriate for the local model | General lookups, summarizing documents the business already holds, formatting assistance, standard Q&A against a predefined prompt library, calendar and scheduling tasks |
| Tasks that typically require a cloud model | Complex legal or compliance drafting, multi-document reasoning, analysis requiring domain expertise the local model does not have, tasks where output quality is objectively better from a frontier model |
| Tasks that should never go to a cloud model | Anything containing unmasked personally identifiable information (PII), patient data, attorney-client privileged material, or non-public financial information — regardless of what the Context Packager is configured to strip |
| How the routing decision is made | You configure the rules. The Orchestrator does not decide this on its own judgment. The routing table is a document you maintain and review. |

| **The Orchestrator is only as good as its routing rules. ** If the routing configuration is vague, incomplete, or stale, requests will be misrouted. Maintaining this configuration is an ongoing operational responsibility, not a one-time setup task. |
| --- |

# **Section 4: What a Phase 1 Deployment Looks Like**

Phase 1 is deliberately constrained. Its purpose is to establish a working, trustworthy foundation — not to automate everything immediately. Every component introduced in Phase 1 is something you will build on. Nothing you configure here is thrown away.

## **4.1  Phase 1 Scope and Guardrails**

Before touching any hardware or software, the business owner and the IT administrator should agree on the following Phase 1 constraints:

| **Constraint** | **Rationale** |
| --- | --- |
| WAL level | All Phase 1 workflows run at WAL-0 only. No automated actions. Every AI output is reviewed by a qualified human before any downstream action is taken. |
| Workflow count | Start with no more than three workflows. Attempting to configure too many simultaneously makes troubleshooting impossible and increases the risk of routing errors. |
| Staff access | Phase 1 access should be limited to a small pilot group — ideally two to four staff members who are willing to provide feedback and report issues. |
| Data types in scope | Phase 1 should involve only low-sensitivity data. No patient records, no legal privileged documents, no financial account data. The system is not yet validated for those workflows. |
| Cloud model calls | Minimize cloud model calls in Phase 1. The goal is to validate the local routing and logging infrastructure first. Cloud escalation can be introduced once local stability is confirmed. |

## **4.2  Deployment Sequence**

The following sequence applies to a standard Phase 1 deployment. Steps are ordered to allow testing at each stage before adding complexity.

| **Stage** | **Name** | **What to Do** |
| --- | --- | --- |
| Stage 1 | Hardware procurement and OS installation | Source or configure the dedicated machine. Install the chosen operating system. Apply current system updates. Configure a static local IP address for the Orchestrator machine. |
| Stage 2 | Network access configuration | Confirm the machine has wired gigabit access to the local network. Configure firewall rules to allow local network traffic to the Orchestrator on the designated port. Block all external inbound access to this port. |
| Stage 3 | Model runtime installation | Install Ollama or llama.cpp per the component documentation. Verify that the runtime starts successfully and is accessible from the local network. Do not install the AI model yet. |
| Stage 4 | AI model download and validation | Download the selected base model (a 7- or 8-billion parameter instruction-tuned model is appropriate for Phase 1). Run a simple test prompt directly against the runtime to confirm the model responds correctly. Document the model name and version. |
| Stage 5 | User interface installation | Install Open WebUI or the chosen interface. Connect it to the runtime. Confirm that a test user account can log in, send a prompt, and receive a response. |
| Stage 6 | Routing configuration (Layer 1) | Configure the routing rules for your three Phase 1 workflows. Assign each workflow a task type label, a destination model, and a default prompt template. Test each routing path with synthetic (non-real) data. |
| Stage 7 | Context Packager configuration (Layer 2) | Define the data minimization rules for any outbound requests. At Phase 1, this may be as simple as a list of prohibited keywords and a rule to strip identifiable information from document prompts. Test each rule explicitly. |
| Stage 8 | Logging configuration (Layer 4) | Confirm that every transaction is being written to a log file with: timestamp, user identifier, task classification, model used, and response status. Review the first ten log entries manually to confirm they contain what you expect. |
| Stage 9 | Pilot staff onboarding | Add the two to four pilot users to the system. Walk them through the interface. Explain what WAL-0 means in practice: they will see suggestions, not automated actions. Ask them to flag any response that seems wrong. |
| Stage 10 | Two-week validation period | Run the system for two weeks before declaring Phase 1 complete. Review logs weekly. Track: response accuracy, routing errors, any data that reached a cloud model unexpectedly, and any staff confusion about workflow expectations. |

## **4.3  What Phase 1 Does Not Include**

The following capabilities are explicitly out of scope for Phase 1. This list exists so that you can push back confidently if a stakeholder asks for them prematurely.

- Automated file creation or document submission (WAL-1 minimum required)

- Integration with external business software such as practice management systems, CRMs, or accounting platforms

- Retrieval-augmented generation (RAG) against the business’s own document library

- Any workflow touching regulated data (PHI, PII, privileged communications) — this requires the Regulated Industry Supplement review first

- Multi-model routing (sending different tasks to different cloud providers based on task type)

- Staff-facing mobile access outside the local network

| **Phase 1 complete means: ** Three workflows running cleanly at WAL-0 for two consecutive weeks, with complete logs and zero unintended cloud data transmissions. That is the success criterion. Not ‘everything is installed.’ |
| --- |

# **Section 5: Common Failure Points and How to Avoid Them**

The following failure modes appear consistently across first deployments of this type of architecture. None of them are exotic. Most are preventable with planning. The ones that are not preventable are manageable if you know they are coming.

**Failure Point 1: RAM exhaustion during model loading**

A 7-billion parameter model in its compressed form typically requires 4–8 GB of RAM. If the machine is running other processes and RAM is insufficient, the model will fail to load or will be swapped to disk, producing drastically slow response times that users interpret as the system being broken.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Dedicate the Orchestrator machine to this purpose only. Size RAM to 64 GB where budget permits. Do not run the Orchestrator on the same machine as production databases or file servers. |
| Detection | Monitor RAM usage during load. Most operating systems provide built-in tools. Look for available RAM dropping below 4 GB during inference. |
| Recovery | Restart the model runtime. If RAM is chronically insufficient, reduce the model size (a 3- or 4-billion parameter model may be adequate for routing tasks) or upgrade hardware before expanding scope. |

**Failure Point 2: Routing misconfiguration sends the wrong task to the wrong model**

If task classification rules are too broad or are not tested against realistic input, requests will be routed incorrectly. A compliance question routed to a general-purpose local model will produce an answer, but not necessarily the right one. Staff will not notice unless they have been trained to review outputs critically.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Write routing rules in plain language before configuring them technically. Have the business owner review the rules. Test every rule category with at least five realistic inputs before pilot launch. |
| Detection | Log review. A sudden pattern of staff flagging incorrect responses, or responses that seem plausible but are factually wrong, indicates a routing error before a context error. |
| Recovery | Revise the routing rule. Retest. Do not promote the workflow to WAL-1 until the classification has been clean for two weeks. |

**Failure Point 3: Sensitive data reaches a cloud model despite Context Packager rules**

The Context Packager is only as effective as the rules it contains. If a staff member submits a request with a client name embedded in a sentence that the filter does not pattern-match, that name will pass through unmasked. This is the highest-severity failure mode in a regulated industry context.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Test the Context Packager against adversarial inputs before launch. Have a staff member who does not know the filter rules try to submit a prompt that includes a real name or sensitive phrase. Review the log to confirm it was caught. |
| Detection | Log all outbound requests in their pre-cloud form. Periodic manual review of outbound logs (not just response logs) will catch leakage patterns. |
| Recovery | Immediately disable cloud routing for the affected workflow. Investigate the log to understand what passed through and when. Add the missed pattern to the filter rules. If the business is in a regulated industry, escalate to the compliance advisor before re-enabling. |

**Failure Point 4: Overreliance by staff at WAL-0**

WAL-0 requires human review before any action. In practice, when AI responses consistently appear reasonable, staff begin approving them without genuine review. This is not a technology failure — it is a behavioral one. It degrades the quality assurance the trust tier is designed to provide.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Set explicit expectations during staff onboarding. The AI recommendation is a draft, not an answer. Periodically introduce intentionally incorrect outputs to test whether staff are catching them. |
| Detection | Review time logs. If a staff member is approving twenty AI outputs in two minutes, the review is perfunctory. Flag this in the quarterly governance review. |
| Recovery | Retrain. If the problem is systemic, reduce the volume of AI-assisted tasks for that workflow until genuine review cadence is re-established. Do not promote the workflow to WAL-1 if overreliance has been detected. |

**Failure Point 5: Software update breaks the model runtime or interface**

Open-source AI tooling is under active development. Update cycles are faster than traditional enterprise software. A major version update to Ollama or Open WebUI can change configuration formats, API endpoints, or model compatibility in ways that break a working setup.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Do not auto-update the Orchestrator machine. Stage all updates in a test environment first. Subscribe to the release notes for each component you use. Review breaking changes before applying any update to production. |
| Detection | After any update, run a smoke test: submit one request from each workflow and confirm it routes and responds correctly. |
| Recovery | Maintain a rollback snapshot of the Orchestrator machine taken immediately before each update. If an update breaks a workflow, restore the snapshot and defer the update until compatibility is confirmed. |

**Failure Point 6: Logging gaps make it impossible to reconstruct what happened**

Trust Tier Governance depends entirely on logs. If logging is incomplete, interrupted, or stored in a format that cannot be searched, you cannot audit the system, diagnose routing problems, or validate a workflow for WAL promotion. Missing logs also represent a compliance gap in regulated industries.

| **Response Type** | **Action** |
| --- | --- |
| Prevention | Define the log schema before deploying. Confirm every field is populated by reviewing the first 10 transactions manually. Set up a log rotation policy that retains at least 90 days of transaction data. |
| Detection | A gap in log timestamps is the clearest signal. Weekly spot-checks during Phase 1 will catch this before it accumulates. |
| Recovery | Fix the logging configuration. Accept that the gap period represents unverifiable activity. If the business is subject to audit obligations, document the gap and the corrective action. |

## **5.1  Failure Point Summary**

| **Failure Point** | **Severity** | **Primary Prevention** |
| --- | --- | --- |
| RAM exhaustion | Medium | Dedicate hardware; size RAM to 64 GB |
| Routing misconfiguration | High | Test every routing rule before pilot launch |
| Sensitive data cloud leakage | **Critical** | Adversarial testing of Context Packager before launch |
| Staff overreliance at WAL-0 | High | Explicit expectations; periodic deliberate error injection |
| Update breaks runtime | Medium | Stage updates; maintain rollback snapshot |
| Logging gaps | High | Define and validate log schema before first transaction |

# **Appendix: Quick Reference — Phase 1 Deployment Checklist**

Use the following as a working checklist during deployment. Each item corresponds to a step or principle described in the main document.

## **Hardware and Infrastructure**

| **Checklist Item** | **Notes** |
| --- | --- |
| ☐  Dedicated machine procured | Not shared with production workloads |
| ☐  RAM at 32 GB minimum (64 GB recommended) | Sized for model loading headroom |
| ☐  500 GB SSD minimum installed | With room for multiple model files and 90 days of logs |
| ☐  Wired ethernet connection confirmed | Not Wi-Fi |
| ☐  Static local IP assigned to Orchestrator machine | Required for reliable client connections |
| ☐  Inbound firewall rule confirmed (local network only) | External inbound blocked |
| ☐  OS up to date before any component installation | Clean baseline |

## **Software Installation**

| **Checklist Item** | **Notes** |
| --- | --- |
| ☐  Model runtime installed and tested | Ollama, llama.cpp, or equivalent |
| ☐  AI model downloaded and version documented | 7B or 8B instruction-tuned model recommended for Phase 1 |
| ☐  Direct model response test passed | Confirm runtime responds before installing UI |
| ☐  User interface installed and connected to runtime | Tested from at least two client devices on the local network |
| ☐  Test user accounts created and login confirmed |  |

## **Configuration and Testing**

| **Checklist Item** | **Notes** |
| --- | --- |
| ☐  Routing rules written in plain language and reviewed by owner | Before technical configuration |
| ☐  Three Phase 1 workflows configured | No more than three to start |
| ☐  Each routing path tested with synthetic data | Five test inputs per workflow minimum |
| ☐  Context Packager rules defined and documented |  |
| ☐  Context Packager tested adversarially | Someone who does not know the rules tries to submit sensitive data |
| ☐  Log schema confirmed and first 10 transactions reviewed manually |  |
| ☐  Log rotation policy configured (90-day minimum retention) |  |
| ☐  Rollback snapshot taken of Orchestrator machine | Taken after successful configuration, before pilot launch |

## **Pilot Launch**

| **Checklist Item** | **Notes** |
| --- | --- |
| ☐  Pilot group limited to 2–4 staff members |  |
| ☐  WAL-0 expectations communicated explicitly to all pilot users | AI outputs are drafts, not answers; human review required before any action |
| ☐  Feedback channel established | How pilot users report incorrect or unexpected outputs |
| ☐  Weekly log review scheduled for first four weeks |  |
| ☐  Two-week clean operation period achieved before declaring Phase 1 complete | Zero unintended cloud transmissions; zero unresolved routing errors |

| **Phase 1 is a foundation, not a final destination. ** When these items are checked and the two-week validation is complete, you have a working, governed AI system that your business can trust enough to expand carefully. The next step — scoped in the Operations and Governance Guide (Document 6) — is evaluating which workflows are ready for WAL-1 promotion and what that promotion requires. |
| --- |

*AI Orchestration Framework for Small Business — Document 3: Plain-Language Reference Architecture*

*Pre-Decisional Working Draft · March 2026 · Part of a five-document series*