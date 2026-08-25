# Performance Guarantee Matrix (PGM)

* [Performance Guarantee Matrix](performance-guarantee-matrix.md) - Inventory of measured-performance guarantees split by side (owed to the offtaker vs held from vendors, incl. supply-agreement commissioning and measured-trigger warranties), with exact calculation methodologies, exclusion asymmetries, and shadow-calculation requirements. Carries a **reference position** (§1.2): the default methodology to use where a contract is silent or still being negotiated.

## Status

* **Template, v0.3 (2026-08-25).** Pre-filled from the OBE availability framework: §1.1 resolves which of the four availability types an instrument actually guarantees, §1.2 states the reference methodology per metric family, the guarantee tables carry pre-filled assessment bases and calc-sheet names, §5 carries the expected excused-treatment pattern per event class, and §6 ships a populated signal list. Guaranteed levels, LD rates and caps remain blank by design: they come from the executed agreement and nowhere else.
* [Calculation sheets](calculations/index.md) - One standalone sheet per guarantee mechanism, plus the shared placeholder schema for the pseudo-SQL sections.
* [References](references/index.md) - The video series behind the availability methodology, its slide decks, and a map of where each part of it is implemented in this repo. Reference, never contractual.
