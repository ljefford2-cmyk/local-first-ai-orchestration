# Project Plan v0.2 Source-DOCX Authentication Record — 2026-08-22

**Record type:** Durable provenance record (non-controlling).
**Background:** The repository carries a committed transcription of Project Plan v0.2 — Frozen Architectural Baseline ([`publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md`](../../publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md)), but the MANIFEST records no SHA-256 for the original source DOCX ("not available from library transcription"). On 2026-08-22 the source DOCX was located in the project library's corpus authoring workspace and authenticated by content comparison.

**Recovered artifact:** `Project Plan v0.2 — Frozen Architectural Baseline.docx`, 54,785 bytes, SHA-256 `c5df913a6032b77c64478f2b2e5ffae1b22b2b462f73a621a6ae8fec7ffd6e47` (computed 2026-08-22). Title block: "Project Plan v0.2 — Frozen Architectural Baseline / A Three-Volume Architecture for Governed AI-Mediated Organizations"; status page: "FROZEN ARCHITECTURAL BASELINE."

**Committed transcription:** `publications/controlled-architecture/Project_Plan_v0.2_Frozen_Architectural_Baseline.md` at repository HEAD `2359ba6` (file introduced at commit `47543cc`, 2026-08-12).

**Comparison method:** Full text extracted from the DOCX (`word/document.xml`, paragraph-aware). Transcription normalized by removing Markdown serialization (heading marks, emphasis marks, list markers, horizontal rules) and the repository-added "Controlled-text note" blockquote, which discloses itself as a transcription notice and is not source text. Both texts normalized for typographic-quote/dash variants and whitespace, then compared word-by-word over the complete documents (3,838 DOCX words vs 3,818 transcription words) with a sequence matcher; every non-equal region was individually inspected in both raw sources.

**Result:** Transcription-format differences only.

**Differences:**

1. **Title-block layout:** the DOCX places "August 2026" as a byline line following "Lawrence Jeffords — End-User and Operator," with "Status / FROZEN ARCHITECTURAL BASELINE" as separate following lines; the transcription renders this as "**Status:** FROZEN ARCHITECTURAL BASELINE — August 2026." Same words, same status, same date; placement and punctuation are transcription formatting.
2. **Arrow-chain whitespace:** in the change-cycle and learning-loop sequences the DOCX spaces its arrows ("Accumulated Evidence → System Evaluation → …"); the transcription serializes some without a preceding space ("Accumulated Evidence→ System Evaluation→ …"). Whitespace-only.
3. **Closing-formula line-join:** the four-clause closing ("Define reality. Evaluate reality. Connect reality. Govern the learning.") appears in the transcription with line breaks rendered without inter-clause spaces ("Define reality.Evaluate reality.…"). Line-ending/serialization only.
4. Ordinary line-break and paragraph-segmentation differences inherent to DOCX→Markdown transcription. Notably, the stray literal asterisks around "Detection latency / Intervention latency" exist **identically in both** the DOCX and the transcription — faithful transcription, not a difference.

No substantive textual differences were found: no wording, sentence, requirement, definition, section, or ordering differs between the source DOCX and the committed transcription beyond the formatting items above.

**Authentication conclusion:** The recovered DOCX is authenticated as the source of the committed Project Plan v0.2 transcription on the basis of exact textual correspondence (transcription-format differences only) across the complete document. Its SHA-256 (`c5df913a…`, above) is registered as the identity of the recovered source artifact as of 2026-08-22.

**Limitation:** No contemporaneous source hash was available, so byte-level historical identity cannot be independently established.
