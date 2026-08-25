# Settlement Reconciliation

* [Settlement Reconciliation](settlement-reconciliation.md) - Shadow-settlement procedure verifying counterparty statements against project meter data and awards; the recurring output is the monthly reconciliation.

## Status

* **Template.** Populated by the `settlement-reconciliation` skill; depends on the Data Interface Register's meter and market interfaces.
* On a non-ISO or tolling project the reconciliation is primarily the invoice walk rather than an ISO statement check. Scope it before the session.
* Moved in from the repo root — see [log.md](log.md).
