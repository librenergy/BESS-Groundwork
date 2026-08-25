---
type: Specification
title: Dashboards
description: "Monitoring and diagnostic dashboards for the asset: design rules, the four-tier dashboard suite (fleet, site, device, analytics) with worked screenshots, and a dashboard register."
---

# Dashboards

**Version:** 0.2 · **Status:** Template

Dashboards **visualize** metrics; they never define them. Every number on a dashboard traces to an authoritative definition in the [Metrics Tree & KPIs](../../Metrics_Tree%28MT%29/index.md) and a source in the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md).

The screenshots below come from a live Grafana deployment of this suite (fictionalized dataset). They are the working reference for what each dashboard contains.

## 1. Design rules

1. **Diagnostic, not contractual.** Dashboards are instruments for finding problems. The numbers of record live in the [Daily Performance Report](../Daily_Performance_Report/index.md), the [Outage Tracker](../Outage_Tracker/index.md) ledger, and the settlement calculations. When a dashboard and a report disagree, the report wins.
2. **Metric parity.** A panel showing "availability" shows one of the defined availability metrics, labeled with which one. No dashboard-only definitions.
3. **Drill-down follows the plant hierarchy.** Fleet → site → PCS → DC bus → battery enclosure → string → module/cell, with consistent IDs at every level (`PCS.1A1` / `Bus.1` / `Bat.1A1.4` / `Str.5`).
4. **Every chart is linkable.** The observation log in [Performance Engineering Management](../Performance_Engineering_Management/performance-engineering-management.md) carries a chart link per observation; a panel that cannot be linked with its time range cannot serve as evidence.
5. **Audience tiers.** Operations sees everything at device grain; management sees trends and exceptions; counterparties see only their shared scope.

## 2. Platform

**Example shown in Grafana (Recommended).** Time-series native, deep-linkable panels, template variables for the hierarchy drill, alerting, folder-level sharing, dashboards as code. Dashboards live in folders by audience (Technical, Commercial, ML) with a standard tag bar on every dashboard linking the folders, and a standard variable bar (site, PCS, bus, battery, string, data source) so every dashboard drills the same way.

## 3. The BESS dashboard suite

### Tier 1: Fleet

**Fleet Overview.** Is every site healthy right now? One row per site: capacity, PCS running/faulted, open CMMS work orders, availability (PCS, string, capability, combined), 24-hour dispatch and SoC sparklines, and **data age**, with a map. The data-age column is the telemetry watchdog: a site quietly going stale shows up here first.

![fleet-overview](dashboards.assets/fleet-overview.png)

### Tier 2: Site/BESS

**Operational Overview.** What did the plant do in the last 24 hours? Site KPI header, BESS power and SoC against setpoint and available-power envelope, key external signals (offtaker-visible energy and SoC beside our own), per-PCS powers, daily energy imported/exported, AC vs DC energy with efficiency, PCS SoCs, and max string module temperature per battery.
![site-operational-power-soc](dashboards.assets/site-operational-power-soc.png)

![site-operational-overview](dashboards.assets/site-operational-overview.png)


**Availability.** What is availability under each definition, and what is dragging it? Header scorecards for PCS inverter, battery, equipment combined, power full-range, and power depth-weighted availability; time-by-device heatmaps (PCS availability, string availability, charge and discharge power availability); and **installed vs contracted** trend panels side by side. The heatmaps are the workhorse: a red column is an event, a red row is a chronic offender.

![availability-overview](dashboards.assets/availability-overview.png)

![availability-trends](dashboards.assets/availability-trends.png)

**Power Distribution & Dispatch Tracking.** Did the plant follow the setpoint, and did every PCS pull its weight? Plant setpoint vs actual with error band, all-PCS setpoints and active power, setpoints as % of nominal, SOC vs setpoint analysis, setpoint spread, and a per-PCS tracking-performance table with tracking error. This is where dispatch non-compliance gets characterised before it becomes a deduction.

![power-distribution-plant](dashboards.assets/power-distribution-plant.png)

![power-distribution-tracking](dashboards.assets/power-distribution-tracking.png)

**Balancing.** Is energy where it should be across the plant? Site KPI header (contracted MW/MWh, last capacity test, tested RTE), PCS and bus SoC treemaps, SoC delta per DC bus, bus-to-bus delta per PCS, string SoC distribution, and a disconnected-strings table. Imbalance is stranded energy; this dashboard prices the balancing backlog.

![balancing-soc-heatmaps](dashboards.assets/balancing-soc-heatmaps.png)

![balancing-string-detail](dashboards.assets/balancing-string-detail.png)

**Telemetry & Energy Validation.** Do the offtaker's numbers match ours? Offtaker-visible SoC and available energy against BMS SoC, SoC error in percentage points, and discharge-energy error. The reconciliation instrument for the counterparty data exchange.

![telemetry-energy-validation](dashboards.assets/telemetry-energy-validation.png)

**Power vs Market Price.** Did dispatch make sense against the market? BESS power and SoC against day-ahead and real-time LMP at the node, daily top-bottom spread capture, and ISO system load context. Diagnostic context for the commercial-vs-technical reconciliation, not a settlement calculation.

![power-vs-market-price](dashboards.assets/power-vs-market-price.png)

### Tier 3: Device

