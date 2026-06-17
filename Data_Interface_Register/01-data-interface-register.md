---
type: Template
title: Data Interface Register
description: "Single source of truth for every data system, signal group, and interface on the project, each traceable to a node or edge on the EIM."
---

# Data Interface Register

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: single source of truth for every data system, signal group, and interface on the project — who owns it, how it moves, who consumes it. Each row should be traceable to a node or edge on the Entity Interaction Map (EIM).

## 1. System Inventory

One row per data system (purple node on the EIM).

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

One row per data flow (edge on the EIM). IDs follow `IF-NN`.

| ID | From → To | EIM Edge | Signal Group / Content | Protocol / Transport | Direction | Rate / Latency | Format | Auth / Network Path | Criticality (H/M/L) | Owner | Status |
|----|-----------|----------|------------------------|----------------------|-----------|----------------|--------|---------------------|---------------------|-------|--------|
| IF-01 | String BMS → Master BMS | `STR_BMS --> M_BMS` | Cell V/T, string SOC, alarms | | up | | | | H | | |
| IF-02 | Master BMS → Battery Controller | `M_BMS --> BAT_CTRL` | SOC, limits, strings connected | | up | | | | H | | |
| IF-03 | Battery Controller → PPC | `BAT_CTRL --> PPC` | SoC, strings connected, P/Q limits | | up | | | | H | | |
| IF-04 | Inverter Controller ↔ PPC | `INV_CTRL <--> PPC` | PQ commands, status | | bi | | | | H | | |
| IF-05 | PPC ↔ Grid RTAC | `PPC <--> RTAC` | Plant telemetry & setpoints | | bi | | | | H | | |
| IF-06 | RTAC ↔ ISO Telemetry | `RTAC <--> GTEL` | APD, APC, SOC, MAXENER, Mode, op metrics | | bi | | | | H | | |
| IF-07 | RTAC ↔ TOP Real-time Desk | `RTAC <--> TOP` | | | bi | | | | H | | |
| IF-08 | P&C → Substation SCADA | `P&C --- SCADA` | Relay events, PQ, meter data | | up | | | | M | | |
| IF-09 | EMS → APM | `EMS -->|API| APM` | Historized telemetry | | up | | | | H | | |
| IF-10 | TAD → Optimizer | `TAD -->|API| OPT` | Awards, schedules, prices | | down | | | | H | | |
| IF-11 | POI Meters → Settlement | `POI --> SET` | Revenue metering (RQMD) | | up | | | | H | | |
| IF-12 | EMS → GOP | `EMS --> GOP` | Operating displays/alarms | | up | | | | M | | |
| IF-13 | … | | | | | | | | | | |

## 3. ISO Telemetry Point List

The contractual/market-facing signals (the `GTEL` subgraph). For each: define precisely, identify the authoritative upstream source, and the full chain of custody.

| Signal | Definition (project-specific) | Authoritative Source | Chain (source → … → ISO) | Update Rate | Validation / Plausibility Check | Known Issues |
|--------|-------------------------------|----------------------|--------------------------|-------------|--------------------------------|--------------|
| APD — Available Discharge Power (MW) | Max sustained discharge considering SOC foldback, BMS limits, PCS derates | | | | | |
| APC — Available Charge Power (MW) | Symmetric charge counterpart | | | | | |
| SOC (MWh) | Dischargeable energy at rated power from present state | | | | | |
| MAXENER (MWh) | Usable capacity at current SOH & availability (slow-varying) | | | | | |
| Mode | AS / AGC / ADS / MAN / OFF | | | | | |
| Operational metrics | | | | | | |

## 4. Historian / Data Platform Ingestion Map

| Source System | Tags / Topics Ingested | Ingestion Path (e.g., Modbus → collector → NATS → ClickHouse) | Retention Tier | Table / `uns_path` Prefix | Gaps |
|---------------|------------------------|---------------------------------------------------------------|----------------|---------------------------|------|
| | | | | | |

## 5. Open Items

| # | Item | Owner | Target Date | Status |
|---|------|-------|-------------|--------|
| | | | | |

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
