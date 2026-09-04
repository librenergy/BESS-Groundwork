# Metrics Tree — calculation sheets

One standalone sheet per **owner metric** (engineering and monitoring), mirroring the PGM's `calculations/` pattern: the main document carries the compact per-segment registry, each sheet carries the complete calculation conditions. Performance-guarantee metrics (PG_xxx) keep their sheets in `../../Performance_Guarantee_Matrix(PGM)/calculations/`: one definition home per metric.

**Each sheet is self-contained**: its inputs declare the source of truth for every quantity it reads, its target/alert threshold if it carries one, and its reconciliation/plausibility checks. There is no central source-of-truth or reconciliation table.

* [Metric sheet template](metric-sheet-template.md) - Copy per owner metric as `MT_<SEG_TYPE>_<NNN>-<name>.md`; codes are append-only per segment-type series, assigned when the metric is drawn on the tree; the sheet is written when it is built.

## Sheet status

One row per coded owner metric on the tree. **template** = the generic sheet exists (method, sources of truth, checks) with `{{PLACEHOLDERS}}` and ❓ for project fill; **defined** = filled for the project; **implemented** = materialised on the platform. A code with no sheet is registered, not built.

| Code | Metric | Sheet |
|:---|:---|:---|
| [`MT_BESS_001`](MT_BESS_001-overall-battery-effectiveness.md) | Overall Battery Effectiveness (OBE) | template |
| [`MT_BESS_002`](MT_BESS_002-equipment-availability.md) | Equipment Availability (EA) | template |
| [`MT_BESS_003`](MT_BESS_003-operational-availability.md) | Operational Availability (OA) | template |
| [`MT_BESS_004`](MT_BESS_004-power-availability.md) | Power Availability (PA) | template |
| [`MT_BESS_005`](MT_BESS_005-energy-capacity.md) | Energy Capacity (QA) | template |
| [`MT_BESS_006`](MT_BESS_006-degradation-trend.md) | Degradation trend | template |
| [`MT_BESS_007`](MT_BESS_007-string-balance.md) | String balance | template |
| [`MT_BESS_008`](MT_BESS_008-telemetry-health.md) | Telemetry health | template |
| [`MT_POM_001`](MT_POM_001-operating-rte.md) | Operating RTE | template |
| [`MT_POM_002`](MT_POM_002-auxiliary-load.md) | Auxiliary load | template |
| [`MT_POM_003`](MT_POM_003-cycle-runway-offtake.md) | Cycle runway, offtake convention | template |
