# Earlier Repository Materials — Reading Guidance

Several files in this repository predate the August 2026 controlled three-volume architecture. They remain useful as historical, methodological, or implementation-facing material, but they no longer control corpus-level architecture when their framing differs from the frozen Plan and Final Controlled Manuscripts.

For the broader progression from these materials through the supporting publications and into the controlled architecture, see the [Design Evolution Reading Guide](../history/design-evolution.md).

## Key earlier documents

- [`docs/why-you-cannot-depend-on-a-single-ai-model.md`](../why-you-cannot-depend-on-a-single-ai-model.md) — earlier reference architecture centered on the limits of a single model and the local-orchestrator pattern.
- [`docs/drnt-specification-technical-overview.md`](../drnt-specification-technical-overview.md) — implementation-facing overview of the original DRNT specification suite.
- [`docs/lightweight-evaluation-loop.md`](../lightweight-evaluation-loop.md) — earlier personal-scale evaluation/follow-up concept.

## How to use them

Use these documents for historical reasoning, technical inheritance, or the bounded subject they explicitly address. Do not use them to override:

1. Project Plan v0.2;
2. Volume 1 — Define Reality;
3. Volume 2 — Evaluate Reality;
4. Volume 3 — Connect Reality; or
5. current gateway implementation evidence.

Where terminology differs, map the older concept into the controlled vocabulary rather than silently changing the controlled architecture.

In particular:

- the local model is not the organizing object of the current architecture;
- “one AI is not enough” is not the final governance thesis;
- multi-model agreement is not a safety property without a defensible independence basis;
- a local orchestrator is one implementation mechanism, not the organizational system;
- historical claim-state terminology does not replace Supported, Narrowed, Indeterminate, Unsupported, and Contradicted / Failed; and
- older implementation-status statements may be stale and must be checked against the gateway repository's current STATUS matrix.
