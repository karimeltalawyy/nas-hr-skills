---
name: nas-hr
description: Full context loader for the NAS HRMS system — Performance module, KPI Plans, Payroll, and all related Angular modules. Loads what's built, what's designed, the planned scope, and session state so any NAS HR work can resume without re-explaining context.
---

# NAS HRMS — Performance Module Skill

When this skill is invoked, do the following **in order** without asking the user anything first:

1. Print the static briefing below
2. Run live health checks
3. Print ready state
4. Wait for user's instruction

---

## 1. Static Briefing

### Project Overview

Angular 17 HRMS (NAS design system, PrimeNG, color `#0c2427`). Production app. Mobile app is **native** (not Flutter).

- **HR** → web dashboard only — configures, approves, monitors everything
- **Managers** → native mobile app — rates team, sends recognition, manages PIPs
- **Employees** → native mobile app — attendance, appraisals, KPIs, peer recognition

**Project path:** `/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features/`

---

### What's Already Built

#### Appraisal (`manage-performance/`) — 19 components
| What | Detail |
|---|---|
| Question Sets | Reusable sets of typed questions: Rating, Yes/No, Short Answer, Multiple Choice |
| Templates | Wire question sets into a named template |
| Run Appraisal | HR assigns template to departments → manager rates employees on mobile → employee accepts or rejects |
| Flow type | **Manager-review only** (not self or peer yet) |
| Routing | Appraisals → Details → Department → Employee Questions/Scoring |

Key models: `models/appraisals.interface.ts`, `models/question.model.ts`
Key services: `services/performance.service.ts`, `services/appraisal-template.service.ts`, `services/question-set.service.ts`

#### KPI Plans (`manage-kpi-plans/`) — 8 components
| What | Detail |
|---|---|
| OKR Library | High-level objectives: Business Sustainability, Growth, etc. |
| KPI Library | Name, unit, type (Quantitative or Qualitative) |
| KPI Plans | Wire OKRs → assign KPIs to OKRs → set weight, cap, gate per KPI item |
| KPI Assignments | Assign plan to employees — includes `bonusAmount` field |
| Achievement Entry | Record achieved values per review period |
| Analytics screen | Exists — details TBD |

Key models: `models/kpi-plan.interface.ts`, `models/kpi-definition.interface.ts`
Key services: `services/kpi-plan.service.ts`, `services/kpi-assignment.service.ts`, `services/kpi-achievement.service.ts`

> **Important:** `bonusAmount` on `KpiAssignment` is intentionally separate from the Rewards module. KPI payouts and Rewards bonuses are independent systems — do not merge them.

#### Rewards — Tier 2 & Tier 3 (Already Built)
| Tier | Type | Who | Flow |
|---|---|---|---|
| Tier 2 | Manager bonus / award | Manager → Employee | Manager sends from mobile directly |
| Tier 3 | Monetary award with approval | Manager nominates → HR approves | Mobile nomination → dashboard approval → payroll line item |

---

### Rewards — Tier 1: Peer-to-Peer Badges (FULLY DESIGNED — TO BUILD)

This is the **only remaining gap** in the Rewards cycle. Tiers 2 & 3 are built. Tier 1 closes the full recognition loop by enabling any employee to recognize any colleague — not just top-down.

#### The Endorsement Model

Peer-to-peer works on an **endorsement threshold** — not a simple one-tap badge send.

- Any employee can **endorse** any colleague for a badge type
- When a colleague reaches **50 endorsements** for a specific badge type AND endorsements came from **at least N different departments** (HR sets N, default = 3) → the badge is **officially earned**
- One person can endorse a colleague **once per badge type only** (no duplicates)
- Each employee has a **monthly send limit** (HR sets, default = 10 endorsements/month)
- Employees **cannot endorse themselves**

#### Privacy Rules — Who Sees X/50 Progress

| Role | Can see progress counter (X/50)? |
|---|---|
| The employee themselves | ✅ Own profile only |
| Their direct manager | ✅ Team view (supervisory access) |
| HR | ✅ Dashboard (admin access) |
| Any other colleague / peer | ❌ Never |

The progress counter is **never shown in the social feed**. The feed shows individual endorsement actions only.

#### What Happens When Badge is Earned

- Badge permanently pinned to employee profile
- Featured post on Recognition Wall: "Sara just earned the Team Player badge!"
- Push notification sent to employee
- Optional company-wide announcement (HR controls this setting)

#### Social Feed — Recognition Wall

- Every endorsement action creates a post in the feed (sender + recipient + badge type)
- Earned badge events get a featured/highlighted post
- Visible to everyone (mobile + dashboard)
- HR can: pin, hide, or flag any post
- Progress counters (X/50) are **never shown** in the feed

#### Performance Score Integration

