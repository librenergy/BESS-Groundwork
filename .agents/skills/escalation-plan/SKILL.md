---
name: escalation-plan
title: Escalation & Communication Plan — Facilitated Session
type: Skill
description: Build or update a BESS project's event escalation and communication plan through a facilitated session with the asset manager. Use whenever the user wants to define who-calls-whom for plant trips, fires/thermal events, battery or PCS faults, telemetry failures, outage notifications, settlement disputes, or weather prep; set severity levels and notification deadlines; document standing meeting cadences; or capture contractual notification obligations (insurer, offtaker, lender, force majeure). Requires the project EIM and Contact Register; the RACI matrix is the ideal companion (run those skills first if missing).
---

# Escalation & Communication Plan — Facilitated Session

Template: `Escalation_Communication_Plan/05-escalation-communication-plan.md` (repo root). This turns the RACI's accountable parties into a 2 AM call sheet with deadlines.

## Prerequisites

- Project EIM and Contact Register (names + 24/7 numbers). RACI matrix strongly preferred — if it exists, pre-fill First Call/Accountable from it.

## Session flow

1. **Severity levels.** Calibrate the four levels to this project: what counts as SEV-1 here, and what notification windows do contracts/ISO rules impose?
2. **Walk the event types.** For each row in the template, narrate the scenario and confirm: who detects it (which system raises the alarm — tie to EIM data nodes), first call, backup, accountable, must-notify list, and the binding deadline with its source (ISO rule, insurance clause, tolling agreement).
3. **Standing communications.** Confirm the recurring cadences from the EIM (optimizer daily call/weekly report, O&M calls, monthly report distribution) and add the project's real meeting rhythm.
4. **Contractual notifications.** Mine the Obligation Matrix and insurance policies for hard notification deadlines — these are the rows where lateness costs money.
5. **Quick card.** Fill the print-friendly card with live 24/7 numbers from the Contact Register.

## Facilitation guidance

- Deadlines need sources. "Notify the insurer promptly" → find the actual clause wording and days.
- Detection matters as much as response: if no system detects the event (e.g., a stale MAXENER), the chain never starts — log detection gaps as data-platform alerting backlog.
- Test the plan verbally: pick one SEV-1 and one SEV-2 and have the user walk it end-to-end against the draft. Gaps surface immediately.

## Outputs

1. `05-escalation-plan-{{project}}-v{{n}}.md` — populated template + quick card.
2. Detection-gap list → alerting backlog.
3. Suggest a periodic drill / annual review date.
