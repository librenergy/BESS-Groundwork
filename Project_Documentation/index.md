# Project Documentation

Client-provided and project reference documents, sorted by domain. Non-markdown source documents (PDF/DOCX/XLSX) are listed for inventory; they are not OKF concept documents.

**Every domain folder carries an `instructions.md`** — the declared starting point for reviewing that kind of document, layered on the mechanical workflow in the skill. Four worked archetypes ship with the template, one per document class: [`PPA/`](PPA/instructions.md) (offtake — the owner *gives* the guarantees, read as the defendant), [`BSA/`](BSA/instructions.md) (supply — what was bought, at what rating, warranted against what), [`LTSA/`](LTSA/instructions.md) (service — the owner *holds* the guarantees, read as the claimant), and [`OEM/`](OEM/instructions.md) (technical documentation, not a contract). Write one for any other domain on its first review, modelled on those.

New documents are reviewed via the `project-document-review` skill: each gets the domain's companion set beside the source file (`summary.md` always; `definitions.md`, `guarantees.md`, `operations-and-maintenance.md`, `outage-management.md`, `telemetry.md`, `red-flags.md` as applicable), a per-folder index entry, a definitions roll-up into the master glossary in the same session, and its facts propagated to `project_info.md` and the matrix documents. When a folder accumulates several unrelated documents, create a subfolder per document so each keeps its own companions.

## Domains

* [PPA](PPA/index.md) - Offtake agreements: PPA, tolling, energy storage agreements, amendments.
* [BSA](BSA/index.md) - Battery supply agreements: the equipment SPA/BSA, amendments, incorporated warranty.
* [LTSA](LTSA/index.md) - Long-term service agreement with the battery integrator/OEM, including exhibits.
* [EPC](EPC/index.md) - EPC agreement, acceptance test plans, commissioning and integration plans.
* [GRID](GRID/index.md) - Interconnection: LGIA, studies, POI and revenue-metering documentation.
* [O&M](O%26M/index.md) - Balance-of-plant O&M agreements, subcontracts, and operating procedures.
* [OEM](OEM/index.md) - Equipment specs and manuals; one subfolder per vendor.
* [SCADA](SCADA/index.md) - SCADA/communications drawings, IFC sets, points lists.
* [EMS](EMS/index.md) - EMS and plant-controls documentation.
* [Design](Design/index.md) - Design basis: sizing summaries, augmentation plans, drawings.
* [Asset Management](Asset_Management/index.md) - AM agreements, budgets, insurance, compliance.
* [Performance Engineering](Performance_Engineering/index.md) - Onboarding checklists, test data, performance analyses.

## Status

* **All domain folders empty — awaiting project documents.**
