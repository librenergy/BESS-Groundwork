---
type: Template
title: Canonical Data Models
description: "Ontology layer 3: per segment type, the standard shape of its time-series record — which measurement terms it carries, at which statistics, under which column names. The spec a data platform generates its canonical tables from."
---

# Canonical Data Models

**Ontology layer 3** · part of the [Groundwork Ontology](ontology.md)

A **canonical model** is the standard record shape for one segment type's time-series, independent of which vendor produced the data. Every source system's tags are remapped into it at ingestion, so that one query works across all sites and all vendors: the whole point of the exercise. One model per segment type; a platform materializes each model as one table (see the [platform projection](platform-projection.md)).

## Storage layers: narrow historian below, wide canonical above

The canonical model does not replace raw storage; it sits on top of it. The two layers have distinct jobs:

| Layer | Shape | Job |
|:---|:---|:---|
| **Historian** | Narrow: one row per reading (`segment, signal, timestamp, value`) | System of record. Lossless capture at native cadence, schema-stable (a new tag is new rows, never DDL), holds the long tail of signals that never earn a canonical column, and serves raw-fidelity forensics. |
| **Canonical** | Wide: one row per segment per interval, one column per model row | Analysis contract. The **ratified subset** of the historian, pivoted wide and **regularized to the ratified grain**, so cross-measurement expressions (the substance of every availability calculation) are one WHERE clause instead of N self-joins, and so the schema itself can carry and enforce the ontology. |

**Promotion is additive and reversible.** A signal not in the canonical model still exists in the historian, so nothing is lost by leaving it unpromoted; ratifying it later adds one nullable column. This is also the industry-standard split: platforms that virtualize the wide view at query time (Palantir Foundry stores series narrow and assembles alignment lazily) can do so because they own the query engine; a SQL-consumed platform (Grafana, notebooks, agents) materializes the wide layer instead, at a small fraction of the historian's row count.

## Building the wide table: alignment rules

Measurements arrive at different native timestamps and cadences; the pivot is therefore also a **regularization**, and its rules are part of the model, not an implementation detail:

1. **Bucket to the grain.** Every reading maps to the interval containing it; `agg_period` names the grain. The interval convention (beginning vs ending) is ratified once in the [platform projection](platform-projection.md).
2. **One bucket statistic per column.** Where several readings land in one bucket, the model's Statistic/Notes fix the reduction: `avg`/`min`/`max` for analog terms, **last** for states and cumulative counters. A population statistic (max cell in the string) and a bucket statistic (over the minute) compose; the Notes say in which order.
3. **States may carry forward; analogs never do.** Event-driven state signals (`connected`, mode, fault codes) hold their last value across empty buckets, up to a ratified staleness cap, after which they go null. Analog measurements are **never** interpolated or carried forward in the canonical table: an empty bucket is `NULL`, and null means "no sample", not zero and not "same as before". Interpolation is a per-analysis choice made downstream, never baked into storage.
4. **Late data is expected.** Builds are idempotent upserts over a lookback window (versioned/replacing semantics), so a reading that arrives an hour late lands in its true interval on the next run. The lookback and build cadence are platform-projection decisions.
5. **Never fabricate a row.** A segment-interval with no readings at all is absent, not a row of nulls.

**Rules**

- Every measurement column resolves to a term in the [measurement vocabulary](measurements.md) plus an optional statistic. Column name = `term` or `term_statistic` (`cell_voltage` × `max` → `cell_voltage_max`). No column may exist without a vocabulary term behind it.
- **Statistic** describes aggregation *across the segment's population and/or the grain window* (the max cell in the string over the minute). Its exact meaning per column is fixed in Notes.
- **Req?** — `Y` means the model is not considered implemented for a site until this column is populated; `O` (optional) columns are nullable where a source has no such tag, and stay null rather than being imputed.
- All models share the **standard envelope** below; models add only their measurement columns.
- Extending a model is additive (new optional columns). Renaming or re-typing a column is a breaking change.

## Standard envelope (every model)

