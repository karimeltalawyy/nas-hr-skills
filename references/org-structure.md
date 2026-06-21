# Organization Structure (reference) — built in Figma, ticketed

A standalone dashboard module that is the **foundation under Approval + Requests**. It supplies the data the Approval engine resolves approvers from. People-derived model (org chart from the per-employee **Reports To** field). **No branches/locations · no positions/vacant-seats.** Jira: **NEW2B-4196** (single task). Detailed point-in-time notes: memory `project_hr_org_structure.md`.

## Model (locked 2026-06-19)
- **Tabs: People · Departments · Job Titles** (Sections is NOT its own tab — a section lives inside its department).
- **Department → Section = 2 levels only** (no recursive parent-department). A **Section always belongs to one Department**. Sections are **optional** (every employee has a Department, may or may not be in a Section).
- **Head on both** Department and Section (one employee each, picker).
- **Reports To** (already on the employee) → the People org chart.
- Department employee count = rolls up its sections.

## Connection to Approval (why this is the foundation)
- **Direct Manager** approver = employee's **Reports To**.
- **Department Manager** approver = the employee's **Section Head** if in a section, else **Department Head**.
Without org structure (reporting lines + heads), the Approval engine can't resolve approvers. See `references/approval-workflow.md`.

## Dashboard POV (built)
- **People** tab — node-link org chart from Reports To; node card = Avatar · Name · **Job Title · Department** · **"N direct reports"** (the old "#784 Connections" wording was removed). Toolbar Filter/Export/Search; Tree↔List toggle (List = Name·Job Title·Department·Reports To·#Direct Reports·Actions). Multiple roots supported. Frame "Organization Structure — People (org chart, fixed)" 6040:132853 (font **Fira Sans**, #0c2427). Optional polish still open: zoom/fit, expand-collapse, click→detail, vacant-manager placeholder, real names.
- **Departments** tab — **Tree↔List**. List (departments only): Name · Head of Department · Sections(count) · Employees · Actions (frame 6021:125682). Tree = node-link **Company → Departments → Sections**, cards show Head·count, per-dept "+ Add Section". Toolbar + **New Department** CTA.
- **Job Titles** tab — master list: ☑·Job Title·Employees·Actions + Add Job Title (frame 6034:132381).
- **Add Department popup**: Name EN/AR · **Head of Department** (picker) · Create.
- **Add Section popup**: Name EN/AR · **Department** (the link) · **Head of Section** (picker) · Create.
- Build patterns: column-oriented table style (user reworked the Departments list this way); standalone forms + in-context "+ Add Section" from the Departments Tree both write the same data.

## Mobile POV (not provided yet)
TBD — likely a read-only org chart / "my team" view for employees & managers. Fold in when the user hands over the mobile flow (two-POV rule).

## Figma
Page where the org frames live; key frames: People chart 6040:132853 · Departments List 6021:125682 · Departments Tree (node-link, Company→Dept→Section) · Job Titles 6034:132381 · "Add Department — Popup" · "Add Section — Popup". (Frames churn as the user co-edits — locate by name, screenshot before editing. NOTE: figma-console MCP must be reconnected next session.)
