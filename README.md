# NAS HR — Claude Code Skills

Internal skills for the NAS HRMS project. Three skills: `/nas-hr`, `/task-builder`, `/confluence-doc`.

---

## Install — All 3 Skills

### Option 1 — npx (recommended)

```bash
npx skills add karimeltalawyy/nas-hr-skills -g -y
```

### Option 2 — Manual via curl ✅ most reliable, no Node required

**Mac / Linux — one command:**
```bash
mkdir -p ~/.claude/skills/nas-hr ~/.claude/skills/task-builder ~/.claude/skills/confluence-doc && \
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/nas-hr/SKILL.md -o ~/.claude/skills/nas-hr/SKILL.md && \
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/task-builder/SKILL.md -o ~/.claude/skills/task-builder/SKILL.md && \
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/confluence-doc/SKILL.md -o ~/.claude/skills/confluence-doc/SKILL.md
```

**Windows (PowerShell) — one command:**
```powershell
@("nas-hr","task-builder","confluence-doc") | ForEach-Object { New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\$_" | Out-Null; Invoke-WebRequest -Uri "https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/$_/SKILL.md" -OutFile "$env:USERPROFILE\.claude\skills\$_\SKILL.md" }
```

### Option 3 — Git clone

**Mac / Linux:**
```bash
git clone https://github.com/karimeltalawyy/nas-hr-skills.git /tmp/nas-hr-skills && \
cp -r /tmp/nas-hr-skills/skills/nas-hr ~/.claude/skills/ && \
cp -r /tmp/nas-hr-skills/skills/task-builder ~/.claude/skills/ && \
cp -r /tmp/nas-hr-skills/skills/confluence-doc ~/.claude/skills/
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/karimeltalawyy/nas-hr-skills.git "$env:TEMP\nas-hr-skills"
@("nas-hr","task-builder","confluence-doc") | ForEach-Object { Copy-Item "$env:TEMP\nas-hr-skills\skills\$_\SKILL.md" "$env:USERPROFILE\.claude\skills\$_\SKILL.md" }
```

---

## Update — Get Latest Skills

Same as install — just re-run your chosen option above. It overwrites the existing files with the latest version.

**Mac / Linux — one command:**
```bash
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/nas-hr/SKILL.md -o ~/.claude/skills/nas-hr/SKILL.md && \
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/task-builder/SKILL.md -o ~/.claude/skills/task-builder/SKILL.md && \
curl -L https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/confluence-doc/SKILL.md -o ~/.claude/skills/confluence-doc/SKILL.md
```

**Windows (PowerShell):**
```powershell
@("nas-hr","task-builder","confluence-doc") | ForEach-Object { Invoke-WebRequest -Uri "https://raw.githubusercontent.com/karimeltalawyy/nas-hr-skills/main/skills/$_/SKILL.md" -OutFile "$env:USERPROFILE\.claude\skills\$_\SKILL.md" }
```

---

## Skills

| Skill | Command | What it does |
|---|---|---|
| NAS HR Context | `/nas-hr` | Loads full project context — Performance module, KPI Plans, Payroll, design decisions, and session state |
| Task Builder | `/task-builder` | Builds structured Jira tasks following the NAS HR schema — user story, screens, modals, acceptance criteria |
| Confluence Doc | `/confluence-doc` | Converts a Confluence page into Jira Epics / Stories / Tasks |
