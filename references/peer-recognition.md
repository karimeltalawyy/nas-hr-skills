# Rewards — Tier 1: Peer-to-Peer Badges (reference)

**FULLY DESIGNED — TO BUILD.** This is the only remaining gap in the Rewards cycle. Tiers 2 & 3 are built. Tier 1 closes the full recognition loop by enabling any employee to recognize any colleague — not just top-down.

## The Endorsement Model

Peer-to-peer works on an **endorsement threshold** — not a simple one-tap badge send.

- Any employee can **endorse** any colleague for a badge type
- When a colleague reaches **50 endorsements** for a specific badge type AND endorsements came from **at least N different departments** (HR sets N, default = 3) → the badge is **officially earned**
- One person can endorse a colleague **once per badge type only** (no duplicates)
- Each employee has a **monthly send limit** (HR sets, default = 10 endorsements/month)
- Employees **cannot endorse themselves**

## Privacy Rules — Who Sees X/50 Progress

| Role | Can see progress counter (X/50)? |
|---|---|
| The employee themselves | ✅ Own profile only |
| Their direct manager | ✅ Team view (supervisory access) |
| HR | ✅ Dashboard (admin access) |
| Any other colleague / peer | ❌ Never |

The progress counter is **never shown in the social feed**. The feed shows individual endorsement actions only.

## What Happens When Badge is Earned

- Badge permanently pinned to employee profile
- Featured post on Recognition Wall: "Sara just earned the Team Player badge!"
- Push notification sent to employee
- Optional company-wide announcement (HR controls this setting)

## Social Feed — Recognition Wall

- Every endorsement action creates a post in the feed (sender + recipient + badge type)
- Earned badge events get a featured/highlighted post
- Visible to everyone (mobile + dashboard)
- HR can: pin, hide, or flag any post
- Progress counters (X/50) are **never shown** in the feed

## Performance Score Integration

Every endorsement received counts toward the Recognition Score — not just earned badges. Earning a badge is a social achievement (profile pin, wall post, notification) separate from scoring.

```
1 endorsement received = 1 point (simple, no multipliers)

Example:
  50 endorsements for Team Player   = 50 pts
  30 endorsements for Super Growth  = 30 pts
  Total points                      = 80 pts

Recognition Score = (total points / maxRecognitionPoints) × 10%
  e.g. (80 / 500) × 10% = 1.6% of overall score

Overall Performance Score (HR sets weights):
  Appraisal Score     50%
  KPI Achievement     40%
  Recognition Score   10%   ← peer-to-peer feeds here
```

**Key rules:**
- 1 endorsement = 1 point, regardless of badge type or weight
- Badge weight (1–10) is for HR catalog ranking/display only — NOT a points multiplier
- Collecting points WITHOUT earning any badge is normal and valid
- Earning a badge does NOT add bonus points — social milestone only (profile pin, wall post, notification)
- No multipliers of any kind
- `maxRecognitionPoints` is configured by HR in RecognitionSettings

---

## Data Model — HR Inputs (Dashboard)

**Entity 1: BadgeType**
| Field | Type | Notes |
|---|---|---|
| `name` | text | e.g., "Team Player" — shown on mobile picker, wall post, profile |
| `nameAr` | text | Arabic name — shown to Arabic language users on mobile |
| `description` | text | What this badge means — shown in mobile badge picker step 2 |
| `icon` | preset picker | Select from system preset icon library — shown everywhere on mobile |
| `endorsementThreshold` | number | Per-badge — how many endorsements needed to earn this specific badge |
| `minDepartmentsRequired` | number | Per-badge — how many of the selected departments must contribute |
| `requiredDepartments` | array | Per-badge — specific departments HR selects. Must not exceed minDepartmentsRequired count |
| `status` | enum | Active / Inactive — Active = visible in mobile picker, Inactive = hidden |

**No `weight` field** — removed entirely. 1 endorsement = 1 point, no multipliers. Badge weight has no business purpose.

**Entity 2: RecognitionSettings** (one global record)
| Field | Type | Notes |
|---|---|---|
| `monthlyEndorsementLimit` | number | Max endorsements an employee can SEND per calendar month. Resets 1st of each month |
| `maxRecognitionPoints` | number | Points ceiling for full recognition score. Formula: (total pts / maxRecognitionPoints) × recognitionScoreWeight% |
| `recognitionScoreWeight` | number % | % contribution to overall performance score. Default 10% |
| `announceOnEarn` | boolean | Company-wide featured post on Recognition Wall when badge is earned |

**Recognition Score is tied to the appraisal cycle period** — only endorsements received within the appraisal cycle start/end date count toward the score. No separate points period setting needed.

