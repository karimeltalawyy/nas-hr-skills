# NAS HRMS — Claude Code Skills (`nas-hr-skills`)

A Claude Code **plugin** that loads full working context for the **NAS HRMS** product — one business seen from two POVs: the Angular HR **Dashboard** (the core source of truth) and the native **Mobile** app. It lets anyone on the team resume NAS HR work without re-explaining context.

> ⚠️ **Internal / confidential.** Contains NAS HR product design, business rules, and Figma references. Keep this repo **private** — share only with the NAS HR team.

## Skills included

- **`nas-hr`** — the router: prints the system map + module index, asks which module/POV you're working on, then loads that module's reference from `skills/nas-hr/references/`.
- **`task-builder`** — builds structured NAS HR Jira tasks (standard schema).
- **`confluence-doc`** — converts a NAS HR Confluence page into Jira issues.

## Install (for teammates)

In Claude Code, add this repo as a plugin marketplace, then install:

```
/plugin marketplace add karimeltalawyy/nas-hr-skills
/plugin install nas-hr-skills@nas-hr-skills
```

Then invoke the context loader with:

```
/nas-hr
```

It will ask which **module** and which **POV** (Dashboard / Mobile / Both) you're working on, then load the right reference.

**Manual alternative** (no marketplace): clone the `nas-hr` skill straight into your skills dir —

```bash
git clone https://github.com/karimeltalawyy/nas-hr-skills.git /tmp/nas-hr-skills
cp -R /tmp/nas-hr-skills/skills/nas-hr ~/.claude/skills/nas-hr
```

## Repo layout

```
.claude-plugin/      plugin + marketplace manifests
skills/
  nas-hr/            SKILL.md (router) + references/<module>.md
  task-builder/
  confluence-doc/
```

## Notes

- Reference files are **point-in-time** design notes — always verify against the current Figma file and code before asserting as fact.
- Some references cite local paths (e.g. a handoff doc on one machine) — those are pointers; the canonical detail lives in the reference files and the team's Figma / Confluence.
