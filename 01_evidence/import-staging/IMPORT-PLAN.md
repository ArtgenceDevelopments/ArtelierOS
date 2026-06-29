# Evidence Import Staging Plan (PREPARED — NOT YET RUN)

> **Status: staged, awaiting charter ratification.** Per the agreed sequence, the charter drafts in `00_charter/` are reviewed and ratified **first**; evidence is then pulled into the Artelier OS alongside them. This folder prepares that import but executes nothing.

**Target workspace:** `artelieros` (the Artelier Brandhorse OS instance — currently empty)
**Action:** `create_evidence` (one call per item)
**Items:** 40 (E-001–E-040; pass 1 + pass 2), all sourced, all validated
**Status on create:** `draft` (the OS creates evidence in draft pending human review — a second review gate after import)

## Validation result

All 40 rows in `master_evidence.csv` map to the canonical `create_evidence` schema with **0 validation errors**. Every enum value is within the allowed set.

| Field | Distribution |
|---|---|
| evidence_type | quote ×18, competitive_proof ×12, market_signal ×8, behavioral_proxy ×2 |
| evidence_format | quote ×20, document ×13, metric ×7 |
| journey_phase | aware ×13, evaluate ×10, use ×6, consider ×6, adopt ×3, churn_risk ×2 |
| visibility_status | internal_only ×21, creatable ×19 |
| confidence | high ×17, medium ×18, low ×5 |

## Field mapping (CSV → create_evidence)

| create_evidence field | Source CSV column | Notes |
|---|---|---|
| `name` | `title` | |
| `content` | `quote` (falls back from `content`) | verbatim fragment; CSV `content` is blank, so the verbatim `quote` is used |
| `quote` | `quote` | explicit verbatim |
| `description` | `reveals` | the analysis / "why it matters" |
| `source` | `description` | short source descriptor (e.g. "Artwork Archive review; 20+ years catalogued") |
| `attachment_url` | `source` | the live URL |
| `evidence_type` | `evidence_type` | validated against enum |
| `evidence_format` | `evidence_format` | validated against enum |
| `theme_buckets` | `theme_buckets` | JSON array → comma-joined string |
| `journey_phase` | `journey_phase` | validated against enum |
| `visibility_status` | `visibility_status` | validated against enum |
| `confidence` | `confidence` | validated against enum |
| `status` | — | hard-set to `draft` |

The fully mapped, ready-to-run payload is in **`import_manifest.json`** (40 objects; pass 1 + pass 2 merged).

## Enrichment available (optional, before run)

- **`source_date`** is not in the CSV but **is** in each folder's `evidence_log.md` (per-entry "Source Date"). Add it during the run for cleaner provenance if desired.
- **`internal-proof` is intentionally empty** — 0 entries by design (no loggable first-party artifacts yet). The import is competitor/market/customer-voice only. Closing that gap (Facebook export, recorded calls, instrumented trial) is a separate next-pass action, not part of this import.

## To run (after ratification)

1. Confirm the `artelieros` workspace is the correct destination.
2. For each of the 40 objects in `import_manifest.json`, call `create_evidence` with those fields.
3. All land as `draft` → review inside the OS → approve.
4. Spot-check 3–5 items against the live source URLs before approving in bulk.

_Nothing in this folder has been written to the OS. This is preparation only._
