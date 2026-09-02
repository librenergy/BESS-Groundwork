---
type: Template
title: "State of Energy Mapping"
description: "How the physical (installed) state of energy reported by the battery fleet remaps to the contractual state of energy reported to the offtaker: the two scales, the remap function block (inputs, outputs, knobs), the computation owner, and the layered counting/steering/recalibration algorithm."
---

# State of Energy Mapping

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companions:** [Grid Telemetry Map](../Grid_Telemetry_Mapping/grid-telemetry-map.md) (SOC and energy points) · [Metrics Tree](../../Metrics_Tree%28MT%29/metrics-tree.md) · [PGM](../../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) · [DIR](../../Data_Interface_Register%28DIR%29/data-interface-register.md) §3

> Purpose: one authoritative mapping between the **installed** state of energy the OEM fleet reports and the **contractual** state of energy the offtaker sees. Oversized fleets carry more installed energy than the contracted range, so "full" and "empty" on the contractual scale occur while the batteries physically sit inside buffered limits. Define the map once, before the telemetry chain hardcodes a raw pass-through. Check early: many DIRs map the offtaker SOC point straight to the OEM's remaining-energy tag, meaning no remap exists in the chain at all.

## 1. The two scales

**Master unit: MWh.** All mapping parameters, formulas, and records are defined in MWh; percent is a derived display convenience only.

> ⚠️ **Percent is scale-relative and the logic inverts between scales.** "100%" on the contractual scale occurs while the installed fleet is *below* 100%, at the bottom of the top buffer. Likewise contractual 0% leaves real energy in the batteries (the bottom buffer). Any dashboard or report showing a percent must state which scale it is on; an unlabeled "100% SOC" is ambiguous and, on the contractual scale, physically means "not full".

| Scale | Range | Source of truth | Basis |
|---|---|---|---|
| Installed | 0 to fleet usable energy | OEM site controller (remaining / full energy; per-unit SOE) | BOL installed capacity; declines with degradation, restored by augmentation |
| Contractual | 0 to contracted energy | The system computing the remap (§3) | Offtake nameplate / guaranteed energy capacity; pick one, cite it |
| Top buffer | Contractual 100% = installed ___ MWh | | "Reporting fully charged" with batteries below physical full |
| Bottom buffer | Contractual 0% = installed ___ MWh | | "Reporting fully discharged" with energy remaining |

Buffers are defined in **MWh**, not % of fleet usable energy. Session questions: buffer sizes and the top/bottom split (buffers need not be symmetrical; §4 Layer 2).

## 2. Mapping definition: the function block

The remap is a **function block**: measured plant values in, contractual values out, adjustable knobs in between. **All energy inputs and parameters are in MWh**, referenced at the **point of metering (POM)**, the contractual reference point where installed energy and contract energy meet. Percent is never a parameter; any % (SOC, buffer %) is a second-order metric calculated from the MWh values. **Zero is zero on both scales**: there is no low-end offset; buffers are knobs inside the block, not shifted anchors.

### Inputs

| Input | From | Order |
|---|---|---|
| BMS installed SOE (MWh) | OEM site controller; BMS SOC % is a derived metric, not the input | Primary |
| Energy meter readings, charge and discharge (MWh) | Revenue meter at POM | Primary |
| Available charge / discharge power (MW) | OEM site controller / plant controller | Primary |
| Equipment availability (units online / offline) | Plant | Primary |
| Battery temperature (°C) | OEM site controller | Second-order; may or may not be used |
| Auxiliary power consumption (MW) | Aux meter / plant | Second-order; not drawn from the battery but can reduce available power; may or may not be used |

### Outputs

| Output | Meaning |
|---|---|
| Contractual SOE (MWh, 0 to contracted range) | The state of charge the offtaker sees |
| Contractual max energy ("tank size") | Current usable contract energy; shrinks when equipment is unavailable |
| Available charge / discharge power (contract) | Installed power capability capped by energy state and equipment availability: the actual contract max power at any moment |

### Algorithm parameters (knobs)

| Knob | Role | Value |
|---|---|---|
| Top buffer (MWh) | Installed energy held above contractual 100%. Not necessarily equal to the bottom buffer; can be manually set to 0 to force a true full charge for rebalancing | |
| Bottom buffer (MWh) | Installed energy held below contractual 0% | |
| Accounting source, per direction | Which source drives contract SOE in each direction: POM meter MWh or BMS SOE change | Default: charge = BMS SOE, discharge = POM meter |
| Gain: POM → SOE_POM, discharge | Metered discharge MWh to contract SOE | 1.0 (−1 MWh SOE per MWh discharged) |
| Gain: POM → SOE_POM, charge | Metered charge MWh to contract SOE (charge efficiency) | |
| Gain: BMS SOE → SOE_POM, charge | BMS SOE change to contract SOE (covers the measurement-location difference, OEM terminals vs POM) | |
| Gain: BMS SOE → SOE_POM, discharge | BMS SOE change to contract SOE | |
| Charge adjustment gain | Reports contract SOE slightly faster or slower on charge to redistribute the top/bottom buffers; re-tuned daily (§4 Layer 2) | |
| Recalibration step limit (MWh) | Largest correction §4 Layer 3 may apply in one step | |
| Recalibration triggers | Conditions that fire a Layer 3 recalibration: edge touch (contractual full/empty), available-power roll-off thresholds | |

