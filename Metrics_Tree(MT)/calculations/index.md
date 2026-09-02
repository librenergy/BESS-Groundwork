# Metrics Tree — calculation sheets

One standalone sheet per **owner metric** (engineering and monitoring), mirroring the PGM's `calculations/` pattern: the main document carries the compact per-segment registry, each sheet carries the complete calculation conditions. Performance-guarantee metrics (PG-xxx) keep their sheets in `../../Performance_Guarantee_Matrix(PGM)/calculations/` — one definition home per metric.

**Each sheet is self-contained**: its inputs declare the source of truth for every quantity it reads, its target/alert threshold if it carries one, and its reconciliation/plausibility checks. There is no central source-of-truth or reconciliation table.

* [Metric sheet template](metric-sheet-template.md) - Copy per owner metric as `MT_<SEG_TYPE>_<NNN>-<name>.md`; codes are append-only per segment-type series and assigned only when the metric is built.
