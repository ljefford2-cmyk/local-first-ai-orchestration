# K-12 Education — Sovereign AI for Rural School Districts

## Environment Profile

A rural Title I school district with approximately 3,400 students, limited IT staff, strong fiber broadband infrastructure, and student data protection obligations under FERPA, COPPA, state student data privacy law, and IDEA. The architecture leverages a regional education service agency (RESA) shared-services model to overcome the staffing constraint that would otherwise make local AI infrastructure unsustainable for a district of this size.

## Documents

| Document | Description |
|----------|-------------|
| [implementation-analysis.md](implementation-analysis.md) | Complete implementation analysis and engineering specification — the primary technical reference |
| [consolidated-report.md](consolidated-report.md) | Consolidated architecture report synthesizing multi-model research findings |
| [day-in-the-life.md](day-in-the-life.md) | Scenario walkthrough: three roles (teacher, principal, superintendent) through one school day |
| [feasibility-assessment.md](feasibility-assessment.md) | Honest feasibility assessment: is this a good idea, and could it work? |
| [landscape-research.md](landscape-research.md) | National landscape research on K-12 AI adoption, costs, and shared-services models |

## The Problem

Teachers are already using AI. Nationally, teacher AI usage doubled from 25% to 53% in a single year. When a teacher pastes IEP content into a consumer chatbot, or a special education coordinator uploads evaluation data to generate a narrative, that educator has transmitted protected student records to a third-party server with no Data Processing Agreement, no audit trail, and no district oversight. This is not a hypothetical risk — it is a current FERPA exposure in virtually every school district in America.

The question is not whether to permit AI. The question is whether to govern it through network architecture and enforceable technical controls, or to continue relying on acceptable use policies that teachers may not read and cannot be technically enforced.

## Key Architectural Adaptations

The reference architecture required specific adaptations for K-12 environments:

- **Student data never leaves the district boundary for Tier 1 and Tier 2.** Approximately 60–80% of daily requests resolve entirely on local hardware at zero cost and zero privacy exposure. Only Tier 3 cloud escalation occurs, and only after structural redaction removes all personally identifiable information.

- **RESA shared-services model.** The binding constraint is IT staffing, not technology or cost. The architecture requires Linux administration, Docker orchestration, and LLM pipeline maintenance — skills that are functionally nonexistent in rural K-12 IT departments. The RESA model amortizes a specialized AI systems administrator across member districts, reducing the per-district cost to $10,000–$15,000/year.

- **Georgia Standards of Excellence via CASE API.** The local system grounds every teacher query in the exact state academic standard using the machine-readable CASE (Competency and Academic Standards Exchange) format, accessible via REST API. This enables standards-aligned responses that no commercial SaaS vendor replicates.

- **BLOCKED tier for prohibited operations.** IEP goal writing, educator evaluation, student placement, and discipline decisions are architecturally blocked — not policy-blocked, but technically prevented from executing.

- **CIPA Safety Classifier.** Content filtering integrated at the inference layer, not dependent on external proxy services.

- **E-Rate and E-SPLOST funding alignment.** The phased implementation timeline is synchronized with state budget cycles, academic calendars, and federal E-Rate filing deadlines.

## Compliance Framework

- FERPA (20 U.S.C. § 1232g; 34 CFR Part 99)
- COPPA 2025 amendments
- Georgia Student Data Privacy Act (O.C.G.A. §§ 20-2-660 through 20-2-668 — SB 89)
- GaDOE AI Guidelines (January 2025 traffic-light classification)
- IDEA (Individuals with Disabilities Education Act)
- Georgia breach notification safe harbor (O.C.G.A. § 10-1-912)

## Adversarial Review History

The architecture was subjected to independent adversarial review by ChatGPT and Gemini. Key findings incorporated include:

- Institutional discipline as the gating constraint (not technology)
- Topology-over-contracts framing for data protection
- Five failure modes in the original proposal
- RESA staffing model as structural prerequisite, not optional enhancement

## The Honest Assessment

From the feasibility analysis: *"Yes, this is a sound concept. The technology exists, the costs are reasonable, and the privacy advantages are real. But no school district in America has done this yet in production. The concept is strongest when deployed through a shared-services cooperative model — like a RESA, BOCES, or ISD — rather than by any single district acting alone."*

## Disclaimer

These documents describe a reference architecture applied to a class of rural K-12 environment. They do not represent the position of any specific school district and were not commissioned or endorsed by any educational institution. District-specific details have been generalized. State-level references (Georgia Department of Education, Georgia state law) are retained as public policy context.
