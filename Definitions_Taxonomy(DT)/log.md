# Log

## 2026-08-25

* **Availability vocabulary added to the glossary (§5).** The toolkit now computes availability four ways, so the terms had to be defined once rather than re-explained in each document: **OBE** (Overall Battery Effectiveness, the per-interval minimum of the four types averaged over the period), **EA / OA / PA / QA** (equipment, operational, power, energy), **ACP / ADP** (available charge and discharge power), and **overbuild**. Two entries carry ⚠️ deliberately: PA has two competing definitions in common use, and QA is a **scalar** that updates on capacity test rather than a per-interval series. The **SOC** entry was amended to warn about declared *basis* as well as units, which is the single most common source of two parties computing different numbers from the same data.

* **Block and station terms added (§3):** **AC Block (ACB)**, **DC Block**, and **Energy Station (ES)**. These replace the previous habit of naming an architecture after one manufacturer's product.

## 2026-08-24

* **Taxonomy §2b restructured** from three "architecture modes" into two container types plus two station configurations. An AC block integrates its own conversion; a DC block is paired with external conversion. An **Energy Station** is whatever sits behind one transformer: multiple AC blocks plus the transformer, or multiple DC blocks plus PCS plus the transformer. The old framing had one mode named after a specific OEM product, which does not survive being reused on a project that bought something else.

* ID prefix `MP-` renamed to `ACB-` throughout, for the same reason. "Central inverter designs" named after a manufacturer became "central-inverter / PCS-skid designs".

* Five architecture illustrations added under `taxonomy.assets/`; one orphaned duplicate removed.