### Consistency rules

- Installed (BMS) energy is never lower than contractual energy.
- A buffer can never go negative. A negative buffer means a measurement error: correct the measurement; the map does not revert.

### Logic regions

Three regions to define per project: **top logic** (at and approaching contractual full), **bottom logic** (at and approaching contractual empty), and **in-between logic** (how energy counts through the middle). The functional form falls out of these three.

## 3. Who computes it, where it lives

Decide and record which system computes the remap (OEM controller configuration, plant controller, SCADA RTU/RTAC scaling), then trace the data path:

| | Where | What |
|---|---|---|
| Data sources (in) | | The §2 inputs: BMS SOE, meter energy, available power, equipment availability |
| Computation | | The remap function block |
| Data output | | The §2 outputs on the telemetry chain to the offtaker: contractual SOE, tank size, available power |

Also decide: whether the offtaker receives contractual or installed SOC on its telemetry point (dispatch follows this number); register the authoritative output tags as derived rows in the Grid Telemetry Map; historian logging of **both scales, permanently**, for dispute evidence.

## 4. The algorithm

Layered: primary counting, steering, recalibration. The SOC is read from the BMS, but the BMS is a reference to recalibrate against, not the master counter.

### Layer 1: Primary counting

- **Scale gain:** every 1 MWh of installed (BMS) energy equates to X MWh of contract energy.
- **Selectable accounting, per direction.** Both sources (POM meter MWh and BMS SOE change) are available in both directions, each with its own gain to SOE_POM (the 2×2 in §2). The algorithm chooses one source per direction.
- **Discharge default: POM meter, −1:1.** Every MWh discharged at the POM drops contractual SOE by 1 MWh. The BMS is ignored.
- **Charge default: BMS SOE change × its gain.** Usually more accurate than the POM meter on charge, since charge efficiency shifts with power and temperature; the gain covers the measurement-location difference (OEM terminals vs POM). Then the charge adjustment gain applies (Layer 2).

### Layer 2: Steering

- The top and bottom buffers are **targets**, not constants. They drift over time from imbalance and other effects.
- The **charge adjustment gain is the only steering control**: report the charge slightly slower or faster to walk the contract window back toward the buffer targets.
- **Re-tune daily:** use the observed top and bottom contract-SOE touches to quantify whether the contract window is still positioned within installed energy per the prescribed buffer sizes, and adjust the gain.
- **Buffers need not be symmetrical.** Set the split from the contract's penalty asymmetry: e.g. where a power derate at top of charge is penalized less than one at the bottom when discharging, run a smaller top buffer and park the contract window high.
- **Manual rebalance mode:** the top buffer can be set to 0 to force the system to a true full charge and help cell balancing (some OEM fleets self-balance; others need the forced full charge).

### Layer 3: Recalibration

- At the top and bottom the plant finds out where it actually is (BMS full/empty, power roll-off). Recalibrate there and restart the counting from the more accurate point.
- **Available-power trigger:** contractual SOE reads short of full on charge, but available power is dropping off hard: the remaining headroom isn't really there. Something has to adjust.
- The algorithm must then decide which output to correct: **tank size** (equipment is offline, so contractual max energy shrinks) or **SOE** (the estimate was off, so step the SOE closer to reality).
- Recalibration events are logged, never silent. Bound them with the step limit; define approval for large steps.

### Re-tuning over time

- **Degradation:** fleet usable energy shrinks and the buffers compress; targets and gains need periodic re-tuning. At some point the installed range can no longer hold the contracted range plus buffers.
- **Augmentation:** installed range steps up; re-tune targets and gains. Define cadence and approval governance.

### Monitoring

Dashboards to watch the algorithm over time, and the evidence trail when numbers are disputed:

- Actual buffer positions vs targets (in-bounds / out-of-bounds excursions).
- Charge adjustment gain history.
- Recalibration events: size, direction, cause (tank vs SOE).
- Meter-vs-BMS drift between recalibrations.

## 5. Contractual touchpoints

- **Dispatch:** the offtaker/ISO dispatches against the SOC it sees; wrong scale = wrong dispatch expectations and compliance/LD exposure.
- **Capacity tests:** test "full" and "empty" definitions must reconcile with the buffer targets.
- **Energy-limited events (Outage Tracker):** judged against contractual SOC; the buffers move the trigger.
- **OEM availability self-assessment:** the OEM's own energy view can diverge from the contractual scale; log both.

## 6. Open Items

Tracked in this folder's `todo.md` (create it with the first item; see the AGENTS.md conventions).