Only **officially earned badges** count toward the Recognition Score — not raw endorsement counts.

```
Recognition Score = Σ (earned badge weight × badges earned)
                   normalized against department average

Overall Performance Score (HR sets weights):
  Appraisal Score     50%
  KPI Achievement     40%
  Recognition Score   10%   ← peer-to-peer feeds here
```

---

#### Data Model — HR Inputs (Dashboard)

**Entity 1: BadgeType**
| Field | Type | Notes |
|---|---|---|
| `name` | text | e.g., "Team Player" |
| `nameAr` | text | Arabic name |
| `description` | text | What this badge means |
| `icon` | image upload | Badge icon |
| `weight` | number (1–10) | Points value — feeds recognition score |
| `endorsementThreshold` | number | HR sets per badge — how many endorsements are needed to officially earn it (no system default) |
| `isActive` | boolean | Active / archived |

**Entity 2: RecognitionSettings** (one global record)
| Field | Type | Notes |
|---|---|---|
| `minDepartmentsRequired` | number | HR sets — minimum number of departments that must contribute endorsements (no system default) |
| `monthlyEndorsementLimit` | number | HR sets — maximum endorsements an employee can send per calendar month (no system default) |
| `earnedBadgeWeightInScore` | number % | Default 10% of overall performance |
| `announceOnEarn` | boolean | Company-wide post on badge earn |
| `featureEnabled` | boolean | On/Off switch |

**Recognition Wall moderation inputs:**
- Filter: department, badge type, date range
- Actions: pin / hide (+ optional reason) / flag (dropdown: Inappropriate / Spam / Other)

HR does NOT manually grant badges — pure peer-to-peer only, no bypassing the threshold.

---

#### Data Model — Manager Inputs (Mobile)

**Send Endorsement** (only form the manager fills):
| Field | Type | Notes |
|---|---|---|
| `recipientEmployeeId` | select / search | Any employee in the company |
| `badgeTypeId` | select | From HR's active badge catalog |
| `message` | text (optional) | Max 200 chars |

System auto-fills: `senderId`, `senderDepartmentId`, `createdAt`

**Team view** (read + filter only):
- Filters: period (month/quarter), employee, badge type
- Sees per team member: earned badges, endorsements received, **X/50 progress per badge**, which departments endorsed so far, endorsements sent this period

---

#### Data Model — Employee Inputs (Mobile)

**Send Endorsement** (identical fields to manager):
| Field | Type | Notes |
|---|---|---|
| `recipientEmployeeId` | select / search | Any colleague (any dept) |
| `badgeTypeId` | select | From HR's active badge catalog |
| `message` | text (optional) | Max 200 chars |

**Own profile** (read-only, private):
- Earned badges (name + date earned + endorsement count + dept count)
- In-progress badges with **X/50 counter** (private — only they see this)
- Which departments have endorsed so far (without specific names)
- Endorsements sent this month vs. monthly limit

**Recognition Feed** (read-only):
- All endorsement posts (sender + recipient + badge)
- Earned badge announcements
- Cannot see anyone else's X/50 progress

---

#### Full Role-Permission Summary

| Entity | HR | Manager | Employee |
|---|---|---|---|
| `BadgeType` | ✅ Creates & manages | ❌ | ❌ |
| `RecognitionSettings` | ✅ Configures | ❌ | ❌ |
| `Endorsement` | ✅ Reads all | ✅ Sends + reads team | ✅ Sends + reads own |
| `X/50 progress` | ✅ Sees all | ✅ Direct reports only | ✅ Own only |
| Recognition Wall | ✅ Reads + moderates | ✅ Reads | ✅ Reads |
| Analytics | ✅ Full company | ✅ Team only | ❌ |

---

### 360-Degree Feedback — Fully Designed

#### Core Concept
Not a new module — an **upgrade to the existing appraisal template system**. Backwards compatible: if only Manager rater type is checked, it behaves exactly like the current appraisal.

#### Template Level — New Fields (Step 1 before adding questions)
| New Field | Type | Notes |
|---|---|---|
| `raterTypes` | checkboxes | Manager / Self / Peer / Upward |
| `maxPeerNominations` | number | Max peers employee can suggest (default 3) |
| `peerSelectionDeadlineDays` | number | Days after launch for peer selection |

#### Question Level — New Field (added to existing Question model)
Each question gets a `visibilityType` enum:
| Value | Meaning |
|---|---|
| `both_sides` | Reviewer AND employee both answer this question |
| `reviewee_only_visible` | Employee answers — reviewer can see the answer |
| `reviewee_only_hidden` | Employee answers — reviewer cannot see the answer |
| `reviewer_only_visible` | Reviewer answers — employee can see the answer |
| `reviewer_only_hidden` | Reviewer answers — employee cannot see the answer |

Reference: Teamflect question-set model (confirmed by user as the right approach).

