# Positive Hash-Verification Record — 2026-08-22

**Record type:** Durable provenance record (non-controlling).
**Date of verification:** 2026-08-22.
**Verification basis:** A project-library artifact-location scan performed earlier on 2026-08-22 reported that every located artifact with a previously registered MANIFEST or LEDGER SHA-256 matched its recorded hash exactly. This record documents an **independent re-verification** of that result, performed the same day in a separate session: every hash below was recomputed directly from the named file or Git blob, not carried forward from the earlier scan.

## Locations examined

- Repository `local-first-ai-orchestration` (local clone, HEAD `2359ba6`) — `publications/MANIFEST.md` as the register of source-DOCX SHA-256 values.
- Repository `drnt-federal-track-frozen` (local clone, HEAD `fc6135a`) — `LEDGER.md` entries 001–004 as the register of frozen federal-track artifact SHA-256 values. Hashes computed from committed Git blobs (`git cat-file blob`), immune to working-tree end-of-line effects.
- The project library, specifically: the corpus authoring workspace (synced 2026-08-22), its parent intake folder, and the library's test-fixtures and harness-and-code folders.

## Artifacts checked against MANIFEST-registered SHA-256

All nine MANIFEST rows that carry a registered SHA-256 were checked against the located project-library copies in the corpus authoring workspace (files 1–9), and additionally against the intake-folder copies for files 1–3.

| # | Artifact (located filename) | Expected (MANIFEST) | Observed | Result |
|---|---|---|---|---|
| 1 | `Volume_1_Define_Reality_Final_Controlled_Manuscript_FINALIZED.docx` | `90870d468c8d311e25f2df253c983af13750edcfedd5c4ff70f45c300bb372a7` | same | **MATCH** (both copies) |
| 2 | `Volume_2_Evaluate_Reality_Public_Edition.docx` | `f53ad6f5cb62624d6b7eccf09d5b580ae2d7d230eb5d19c120ef72e2e6d415c6` | same | **MATCH** (both copies) |
| 3 | `Volume_3_Connect_Reality_Final_Controlled_Manuscript.docx` | `5148d369884cbcf9f9d6dcb5fe7aae3ae96775fe4731f8512574eb2523bb1169` | same | **MATCH** (both copies) |
| 4 | `2nd - AI_Orchestration_Governance_The_Agent_Is_Not_The_System.docx` | `4a5a7a7ac75f04ebc7016bb4947cbc50bdbba6f705d5b1bc8c6769ffb419ee74` | same | **MATCH** |
| 5 | `The_Controller_Is_Part_of_the_System_Under_Test_Public_Edition.docx` | `062627c61ec5661d9696d89b7e22d6d991d8d37e8c6a71340791018dd65823c5` | same | **MATCH** |
| 6 | `The_Model_Is_Not_the_Claim_Discussion_Paper.docx` | `ac90897398c94e773d27088594f62aa27519c7eb0af573c5974dbf0c492367e8` | same | **MATCH** |
| 7 | `From Defect Detection to Governed Enterprise Learning REVISED 1.docx` | `0e3706931ab984c1992b4edd9fe5db548b21944ac12c01cbb4269c89fb72323a` | same | **MATCH** |
| 8 | `Enterprise_DRNT_Signed_Off_Final_July_2026.docx` | `3574864c703ed76791c0f7db1be1423f7e92694471a281767c13e91d6a30443d` | same | **MATCH** |
| 9 | `Federal Scale Orchestration Test Fixture v1 2.docx` | `492fd85b92ab513225d36eab3cc8fce2c2a37631b046686bcb61f8efa2d0016a` | same | **MATCH** |

The MANIFEST's Project Plan v0.2 row records no SHA-256 ("not available from library transcription"); it is therefore not hash-verifiable and is addressed by the separate [source-DOCX authentication record](2026-08-22-project-plan-v0.2-source-docx-authentication.md).

## Artifacts checked against LEDGER-registered SHA-256 (`drnt-federal-track-frozen`)

| # | Artifact | Register | Expected | Observed | Result |
|---|---|---|---|---|---|
| 10 | `artifacts/drnt-harness-v1.1.zip` (HEAD blob) | Entry 001 | `974ed41062f05bfd1969bbd286645b8d200c7fc1565e4fa113faafb9435b2955` | same | **MATCH** |
| 11 | `design-inputs/tier3_design_input_note.md` (HEAD blob) | Entry 001 | `c597d2f82ffe43dee8db3069ce2f587508ef7d9fc83822b16c8a4b4e57aa1326` | same | **MATCH** |
| 12 | `docs/Federal_Scale_Orchestration_Test_Fixture_v1_2_1.docx` (HEAD blob) | Entry 002 | `d342eb8766a7aae6e5f6e9d3ec8ee5fbceaf61b223ac0c4f0e154156da859878` | same | **MATCH** |
| 13 | `docs/Federal_Scale_Orchestration_Test_Fixture_v1_2_1_DERIVED.md` (HEAD blob) | Entry 002 | `797d55b382f31949f62c343cf4cf3f7617729d2b18c4046511981ec75c0ad254` | same | **MATCH** |
| 14 | `docs/OVERVIEW.md` (HEAD blob) | Entry 004 | `834daf03a16ee40ee985d719138aa14267efa01c40a561f4d132b0f70a922fb1` | same | **MATCH** |
| 15 | `README.md` | Entry 004 | `9ac28b71bc436f564be66267b003b51a69a18f935ca5bc41f62b27648952e27c` | see observation below | **MATCH at registered state** |
| 16 | Project-library copy (test-fixtures folder): `Federal Scale Orchestration Test Fixture v1 2 1.docx` | Entry 002 | `d342eb87…` | same | **MATCH** |
| 17 | Project-library copy (harness-and-code folder): `drnt-harness-v1.1.zip` | Entry 001 | `974ed410…` | same | **MATCH** |

**README.md observation (exact, not drift):** the blob at HEAD (`fc6135a`) hashes `f630dca1096f0d8d33b7f8cab239a532284c62af95a707a3b437f5c3bc26c1c1`, which differs from the Entry 004 value. The Entry 004 value binds the README at the entry's own commit (`0aee9c1`), where the blob hashes exactly `9ac28b71…` (**MATCH**). The difference at HEAD is fully explained by the later committed change `d1f2f36` ("Clarify federal testbed lifecycle"), which post-dates Entry 004 in ordinary Git history. Entry 004 itself identifies the README hash as a "companion change, not an independent artifact." This is normal committed evolution of a pointer file, not drift of a frozen artifact; no ledger entry records the README supersession, which the corpus owner may add if desired.

**Not re-verified here:** Entry 001's "Verified harness log head" (`f72fbbb6…`) is a run-output value, not a stored-file hash; it was reproduced by the 2026-08-22 harness re-execution documented in the [preserved Current-Truth Review](2026-08-22-current-truth-review-preserved.md) (§C: 21/21 unit tests, 8/8 scenarios, log head byte-identical to Entry 001), and is not independently re-derived in this record.

## Result

**Zero drift.** Every artifact located and checked for which a MANIFEST or LEDGER SHA-256 already existed matched its registered SHA-256 exactly, at every location checked. This is a positive assurance result: the visual-publication authorities behind the August 2026 corpus and the frozen federal-track artifacts are intact and byte-identical to their registered identities as of 2026-08-22.

This statement covers exactly the seventeen checks enumerated above and is not generalized beyond them.
