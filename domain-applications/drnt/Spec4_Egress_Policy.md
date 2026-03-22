
[Spec4_Egress_Policy.md](https://github.com/user-attachments/files/26163509/Spec4_Egress_Policy.md)
# DRNT INTERFACE SPECIFICATION

## Spec 4: Egress Policy Binding

FINAL  •  March 2026  •  Depends on: Spec 1, Spec 2, Spec 3

# 1. Purpose

This spec defines how Context Packager decisions become routing constraints at the network layer: the egress registry, the gateway check sequence, and the Docker network topology that enforces the boundary.

# 2. The Egress Registry

/var/drnt/config/egress.json

Human-edited. Changes emit system.config_change. Default: deny. If an endpoint is not in the registry, no payload reaches it.

## 2.1 Entry Schema

```json
{
  "route_id": "claude-sonnet-default",
  "provider": "anthropic",
  "endpoint_url": "https://api.anthropic.com/v1/messages",
  "model_string": "claude-sonnet-4-*",
  "allowed_capabilities": ["route.cloud.claude", "route.multi"],
  "auth": { "method": "bearer_token", "secret_ref": "ANTHROPIC_API_KEY" },
  "constraints": { "max_tokens_per_request": 200000,
    "max_cost_per_request_usd": 2.00,
    "rate_limit_rpm": 60, "rate_limit_tpd": 1000000,
    "tls_minimum": "1.2" },
  "health": { "check_interval_seconds": 300,
    "timeout_ms": 5000, "consecutive_failures_before_down": 3 },
  "enabled": true
}
```

**auth.secret_ref**is an env var reference, never the secret itself. Resolved at dispatch time from /var/drnt/secrets/.env.

# 3. Network Architecture

The orchestrator cannot reach the internet. This is Docker network topology, not a firewall rule.

Docker bridge: drnt-internal

Orchestrator: drnt-internal only. No external access.

Gateway: drnt-internal + host (outbound only).

Log writer: drnt-internal only. Unix socket.

Tailscale: phone/watch connectivity. No egress path.

# 4. Gateway Check Sequence

job.dispatched is emitted when the gateway confirms outbound transmission, BEFORE the model responds. It IS the authorization record—no separate egress-allow event.

| **#** |**Check** | **Block Condition** |**Event on Block** |
| --- | --- | --- | --- |
| 1 | Route exists | route_id not in registry | egress.validation_failure (route_mismatch) |
| 2 | Route enabled | enabled: false | egress.validation_failure (policy_violation) |
| 3 | Capability binding | capability_id not in allowed_capabilities | egress.validation_failure (policy_violation) |
| 4 | Model binding | target_model doesn’t match model_string glob | egress.validation_failure (model_mismatch) |
| 5 | Token limit | Re-estimated tokens > max_tokens_per_request | egress.validation_failure (token_overflow) |
| 6 | Cost limit | cost > max_cost_per_request_usd | egress.validation_failure (policy_violation) |
| 7 | Rate limit | RPM or TPD exceeded | egress.validation_failure (policy_violation) |
| 8 | Payload hash | SHA-256(payload) ≠ assembled_payload_hash | egress.validation_failure (policy_violation) |
| 9 | Sensitivity scan | Credential/sensitive patterns found | context.strip_detail (egress catch) |
| 10 | Auth resolution | secret_ref unresolvable | egress.validation_failure (policy_violation) |
| 11 | TLS/connectivity | Endpoint unreachable or TLS < minimum | egress.validation_failure (endpoint_unavailable) |

**Only check 9 emits context.strip_detail.**All others emit egress.validation_failure with no strip-failure trust consequence.

**Failure attribution:**sensitivity catch → failing_capability_id: "context.package". Route/policy → sentinel "egress_config". Connectivity → sentinel "egress_connectivity". Sentinels excluded from WAL counters.

**Gateway re-estimates tokens**from the provider-formatted request using the provider adapter’s tokenizer. The gateway is authoritative for constraint enforcement.

**egress_config_hash**is stamped into job.dispatched so forensic reconstruction is exact.

# 5. Multi-Model Dispatch

route.multi: one context package, multiple independent dispatches. Each goes through the full check sequence. Each gets its own job.dispatched event (same job_id, different route_id, same assembled_payload_hash). Suspended providers excluded. All suspended = job blocked. Sensitivity decisions made once, applied uniformly.

# 6. Rate Limiting and Health

Per-route rate limits (RPM sliding window, TPD rolling 24h). In-memory, reset on restart. Health probes every check_interval_seconds; consecutive failures mark route down; auto-recovery on successful probe.

# 7. Provider Adapters

| **Provider** |**Endpoint** | **Auth** |
| --- | --- | --- |
| anthropic | https://api.anthropic.com/v1/messages | x-api-key header + anthropic-version |
| openai | https://api.openai.com/v1/chat/completions | Bearer token |
| google | https://generativelanguage.googleapis.com/v1beta/... | API key or OAuth2 |

# 8. Forensic Reconstruction

Given a job_id: job.dispatched (route_id, context_package_id, assembled_payload_hash, egress_config_hash) → context.packaged (field decisions, hashes) → context store (full payload) → egress registry via route_id + config change history → model.response (result_id, response_hash). Every link cryptographically anchored.

# 9. Startup Validation

- Unique route_ids. allowed_capabilities reference registered capabilities. secret_refs resolvable. HTTPS only. TLS ≥ 1.2. Constraints internally consistent.

Failures prevent gateway startup. No gateway = no dispatch.

# 10. Scope Boundaries

- Override mechanics for dispatched jobs (Spec 5)

- Cloud provider ToS / data retention (operational due diligence)

- DNS resolution, certificate pinning (implementation)

*End of Spec 4 — FINAL.*
