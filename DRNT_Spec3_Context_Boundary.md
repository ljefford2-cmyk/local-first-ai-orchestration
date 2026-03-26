# DRNT Interface Specification

## Spec 3: Context Boundary Specification

**FINAL** · March 2026 · Depends on: Spec 1 (Audit/Event Schema), Spec 2 (Capability Model)

---

## 1. Purpose

This spec defines the data structure of a DRNT request as it moves through the Context Packager, the sensitivity classification rules, the contract between the Packager's primary scan and the egress gateway's secondary scan, and the tamper-evident linkage between the audit log and the context store. The Context Packager is owned by the `context.package` auxiliary capability (Spec 2 Section 7.2).

## 2. The Context Object

A context object is the complete data structure assembled for a single dispatch. One per dispatch; retries create a new one.

### 2.1 Schema

```json
{
  "context_package_id": "UUIDv7",
  "job_id": "UUIDv7",
  "governing_capability_id": "route.cloud.claude",
  "governing_wal_level": 0,
  "context_package_wal_level": 0,
  "effective_packaging_level": 0,
  "target_model": "claude-sonnet-4-20250514",
  "route_id": "claude-sonnet-default",
  "task_specification": {
    "request_category": "",
    "parsed_intent": "",
    "output_format": "",
    "prompt_template_id": "",
    "prompt_template_version": ""
  },
  "retrieved_candidates": [
    {
      "source_id": "",
      "source_type": "user_input|memory_object|system_context",
      "shareability": "cloud_eligible|local_only",
      "confidence": 0.0,
      "excluded": false,
      "exclusion_reason": "local_only|below_confidence|over_budget|null"
    }
  ],
  "eligible_context_fields": [
    {
      "field_id": "UUIDv7",
      "field_name": "",
      "source_type": "",
      "source_id": "",
      "sensitivity_class": "none|name|location|date|financial|credential|custom|unclassifiable",
      "classification_status": "matched|safe_none|unclassifiable",
      "shareability": "cloud_eligible",
      "confidence": 0.0,
      "content": "the actual data",
      "content_hash": "SHA-256"
    }
  ],
  "assembled_payload": "the prompt string",
  "assembled_payload_hash": "SHA-256",
  "token_estimate": 0,
  "created_at": "ISO 8601"
}
```

**`retrieved_candidates`:** complete pre-scan inventory. Everything retrieved, including excluded items with reasons. `eligible_context_fields`: post-gate subset entering sensitivity scan. Together they reconstruct the full decision process.

**`effective_packaging_level` = min(governing WAL, context.package WAL).** A WAL-0 Packager servicing a WAL-2 governing capability operates at level 0. Most restrictive wins.

## 3. Memory Selection Policy

Three stacked gates before anything reaches the sensitivity scanner:

### 3.1 Gate 1: Shareability

`local_only` objects are excluded absolutely. Recorded in `retrieved_candidates` with `exclusion_reason: local_only`. Default: `local_only`. User explicitly tags objects as `cloud_eligible`.

### 3.2 Gate 2: Confidence Threshold

Below minimum threshold (default 0.3): excluded with `exclusion_reason: below_confidence`.

| Source Type | Default | Rationale |
| --- | --- | --- |
| User-stated fact | 1.0 | Highest trust. User said it directly. |
| Extracted from uploaded doc | 0.8 | High trust. User provided the document. |
| Inferred from conversation | 0.6 | Medium. Derived, not stated. |
| Derived summary | 0.4 | Lower. Lossy compression. |
| External source | 0.3 | Minimum. Defense against memory poisoning. |

### 3.3 Gate 3: Token Budget

Three pools: system context (fixed, ~500–2000 tokens), memory context (configurable fraction), user input + padding. Highest-confidence objects included first. Whole objects only—no mid-content truncation. Excess: `exclusion_reason: over_budget`.

## 4. Sensitivity Classification

