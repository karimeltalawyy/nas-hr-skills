---
name: confluence-doc
description: Converts NAS HR Confluence documentation into Jira issues. Takes a Confluence page URL or uses the current conversation context, parses it into Epics / Stories / Tasks, shows the proposed breakdown for confirmation, then creates the issues in Jira. NAS HR context (cloud ID, known pages, skip rules) is built in.
---

# Confluence → Jira Skill

When this skill is invoked, execute the following steps **in order** without asking unnecessary questions upfront. Move fast — the goal is to go from doc to Jira board in one flow.

---

## Step 1: Identify the Source Document

Check in this order:

1. **Argument provided** — if the user invoked `/confluence-doc <URL or page ID>`, use that directly.
2. **Current conversation context** — if there is an active Confluence page being discussed (e.g., a recent update to a performance module page), use that page. Print: `"Using [page title] from the current conversation."`
3. **Ask** — only if neither of the above applies: `"Which Confluence page do you want to convert to Jira? Paste the URL or page ID."`

---

## Step 2: Fetch the Page Content

Use `getConfluencePage` with `contentFormat: "html"` and `cloudId: "49d0fe33-339b-4873-9930-c4c6250918ca"` (NAS HR default cloud).

If the page URL is from a different Confluence instance, extract the cloud ID from the URL or ask the user.

If the page is already loaded in the current conversation context (content was fetched earlier in the session), skip the fetch and use what you have.

---

## Step 3: Parse the Content into Jira Structure

Analyze the page and propose a Jira issue breakdown. Use this mapping:

| Confluence Element | Jira Issue Type |
|---|---|
| The page itself | **Epic** — one Epic per page |
| Each numbered section (h2) | **Story** — one Story per major section |
| Each Feature N subsection (h3) | **Task** — one Task per feature |
| Validation Guardrails section | **Tasks** — one Task per guardrail rule |
| "Already Live" items | Skip — do not create issues for things already done |

**NAS HR module pages follow this pattern:**
- Section 1 (Executive Summary) → skip (context only, not a build task)
- Section 2 (Glossary) → skip (reference only)
- Sections 3–N (features, flows, config, roles) → Stories + Tasks
- Validation Guardrails → Tasks under the relevant Story

**Naming convention for issues:**
- Epic: `[Module Name] — [Page Title]`
- Story: `[Module] — [Section Title]`
- Task: `[Module] — [Feature/Rule Name]`

---

## Step 4: Show Proposed Breakdown

Print the full proposed issue tree before creating anything. Format:

```
Epic: [title]
  Story: [section title]
    Task: [feature/rule]
    Task: [feature/rule]
  Story: [section title]
    Task: [feature/rule]
    ...
```

Then ask:
> "Does this breakdown look right? You can tell me to skip any section, merge stories, or change priorities. Type 'yes' to create all, or give me adjustments."

---

## Step 5: Gather Jira Target Info

Before creating, confirm:
- **Project key** — ask if not known from context. NAS HR default: ask the user since the project key may vary.
- **Epic link** — if there is already a parent Epic in Jira, ask for its key so Stories can be linked. Otherwise, create the Epic first.
- **Priority** — default to `Medium`. Ask only if the user has a preference.
- **Assignee** — skip by default. Only set if the user specifies.

---

## Step 6: Create the Issues

Create in this order:
1. Epic first
2. Stories (linked to the Epic)
3. Tasks (linked to their parent Story)

For each issue, include in the **description**:
- A short plain-English summary of what this issue is about (1–3 sentences)
- A link back to the source Confluence page: `"Full spec: [Confluence page URL]"`
- For Tasks: the specific rule or feature text from the Confluence doc

Use `createJiraIssue` for each item. Print confirmation as you go:
```
✅ Epic created: [key] [title]
✅ Story created: [key] [title]
  ✅ Task: [key] [title]
  ...
```

---

## Step 7: Summary

When all issues are created, print a final summary:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Confluence → Jira — Done
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Source:    [Confluence page title + URL]
Project:   [Jira project key]
Created:
  1 Epic
  X Stories
  X Tasks
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## NAS HR Context

When working with NAS HR Performance module pages, apply these defaults automatically:

- **Confluence cloud ID:** `49d0fe33-339b-4873-9930-c4c6250918ca`
- **Known pages:**
  - Performance Module (overview): `139952129`
  - Peer-to-Peer Recognition: `139886594`
  - 360-Degree Feedback: `139984899`
- **Skip sections:** Executive Summary, Glossary, Role Permission Summary (reference only — not build tasks)
- **Must-build sections:** All Feature N subsections, all Validation Guardrails

---

## Rules

- **Never create duplicate issues.** Before creating, ask if there is already a sprint or board where similar issues might exist.
- **Never skip Validation Guardrails.** These are the most critical backend rules — every guardrail must become a Task.
- **Always link back to Confluence.** Every issue description must include the source page URL.
- **Already Live = skip.** If a Confluence section is marked "Live" or "Already Built", do not create a Jira issue for it.
