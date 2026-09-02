---
type: Template
title: Measurement Vocabulary
description: "Ontology layer 2: the controlled list of measured quantities, one canonical name, one unit, one datatype, and one definition per term, with the confusable near-misses each term explicitly is not."
---

# Measurement Vocabulary

**Ontology layer 2** · part of the [Groundwork Ontology](ontology.md)

The controlled vocabulary of quantities the plant produces. Every signal in the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md), every column in a [canonical model](canonical-models.md), and every input in a [Metrics Tree](../Metrics_Tree%28MT%29/metrics-tree.md) formula names one of these terms. Source-system tag names never travel past ingestion; they are mapped onto these terms at the binding layer.

**Rules**

1. **One unit per term.** The same quantity in a different unit or basis is a *different term*: `soc_percent` and `soc_kwh` are two terms, not one term with two units. Ratifying a term means picking its unit forever; converters live in ingestion, not in the vocabulary.
2. **Statistics are not terms.** `avg` / `min` / `max` / `sum` over a population or window belong to the canonical model (`cell_voltage` × `max` → column `cell_voltage_max`). The vocabulary carries only `cell_voltage`.
3. **Grains are not terms.** "Daily", "Hourly", and friends are aggregation periods, never measurement types. (A common registry pollution; keep it out.)
4. **Kind is one of** `measurement` (observed time-series), `state` (enumerated/coded status), `parameter` (static rating; lives in dimension data, listed here so the name is controlled). Metrics are not in this file; they live in the Metrics Tree and are registered by `metric_code`.
5. **Unit symbols**, QUDT-aligned, case-exact: `V`, `A`, `kW`, `kVA`, `kvar`, `kWh`, `Hz`, `degC`, `%`, `ohm`, `s`, `m/s`, `deg`, `mm`, `W/m2`, `count`, and `1` for dimensionless ratios. `MW`/`MWh` are permitted only for grid- and market-boundary terms; plant-chain terms use `kW`/`kWh`. Never both for one term.
6. **Sign conventions are part of the definition.** State them in the definition, never in the name: discharge-positive at the DC chain, export-positive at the grid boundary, unless the project ratifies otherwise.
7. **The "Is not" column is load-bearing.** It names the near-miss quantity a term is routinely confused with; keep it sharp.

## Electrical (DC chain)

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `voltage` | measurement | V | float | DC terminal voltage of the segment. | Not cell voltage; string voltage is measured at string terminals. |
| `current` | measurement | A | float | DC current of the segment; discharge-positive. | Sign convention must be ratified per source. |
| `cell_voltage` | measurement | V | float | Voltage of an individual cell, or a cell-population statistic per rule 2. | Not module or string voltage. |
| `cell_temperature` | measurement | degC | float | Cell (or cell-probe) temperature, or a population statistic. | Not enclosure ambient (`ambient_temperature`). |
| `resistance` | measurement | ohm | float | Measured or estimated internal resistance. | — |

## Electrical (AC / grid boundary)

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `active_power` | measurement | kW | float | AC active power at the segment terminals. Sign per ratified boundary convention. | Not `power_setpoint` (the command), not meter-integrated energy. |
| `reactive_power` | measurement | kvar | float | AC reactive power at the segment terminals. | — |
| `apparent_power` | measurement | kVA | float | AC apparent power. | — |
| `power_factor` | measurement | 1 | float | Power factor, signed per convention. | Dimensionless ratio, not `%`. |
| `frequency` | measurement | Hz | float | AC frequency at the measurement point. | — |
| `energy_exported` | measurement | kWh | float | Cumulative or interval energy delivered out of the boundary (meter register semantics per binding). | Not `energy_imported`; register vs interval semantics ratified per source. |
| `energy_imported` | measurement | kWh | float | Cumulative or interval energy drawn into the boundary. | — |

## Energy state and capability

**"State of charge" here is really state of energy.** Despite the name, every SoC term in this vocabulary is an **energy** quantity (watt-hours and fractions of an energy capacity), not electrochemical charge (amp-hours, coulomb counting). The industry name SOC is kept by convention; vendors increasingly say SOE for exactly this reason, and they are the same quantity here.

