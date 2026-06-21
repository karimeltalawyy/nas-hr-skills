# Competency Module (reference) — Designed (Session 2026-05-31)

A separate module from Performance. Built on top of the **Qualifications** module (the competency library). Full spec: `docs/superpowers/specs/2026-05-31-competency-module-design.md` (in project repo). See also memory `project_hr_competency_module.md`.

## Qualifications (the library) — Figma designed ✅
- HR dashboard page: catalog of qualification types with **Source chip** (System vs LMS)
- Two sources: HR creates **System** types manually; **LMS** types auto-sync from the LMS backend (backend owns sync, frontend reads one unified API; `source` flag distinguishes them)
- LMS rows are read-only (HR cannot edit/deactivate); System rows are editable
- Cannot delete — only deactivate. New Qualification popup = System types only (Name EN, Name AR, Description, Status)
- Employee profile shows assigned qualifications with source chips (System rows removable, LMS rows not)
- **Education vs Qualifications:** existing single `QualificationId` = academic education level — SEPARATE field, do not merge with competency qualifications
- Figma page: "Qualifications" (to be renamed "Competency"). Built screens: list, new-qualification popup, employee profile section

## 5 locked business decisions
1. **Binary** — employee holds a qualification or doesn't (no proficiency levels)
2. **Two tiers** — each role has **Mandatory** (blocks fit/promotion) + **Recommended** (doesn't block) qualifications
3. **Career ladder** — promotion defined by explicit ladder, not free comparison
4. **Single path** — each role has one next role up; a role belongs to one ladder only
5. **Both views** — per-employee gap view on profile + company-wide dashboard

**Core rule:** Readiness = all mandatory gaps closed. Recommended never affects readiness. Module flags readiness; promotion stays a manual HR action (change `JobId`).

**Nav:** Structure Management → Competency → { Overview (dashboard) · Qualifications (built) · Job Role Profiles · Career Ladders }

## 4 screens (Overview, Job Role Profiles, Career Ladders designed; not yet in Figma)
- **Job Role Profiles** — per role: Mandatory + Recommended qualification lists (Next Role lives in Career Ladders, not here)
- **Career Ladders** — visual builder; named tracks, drag roles into promotion chains; one role per ladder
- **Competency Overview** — dashboard: 4 stat cards (Ready Now · Close 1-gap · In Progress · No Path) + readiness table + dept filter + export
- **Employee Gap View** — profile Qualifications tab: Panel 1 Current Role Fit, Panel 2 Promotion Readiness (both read-only, auto-computed)

**New entities:** `JobRoleProfile` (jobId, mandatoryQualificationIds[], recommendedQualificationIds[]), `CareerLadder` (name, ordered steps). Computed: `RoleFitResult`, `PromotionReadinessRow` (bucket: ready|close|in_progress|no_path). Reuses `EmployeeQualification`.

## Role Staffing Search — "Fill A Role" (designed 2026-06-10, spec §5.5)
Reverse-lookup search for role SHORTAGES — start from a role, rank the whole workforce against it (opposite of the per-employee Gap View). Use case: cover a vacancy with an existing employee from ANY department.
- **Flow:** `Fill A Role` button on Competency Overview → opens a **popup** (define the search) → on Search, **navigates to a dedicated results page** (NOT inline on Overview — that caused a "sandwich" confusion).
- **Popup fields:** `Job *` (the role to staff — searchable dropdown of all jobs; Department field was DROPPED from the popup) + `Qualifications *` (seeded from the job's profile, editable chips). Button = **"Search"** (not "Add"). Manually-added quals default to Recommended.
- **Results page** lives under **Structure Management → Competency → Overview → Fill a Role** (NOT under Configurations — that was a routing bug). Shows:
  - Active-search summary + **`Edit Search`** (reopens popup pre-filled) so HR keeps context.
  - **Table ONLY** (no readiness cards): `Employee · Current Role · Department · Mandatory (n/n) · Recommended (n/n) · Match badge`. **Department column is required** — candidates come from anywhere. Match badge = Full match (green) / 1 gap / 2 gaps.
  - Ranking: **mandatory met desc → recommended met desc**. No cap, paginated + Show All. Export = filtered view. Row click → employee Qualifications tab.
- **Candidate pool:** ALL employees company-wide, EXCLUDING those already in the target job.
- **Flags only — NEVER assigns** (moving someone = manual `JobId` change), consistent with the module's core rule.
- New computed entity `RoleStaffingMatchRow` (bucket: full_match|one_gap|two_plus_gaps|no_match); API `POST /competency/role-staffing-search`.
- **Overview subtitle** must read *"Promotion readiness and role-fit across the company."* (NOT the peer-recognition "Endorsement activity…" copy — it kept reverting in Figma).

**Status:** Business design locked · data model defined · Figma in progress (Qualifications + 4 competency screens + Role Staffing Search built; pending corrections: results-page route, Department column, popup copy) · Confluence + handoff after Figma.

## Mobile POV
⏸ **Mobile flow not yet provided.** Likely employee-facing touchpoint: the Employee Gap View (Current Role Fit + Promotion Readiness, read-only) on the employee profile. Fold in the mobile PD when provided.