**Hard business rules (enforced in backend, not configurable):**
- Employee cannot endorse themselves — excluded from picker entirely
- Employee cannot endorse same person for same badge twice — system blocks it
- Earned badges are permanent — nothing HR does retroactively removes them
- Edit and Set Inactive only affect future behavior — historical data is never changed
- Inactive badge: hidden from picker, no new endorsements, in-progress employees freeze at current count
- When setting a badge to Inactive: show warning dialog if any employees have in-progress endorsements — "X employees are working toward this badge. Setting it to Inactive will freeze their progress. Are you sure?" → Cancel | Set Inactive. HR proceeds knowing the impact.
- No "Winding Down" state — only Active / Inactive. Simpler is better.
- No "In Progress" column in badge table — warning dialog is sufficient

HR does NOT manually grant badges — pure peer-to-peer only, no bypassing the threshold.

**Backend data requirements:**
- `BadgeEarnEvent` table with `earnedAt` timestamp — needed for stat cards + chart
- `firstEndorsementReceivedAt` per (employeeId, badgeTypeId) — needed for Avg Time to Earn
- Recognition Score period = appraisal cycle period (uses appraisal start/end dates)
- Chart API: `GET /api/recognition/chart?period=THIS_YEAR|LAST_6_MONTHS|LAST_30_DAYS&departmentId=all|{id}` returns `{ thisYear: [{month, count}], lastYear: [{month, count}] }`
- Active Participants = DISTINCT senderIds ∪ recipientIds in period / totalActiveEmployees × 100

---

## Data Model — Manager Inputs (Mobile)

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

## Data Model — Employee Inputs (Mobile)

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

## Full Role-Permission Summary

| Entity | HR | Manager | Employee |
|---|---|---|---|
| `BadgeType` | ✅ Creates & manages | ❌ | ❌ |
| `RecognitionSettings` | ✅ Configures | ❌ | ❌ |
| `Endorsement` | ✅ Reads all (table) | ✅ Sends + reads team | ✅ Sends + reads own |
| `X/threshold progress` | ✅ Sees all | ✅ Direct reports only | ✅ Own only |
| Recognition Wall | ✅ Reads (no moderation needed) | ✅ Reads | ✅ Reads |
| Analytics | ✅ Full company | ✅ Team only | ❌ |

---

## HR Dashboard — Peer Recognition Structure (FULLY DESIGNED ✅)

Left nav under Performance → Peer Recognition:
```
├── Overview      ← Stats + chart + leaderboard + recent endorsements (Analytics merged here)
├── Badges        ← Badge catalog management
├── Endorsements  ← Table of all endorsements sent
└── Settings      ← Global config (4 fields)
```

**Analytics is merged into Overview** — no separate Analytics page needed.

