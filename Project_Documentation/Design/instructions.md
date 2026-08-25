---
type: Guide
title: Design Documents — Filing & Review Instructions
description: "How to file design documents (one subfolder per discipline) and the light-touch review method: extract quantities, metering, auxiliary power, labeling schemes, and SCADA/EMS references for performance engineering. Not an engineering check."
---

# Design Documents: Filing and Review

The design folder is a **general reference**: it exists so that questions about the plant design ("how many feeders?", "where is the revenue meter?", "what keeps the plant alive off-grid?") can be answered from Markdown without opening a drawing. It is not an engineering review; do not re-derive or verify the design.

## Filing

One subfolder per discipline, created as needed:

| Subfolder | Holds |
|:---|:---|
| `SLD/` | Single-line diagrams |
| `Sizing/` | DC/energy sizing basis |
| `Degradation/` | Degradation curves, capacity-fade projections, cycling assumptions |
| `Augmentation/` | Augmentation plans and campaign designs |
| `Layout/` | Site and equipment layout drawings |
| `Networking/` | Network and communications architecture drawings (SCADA IFC sets and points lists stay in `../SCADA/`) |
| `Studies/` | Designer-provided engineering studies: load flow, arc flash, grounding, coordination (owner-run studies live in `Work_Product(WP)/`) |

Add new disciplines the same way (e.g. `Civil/`). Keep original drawing filenames; companions (`summary.md`, `index.md`, `log.md`, `todo.md`) sit beside the sources per [INSTRUCTIONS §4](../../INSTRUCTIONS.md#4-adding-a-source-document). Each discipline folder keeps **one `summary.md` with a section per document or sheet**.

## Review method: light touch

Extract what performance engineering will ask for, and stop there.

1. **Quantities.** Counts of anything countable: battery units, PCS, transformers, feeders, breakers, meters. Reconcile against [`project_info.md`](../../project_info.md); flag mismatches ⚠️.
2. **Metering locations.** Revenue and check meters: which bus or feeder they sit on, and the model if plainly labeled.
3. **Auxiliary / station power.** Sources, transfer switches, backup generation: what keeps the plant alive when the grid or the plant is down.
4. **Labeling schemes.** Feeder and equipment naming (e.g. feeders `1A/1B` … `nA/nB`): telemetry points and outage records will use these names, so capture the scheme once.
5. **SCADA / EMS references.** Any control system, comms path, PPC, or RTU that appears on a drawing: note it and flag it as a [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md) candidate.
6. **Contractual echoes.** Ratings that correspond to contract values, redundancy or its absence, RAS hardware: one line each, cross-linked to the document that cares.

**Out of scope** unless a specific question demands it: CT/PT ratios, relay models and settings, protection coordination. Detail already extracted may stay, but do not chase it.

## Close-out

Index bullet (discipline folder + [`Project_Documentation/index.md`](../index.md)), entry in this folder's `log.md`, open items to the discipline `todo.md`, DIR candidates flagged.
