# NAS HRMS — Context Skill (`nas-hr`)

A Claude Code **skill** that loads full working context for the **NAS HRMS** product — one business seen from two POVs: the Angular HR **Dashboard** (the core source of truth) and the native **Mobile** app. It lets anyone on the team resume NAS HR work without re-explaining context.

> ⚠️ **Internal / confidential.** Contains NAS HR product design, business rules, and Figma references. Keep this repo **private** — share only with the NAS HR team.

## What's inside

- `SKILL.md` — the router: prints the system map + module index, asks which module/POV you're working on, then loads that module's reference.
- `references/` — one file per module (Dashboard POV + Mobile POV), e.g. `time-off.md`, `approval-workflow.md`, `org-structure.md`, `permissions-roles.md`, `competency.md`, `errands-partial-day.md`, `peer-recognition.md`, `360-feedback.md`, `built-modules.md`.
- `confluence-doc/` and `task-builder/` — bundled sub-skills (Confluence page → Jira issues; structured NAS HR Jira tasks).

## Install (for teammates)

Clone into your Claude Code skills directory:

```bash
git clone <this-repo-url> ~/.claude/skills/nas-hr
```

Then in Claude Code, invoke it with:

```
/nas-hr
```

It will ask which **module** and which **POV** (Dashboard / Mobile / Both) you're working on, then load the right reference.

## Notes

- Reference files are **point-in-time** design notes — always verify against the current Figma file and code before asserting as fact.
- Some references cite local paths (e.g. a handoff doc on one machine) — those are pointers; the canonical detail lives in the reference files and the team's Figma / Confluence.