**Overview page (Figma node: 4997:19259 — built ✅):**
- Filter: All Departments ↓ only — NO period chip at top (removed "This Quarter")
- Stat Cards (4) — always show current quarter, stated in each card sub-label:
  1. **Endorsements Sent** — dark card (#0c2427), value + "+X% vs last period" trend pill
  2. **Active Participants** — % of all employees who sent OR received ≥1 endorsement
  3. **Close to Earning** — count of employees who are ≥80% of their threshold for any badge
  4. **Top Badge** — most sent badge this quarter (name only)
- **Recognition Activity chart** — line chart, Y-axis: endorsements/month (0/200/400/600/800), X-axis: months (Jan–Aug)
  - Two lines: This Year (dark solid) vs Last Year (green dashed)
  - Chart has its OWN period toggle: This Year | Last 6M | Last 30D — independent of department filter
  - Department filter from top applies to chart data
- **Top Achievers** — right panel, top 2 featured (dark cards: rank + avatar + name + dept + endorsements count), list ranks 3–5 (avatar + name + dept + count)
- **Recent Endorsements table** — Sender · Sender Dept · Recipient · Recipient Dept · Badge · Date · Export button (no "Progress to Earn" — historical rows, number changes per row)

**Badges page (Figma nodes: 5229:63599, 5229:64081, 5234:65441 — built ✅):**
- Table columns: ☐ · Badge (icon) · Badge Name (EN) · Badge Name (AR) · Threshold · Min Depts (e.g. "3 of 4") · Times Earned · Status · Actions (···)
- Status: **Active** (green badge) / **Inactive** (grey badge) — only 2 states, no "Winding Down"
- Actions 3-dot: Edit | Set Inactive (if Active) / Set Active (if Inactive)
- Warning on Set Inactive if any employees have in-progress endorsements for this badge
- Cannot delete badges — only deactivate. Historical data preserved.
- Toolbar: Search by name · Export · + New Badge (no Import)
- Create/Edit modal: Badge Name EN · Badge Name AR · Description · Icon (preset grid picker, NOT upload) · Endorsement Threshold · Min Departments Required (number) · Required Departments (multi-select from company depts) · Status toggle
- Validation: Min Departments Required ≤ count of selected departments

**Endorsements page (built ✅):**
- Filters: All Departments ↓ | All Badges ↓ | Date Range ↓
- Table: Sender (avatar+name) · Sender Dept · Recipient (avatar+name) · Recipient Dept · Badge · Date
- Export only — no Import, no moderation, read-only
- Search: by sender or recipient name

**Settings page (built ✅):**
- Subtitle: "Configure how peer recognition works across the company — limits, scoring weight, and announcement behavior."
- 4 fields: Monthly Endorsement Limit · Max Recognition Points · Recognition Score Weight (%) · Announce on Badge Earn (toggle)
- Save button top right

**Employee My Details tab — department progress:**
```
Team Player        32 / 50
──────────────────────────
Engineering        ✅
HR                 ✅
Sales              ⏳ not yet
Operations         ⏳ not yet
```
Department status only — no names shown (private to employee).

---

## Mobile App Design — Peer-to-Peer Badges (Session 2026-05-19)

### Recognition Wall Screen
- **Screen title:** "Peer Recognition Wall"
- **Two tabs:** Tab 1 "All Peer Recognitions" — community feed (default); Tab 2 "My Details" — personal stats
- **Filter chips:** All · My Team · [Department names] — no "Earned" chip (the ★ Earned pill on cards handles that distinction)
- **FAB (+):** Bottom right — triggers Send Endorsement 3-step bottom sheet
- **No points/score in header** — personal score only visible inside My Details tab

### Feed — Earned Badge Post Card (designed ✅)
| Element | Content |
|---|---|
| Top row | ★ Earned pill (green) + timestamp |
| Content | Badge icon + employee photo + name (truncated with ...) |
| Sub-line | Badge name (teal/brand color) |
| Stats | "X endorsements · Y departments" |
| Quote | Auto-generated text based on endorsement data |
| Bottom row | "Details →" link + reaction counts (🚀 🔥 ❤️) + ··· menu |
| ··· menu | Employee: Report only · HR: Pin / Hide / Flag |

### Feed — Regular Endorsement Post Card
⏸ Not yet designed — "Yusuf endorsed Ahmed for Team Player" simplified format

### My Details Tab
⏸ Not yet designed — will show: earned badges + in-progress X/50 bars + dept breakdown + monthly send limit

### Send Endorsement — 3-Step Bottom Sheet (designed ✅)
| Step | Title | Key Content |
|---|---|---|
| 1 | "Who do you want to recognize?" | Employee dropdown (single select) → selected peer card: photo + name + dept + title (no employee ID) · Next disabled until selected |
| 2 | "Choose a Badge" | Badge list: icon + name + description + points pill + radio select · Search field at top · Next |
| 3 | "Add a Comment" | Summary card: badge icon + "You are endorsing" + employee name + badge name · Optional comment field ("Why do they deserve this badge?") · Submit endorsement |

**All steps:** Step indicator `● ● ○`, back arrow on steps 2 & 3, X closes entire sheet
**Phase 1:** Single employee only — no multi-select
**Self-endorsement:** Employee excluded from the list entirely (not shown, not greyed)

---

## Design Session Status (as of 2026-05-21)

**HR Dashboard — Peer Recognition (ALL DESIGNED ✅):**
- ✅ Overview — built in Figma (node 4997:19259)
- ✅ Badges — built in Figma (nodes 5229:63599, 5229:64081, 5234:65441)
- ✅ Endorsements — built in Figma
- ✅ Settings — built in Figma
- ❌ Analytics removed — merged into Overview

**Mobile — Peer-to-Peer (ALL DESIGNED ✅):**
- ✅ Recognition Wall (tabs + FAB, node 5061-30534)
- ✅ Send Endorsement (3-step bottom sheet)
- ✅ My Details tab (earned + in-progress with dept breakdown)
- ✅ Endorsement Details screen (tap card → see details)
- ✅ Success screen
- ✅ Badge Earned notification + screen
- ✅ Monthly limit edge case (bottom sheet blocks sending)
- ⏸ Regular endorsement post card (simplified wall card — not yet designed)
- ⏸ Empty states (empty wall, no badges yet)

**Angular build:** new `manage-recognition/` module (see `built-modules.md` → What To Build Next).
Spec document: `docs/superpowers/specs/2026-05-17-performance-module-full-scope.html`
