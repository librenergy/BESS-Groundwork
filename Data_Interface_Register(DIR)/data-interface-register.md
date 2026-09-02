---
type: Template
title: Data Interface Register
description: "Register of every data system on the project and the interfaces between them: who owns each system, how they communicate, and where the gaps are. Signal-level detail lives in the telemetry data products, not here."
---

# Data Interface Register

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: the register of **every data system and every interface between them**: who owns each system, how it communicates, who consumes it, and where the gaps are. Systems trace to nodes on the Entity Interaction Map (EIM). **Point- and tag-level detail deliberately lives elsewhere** (see §3); this document stays at the system/interface grain so it can be reviewed in one sitting.

## 1. System Inventory

One row per data system (purple node on the EIM).

> **Integrated OEM systems:** when PCS/BMS live inside the battery enclosure and a single OEM site controller is the only interface, collapse the internal rows to one *"internal controls — access via site controller only"* row; don't inventory interfaces that don't physically exist. **Non-ISO markets:** remap the ISO telemetry/settlement rows to the BA/offtaker equivalents (dispatch notices, utility RTU chain, invoice-based settlement).

| ID | System | EIM Node | Owner (Entity) | Vendor/Product | Location (site/cloud) | Access Method | Admin Contact | Notes |
|----|--------|----------|----------------|----------------|----------------------|---------------|---------------|-------|
| SYS-01 | PPC / Plant SCADA | `PPC` | | | | | | |
| SYS-02 | EMS | `EMS` | | | | | | |
| SYS-03 | Substation RTAC/SCADA | `SCADA` | | | | | | |
| SYS-04 | Grid RTAC | `RTAC` | | | | | | |
| SYS-05 | Master BMS | `M_BMS` | | | | | | |
| SYS-06 | String BMSs | `STR_BMS` | | | | | | |
| SYS-07 | Battery Controller | `BAT_CTRL` | | | | | | |
| SYS-08 | Inverter Controller | `INV_CTRL` | | | | | | |
| SYS-09 | Substation P&C / Power Quality | `P&C` | | | | | | |
| SYS-10 | POI Revenue Meters | `POI` | | | | | | |
| SYS-11 | APM / Battery Analytics | `APM` | | | | | | |
| SYS-12 | LTSA CMMS | `CMMS` | | | | | | |
| SYS-13 | LTSA Ticketing | `BESS_LTSA_TS` | | | | | | |
| SYS-14 | EMS Support Ticketing | `EMS_TICK` | | | | | | |
| SYS-15 | ISO Settlement System | `SET` | | | | | | |
| SYS-16 | ISO Market Data | `MD` | | | | | | |
| SYS-17 | Trade & Award Data | `TAD` | | | | | | |
| SYS-18 | ISO OMS | `OMS` | | | | | | |
| SYS-19 | Accounting Platform | `AM_A` | | | | | | |

## 2. Interface Register

One row per interface between two systems. IDs follow `IF-NN` and are **stable** once assigned (calc sheets and the Metrics Tree reference them). What moves is stated in one line; per-point detail lives with the telemetry products (§3). Criticality: H = touches dispatch, safety, or money.

| ID | From → To | What moves | Protocol / Transport | Dir | Crit | Status / notes |
|----|-----------|------------|----------------------|-----|------|----------------|
| IF-01 | String BMS → Master BMS | Cell V/T, string SOC, alarms | | up | H | |
| IF-02 | Master BMS → Battery Controller | SOC, limits, strings connected | | up | H | |
| IF-03 | Battery Controller → PPC | SoC, strings connected, P/Q limits | | up | H | |
| IF-04 | Inverter Controller ↔ PPC | PQ commands, status | | bi | H | |
| IF-05 | PPC ↔ Grid RTAC | Plant telemetry & setpoints | | bi | H | |
| IF-06 | RTAC ↔ ISO Telemetry | Market/BA telemetry set (point list in the Grid Telemetry Map) | | bi | H | |
| IF-07 | RTAC ↔ TOP Real-time Desk | | | bi | H | |
| IF-08 | P&C → Substation SCADA | Relay events, PQ, meter data | | up | M | |
| IF-09 | EMS → APM | Historized telemetry for analytics/shadow calcs | | up | H | |
| IF-10 | TAD → Optimizer | Awards, schedules, prices | | down | H | |
| IF-11 | POI Meters → Settlement | Revenue metering (RQMD) | | up | H | |
| IF-12 | EMS → GOP | Operating displays/alarms | | up | M | |
| IF-13 | … | | | | | |

## 3. Where the telemetry detail lives

This register deliberately stops at the system/interface grain, so it can be reviewed in one sitting. Point lists, tag mappings, and ingestion specs each live in exactly one home:

- **Grid/market-facing points (BESS ↔ BA/ISO/offtaker)** — the [Grid Telemetry Map](/Data_Product%28DP%29/Grid_Telemetry_Mapping/grid-telemetry-map.md) data product: every point on the grid interface with its source tag, monitoring tag, and interface address, including the market signal set (APD, APC, SOC, MAXENER, Mode) with per-point definitions and validation checks.
- **Vendor/OEM tag semantics** — the OEM telemetry extraction in `Project_Documentation/` (what each signal means and its contract role).
- **Per-guarantee capture requirements (what must be historized, at what resolution, from which source, retained how long)** — the Performance Guarantee Matrix §6 Shadow Calculation Requirements.
- **Owner metric definitions computed from the captured data** — the Metrics Tree and its calculation sheets.

## 4. Open Items

Tracked in this folder's `todo.md` (create it with the first item — see the AGENTS.md conventions).
