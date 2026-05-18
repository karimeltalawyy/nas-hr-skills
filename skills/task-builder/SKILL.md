---
name: task-builder
description: Builds structured Jira tasks for the NAS HR system following the standard schema — user story summary, background with Figma reference, screen column tables, status enums, modals, and numbered acceptance criteria. Use whenever creating a new feature task for any NAS HR module.
---

# NAS HR — Task Builder Skill

When this skill is invoked, gather the information needed to build a fully structured Jira task following the NAS HR task schema. Ask questions one at a time, then draft the full task and create it in Jira.

---

## The Task Schema

Every NAS HR Jira task must follow this exact structure:

---

### Summary
A user story in this format:
> As a [role], I need to [action], so that [benefit].

**Roles:** HR Director / HR Administrator / Manager / Employee

---

### Background
A short paragraph explaining:
- What this feature is and where it lives in the system
- Why it exists / what problem it solves
- Design reference: `Figma → NAS HR Dashboard → [Module] (node [ID])`

---

### Screen: [Screen Name] (one section per screen/view)

A table of UI columns:

| Column | Type | Notes |
|---|---|---|
| ... | ... | ... |

**Column types:** Text / Date / Badge / Buttons / Checkbox / Number / Avatar / Select

Followed by:
- **Toolbar:** list of toolbar actions (Filter, Export, Import, Search by [field], Column settings, primary CTA button)
- **[Entity] Status Enum** (if applicable): table of status values with descriptions

---

### Modal: [Modal Name] (one section per modal/form)

List of fields:
- Field name — required/optional — type
- ...
- Actions: Cancel / Save (or Submit / Close, etc.)

---

### Acceptance Criteria

Numbered list. Each AC must be specific, testable, and cover:
- **Display:** what the user sees (columns, layout, pagination)
- **Actions:** what buttons do (modals, confirmations, status changes)
- **Validation:** required fields, inline errors, block on save
- **Business rules:** what can/cannot be done by status, role, or state
- **Success states:** notifications, table refresh, modal close
- **Edge cases:** API-level protection, disabled states, bulk actions

---

## Step-by-Step Process

### Step 1: Gather Core Info

Ask these questions **one at a time**:

1. **What module does this task belong to?** (e.g., Performance KPIs, Peer-to-Peer Recognition, 360-Degree Feedback, Payroll, etc.)
2. **What is the screen/feature name?** (e.g., OKR Library, Badge Catalog, Salary Components)
3. **Who is the primary user role?** (HR Director / HR Administrator / Manager / Employee)
4. **What does this role need to do?** (the action in the user story)
5. **What is the benefit?** (the "so that" in the user story)
6. **What is the Figma node ID?** (format: node XXXX-XXXXX — if unknown, skip)
7. **How many screens does this feature have?** (e.g., List View, Detail View, Settings)
8. **Does the feature have any modals or forms?** (Create, Edit, Filter, etc.)

---

### Step 2: Gather Screen Details

For **each screen**, ask:
- What columns does the table show? (name + type + any notes)
- What toolbar actions are available?
- Are there any status enums? If yes, list each status and what it means.

---

### Step 3: Gather Modal Details

For **each modal/form**, ask:
- What fields does it contain? (name, required/optional, type)
- What are the action buttons?

---

### Step 4: Draft Acceptance Criteria

Based on everything gathered, **auto-generate the ACs** following this pattern:

- **AC1** — List view: columns, pagination, table layout
- **AC2** — Actions column: buttons, 3-dot menu, disabled states by status
- **AC3** — Create modal: trigger, fields present
- **AC4** — Validation: required fields, inline errors, save block
- **AC5** — Success state: table refresh, modal close, notification
- **AC6** — Status transitions: confirmation dialog, badge update
- **AC7** — Edit behavior: no retroactive effect on existing assignments
- **AC8** — Delete (Draft): confirmation dialog required
- **AC9** — Delete (Published/Active): blocked in UI and at API level
- **AC10** — Toolbar: filter panel, export, import, search behavior
- **AC11** — Search: real-time filtering by name
- **AC12** — Pagination: prev, next, page numbers, Show All
- **AC13** — Date format: DD MMM YYYY throughout

Add extra ACs for any feature-specific rules (bulk actions, role restrictions, etc.).

---

### Step 5: Show Draft for Review

Print the full task in the schema format. Ask:
> "Does this look right? Tell me anything to add, change, or remove before I create it in Jira."

---

### Step 6: Create in Jira

Once the user approves:
1. Ask for the **Jira project key** (if not already known — NAS HR default: ask the user)
2. Ask for the **Epic key** to link this task to (optional)
3. Ask for **priority** (default: Medium)
4. Create the issue using `createJiraIssue`
5. Print: `✅ Task created: [KEY] [Title]` with a link to the issue

---

## Rules

- **Never skip Acceptance Criteria.** Every task must have numbered ACs covering display, actions, validation, business rules, success states, and edge cases.
- **Never skip the Figma reference.** If the node ID is unknown, write `Figma → NAS HR Dashboard → [Module] (node TBD)` as a placeholder.
- **Always use the user story format** for the Summary. Never write a plain sentence.
- **Status enums must always be a table** — never a bullet list.
- **Screen column tables must always include** Column, Type, and Notes columns.
- **Bilingual fields** (English + Arabic) are standard for any name/title field in NAS HR — include both whenever a "Name" field is part of a form.
- **Date format is always DD MMM YYYY** — include this as an AC whenever dates appear in a table.
- **Published/Active records cannot be deleted** — this is a global NAS HR rule. Include the API-level protection AC whenever a status lifecycle exists.

---

## NAS HR Context

- **Modules:** Performance (Appraisal, KPI Plans, Peer-to-Peer Recognition, 360-Degree Feedback), Payroll, Attendance, Recruitment
- **Roles:** HR Director, HR Administrator, Manager, Employee
- **Platform:** HR → Web Dashboard | Manager + Employee → Native Mobile App
- **Figma project:** NAS HR Dashboard
- **Standard toolbar actions:** Filter · Export · Import · Search by [field] · Column settings (gear icon) · [Primary CTA] button
- **Standard table columns:** Checkbox (bulk select) · [Entity fields] · Created at (DD MMM YYYY) · Status (badge) · Actions (buttons + 3-dot menu)
