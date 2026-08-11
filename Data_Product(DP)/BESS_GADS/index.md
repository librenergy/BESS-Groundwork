# BESS GADS

* [BESS GADS](bess-gads.md) - GADS-aligned outage/derate event taxonomy — one authoritative event record feeding every contractual availability calculation, NERC-export-ready.

## Status

* **Template.** Populated by the `bess-gads` skill; depends on the Performance Guarantee Matrix and the Metrics Tree & KPIs availability formulas.
* **2026-08-10 — moved under `Data_Product(DP)/`** from the repo root, acronym suffix dropped (`BESS_GADS(GADS)` → `BESS_GADS`), because the recurring output is the event ledger. ⚠️ The document is still **half taxonomy**: the Metrics Tree and Performance Guarantee Matrix depend on the event-type and cause-family definitions, not on the ledger — treat it as a reference document that produces a deliverable.