#### Appraisal Run — New Fields
| New Field | Type | Notes |
|---|---|---|
| `peerSelectionDeadline` | date | Deadline for peer nominations |
| `submissionDeadline` | date | Deadline for all reviewers to submit |
| `managerScoreWeight` | number % | e.g., 50% |
| `selfScoreWeight` | number % | e.g., 20% |
| `peerScoreWeight` | number % | e.g., 30% |
| Upward score | — | Informational only — not included in employee's final score |

#### Workflow — 6 Statuses
`Draft → Active → Peer Selection → Collection → Calibration → Closed`
- **Draft**: HR creates template + appraisal run, sets deadlines
- **Active**: All parties notified (push). Manager rates employees. Employees start self-assessment.
- **Peer Selection**: Employee suggests up to 3 peers on mobile → manager approves/modifies
- **Collection**: All parties submit forms. HR sees real-time completion per reviewer.
- **Calibration**: Manager reviews aggregated scores, adds calibration notes, can adjust final score
- **Closed**: Final report published, scores locked. Employee receives result on mobile.

#### Peer Selection Flow
- Employee nominates up to `maxPeerNominations` colleagues (any dept)
- Manager approves / modifies the list
- System sends push notification to approved peers
- Peer responses are anonymous to the reviewed employee (names visible to manager + HR only)

#### Visibility Rules — Who Sees What
| Data | Employee | Manager | HR |
|---|---|---|---|
| Own self-assessment | ✅ | ✅ | ✅ |
| Manager score | ✅ (after closed) | ✅ | ✅ |
| Peer scores — aggregated | ✅ no names | ✅ with names | ✅ |
| Individual peer responses | ❌ | ✅ | ✅ |
| Upward scores (employee rates manager) | ❌ | ❌ aggregated only | ✅ |
| Final weighted score | ✅ | ✅ | ✅ |

#### Data Inputs Per Role
**HR (Dashboard):** Template rater types + question visibilityType + appraisal run weights + deadlines + completion monitoring
**Manager (Mobile):** Rate employees (existing) + approve peer list + calibration notes + optional score override
**Employee (Mobile):** Self-assessment form + nominate peers + fill peer feedback form (when nominated) + upward review (if enabled)

#### Role-Permission Table
| Action | HR | Manager | Employee |
|---|---|---|---|
| Create template + enable rater types | ✅ | ❌ | ❌ |
| Set score weights | ✅ | ❌ | ❌ |
| Rate employee | ❌ | ✅ | ❌ |
| Self-assessment | ❌ | ❌ | ✅ |
| Nominate peers (suggests) | ❌ | ❌ | ✅ |
| Approve peer list | ❌ | ✅ | ❌ |
| Fill peer feedback | ❌ | ❌ | ✅ (when nominated) |
| Upward review | ❌ | ❌ | ✅ (if template enables it) |
| Calibration + publish | ❌ | ✅ | ❌ |
| See final results | ✅ Full | ✅ Team | ✅ Own only |

---

### Module Roadmap — Full Scope

| Priority | Module | Status | Notes |
|---|---|---|---|
| 🔴 **Phase 1** | **Rewards — Peer-to-Peer Badges** | ✅ Designed, ready to build | See full design above |
| 🔴 **Phase 1** | **360-Degree Feedback** | ✅ Designed, ready to build | See full design above |
| ⏸ Deferred | **Calibration** | 📋 Next phase | Needs 360 data flowing first |
| ⏸ Deferred | **Performance Improvement Plan (PIP)** | 📋 Next phase | Needs calibration data |
| ⏸ Future | **Succession Planning** | 📋 Future | Needs all modules running |
| ⏸ Future | **Competency Framework** | 📋 Future | Needs all modules running |

---

### Integration Points

| From | To | What flows |
|---|---|---|
| Peer-to-Peer Badges | Performance Score | Earned badge weight → Recognition Score (10% of overall) |
| KPI Achievement | Performance Score | KPI score → 40% of overall |
| Appraisal / 360 | Performance Score | Appraisal score → 50% of overall |
| Performance Score | Calibration | Feeds Performance axis of 9-box grid |
| Calibration | Succession Planning | Star Performer + High Potential → talent pool |
| Performance Score (low) | PIP Trigger | Below threshold → PIP can be created |
| Rewards Tier 3 (monetary) | payroll-v3 | Approved bonus → additional salary line item |

---

### Mobile App Design — Peer-to-Peer Badges (Session 2026-05-19)

#### Recognition Wall Screen

- **Screen title:** "Peer Recognition Wall"
- **Two tabs:**
  - Tab 1: "All Peer Recognitions" — community feed (default)
  - Tab 2: "My Details" — personal stats (not yet designed)
