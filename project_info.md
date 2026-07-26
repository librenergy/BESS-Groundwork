---
type: Template
title: Project Information
description: "Scaffold for the key facts of the project a repo clone is dedicated to - capacity, equipment, commercial structure, counterparties, milestones, and open items."
---

# Project Information

Each clone of this toolkit is dedicated to a single project (BESS, PV, wind…). This document is the standard place for its key facts — the first thing an agent or teammate reads for context, and the record of what is confirmed vs still open. Replace the dummy values below (shown for a fictional example project) with real ones, keep the `# comments` discipline for provenance (which contract/document confirmed each fact), and park anything unverified in `open_items`.

```yaml
project:
  code: EXPL                      # short internal project code
  name: Example Storage Project
  technology: standalone-bess     # standalone-bess | pv | pv-plus-storage | wind ...
  owner: TBC                      # ultimate owner / sponsor
  project_company: Example ProjectCo, LLC   # the SPV named in the contracts
  status: development             # development | construction | commissioning | operating
  phase: pre-cod
  location: TBC                   # street address, city, county, state
  gps: TBC                        # lat, lon - note the source (survey vs geocoded)
  poi: TBC                        # named substation / interconnection point
  interconnection_voltage_kv: TBC
  market_ba: TBC                  # ISO/RTO or balancing authority; note RA program if any

  capacity:
    power_mw: 100
    energy_mwh: 400
    duration_h: 4
    poi_limit_mw: 100

  equipment:
    battery: TBC                  # OEM + product/model
    pcs: TBC
    ems: TBC
    scada: TBC
    controls_architecture: TBC    # who masters whom (plant controller vs OEM controller)

  commercial:
    offtake_structure: TBC        # tolling | PPA | merchant | hybrid; note who dispatches and who keeps market revenues
    offtaker: TBC
    contract_term: TBC
    contract_price: TBC           # with units, e.g. $/kW-month
    guarantees: TBC               # e.g. [availability, capacity, rte, dispatch compliance] - detail in the Performance Guarantee Matrix
    usage_limits: TBC             # cycles/day, cycles/yr, annual throughput, banking

  performance_engineering:        # value-of-performance quick reference, derived from the offtake contract
    availability_guarantee: TBC
    value_of_1pct_availability: TBC
    value_of_1h_full_outage: TBC
    value_of_1pct_capacity: TBC
    value_of_1pct_rte: TBC

  counterparties:
    bess_integrator: TBC
    epc: TBC
    bop_contractor: TBC
    substation_contractor: TBC
    bess_ltsa_provider: TBC
    om_provider: TBC
    gop: TBC                      # generator operator
    rocc: TBC                     # remote operations control center
    optimizer_trader: TBC
    scheduling_coordinator: TBC

  milestones:
    ntp: TBC
    substantial_completion: TBC
    cod_target: TBC
    cod_guaranteed: TBC           # contractual guaranteed COD, with source

  open_items:
    - Confirm owner / parent structure against the executed contracts
    - Confirm market / balancing authority and POI details
    - Obtain the documents that define guaranteed capacity values
```
