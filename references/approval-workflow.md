# Approval Workflow / Cycle Engine (reference) — built, ticketed

Generic, reusable **approval-cycle engine**. Replaces the old per-module "requires HR approval" checkbox. HR names a workflow → attaches it to a category/module → defines ordered approver steps → any request type (Time Off, Errands, …) plugs in via a per-type `Approval Workflow` field. Jira: **NEW2B-4194–4195**. Detailed point-in-time notes: memory `project_hr_approval_workflow.md`.

## Locked design (as of 2026-06-19)
- **Approver types per step (3):** Direct Manager (dynamic, = Reports To) · Department Manager (dynamic, = Section Head else Department Head) · Role + Title (fixed). Multi-level chains = stack steps. **Depends on Organization Structure** to resolve the dynamic ones — see `references/org-structure.md`.
- **Flow type:** Sequential (default, one step at a time, stops at first reject) **or** Parallel (all at once, all must approve, Pending until all approve).
- **Per-step "Configure" popup** (the two old links Configure Actions + Customize Roles merged into ONE): (A) **Approver — role & title** (Role+Title steps pick Role + Title dropdowns; dynamic steps show the type read-only); (B) **Actions** — which of Approve / Reject / Send back to edit are available + "Require a reason when rejecting".
- **Approver actions = Approve / Reject / Send back to edit only.** ("Allow approver to edit before approving" REMOVED — redundant with Send-back, muddies audit.)
- **Removed:** workflow-level "On Rejection" radios (redundant with per-step actions); the "multiple approvers at one step (any-one/all)" toggle (duplicated Flow Type; many-people-at-one-step → backend default any-one-of); the "Submission Requirements" section (belongs on the module/type, not the routing engine).
- One **Active** workflow per category; self-request auto-skips that step; edits affect only **future** requests. No-workflow fallback = single HR-approval step. **No conditional routing in v1** (amount/hours/days).

## Dashboard POV (built — config only)
- **Workflow List** (5549:22103): Name · Category · Flow Type · Steps · Status · Actions. Status (Active/Inactive) managed here (··· → Set Active/Inactive), NOT on the form; new workflow defaults Active on save.
- **Create Workflow** (5549:22714 / Configure-popup frame 6004:117130): bilingual names (EN+AR) · Flow Type + Category selects · CONFIGURATIONS box = numbered draggable approver-step rows + per-step **Configure** link + Add Step · Save To Draft / Save Workflow.
- **Parallel-flow variant** (6010:120054): section relabeled "APPROVERS (NO ORDER — ALL AT ONCE)", sequence arrow removed, number badges → neutral dots, approver cards as **horizontal side-by-side blocks**. (Sequential = vertical numbered chain + arrow; Parallel = horizontal unnumbered blocks.)
- **Save-confirmation dialog** (5597:148832): enforces one Active per category — "Replace active workflow?" (amber, Cancel / Replace & Save); in-flight requests keep their original workflow.
- **HR approves on each module's OWN Requests page** (e.g. Time Off → Requests), NOT a dashboard Inbox. **Inbox was DROPPED from dashboard scope** (redundant with per-module Requests pages).

## Mobile POV
- **Approval Inbox** = managers, mobile — one cross-type "Pending my approval" queue (managers have no per-module admin pages).
- **Request Tracking** = employees, mobile — track own requests.
- Approve/Reject/Send-back sheet honors the per-step Configure Actions config (incl. mandatory rejection reason). (Mobile Figma screens still to build by mobile team — fold flow here when handed over.)

## Open decisions (decide before build)
G4 delegation on approver absence · G5 escalation/reminders (rec: remind after 2 days) · G8 withdraw while pending · G9 audit trail · G10 checkbox migration · G11 notifications (push + in-app). (G6 any-one-of and G7 mandatory reason already resolved.)

## Artifacts
- Spec: `docs/superpowers/specs/2026-06-09-approval-workflow-engine-design.md` (HR-front-end repo).
- Confluence: "Approval Cycle" https://2b-it.atlassian.net/wiki/x/AQDSC (pageId 147980289).
- Figma: NAS HR Dashboard, page "Approval Cycle". (Locate frames by name — node IDs churn as the file is co-edited.)
