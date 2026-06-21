# Permissions & Roles (reference) — 🟢 DESIGN LOCKED + Figma v1 built (2026-06-20)

Access control for the HR dashboard. **RBAC (action-gating) only** — roles gate what a user can *do*, not which records they see. Spec: `docs/superpowers/specs/2026-06-20-permissions-roles-design.md` (write to HR-front-end repo when on that machine — repo not on disk 2026-06-20). Memory: `project_hr_permissions_roles.md`.

## Figma (dashboard screens built 2026-06-20, cloned from Org Structure shell)
File: NAS HR Dashboard (Zf77LBcrb9Bt61ICdjUZGN). **Page "Permissions & Roles"** (id 6055:3417), 4 frames left→right:
- **P&R 1 · Roles List** 6055:3418 (x0) — tabs Roles/User Access, table Role Name·Modules Granted·Users Assigned·Actions, New Role CTA.
- **P&R 2 · Create Role** 6056:4126 (x1540) — Role Name EN/AR inputs + **permission matrix** (modules × View/Create/Edit/Approve/Configure; greyed `—` = unsupported), Cancel/Save Role.
- **P&R 3 · User Access** 6056:3776 (x3080) — Name(avatar)·Job Title·Department·Roles·Actions; no create CTA (people come from Org Structure).
- **P&R 4 · Manage Access dialog** 6056:4564 (x4620) — assigned-role chips (Employee base locked + removable roles + "+ Add role"), read-only **effective-permissions preview** with legend (dark=granted by role, teal ring=per-person override).
Sidebar active item = **System Users**. figma-console MCP: REST token expires — use plugin capture (figma_capture_screenshot); batch edits in small chunks (large execute batches time out).

## Locked design (2026-06-20)
- **RBAC only.** No data scoping / row-level visibility in v1 (candidate v2).
- **Permission = (module × action).** Actions: **View · Create · Edit · Approve · Configure**. Unsupported cells greyed (e.g. Org Structure has no Approve).
- **Roles fully dynamic** — created/edited/deleted from dashboard. Nothing hardcoded or locked. Reusable named bundles (matrix). The old "HR Director/Admin/Manager/Employee" seed roles are NOT special — just examples.
- **Roles stack (union).** `effective = base self-service + union(roles) + per-person override grants`.
- **Per-person overrides = grant-only** (add extra to one user; never remove — to reduce, adjust roles).
- **Base self-service** automatic for every user, not a role, can't be removed (my profile/requests/attendance). User with no role sees self-service only.
- **Dashboard renders only modules where user has ≥ View** (+ self-service). Others hidden, not disabled.
- **"System Admin" = whoever holds *Configure* on the Permissions & Roles module itself** (self-referential). One bootstrap user seeded; protect the last admin.
- **Module registry** drives matrix rows → future modules appear automatically.

## Dashboard POV (designed — two tabs, copy `manage-kpi-plans/` pattern)
- **Tab 1 — Roles:** `p-table` list (Role Name · Modules granted · Users assigned · Edit/Delete) + New Role. Create/Edit = bilingual name (EN+AR) + permission matrix (modules × 5 actions, per-row select-all, greyed unsupported).
- **Tab 2 — User Access:** `p-table` (Name · Job Title · Department · Roles chips · Manage). **People/Title/Department read from Org Structure (display+filter only — assignment happens ONLY here, never in Org Structure).** Manage Access dialog = (1) multi-select roles, (2) **effective-permissions merged preview** (role cells locked), (3) grant-only per-person overrides (visually distinct).

## Connection to Approval Cycle (the seam)
- Approval engine's **"Role + Title" approver dropdown is fed by the roles created here** — one shared Role object: permission-bundle (this module) + approver-group (Approval Workflow).
- **Routing** (Approval Workflow: who is asked) vs **capability** (this module's Approve action: who can actually act/see). Role+Title step resolves = users assigned that Role + matching Title (backend any-one-of).
- **Consistency: soft warning** (not block) if an approver role lacks Approve on that module.
- Guardrails: deleting a role in use → block/warn (N users / M workflows); renaming → propagates (same object); self-approval already handled by engine (auto-skip).

## Mobile POV (deferred)
Config is dashboard-only. Mobile simply **honors the same effective permissions** (manager inbox, visible modules). Fold in when mobile flow handed over.

## Out of scope v1 (candidate v2)
Data scoping/row-level · revoke-overrides · auto-assign roles by org position · per-field perms · mobile config UI.

## Open for Figma/build
Final module list + per-module supported-action set (vs live feature registry) · matrix visual treatment (role vs override vs greyed cells) · preview read-only vs editable · create Jira tickets + memory when build starts.