- **Filter chips:** All · My Team · [Department names] — no "Earned" chip (the ★ Earned pill on cards handles that distinction)
- **FAB (+):** Bottom right — triggers Send Endorsement 3-step bottom sheet
- **No points/score in header** — personal score only visible inside My Details tab

#### Feed — Earned Badge Post Card (designed ✅)

| Element | Content |
|---|---|
| Top row | ★ Earned pill (green) + timestamp |
| Content | Badge icon + employee photo + name (truncated with ...) |
| Sub-line | Badge name (teal/brand color) |
| Stats | "X endorsements · Y departments" |
| Quote | Auto-generated text based on endorsement data |
| Bottom row | "Details →" link + reaction counts (🚀 🔥 ❤️) + ··· menu |
| ··· menu | Employee: Report only · HR: Pin / Hide / Flag |

#### Feed — Regular Endorsement Post Card
⏸ Not yet designed — "Yusuf endorsed Ahmed for Team Player" simplified format

#### My Details Tab
⏸ Not yet designed — will show: earned badges + in-progress X/50 bars + dept breakdown + monthly send limit

#### Send Endorsement — 3-Step Bottom Sheet (designed ✅)

| Step | Title | Key Content |
|---|---|---|
| 1 | "Who do you want to recognize?" | Employee dropdown (single select) → selected peer card: photo + name + dept + title (no employee ID) · Next disabled until selected |
| 2 | "Choose a Badge" | Badge list: icon + name + description + points pill + radio select · Search field at top · Next |
| 3 | "Add a Comment" | Summary card: badge icon + "You are endorsing" + employee name + badge name · Optional comment field ("Why do they deserve this badge?") · Submit endorsement |

**All steps:** Step indicator `● ● ○`, back arrow on steps 2 & 3, X closes entire sheet
**Phase 1:** Single employee only — no multi-select
**Self-endorsement:** Employee excluded from the list entirely (not shown, not greyed)

---

### Design Session Status (as of 2026-05-19)

- ✅ **Peer-to-Peer Badges** — data model complete + mobile Recognition Wall + Send Endorsement flow designed
- ✅ **360-Degree Feedback** — fully designed, data model complete for all 3 roles
- ⏸ Calibration, PIP, Succession, Competency — deferred to next phase
- **Phase 1 scope locked:** Peer-to-Peer Badges + 360-Degree Feedback only
- **Remaining mobile screens to design:** Regular endorsement post card · My Details tab · Empty states
- Spec document: `docs/superpowers/specs/2026-05-17-performance-module-full-scope.html`
- Figma file: `https://www.figma.com/design/Zf77LBcrb9Bt61ICdjUZGN/NAS-HR-Dashboard` → 🥅 Performance page
- Memory files:
  - `~/.claude/projects/-Users-krim/memory/project_hr_performance_module.md`
  - `~/.claude/projects/-Users-krim/memory/project_hr_kpi_plans.md`
  - `~/.claude/projects/-Users-krim/memory/project_hr_payroll_v3.md`

---

## 2. Live Health Checks

Run these and show results:

```bash
# 1. Project exists and feature count
ls "/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features/" 2>/dev/null

# 2. Performance module component count
find "/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features/manage-performance" -name "*.component.ts" | wc -l 2>/dev/null

# 3. KPI module component count
find "/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features/manage-kpi-plans" -name "*.component.ts" | wc -l 2>/dev/null

# 4. Peer-to-peer module (if started)
find "/Users/krim/Downloads/HR-front-end-Development 2/src/app/newTheme/features" -name "*recognition*" -o -name "*badge*" -o -name "*endorsement*" 2>/dev/null | wc -l

# 5. Last 3 commits
git -C "/Users/krim/Downloads/HR-front-end-Development 2" log --oneline -3 2>/dev/null

# 6. Existing performance specs
find "/Users/krim/Downloads/HR-front-end-Development 2/docs/superpowers/specs" -name "*performance*" 2>/dev/null
```

Display like this:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HR Performance Module — Health Check
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Project:              ✅ Found / ❌ Missing
Appraisal components: X files
KPI components:       X files
Peer-to-peer files:   X files (0 = not started yet)
Last commits:
  <hash> <message>
  ...
Existing specs:       <file names>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 3. Ready State

After the health check, say:

> **HR Performance ready.** Peer-to-peer badges are fully designed (data model done for all 3 roles). Next up: 360-Degree Feedback design, or jump straight to building peer-to-peer. What do you want to work on?

You are now ready to:
- Continue the 360-Degree Feedback design session
- Build the peer-to-peer badges Angular module (follow existing `manage-kpi-plans/` pattern: `models/` → `services/` → `components/` → `popups/`)
- Design any other planned module (Calibration, PIP, Succession, Competency)
- Integrate with existing Appraisal or KPI Plans code

Go directly to the right file without asking for context.
