# NAS HR — Claude Code Skills

Internal skills for the NAS HRMS project. Installs `task-builder` and `confluence-doc` for Claude Code.

## Install

### Option 1 — npx (recommended)

```bash
npx skills add karimeltalawyy/nas-hr-skills -g -y
```

### Option 2 — Inside Claude Code (if npx fails)

> **Note:** This plugin is not in the official Anthropic marketplace — you won't find it by browsing. You need to add it as a custom source first using the two commands below.

Open Claude Code and run these two commands:

```
/plugin marketplace add karimeltalawyy/nas-hr-skills
/plugin install nas-hr-skills@nas-hr-skills
```

The first command registers the GitHub repo as a source. The second installs from it.

### Option 3 — Manual via curl (no Node required)

**Mac / Linux:**
```bash
mkdir -p ~/.claude/skills/task-builder ~/.claude/skills/confluence-doc

curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/task-builder/SKILL.md \
  -o ~/.claude/skills/task-builder/SKILL.md

curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/confluence-doc/SKILL.md \
  -o ~/.claude/skills/confluence-doc/SKILL.md
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\task-builder"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\confluence-doc"

Invoke-WebRequest -Uri "https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/task-builder/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\task-builder\SKILL.md"

Invoke-WebRequest -Uri "https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/confluence-doc/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\confluence-doc\SKILL.md"
```

### Option 4 — Git clone

**Mac / Linux:**
```bash
git clone https://github.com/karimeltalawyy/nas-hr-skills.git /tmp/nas-hr-skills
cp /tmp/nas-hr-skills/skills/task-builder/SKILL.md ~/.claude/skills/task-builder/SKILL.md
cp /tmp/nas-hr-skills/skills/confluence-doc/SKILL.md ~/.claude/skills/confluence-doc/SKILL.md
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/karimeltalawyy/nas-hr-skills.git "$env:TEMP\nas-hr-skills"
Copy-Item "$env:TEMP\nas-hr-skills\skills\task-builder\SKILL.md" "$env:USERPROFILE\.claude\skills\task-builder\SKILL.md"
Copy-Item "$env:TEMP\nas-hr-skills\skills\confluence-doc\SKILL.md" "$env:USERPROFILE\.claude\skills\confluence-doc\SKILL.md"
```

---

## Update

```bash
npx skills update
```

---

## Skills

| Skill | Command | What it does |
|---|---|---|
| NAS HR Context | `/nas-hr` | Loads full project context — Performance module, KPI Plans, Payroll, design decisions, and session state |
| Task Builder | `/task-builder` | Builds structured Jira tasks following the NAS HR schema — user story, screens, modals, acceptance criteria |
| Confluence Doc | `/confluence-doc` | Converts a Confluence page into Jira Epics / Stories / Tasks |
