# Metrics Tree (MT)

* [Metrics Tree](metrics-tree.md) - The tree (how contract value decomposes through LDs and guarantees, into the OBE engineering measurement layer, down to what gets monitored) plus the per-segment metric registry: PG_xxx contractual rows referencing the PGM, MT_ owner metrics with one calculation sheet each.
* [Calculation sheets](calculations/index.md) - One standalone sheet per owner metric (`MT_<SEG_TYPE>_<NNN>`), self-contained (formula, inputs with sources of truth, boundary, clock, target/alert, reconciliation checks); includes the sheet template.

## Status

* **Template, v0.3 (2026-08-31).** Restructured from the first project clone's practice: the KPI notion removed (everything is a metric; a target is a property a metric carries in its one home), guarantee IDs on the counterparty series scheme (PG_1xx/2xx/3xx), owner metric codes `MT_<SEG_TYPE>_<NNN>`, core metrics organized per segment, and the KPI table / source-of-truth table / reconciliation rules removed in favor of self-contained calculation sheets. Underlying v0.2 tree shape (four limbs over the OBE layer, cloud-Mermaid working-copy convention) unchanged. Populated through the `metrics-tree` skill; depends on the EIM, the [Ontology](../Ontology%28ONT%29/ontology.md) (ratified terms), the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md), and the [Performance Guarantee Matrix](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md).