**PCS & Battery Balancing** (per-PCS drill down). Everything about one PCS and its batteries: status, SoC, available charge/discharge, DC bus voltages and power split, bus setpoint deltas, connected strings vs expected with per-bus availability, string SoC/voltage/current/daily energy, then cell voltage (average, extremes, max-min delta) and cell temperature. Prev/Next paging walks the fleet one PCS at a time.

![pcs-battery-balancing-overview](dashboards.assets/pcs-battery-balancing-overview.png)

![pcs-battery-balancing-strings](dashboards.assets/pcs-battery-balancing-strings.png)

![pcs-battery-balancing-cells](dashboards.assets/pcs-battery-balancing-cells.png)

**PCS Fault & Status Explorer.** What is faulting, and is it getting worse? Status distribution (state-machine codes), top fault and warning codes, faults by PCS (Pareto), PCS status timelines, and fleet status distribution over time. Feeds the observation log's fault families.

![pcs-fault-status-explorer](dashboards.assets/pcs-fault-status-explorer.png)

**PCS Health Explorer.** Is the inverter itself healthy? Reactive power vs setpoint, inverter status timeline, module IGBT temperatures (max and per phase), ambient temperature and capability.

![pcs-health-explorer](dashboards.assets/pcs-health-explorer.png)

**PCS Transformer Health Explorer.** Are the MV transformers running hot? Per-winding temperatures, average per PCS, **rise above admission temperature (ΔT)** per PCS, and AC module admission and internal temperatures. ΔT is the early-warning number; absolute temperature follows the weather.

![pcs-transformer-health-explorer](dashboards.assets/pcs-transformer-health-explorer.png)

**Battery Alarm & Events Explorer.** What are the batteries complaining about? Strings and batteries connected vs total, battery status table (strings closed, SOC, SOH, max temp), top alarms (Pareto), alarms by battery and by PCS, severity distribution, alarms over time, and cell voltage/current/SoC time series for the selected scope.

![battery-alarm-events-timeseries](dashboards.assets/battery-alarm-events-timeseries.png)

### Tier 4: Analytics (digital twin of battery SoC and SoH) [Advanced]

Model-derived views. These read the twin's estimates, not raw telemetry, and say so on the dashboard.

**Daily Site Capacity.** How much energy can the site actually deliver, and where did the rest go? Usable energy at POI vs contracted, DC→POI discharge efficiency, and a **capacity waterfall** (design → degradation → offline → weak string → imbalance → usable) with a top-issue-buses table splitting each bus's shortfall into its causes.

![ml-daily-site-capacity](dashboards.assets/ml-daily-site-capacity.png)

**SoC Twin per Bus.** What does the model think SoC and SoH really are? Twin vs BMS SoC with the usable window, per-bus residuals, bus SoH distribution, and a per-bus capacity and SoH table (worst first). The twin-vs-BMS residual is the calibration alarm.

![ml-soc-twin-bus](dashboards.assets/ml-soc-twin-bus.png)

**Power Distribution per Node.** What should the controller ask of each node? SoH-proportional recommended power per controllable node (DC bus), node capacity deliverables with SoC gains, and imbalance headroom. The deliverable feeding the plant controller's proportional dispatch.

![ml-power-distribution-per-node](dashboards.assets/ml-power-distribution-per-node.png)

## 4. Patterns

Conventions visible across the suite, worth keeping on any new dashboard:

- **KPI header row** grounding every technical view in contract context: contracted vs installed MW/MWh, PCS and battery counts, COD, last capacity test date.
- **Time-by-device heatmaps** for anything with per-device availability or state.
- **Same metric, two sources, one panel** wherever two systems report the same quantity (offtaker-visible SoC vs BMS SoC, twin vs BMS). Divergence is the diagnostic.
- **Contracted vs installed side by side**, never merged.
- **Pareto first** for alarms and faults: top offenders, then by device, then over time.
- **Standard variable bar and folder tags** on every dashboard, so the drill path and the escape route are always the same.

## 5. Dashboard register

One row per deployed dashboard; fill the links and metric/DIR IDs as dashboards go live.

| Dashboard | Tier | Folder | Metrics (MT IDs) | Sources (DIR IDs) | Link | Status |
|:---|:---|:---|:---|:---|:---|:---|
| Fleet Overview | Fleet | Technical |                  |                   |      |        |
| Operational Overview | Site | Technical |                  |                   |      |        |
| Availability | Site | Technical |                  |                   |      |        |
| Power Distribution & Dispatch Tracking | Site | Technical |                  |                   |      |        |
| Balancing | Site | Technical |                  |                   |      |        |
| Telemetry & Energy Validation | Site | Technical |                  |                   |      |        |
| Power vs Market Price | Site | Technical |                  |                   |      |        |
| PCS & Battery Balancing | Device | Technical |                  |                   |      |        |
| PCS Fault & Status Explorer | Device | Technical |                  |                   |      |        |
| PCS Health Explorer | Device | Technical |                  |                   |      |        |
| PCS Transformer Health Explorer | Device | Technical |                  |                   |      |        |
| Battery Alarm & Events Explorer | Device | Technical |                  |                   |      |        |
| Daily Site Capacity | Analytics | ML |                  |                   |      |        |
| SoC Twin per Bus | Analytics | ML |                  |                   |      |        |
| Power Distribution per Node | Analytics | ML |                  |                   |      |        |

Open items: tracked in this folder's `todo.md`.
