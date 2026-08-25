---
type: Template
title: "Grid Telemetry Map"
description: "The grid telemetry points (BESS ↔ grid operator): one table, from the BESS SCADA's point of view, with each point's source, the owner's monitoring tag, and the grid-side address, since the utility side of the interface is not owner-readable."
---

# Grid Telemetry Map

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companions:** [DIR](../../Data_Interface_Register%28DIR%29/data-interface-register.md) · [SOE Mapping](../State_of_Energy_Mapping/state-of-energy-mapping.md) · [Metrics Tree & KPIs](../../Metrics_Tree%28MT%29/metrics-tree.md)

> **Scope: grid telemetry only.** The points exchanged between the BESS and the grid operator (balancing authority, ISO, or offtaker dispatch desk); nothing internal. This is the most important interface on the site: dispatch, compliance, and LD exposure all ride on these points. For each one the table answers where the value comes from and how the owner monitors it. The utility side of the interface rides a private network the owner cannot read, so monitoring means reading the same value on our side (SCADA, EMS, or historian tag).
>
> **Point of view: the BESS SCADA.** The boundary is the BESS SCADA's DNP/Modbus server facing the grid RTAC/RTU. *Out* = telemetry we serve to the grid. *In* = setpoints and commands the grid writes to us.
>
> **How to populate.** Fill from the SCADA integrator's points list (the grid-interface DNP/Modbus map): give on-interface rows their real `dnp3.*` addresses, and mark rows the operator has not yet taken as `reserved` (most interfaces reserve spare slots). Keep only performance-relevant points; static configuration readbacks (droop/FFR parameters, operator limits) stay in the points list. The grid operator's final point list usually arrives late; keep default names until it lands.

## The grid telemetry table

Column notation is `source.tag`: `ems.` (OEM site controller, register/tag), `rtac.` (BESS SCADA RTAC), `scada.`, `historian.`, `plc.`/`hmi.`, `meter.`, `dnp3.` (address on the grid interface). **Monitoring Tag is the performance engineer's read path**: the historian (or EMS), not the RTAC, which operations staff alone can reach. The `historian.GRID_*` names are a proposed convention; ratify the naming and confirm the RTAC → SCADA → historian mirroring that creates them.

| ID | Signal | Unit | Source (source.tag/address) | Monitoring Tag (source.tag) | Grid RTAC tag address | Rate |
|---|---|---|---|---|---|---|
| **Out: telemetry, BESS → grid** | | | | | | |
| GT-01 | Heartbeat counter | count | rtac.generated | `historian.GRID_HEARTBEAT` | | 1 s |
| GT-02 | Active Power | MW | ems.meter feed | `historian.GRID_ACTIVE_POWER_MW` | | live |
| GT-03 | Reactive Power | MVAr | ems.meter feed | `historian.GRID_REACTIVE_POWER_MVAR` | | live |
| GT-04 | Voltage | kV | ems.meter feed | `historian.GRID_VOLTAGE_KV` | | live |
| GT-05 | Plant frequency (live) | Hz | ems.meter feed | `historian.GRID_FREQUENCY_HZ` | | live |
| GT-06 | Active Power Setpoint Echo | MW | ems.real-power command readback | `historian.GRID_P_SETPOINT_ECHO_MW` | | on change |
| GT-07 | Full Battery Energy (usable capacity) | MWh | ems.battery aggregate | `historian.GRID_FULL_ENERGY_MWH` | | slow |
| GT-08 | Remaining Battery Energy | MWh | ems.battery aggregate | `historian.GRID_REMAINING_ENERGY_MWH` | | live |
| GT-09 | Available charge energy (headroom to full) | MWh | ems.(Full − Remaining, availability-reflective) | `historian.GRID_CHARGE_HEADROOM_MWH` | | live |
| GT-10 | State of Charge / SOE | % or MWh | rtac.calculated (or ems.) | `historian.GRID_SOC_PCT` | | live |
| GT-11 | Dispatchable Charge Power | MW | ems.battery aggregate | `historian.GRID_DISPATCHABLE_CHARGE_MW` | | live |
| GT-12 | Dispatchable Discharge Power | MW | ems.battery aggregate | `historian.GRID_DISPATCHABLE_DISCHARGE_MW` | | live |
| GT-13 | Available units | count | ems.battery aggregate | `historian.GRID_AVAILABLE_UNITS` | | on change |
| GT-14 | Auxiliary load | MW | ems.aux load | `historian.GRID_AUX_LOAD_MW` | | live |
| GT-15 | Connection status · Local/Remote status | bool ×2 | plc./rtac. | `historian.GRID_CONNECTED / GRID_LOCAL_REMOTE` | | on change |
| GT-16 | Main / high-side breaker status | bool | rtac.substation statuses | `historian.GRID_MAIN_BKR_CLOSED` | | on change |
| GT-17 | Frequency-response enabled (aggregate) | bool | ems.droop/FFR enables | `historian.GRID_FREQ_RESPONSE_ENABLED` | | on change |
| GT-18 | Active / Reactive Power Blocking | bool ×2 | ems.derate alerts | `historian.GRID_P_BLOCKING / GRID_Q_BLOCKING` | | on change |
| GT-19 | Meter health: Invalid / comm loss / CT-PT / Out-of-Bounds | bool ×4 | ems.meter flags | `historian.GRID_METER_INVALID / _MIA / _CTPT_FAIL / _OOB` | | on change |
| **In: setpoints & commands, grid → BESS** | | | | | | |
| GT-51 | Active power dispatch setpoint | MW | grid dispatch (mechanism per offtake/ISO) | `historian.GRID_DISPATCH_SETPOINT_MW` | | per dispatch |
| GT-52 | Real power ramp rate setpoint | MW/s | grid write → plc → ems. | `historian.GRID_RAMP_RATE_SP_MWPS` | | on demand |

**Row notes.**
- **Measurement basis:** decide it once and state it; the default assumption is all points at the **point of delivery**. A second gross/terminal basis is usually redundant. Operating SOC limit points are usually redundant too: the max is Full Battery Energy (GT-07), which self-adjusts on capability loss, and the range runs 0 to that value.
- **GT-02/03/04:** expect the operator to also ask for SOC in MW and % on these slots, and to describe voltage as a maintain-voltage command; resolve explicitly (readings here, SOC on GT-10, the command as a write).
- **GT-10 is the [SOE Mapping's](../State_of_Energy_Mapping/state-of-energy-mapping.md) landing point:** pin down which system calculates it and whether its basis is installed or contractual.
- **GT-51 is the single most important operational input.** Pin its mechanism early (setpoint down this interface vs electronic/voice notice); it decides dispatch-compliance evidence.
- **GT-09** carries the ERCOT-style lesson: energy points should be **availability-reflective**, not nameplate arithmetic.

## Open items

Track in this folder's `todo.md` (create it with the first item — see the AGENTS.md conventions). Typical first items: the grid operator's final point list; standing up the `historian.GRID_*` monitoring tags (mirroring + naming); which `reserved` rows to propose to the operator.
