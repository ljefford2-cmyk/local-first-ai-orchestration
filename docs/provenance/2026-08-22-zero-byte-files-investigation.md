# Zero-Byte `v2.md` / `v3.md` / `v4.md` Investigation and Disposition — 2026-08-22

**Record type:** Durable provenance record (non-controlling).
**Subject files:** `v2.md`, `v3.md`, `v4.md` at the root of the `drnt-project` working tree (repository `local-first-ai-gateway`). All three are zero bytes.

## Evidence gathered (2026-08-22)

- **Filesystem timestamps:** all three files have identical creation, last-write, and last-access times: **2026-07-03 18:37:51** local — the same second for all three, size 0. Created and never written.
- **Git status:** untracked (`??`) in `drnt-project`; **never committed** — `git log --all` over the three paths returns no history in any branch.
- **Repository references:** no tracked file, script, test, or configuration in `drnt-project` references `v2.md`, `v3.md`, or `v4.md`.
- **Session-tooling trace (the substantive lead):** local records of the assisted session that performed the federal-track freeze intake on July 3 (seeding `drnt-harness-v1.1.zip` and `tier3_design_input_note.md` and verifying them against the hashes later recorded in `drnt-federal-track-frozen` LEDGER Entry 001) show that files named `v2.md`, `v3.md`, and `v4.md` existed as temporary working files in that session's own out-of-repository workspace and were deliberately deleted by it before the session ended. That temporary workspace and the session's own records no longer exist.
- **Timeline correlation (July 3, 2026):** **18:37:51 — the three zero-byte files appear in the repo root**; 18:39:37 — `drnt-federal-track-frozen` seed commit `a49adc5` (the harness freeze, LEDGER Entry 001). The files were created in the middle of the harness-freeze workflow.
- **Recoverable content:** none through ordinary local evidence. The root files never contained bytes (created and last-written in the same second at size 0); the same-named temporary working files were deliberately deleted by the session that owned them, and that workspace no longer exists. No forensic disk recovery was performed, per task constraints.

## Assessment

The names were meaningful *inside* the July 3 harness-freeze session (as temporary working files, most plausibly iteration drafts, which the session itself cleaned up). The three root files are consistent with an errant command creating empty files in the repository root — three names touched in the same second, never written, never referenced, never committed. There is no evidence that the root files themselves ever held content, and therefore no evidence of a failed or truncated write of meaningful material *at these paths*.

## Disposition

**Confirmed zero-byte stray artifacts; no repository references or recoverable content found. Retained pending owner disposition.**

The files remain in place in the `drnt-project` working tree. Deletion is a separate decision for the corpus owner, made outside this provenance task.
