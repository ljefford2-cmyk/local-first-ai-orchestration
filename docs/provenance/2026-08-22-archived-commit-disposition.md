# Archived-Commit Disposition Record — 2026-08-22

**Status:** Non-controlling provenance record.
**Nature of contents:** Disposition of the four commits preserved under local archive refs. Categories and dispositions only; the specific content that produced each blocked classification is deliberately not recorded here.

| Commit | Disposition |
|---|---|
| `3a8d673` | Disclosure-clean; published via annotated archival tag as the B1.3 extraction-completion snapshot |
| `2ee46ed` | Retained privately; disclosure-blocked pre-redaction Track A state; must not be published as-is |
| `1edda54` | Retained privately; disclosure-blocked intermediate Track A redaction state; must not be published as-is |
| `ea13874` | Retained privately; disclosure-clean; not tagged — the work entered public history in evolved form via `82b6670` and `47e1b9f` |

`2ee46ed` and `1edda54` together constitute the redaction history of the published Track A closure `d239ef8`. That is why they are retained — they document how the published closure was produced — and why they cannot be published: publishing the redaction history would disclose what the redaction removed.

Publication of `3a8d673` was completed after this disposition record was initially committed; remote retrievability was confirmed through the annotated tag and recorded in the B1.4 manifest at `33ceb43`.
