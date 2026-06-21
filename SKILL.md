---
name: nas-hr
description: Full context loader for the NAS HRMS product — one business seen from two POVs (the Angular HR dashboard, which is the core source of truth, and the native mobile app). Loads each module's dashboard + mobile design, what's built vs designed, scope, and session state so any NAS HR work can resume without re-explaining context.
---

# NAS HRMS — Context Skill

NAS HRMS is **one product, one business — viewed from two POVs**: the **Dashboard POV** (HR web, the user's side — the CORE, always the richest source of truth) and the **Mobile POV** (native iOS+Android, employees+managers). The two are tightly coupled and built to mirror each other; the backend owns the shared business logic. The skill is a router: it loads only the module you pick, then reads that module's reference file.

## On invocation — do this in order

1. Print the **System Map** and **Module Index** below (they are short).
2. **Ask the user what they're working on today** using AskUserQuestion — two questions: (a) which **module**, (b) which **POV** (Dashboard / Mobile / Both). Do NOT auto-print a big briefing.
   - **Skip a question if the user already told you.** If their invocation message already names a module and/or POV (e.g. "mobile, time off"), don't re-ask — go straight to step 3.
3. **Read the chosen module's reference file** (`references/<module>.md`) before proposing any work. Read more than one if the task spans modules. Each reference has a **Dashboard POV** and a **Mobile POV** — read both so the two stay connected.
4. For new feature/design work, invoke `superpowers:brainstorming` first.

## The two-POV rule (IMPORTANT)

- **Dashboard POV is the core** and will always be the richest — it is the source of truth for each module's business.
- **Mobile POV is provided incrementally.** The user hands over the **mobile flow / PD per module** over time. When they do, **update that module reference's `Mobile POV` section** so dashboard and mobile stay in lockstep (they depend on each other — same business, two views).
- Do NOT document team/repo locations (iOS/Android/Backend repos) — that's out of scope. Just keep each module's two POVs aligned.

## System Map — one business, two POVs

| POV | Stack | Users | Context status |
|---|---|---|---|
| **Dashboard** (CORE) | Angular 17, NAS design system, PrimeNG, color `#0c2427` | HR (web) — configures, approves, monitors | Richly documented (source of truth) |
| **Mobile** | Native (iOS + Android, one flow) | Employees + Managers | Provided incrementally via mobile PD — fold into each module's Mobile POV |
| **Backend** | API — owns all business logic (balances, scoring, validation, workflow state) | serves both POVs | Logic owner; not separately documented |

Dashboard project path: `/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features/` (verify — may not be on disk).

## Module Index — read the reference when that module is chosen

| Module | Reference file | Status |
|---|---|---|
| Built: Appraisal · KPI Plans · Payroll · Rewards T2/T3 | `references/built-modules.md` | ✅ shipped |
| Rewards Tier 1 — Peer-to-Peer Badges | `references/peer-recognition.md` | 🔴 designed, to build |
| 360-Degree Feedback | `references/360-feedback.md` | 🔴 designed, to build |
| Competency (Qualifications + Profiles + Ladders) | `references/competency.md` | 🟡 designed, Figma in progress |
| Errands / Partial-Day Permissions (إذن) | `references/errands-partial-day.md` | 🟡 designing |
| Time Off / Vacation | `references/time-off.md` | 🟢 built in Figma · ticketed NEW2B-4189–4193 |
| Approval Workflow / Cycle (cross-cutting) | `references/approval-workflow.md` | 🟢 built in Figma · ticketed NEW2B-4194–4195 |
| Organization Structure (People · Departments · Job Titles) | `references/org-structure.md` | 🟢 built in Figma · ticketed NEW2B-4196 |
| Permissions & Roles | `references/permissions-roles.md` | 🟢 design locked + Figma v1 built (2026-06-20) |

## Module Roadmap — Full Scope

| Priority | Module | Status |
|---|---|---|
| 🔴 Phase 1 | Rewards — Peer-to-Peer Badges | Designed, ready to build |
| 🔴 Phase 1 | 360-Degree Feedback | Designed, ready to build |
| 🟡 Active | Competency (library + profiles + ladders) | Figma in progress |
| 🟡 Active | Errands / Partial-Day Permissions | Business design locked |
| 🟢 Built | Time Off / Vacation | Figma done + ticketed (NEW2B-4189–4193) |
| 🟢 Built | Approval Workflow / Cycle | Figma done + ticketed (NEW2B-4194–4195) |
| 🟢 Built | Organization Structure | Figma done + ticketed (NEW2B-4196) |
| 🟢 Built | Permissions & Roles | RBAC design locked + 4 Figma screens built (2026-06-20); not yet ticketed |
| ⏸ Deferred | Calibration | Needs 360 data flowing first |
| ⏸ Deferred | Performance Improvement Plan (PIP) | Needs calibration data |
| ⏸ Future | Succession Planning | Needs all modules running |

## Integration Points

| From | To | What flows |
|---|---|---|
| Peer-to-Peer Badges | Performance Score | 1 endorsement = 1 point → Recognition Score (weight %, tied to appraisal cycle) |
| KPI Achievement | Performance Score | KPI score → 40% of overall |
| Appraisal / 360 | Performance Score | Appraisal score → 50% of overall |
| Performance Score | Calibration | Feeds Performance axis of 9-box grid |
| Calibration | Succession Planning | Star Performer + High Potential → talent pool |
| Performance Score (low) | PIP Trigger | Below threshold → PIP can be created |
| Rewards Tier 3 (monetary) | payroll-v3 | Approved bonus → additional salary line item |
| Time Off / Errands (approved) | Attendance → payroll-v3 | Sets day-type; payroll derives deductions from attendance |
| Organization Structure (Reports To · Dept/Section Head) | Approval Workflow | Resolves approvers: "Direct Manager" = Reports To; "Department Manager" = Section Head else Department Head |
| Approval Workflow (per type/module) | Requests (Time Off · Errands) | Routes each request through the type's Approval Cycle; status reflects the current workflow step |

## Dashboard build patterns (Angular)

- Module structure → copy `manage-kpi-plans/` (models/ → services/ → components/ → popups/)
- Design system → NAS, PrimeNG, color `#0c2427`
- List views → PrimeNG `p-table`, lazy load, filter toolbar
- Modals/popups → `popups/` subfolder, DynamicDialog
- Services → HttpClient + localStorage fallback (POC mode)
- Models → interfaces only, no classes

(Mobile is native and built by the mobile team; the skill only tracks the mobile *flow/design* per module — see the two-POV rule above — not mobile build patterns.)

## Cross-cutting

- **Approval Workflow Engine** — generic reusable approval-cycle module; reused by Errands AND Time Off (per-type `Approval Workflow` field). Reference: `references/approval-workflow.md` · Memory: `project_hr_approval_workflow.md`.
- **Organization Structure** — foundation under Approval/Requests (supplies Reports To + Department/Section Heads). Reference: `references/org-structure.md` · Memory: `project_hr_org_structure.md`.
- **Permissions & Roles** — RBAC access control; the roles defined here feed the Approval Workflow "Role + Title" approver dropdown; reads People/Title/Dept from Org Structure. Reference: `references/permissions-roles.md` · Memory: `project_hr_permissions_roles.md`.
- **Sub-skills:** `confluence-doc` (Confluence page → Jira issues), `task-builder` (structured NAS HR Jira tasks).

## Memory files (point-in-time; verify against code)
`project_hr_performance_module.md` · `project_hr_kpi_plans.md` · `project_hr_payroll_v3.md` · `project_hr_competency_module.md` · `project_hr_approval_workflow.md` · `project_hr_errands_partial_day.md` · `project_hr_timeoff_module.md` · `project_hr_org_structure.md` · `project_hr_permissions_roles.md`

Figma file: `https://www.figma.com/design/Zf77LBcrb9Bt61ICdjUZGN/NAS-HR-Dashboard`