**State of charge always names its basis.** There is no bare `soc` term: every SoC quantity is `soc_<unit>_<basis>` with basis ∈ `installed` | `contract`, because the two are different scales by design (bottom buffers/offsets, contract caps). The installed→contract remap is owned by the [State of Energy Mapping](../Data_Product%28DP%29/State_of_Energy_Mapping/state-of-energy-mapping.md) data product; the ontology fixes only the names and scales.

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `soc_percent_installed` | measurement | % | float | State of charge, 0 to 100, on the **installed/usable basis** as the BMS/vendor reports it. Vendor-computed; received, therefore a measurement. | The default "SoC". Not the contract scale; a 0-to-1 ratio source is converted at ingestion. |
| `soc_percent_contract` | measurement | % | float | Stored energy as a percentage of the **contracted value**: 0% = bottom of the contract band, 100% = contracted energy. **Never exceeds 100% by construction** — the contracted range is defined so it cannot; a value above 100% is a remap or denominator error, not headroom. | Not the installed scale (bottom buffer/offset — 0% contract can sit above 0% installed). Ratify per project **what the reported scale's 100% anchors to** (contracted vs installed value) before trusting any counterparty SoC feed. |
| `soc_kwh_installed` | measurement | kWh | float | Stored dischargeable energy, installed basis. | Not available discharge energy under current limits. |
| `soc_kwh_contract` | measurement | kWh | float | Stored dischargeable energy, contract basis; **capped at contracted energy**. | — |
| `soh_percent` | measurement | % | float | Vendor-reported state of health. The owner's recomputed SoH is a metric, not this term. | — |
| `available_charge_power` | measurement | kW | float | Power the segment reports it can accept right now (ACP). | Not nominal rating (a parameter), not the setpoint. |
| `available_discharge_power` | measurement | kW | float | Power the segment reports it can deliver right now (ADP). | Contractual availability input; the owner replica is a metric. |
| `available_charge_energy` | measurement | kWh | float | Energy the segment reports it can accept before full. | — |
| `available_discharge_energy` | measurement | kWh | float | Energy the segment reports it can deliver before empty. | — |
| `depth_of_discharge` | measurement | % | float | Depth of discharge of a cycle or interval. | — |
| `cycle_count` | measurement | count | float | Cumulative equivalent full cycles as counted by the reporting system. Owner-recomputed EFC is a metric. | Basis (EFC definition) ratified per source. |

## Thermal and environment

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `ambient_temperature` | measurement | degC | float | Air temperature at the segment (enclosure interior or site ambient per binding). | Not `cell_temperature`. |
| `humidity` | measurement | % | float | Relative humidity. | — |
| `wind_speed` | measurement | m/s | float | Wind speed. | — |
| `wind_direction` | measurement | deg | float | Wind direction, degrees from north. | — |
| `irradiance` | measurement | W/m2 | float | Solar irradiance (pyranometer). | — |
| `precipitation` | measurement | mm | float | Precipitation over the reporting interval. | — |
| `gas_concentration` | measurement | % | float | Off-gas concentration (safety monitoring). | — |
| `pressure` | measurement | * | float | Pressure; unit ratified on first use per sensor class. | — |

## Status and state

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `connected` | state | 1 | bool | Contactor/breaker closed: 1 closed, 0 open, null no sample. | Not "available"; a closed contactor on a derated string is still connected. |
| `operating_mode` | state | — | enum | Operating mode as coded by the source; codes mapped at binding. | — |
| `status_code` | state | — | enum | General status word of the segment. | — |
| `fault_code` | state | — | enum | Active fault identifier as coded by the source. | Not `warning_code`. |
| `warning_code` | state | — | enum | Active warning identifier. | — |
| `heartbeat` | state | count | int | Liveness counter from the source system. | Data-quality input, not equipment status. |

## Dispatch and control

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `power_setpoint` | measurement | kW | float | Active-power command received or issued at the segment. | Not `active_power` (the response). The pair drives Operational Availability. |
| `reactive_power_setpoint` | measurement | kvar | float | Reactive-power command. | — |
| `frequency_response_state` | state | — | enum | Armed/enabled state of a frequency response service. | — |

## Market and commercial (grid/market boundary only)

| Term | Kind | Unit | Dtype | Definition | Is not / notes |
|:---|:---|:---|:---|:---|:---|
| `traded_volume` | measurement | MWh | float | Bought or sold volume for a settlement period, signed buy-negative. | — |
| `cashflow` | measurement | * | float | Cashflow for a settlement period; unit is the project currency, ratified once. | Never verify against the counterparty's own number (Metrics Tree rule). |
| `price` | measurement | * | float | Market or contract price; unit `currency/MWh`, ratified once. | — |
| `settlement_period` | measurement | count | int | Market settlement period index. | — |

## Common parameters (names controlled here, values in dimension data)

| Term | Kind | Unit | Dtype | Definition |
|:---|:---|:---|:---|:---|
| `nominal_power` | parameter | kW | float | Nameplate continuous power of the segment. |
| `nominal_energy` | parameter | kWh | float | Nameplate energy of the segment. |
| `nominal_voltage` | parameter | V | float | Nameplate voltage. |
| `nominal_capacity_ah` | parameter | * | float | Nameplate charge capacity (unit Ah; flagged because Ah is admitted only for battery-chain parameters). |
| `cells_in_series` | parameter | count | int | Cell count in series in the segment. |
| `contracted_power` | parameter | MW | float | Contracted power at the applicable boundary. |
| `contracted_energy` | parameter | MWh | float | Contracted energy at the applicable boundary. |

Terms marked `*` have a project-ratified unit recorded at first use; every other unit is fixed by this template. New terms are added only with all six columns filled and a glossary roll-up in the same session.
