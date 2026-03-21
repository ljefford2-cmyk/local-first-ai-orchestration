# Federal Regulatory Agency — Departmental AI Orchestrator

## Environment Profile

A large federal regulatory agency responsible for the safety of critical national infrastructure across multiple operational divisions. The agency employs thousands of inspectors, analysts, and support personnel who depend on mission-critical information systems to make safety-critical decisions. Each operational division maintains specialized systems for its regulatory domain.

## Documents

| Document | Description |
|----------|-------------|
| [architecture-unified.md](architecture-unified.md) | Complete architecture, implementation plan, and gap assessment — the primary technical reference |
| [case-for-departmental-orchestrator.md](case-for-departmental-orchestrator.md) | Design philosophy paper: why the agency's AI agents should route, not reason |
| [executive-briefing.md](executive-briefing.md) | Executive-level briefing on architecture, project plan, and design philosophy |

## Key Architectural Adaptations

The reference architecture required specific adaptations for federal regulatory operations:

- **"Local" redefined for Zero Trust.** In a federal environment, "local" cannot mean field laptops — it means departmental compute infrastructure within the agency's authorization boundary, subject to enclave controls and Zero Trust architecture per OMB M-22-09 and NIST SP 800-207.

- **FedRAMP-authorized cloud models.** All external AI services must be FedRAMP-authorized at the appropriate impact level. The approved services catalog enforces this architecturally.

- **CUI and data classification.** The Context Packager enforces 32 CFR Part 2002 CUI minimization and agency-specific data classification on every outbound request — structurally, not by policy instruction.

- **Multi-division shared service model.** The orchestrator serves as infrastructure shared across operational divisions, while each division develops domain-specific skills and workflow contracts through its own engineering team.

- **Mission system integration.** The orchestrator interfaces with division-specific legacy information systems through typed workflow contracts. Many mission systems predate modern API standards, requiring adapter layers as a deployment prerequisite.

- **Inspector-centric design.** Field personnel are domain experts who will rightfully question any system that claims to assist their judgment. The human factors strategy is built around earned trust, not mandated adoption.

## Compliance Framework

The architecture maps every component to the controlling federal policy stack:

- OMB M-22-09 / NIST SP 800-207 (Zero Trust)
- OMB M-21-31 / NIST SP 800-92 (Logging)
- OMB M-24-10 / NIST AI RMF / EO 14110 (AI Governance)
- NIST SP 800-53 Rev. 5 (Security Controls)
- HSPD-12 / FIPS 201-3 (Identity)
- 32 CFR Part 2002 / SP 800-171 Rev. 3 (CUI)
- FIPS 140-3 (Cryptography)
- FedRAMP (Cloud Authorization)
- NIST SP 800-218 (SSDF — Secure Development)
- Section 508 / WCAG 2.1 AA (Accessibility)
- FISMA 2014 (Continuous Security)

## Adversarial Review History

These documents incorporate findings from independent adversarial reviews conducted across Claude (Anthropic), GPT-4 (OpenAI), and Gemini (Google). Each review's findings were incorporated into successive versions.

## Disclaimer

These documents describe a reference architecture for a *class* of federal regulatory environment. They do not represent the position of any specific federal agency and were not commissioned, endorsed, or reviewed by any government organization. All content is derived from publicly available federal policy, published standards, and general knowledge of federal IT environments.
