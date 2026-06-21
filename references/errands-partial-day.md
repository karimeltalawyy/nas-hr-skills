# Errands / Partial-Day Permissions (reference) — Designing (Session 2026-06-10)

Extending the existing **Errands** module (mobile request + dashboard `errand-types`). Today `ErrandType` is thin (name AR/EN, notes, errandStart, errantRequestNumAfterday, mangerApproved/hrApproved, isAttachementRequired) and "types" ARE durations (1h/2h/3h/4h) with a separate monthly-hours cap. Goal: support the Egyptian-law **إذن / partial-day permission** (half day or any partial day) + a real **check-out → check-in bracket** against the shift. See memory `project_hr_errands_partial_day.md`.

## Locked business decisions (this session)
1. **Single monthly time pool**, counted to the minute (h:mm). Every request — fixed or partial — draws its duration from the one pool. Validated vs remaining balance at submission.
2. **Request carries the time** (employee enters): reason(type) · **date · start time · end time** · **destination (free text — where he's going)** · note. Duration = computed from start→end.
3. **Excused & paid** — the approved window counts as present/paid, reduces the shift requirement for that window, no late/early-leave penalty; the day records "on errand at [location] for [hours]." Pool decremented.
4. **Approved window is the truth** — only the approved minutes are excused + drawn from pool. Overstaying (late return) drops back to **normal late/early-leave rules**. Returning early still draws the approved amount (no refund).
5. **Type = reason, NOT duration (Approach A).** DECIDING INSIGHT: the check-out/check-in bracket requires every errand to have a time-of-day window, so a "2-hour type" with no start time can't be bracketed → duration MUST move onto the request. `ErrandType` becomes a reason/category (name AR/EN · active · max requests/day) holding policy only. Old 1h/2h/3h/4h types retire/collapse; historical requests keep recorded durations.

## Grounded in ERP research (Odoo + ERPNext/Frappe)
- **Odoo Time Off** = hourly/half-day/**Custom Hours** (From–To) request, auto-computes duration, deducts from an **allocation** (= our monthly pool). Attendance is separate (no bracket).
- **ERPNext/Frappe** = **Employee Check-in logs** (Log Type IN/OUT) + Shift Type threshold → auto-attendance marks Present/**Half Day**; "Attendance Request" excuses on-duty gaps. Our check-out/check-in bracket = ERPNext **multi-punch** model (IN→OUT errand-start→IN errand-return→OUT); approved permission excuses the OUT→IN gap. Slightly more capable than stock Odoo.
- Both: **approval = a state machine/workflow, NOT a boolean flag** → confirms using our engine.

## Approval = via the Approval Workflow Engine — NOT per-type flags
Register **"Errands"** as a category; HR builds one Active workflow (e.g. Direct Manager → HR, sequential). Delete `mangerApproved`/`hrApproved` from ErrandType (the engine's §8.6 no-workflow fallback = single HR step = clean migration). **Submission Requirements (Attachment / Take Picture / Note / GPS-Location) are owned by the WORKFLOW (engine spec §4.4), NOT the errand type** — so delete `isAttachementRequired` too. NB: workflow's "Location Required" = GPS share; errand's "destination (text)" is a separate native field. Errand inherits the engine's status lifecycle + Approval Inbox + Request Tracking (mobile+dashboard) for free. Engine gaps G4 (delegation) / G5 (reminders — errands are same-day, time-sensitive) / G7 (rejection reason) hit errands directly but are owned by the engine. (See memory `project_hr_approval_workflow.md`.)

## Final model
`ErrandType`=reason (name AR/EN·active·max/day) · **Errand Policy**=monthly pool (h:mm)+time-frame bounds (earliest/latest, max per request, min notice) · **Request**(native)=reason·date·start·end·destination(text) · **Approval**=Errands workflow + its submission requirements · **Attendance bracket**=check-out@start→check-in@end, approved window excused/paid, overstay→normal rules.

**Relationship to Time Off:** Errands = on-duty HOURLY partial-day (إذن). Time Off = full-day leave (days only). When half-day LEAVE arrives, define the boundary (half-day leave deducts 0.5 from a leave balance here vs hourly permission in Errands). See `time-off.md`.

**Status:** Business design locked in conversation · NOT yet written to spec (planned: `docs/superpowers/specs/2026-06-10-errands-partial-day-design.md`) · no Figma yet · existing code at `src/app/demo/components/info/components/custom/errand-types/`.

## Mobile POV
⏸ **Mobile flow not yet provided.** Known mobile touchpoint: employee submits the errand/permission request (reason · date · start · end · destination · note) and does check-out@start → check-in@end against the shift; inherits the Approval Engine's Approval Inbox + Request Tracking. Fold in the mobile PD when provided.
