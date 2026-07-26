---
type: Template
title: BESS Project Contact List
description: "Master contact directory of every role, company, and named contact on a BESS project."
---

# BESS Project Contact List

**Project:** ______________  **Market/ISO:** ______________  **Last updated:** ______________

> **Multi-role counterparties:** when one company holds several roles (offtaker + transmission owner + BA + SC is common on utility tolls), give it its **own section with one row per group/desk** — different people, numbers, and response expectations — rather than one combined row. **First source for named contacts:** the notice exhibits of the executed contracts.

## Ownership & Finance

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 1 | BESS Asset Owner | | | | | — | Central counterparty |
| 2 | Asset Manager | | | | | AMA | Daily ops oversight; weekly optimizer call |
| 3 | Investors / Lender | | | | | Credit Agmt | |
| 4 | Independent Engineer | | | | | IE Mandate | Retained by lender |
| 5 | Insurer | | | | | Policy # | Via Asset Manager |
| 6 | Land Owner | | | | | Lease | |

## Market & Revenue

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 7 | Offtaker / Revenue Counterparty | | | | | Tolling Agmt | |
| 8 | Optimizer | | | | | Optimization Agmt | API access to trade & award data |
| 9 | Scheduling Coordinator / QSE | | | | | SC Agmt | ISO trading platform; outage submissions to OMS |

## Grid & Regulatory

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 10 | Balancing Authority / ISO | | | | | Tariff | Settlements, market data, OMS |
| 11 | Transmission Owner / Interconnection Provider | | | | | GIA | |
| 12 | Transmission Grid Operator — Real-time Desk | | | | | — | 24/7 desk; RTAC coordination |
| 13 | Generator Operator (GOP) | | | | | GOP Agmt | PPC/SCADA interface; TOP coordination |

## O&M / Service Providers

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 14 | BESS LTSA / O&M Provider | | | | | LTSA | Ticketing system + CMMS; manages BESS EPC warranty |
| 15 | ROCC (Remote Ops Control Center) | | | | | — | Under BESS LTSA; 24/7 monitoring |
| 16 | Substation LTSA / O&M Provider | | | | | Sub LTSA | Manages substation EPC warranty + spares |

## OEMs (via BESS LTSA)

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 17 | Battery OEM | | | | | Defect/Capacity Warranty; Avail Guaranty | OEM CMMS; spares |
| 18 | PCS OEM | | | | | Warranty; Service Agmt | Spares |
| 19 | EMS OEM | | | | | Warranty; Service Agmt | Support ticketing; spares |

## Technology Vendors

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 20 | Plant Control / SCADA Vendor | | | | | — | PPC/HMI/historian support |
| 21 | APM / Battery Analytics Vendor | | | | | — | Owner-side data platform |

## EPC Contractors

| # | Role | Company | Primary Contact | Email | Phone | Contract Ref | Notes |
|---|------|---------|----------------|-------|-------|--------------|-------|
| 22 | BESS EPC Contractor | | | | | Workmanship Warranty | Warranty claims via BESS LTSA |
| 23 | Substation EPC Contractor | | | | | Workmanship Warranty | Warranty claims via Sub LTSA |

## Escalation Quick Reference

| Issue Type | First Call | Backup |
|-----------|-----------|--------|
| Plant trip / grid event | GOP / ROCC | TOP Real-time Desk |
| Battery fault / capacity | BESS LTSA | Battery OEM |
| PCS fault | BESS LTSA | PCS OEM |
| EMS / dispatch issue | EMS OEM ticketing | Optimizer / SC |
| Telemetry / ISO data (e.g. MAXENER) | SC / QSE | ISO help desk |
| Substation / HV protection | Substation LTSA | Substation EPC |
| Settlement dispute | SC | ISO settlements |
| Insurance claim | Asset Manager | Insurer |