| Class | Detection | Default Action | Status | Examples |
| --- | --- | --- | --- | --- |
| `name` | Regex + NER + user tags | strip | matched | Full names, emails |
| `location` | Regex + geocoding + tags | generalize | matched | Addresses, GPS |
| `date` | Regex + temporal NER | generalize | matched | Birthdates |
| `financial` | Regex + tags | strip | matched | Account numbers |
| `credential` | Regex (hardcoded) | strip (always) | matched | API keys, passwords |
| `custom` | User-defined rules | User-defined | matched | Project codenames |
| `none` | No rule; classifiable safe | pass | safe_none | General queries |
| `unclassifiable` | No rule; not classifiable | strip (default-deny) | unclassifiable | Ambiguous patterns |

**Default-deny:** unclassifiable content is stripped. This is conservative success (`failing_capability_id: null`), not a strip failure. Over-redacting is recoverable; under-redacting is not.

Sensitivity config: `/var/drnt/config/sensitivity.json`. `credential` class `hardcoded: true`.

## 5. Transform Pipeline

- **Stage 1: Candidate Retrieval** — Collect all potential context. Produce `retrieved_candidates` with exclusion records.
- **Stage 2: Eligibility Gating** — Apply shareability, confidence, budget. Survivors become `eligible_context_fields` with field_ids.
- **Stage 3: Sensitivity Scan + Transform** — Per-field (and per-span for mixed content). Emit `context.strip_detail` for each non-pass. Precedence: strip > generalize > pass. Credential always strip. No config override.
- **Stage 4: Payload Assembly** — Construct prompt from template + surviving context. Store full context object in `/var/drnt/context/{context_package_id}.json`. Emit `context.packaged` with hashes.

**Mixed-content fields:** a single field may produce multiple strip_detail events with different spans. `field_id` may appear in multiple rollup arrays. The arrays are not mutually exclusive.

## 6. WAL-Level Context Rules

| Level | Context Rules |
| --- | --- |
| **0** | Strictest. Confidence 0.3. Memory pool 40%. All rules enforced, no exceptions. |
| **1** | Same rules. Memory pool 50%. Prompt template formatting applied. |
| **2** | Same core rules. Memory pool 60%. Confidence may lower to 0.2. Narrow task-type exceptions allowed. |

Core pipeline is invariant across all levels: strip credentials, default-deny on unclassifiable, most-restrictive-wins.

## 7. Egress Gateway Secondary Scan

### 7.1 Category A: Sensitivity Catches (Strip Failures)

Gateway catches content Packager missed → blocks outbound → `context.strip_detail(detected_by: egress_gateway, failing_capability_id: "context.package")` → `job.failed(context_error)` → trust consequences fire. Gateway never silently fixes.

### 7.2 Category B: Transport/Policy Failures

Route mismatch, token overflow, endpoint unavailable, policy violation, model mismatch → `egress.validation_failure` → does NOT trigger strip-failure trust path. These are egress config/connectivity errors, not Packager failures.

## 8. Auditable Snapshots

**`job.classified` (Plan):** before Packager runs. `governing_capability_id`, `declared_pipeline`, `routing_recommendation`.

**`context.packaged` (Outcome):** after Stage 4. field_id rollups, `exclusion_summary`, `assembled_payload_hash`, `context_object_hash`.

**Context store:** `/var/drnt/context/{context_package_id}.json`. Full context object. Written once. Verifiable: hash stored payload → compare to `assembled_payload_hash`. Hash stored file → compare to `context_object_hash`.

Prompt templates: `/var/drnt/config/templates/`. One per task type. Versioned.

## 9. Scope Boundaries and Cross-References

- Egress endpoint registry and gateway checks (Spec 4)
- Override interaction with dispatched context (Spec 5)
- Worker execution egress privacy enforcement (Spec 6 Section 3.4)
- NER model selection, memory lifecycle (implementation / future spec)

---

*End of Spec 3 — FINAL. Part of the DRNT Interface Specification series: Spec 1 (Audit/Event Schema), Spec 2 (Capability Model), Spec 3 (Context Boundary), Spec 4 (Egress Policy Binding), Spec 5 (Override Semantics), Spec 6 (Silo Runtime Security).*
