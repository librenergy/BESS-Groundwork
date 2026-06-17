---
name: settlement-reconciliation
title: Settlement Reconciliation — Facilitated Session
type: Skill
description: Build or update a BESS project's settlement reconciliation (shadow settlement) procedure through a facilitated session with the asset manager. Use whenever the user wants to verify ISO settlement statements against project meter data and awards, set up shadow settlement logic, validate revenue meter interval data (gaps, duplicates, sentinel values, timezone issues), prepare or track settlement disputes, or investigate revenue discrepancies in CAISO/ERCOT/other markets. Requires the project EIM and the Data Interface Register's meter and market interfaces (run those skills first if missing).
---

# Settlement Reconciliation — Facilitated Session

Template: `Settlement_Reconciliation/08-settlement-reconciliation.md` (repo root). Per the EIM, the chain under audit is: POI revenue meters → ISO settlement system → AO, cross-checked against Trade & Award Data via the Optimizer/SC.

## Prerequisites

- Project EIM; Data Interface Register rows for `POI → SET` and `TAD` (access methods, formats, conventions). If missing, capture inline and back-fill the register.
- Market specifics: ISO settlement calendar (initial run + re-runs), dispute windows, statement file formats.

## Session flow

1. **Data inputs.** Confirm access to each input (statements, meter intervals, TAD, dispatch records, prices): who provides it, in what format, at what latency. No access = no shadow; missing access becomes the first action item.
2. **Meter validation rules.** Walk the template's checks and set project values: interval convention and timezone, dedup key, sentinel-value patterns for this meter model, meter-vs-PPC tolerance. Ask for known meter history — past bugs become permanent watchlist entries.
3. **Shadow calculation scope.** Pick the streams to shadow (start with the big ones: energy DA/RT, primary AS products) and agree tolerances per stream. Full charge-code coverage can come later; a shadow that catches 95% of dollars this quarter beats one that catches 100% next year.
4. **Workflow & calendar.** Pin the monthly steps to the ISO's actual settlement timeline so dispute deadlines are never met by accident. Name the owner per step (PE pipeline vs. SC vs. AM).
5. **Dispute process.** Who files (usually the SC), with what evidence pack, and how outcomes feed back into the exception log and accounting (`AM_A` per the EIM).

## Facilitation guidance

- Timezone/DST and interval-beginning-vs-ending conventions cause more false exceptions than real ISO errors — settle them in step 2 before anyone panics over deltas.
- Re-settlement runs supersede initial statements; the exception log must track which run each delta was measured against.
- Recovered dollars are the KPI of this whole procedure — log them. They also happen to justify the PE retainer.

## Outputs

1. `08-settlement-recon-{{project}}-v{{n}}.md` — populated template.
2. Implementation backlog: statement parser, meter validation job, shadow models, exception report.
3. Watchlist seeded with the project's known meter/telemetry issue patterns.
