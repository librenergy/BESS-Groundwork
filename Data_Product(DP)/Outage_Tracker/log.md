# Log

## 2026-08-17

* **Renamed from `BESS_GADS`** (`bess-gads.md` → `outage-tracker.md`, skill `bess-gads` → `outage-tracker`). "GADS" over-promised a NERC filing that doesn't exist for standalone BESS; the product is the commercial outage tracker. The taxonomy inside stays GADS-aligned and NERC-export-ready, so nothing downstream had to change beyond the path.

## 2026-08-10

* **Moved under `Data_Product(DP)/`** from the repo root, acronym suffix dropped (`BESS_GADS(GADS)` → `BESS_GADS`), because the recurring output is the event ledger. ⚠️ The document is still **half taxonomy**: the Metrics Tree and Performance Guarantee Matrix depend on the event-type and cause-family definitions, not on the ledger. Treat it as a reference document that produces a deliverable.
