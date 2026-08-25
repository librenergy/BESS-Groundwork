# Daily Performance Report

The operational counterpart to the monthly pack: what happened yesterday, why, and what to do today. Availability under each measurement definition (equipment, power delivery, operational, combined) with excused and depth-weighted variants, energy and efficiency, data quality, the two contractual views, and the settings block that makes the numbers reproducible. An early-warning instrument, not the contractual calculation.

## Documents

* [daily-performance-report.md](daily-performance-report.md) - The specification: objective, audience, build architecture, the report's section structure with worked panel screenshots, what each availability definition means and how it is computed, the offtaker and service-provider contractual views with their recurring traps, and the conventions to settle before the first issue.

## Status

* **Template, v0.3 (2026-08-25).** §2 rebuilt on the **OBE (Overall Battery Effectiveness)** framework: four availability types (equipment, operational, power, energy), each with its computation from the signal level up, plus the capacity-normalisation rules every MW figure depends on. §3 reduced to a transcription frame, since offtake availability definitions have no market standard worth reproducing. §4 now states the power availability calculation the owner should **negotiate into** the service agreement, which is §2's definition unchanged. Populated through the `data-product` skill; depends on the [Outage Tracker](../Outage_Tracker/index.md) ledger and the [PGM](/Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) calculation sheets.
* **Template, v0.2 (2026-08-25).** Rebuilt from the first project clone's matured draft: restructured from a rendered-report template into a specification, with the equipment measures separated from the two contractual views, ten worked panel screenshots from an implementation on a fictionalized dataset, and the build architecture (time-series store, OLTP event store, notebook, pre-computed agentic insights).
* **HTML render skeleton removed 2026-08-25.** The document now specifies structure only. Rebuild the render template from whatever the generator actually emits. Recoverable from git if wanted: blob `dd61624b425bb8a5516ba460bc812662bccd124c`.
