# Settlement Reconciliation

* [Settlement Reconciliation](settlement-reconciliation.md) - Shadow-settlement procedure verifying counterparty statements against project meter data and awards; the recurring output is the monthly reconciliation.

## Status

* **Template.** Populated by the `settlement-reconciliation` skill; depends on the Data Interface Register's meter and market interfaces.
* **2026-08-10 — moved under `Data_Product(DP)/`** from the repo root, acronym suffix dropped (`Settlement_Reconciliation(SR)` → `Settlement_Reconciliation`). On a non-ISO or tolling project the reconciliation is primarily the invoice walk rather than an ISO statement check — scope it before the session.