| Column | Dtype | Definition |
|:---|:---|:---|
| `src_id` | string | Source/site identifier (the platform's site key). |
| `seg_id` | uuid | Stable segment identifier from the segment dimension. |
| `seg_code` | string | Human-readable, site-unique segment code (e.g. `Bat.1_Str.2`). |
| `seg_path` | string | Hierarchy/UNS topic path of the segment. |
| `timestamp_utc` | timestamp | Interval timestamp, UTC. Interval-beginning unless the project ratifies otherwise; state it once, in the platform projection. |
| `agg_period` | string | Grain of the row (`1m`, `5m`, `1h`). |
| `metadata` | json/map | Source-specific extras that earned no canonical column (yet). |
| `loaded_utc` | timestamp | Load time (dedup/versioning key). |
| `record_src` | string | Producing pipeline/process. |

## `Bat_Str` — Battery String  ✅ worked example

The reference model, populated first because string-level telemetry is where availability, imbalance, and thermal analysis all live.

| Column | Term | Statistic | Unit | Req? | Notes |
|:---|:---|:---|:---|:---|:---|
| `voltage` | `voltage` | — | V | Y | String DC terminal voltage. |
| `current` | `current` | — | A | Y | String DC current, discharge-positive. |
| `cell_voltage_avg` | `cell_voltage` | avg | V | Y | Mean over the string's cells, over the grain window. |
| `cell_voltage_min` | `cell_voltage` | min | V | Y | Weakest cell; drives imbalance and derate analysis. |
| `cell_voltage_max` | `cell_voltage` | max | V | Y | Strongest cell; overvoltage margin. |
| `cell_temperature_avg` | `cell_temperature` | avg | degC | Y | Mean cell/probe temperature. |
| `cell_temperature_min` | `cell_temperature` | min | degC | Y | — |
| `cell_temperature_max` | `cell_temperature` | max | degC | Y | Hottest probe; warranty envelope input. |
| `soc_percent` | `soc_percent` | — | % | Y | BMS-reported, on the ratified basis. |
| `soc_kwh` | `soc_kwh` | — | kWh | O | Null where the site has no energy-basis tag. |
| `connected` | `connected` | — | 1 | Y | DC contactor closed this interval: 1/0/null. Feeds Equipment Availability. |

## `PCS` — Power Conversion System  ❓ to ratify

Candidate columns; ratify in-session against the plant's PCS tag lists.

| Column | Term | Statistic | Unit | Req? | Notes |
|:---|:---|:---|:---|:---|:---|
| `active_power` | `active_power` | — | kW | Y | AC terminals. |
| `reactive_power` | `reactive_power` | — | kvar | Y | — |
| `power_setpoint` | `power_setpoint` | — | kW | Y | Received command; pairs with `active_power` for OA. |
| `available_charge_power` | `available_charge_power` | — | kW | Y | ACP as reported. |
| `available_discharge_power` | `available_discharge_power` | — | kW | Y | ADP as reported. |
| `frequency` | `frequency` | — | Hz | O | — |
| `operating_mode` | `operating_mode` | — | — | Y | Codes mapped at binding. |
| `fault_code` | `fault_code` | — | — | Y | — |
| `connected` | `connected` | — | 1 | Y | AC breaker state. |

## `Bat` — Battery Enclosure  ❓ to ratify

Roll-up of its strings plus enclosure-level signals (HVAC, ambient, aggregate SoC). Define after `Bat_Str`; prefer computing roll-ups from the string model over ingesting a second vendor aggregate, and where both exist keep them as separate bindings, never merged.

## `Meter` — Settlement / Performance Meter  ❓ to ratify

| Column | Term | Statistic | Unit | Req? | Notes |
|:---|:---|:---|:---|:---|:---|
| `active_power` | `active_power` | — | kW | Y | Export-positive at the boundary. |
| `energy_exported` | `energy_exported` | — | kWh | Y | Register semantics ratified per meter. |
| `energy_imported` | `energy_imported` | — | kWh | Y | — |
| `reactive_power` | `reactive_power` | — | kvar | O | — |
| `frequency` | `frequency` | — | Hz | O | — |

## `BESS` — Whole system  ❓ to ratify

Site/system head-end model: POM power, aggregate availability inputs, aggregate SoC. Most columns are computed roll-ups; ratify which are ingested vs derived, and keep derived ones out of the canonical table (they are metrics).

## `TX` — Transformer  ❓ to ratify

Temperatures (winding, oil), tap position, load. Define on first project with transformer telemetry.

## `Weather_Station`  ❓ to ratify

`ambient_temperature`, `humidity`, `wind_speed`, `wind_direction`, `irradiance`, `precipitation` per the vocabulary; single flat model.

---

**Adding a model**: copy the worked-example structure, fill from the vocabulary (adding missing terms to `measurements.md` first, never inline), mark every row `Y`/`O` deliberately, and log the ratification in this folder's `log.md`.
