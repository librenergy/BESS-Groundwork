# Grid Telemetry Mapping

* [Grid Telemetry Map](grid-telemetry-map.md) - The grid telemetry points (BESS ↔ grid operator), from the BESS SCADA's point of view: outputs (telemetry to the grid), inputs (setpoints/commands from the grid), with each point's source, the performance engineer's monitoring tag, and the grid-side address.
* [Log](log.md) - Change history.

## Status

* **Template.** Fill from the SCADA integrator's grid-interface points list; the `historian.GRID_*` monitoring-tag convention is proposed, ratify per project. Grid telemetry only: internal plant tags belong to the DIR §4 shadow set and the dashboards. No dedicated skill; scope a project instance through the `data-product` skill.
