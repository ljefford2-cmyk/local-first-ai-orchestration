# Track B — Method Record — Prompt Representation Provenance

**Status:** Non-controlling Track B method record. Not part of the controlling architecture and does not supersede Volumes 1–3.
**Date:** 2026-08-22
**Nature of contents:** Representation provenance for the two canonical prompt records committed in this directory: the B1.1 prompt containing ER-1.0 (`B1.1-prompt-canonical-lf.md`) and the executed B1.3/B1.4 task prompt (`B1.3-B1.4-task-prompt-canonical-lf.md`). This record fixes the identity of each canonical LF file, the identity of the recovered CRLF representation it was normalized from, and the normalization operation relating them.

**Counting convention (both prompts):** character counts are Unicode code points obtained by decoding the file's bytes as UTF-8. Each `LF` newline counts as one character; each `CRLF` counts as two.

---

## B1.1 prompt containing ER-1.0

| Property | Value |
|---|---|
| Canonical LF file (this directory) | `B1.1-prompt-canonical-lf.md` |
| Canonical LF SHA-256 | `fcbc53be3792d281fc33eed1f2e172d795556e203eba6219ab3db93d2e078eb7` |
| LF character count | 7,961 |
| Recovered CRLF file (off-repository) | `b11_prompt.md` |
| Recovered CRLF SHA-256 | `8c5c8bf74e4cbb2b6c328a87607fe3ffde255703a9572257359850ace3326e89` |
| CRLF character count | 8,040 |
| CRLF pair count | 79 |
| Lone `CR` bytes (a `CR` not followed by `LF`) | 0 |

**Normalization operation:** byte-level replacement of every `CRLF` (`0x0D 0x0A`) sequence with `LF` (`0x0A`), applied to the untouched recovered CRLF file's bytes read in binary mode. No other transformation: no whitespace stripping, no re-encoding, no heading or content changes.

**Verification:** normalization reproduces the recorded canonical LF hash — confirmed. The SHA-256 of the normalized result equals `fcbc53be3792d281fc33eed1f2e172d795556e203eba6219ab3db93d2e078eb7` and its character count equals 7,961.

## Executed B1.3/B1.4 task prompt

| Property | Value |
|---|---|
| Canonical LF file (this directory) | `B1.3-B1.4-task-prompt-canonical-lf.md` |
| Canonical LF SHA-256 | `b96af7a7de3ecf43ee27e533a0424e5fdad05b91d577cbaeaaf91ff9dbc4f4c6` |
| LF character count | 10,530 |
| Recovered CRLF file (off-repository) | `executed_task_prompt.md` |
| Recovered CRLF SHA-256 | `9dbdca39146ea5a10c8d668ba467334f9f6f95213fcf4b4fc3de4c3e83f778e4` |
| CRLF character count | 10,747 |
| CRLF pair count | 217 |
| Lone `CR` bytes (a `CR` not followed by `LF`) | 0 |

**Normalization operation:** byte-level replacement of every `CRLF` (`0x0D 0x0A`) sequence with `LF` (`0x0A`), applied to the untouched recovered CRLF file's bytes read in binary mode. No other transformation: no whitespace stripping, no re-encoding, no heading or content changes.

**Verification:** normalization reproduces the recorded canonical LF hash — confirmed. The SHA-256 of the normalized result equals `b96af7a7de3ecf43ee27e533a0424e5fdad05b91d577cbaeaaf91ff9dbc4f4c6` and its character count equals 10,530.

---

## Serialization caveat — both prompts

The precise newline serialization presented at the model boundary is not independently established. The recovered copies are CRLF-serialized; each canonical LF form is a deterministic normalization of its recovered copy, not an independently witnessed serialization of what the executing model received.

## Durable-storage status of the untouched CRLF representations

The untouched recovered CRLF representations (`b11_prompt.md`, `executed_task_prompt.md`) are preserved, unmodified, in durable off-repository storage. Their identities are fixed by the SHA-256 values recorded above.
