---
type: Template
title: "MT_BESS_004 — Power Availability (PA)"
description: "Owner metric: can the plant deliver full power right now? ACP and ADP kept separate to the last step, weakest-string bus roll-up, rail-aware min. Evidences PG_201."
---

# `MT_BESS_004` — Power Availability (PA)

**Class:** engineering · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** The plant's **capability right now**, dispatched or not: the power it could accept and the power it could deliver. Both come from the BMS and PCS capability signals, normalised to the POI and combined rail-aware. Full calendar coverage; an EMS estimate rather than a measurement, so it needs deliberate validation. It is **not** delivered power (`MT_BESS_003`), not on/off (`MT_BESS_002`), and not the vendor's availability figure: `PG_201` may be defined on `max(charge, discharge)`, which this sheet refuses. It evidences `PG_201` and is the definition to negotiate into the service agreement.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_004` |
| **Formula** | see Method. Two series to the last step: `BESS_ACP(i)` and `BESS_ADP(i)`, each `min(Σ_PCS Σ_bus min( min(String_AXP) × n_connected, PCS_Module_AXP ), contracted_power)`. Combined: `PA_MW(i) = ADP` at SOC 100%, `ACP` at SOC 0%, `min(ADP, ACP)` otherwise. `PA_%(i)` against installed and contracted MW. **Gaps:** a bus with no capability sample is 0 for that interval (unproven is unavailable), credited back only through an Outage Tracker event; a missing SOC sample with capability present falls back to `min(ADP, ACP)` with a flag. |
| **Units** | MW (ACP, ADP, PA), % on installed and contracted bases |
| **Measurement boundary** | the POI: PCS capability already includes conversion and POI efficiency; string capability converted with the directional losses and the sustainable C-rate in [Daily Report §9.2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| **Inputs & sources of truth** | `available_charge_power` / `available_discharge_power` per `Bat_Str` (**master BMS at the DC bus** wins over each string BMS's own figure, so one view of the bus governs); per `PCS_Mod` (**PCS**, from its derate curve, voltage limits, bus balancing and reactive-power priority); `connected` per string (**BMS**); `soc_percent_installed` at BESS grain (**master BMS**, installed basis, with any balancing override flagged); `contracted_power` |
| **Secondary / cross-check source** | `active_power` delivered during dispatch (`MT_BESS_003`): delivered can never exceed available; controlled tests with forced imbalance or imposed PCS limits validate the reported ACP/ADP |
| **Calculation interval** | `{{1 to 5}}` min; minimum valid intervals per hour `{{n}}` |
| **Aggregation** | string → bus (weakest × count vs PCS) → PCS → BESS per interval; period mean of `PA_MW(i)`; derate attribution table per day |
| **Timezone & clock convention** | `{{TZ}}`, calendar engineering series |
| **Exclusions** | none; excused events are `MT_BESS_002`'s. The SOC rails are handled in the formula, not as exclusions |
| **Target / alert** | no contractual target. Alert on a one-sided derate (`|ACP − ADP| > {{x}}` MW away from the rails) persisting `{{n}}` intervals, and on any bus reading below its weakest connected string. Review daily |
| **Related PG** | evidences `PG_201` |
| **Owner of the calculation** | `[PE]`, materialised in the platform at bus and BESS grain, with the two directions stored separately |
| **Reporting chain** | Daily Performance Report §2.3 and §4.1 (beside the vendor's figure), `MT_BESS_001`, dashboards, Monthly Performance Report |
| **Reconciliation / plausibility checks** | `ACP = 0` at SOC 100% and `ADP = 0` at SOC 0%, always; `PA ≤ MT_BESS_002` in MW at every interval (capability cannot exceed what is in service); delivered `≤` available during dispatch; every PCS operating mode (idle, standby) has a written availability meaning; the accuracy-test results are on file before the first monthly number |
| **Known discrepancies** | The vendor's `PG_201` figure reads **higher** wherever the agreement uses `max(charge, discharge)`, with the divergence concentrated in one-sided derates. Expected; it is the argument, not the error. SOC estimation error at the rails makes raw PA read low near full and empty; depth weighting (`MT_BESS_001`) handles it, this sheet does not |

## Method

**String.** Computed by the BMS from min and max cell voltage, current limit and SOC; best computed by a master BMS at the DC bus so one view governs. The SOC boundaries are absolute:

```
if SOC = 100%:  ACP = 0        (a full battery cannot charge)
if SOC = 0%:    ADP = 0        (an empty battery cannot discharge)
```

**PCS module.** From the PCS's temperature derate curve, voltage limits, bus power balancing and reactive-power priority, already at POI efficiency.

**Bus.** Replace X with Charge or Discharge throughout:

```
Bus_AXP = min( min(String_AXP) × number of connected strings, PCS_Module_AXP )
```

The inner term is the **weakest** string multiplied by the count, not the sum: strings on a common DC bus are voltage-locked, so the weakest one sets what the bus can sustain.

**BESS.**

```
PCS_AXP  = Σ Bus_AXP
BESS_AXP = min( Σ PCS_AXP, Contracted_Power )
```

**Combining the directions.** The definition to adopt and to write into contracts:

```
BESS_AP = BESS_ADP                      if BESS_SOC = 100%
        = BESS_ACP                      if BESS_SOC = 0%
        = min( BESS_ADP, BESS_ACP )     otherwise
```

Away from the rails a plant that can discharge but not charge is not fully available: it cannot take the next instruction whichever way it comes.

**What derates it**, so a drop can be attributed: equipment unavailability, SOC imbalance, BMS current limiting, PCS temperature, PCS AC voltage, PCS reactive-power priority.

## Easy to get wrong

`max(charge, discharge)`: under it a unit that has lost all discharge capability scores fully available for the life of the contract. Summing strings on a bus instead of weakest × count, which overstates capability and always errs in the owner's disfavour later. Trusting the EMS figure without the forced-imbalance accuracy test. Not knowing about a balancing SOC override, which the calculation then reads as a fault.

## References

[M01 E04 Power Availability Deep Dive](../../BESS_Experts/m01-e04-power-availability-deep-dive.pdf); implemented in [Daily Performance Report §2.3](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) and §4.1.
