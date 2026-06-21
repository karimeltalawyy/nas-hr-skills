# NAS HR — Built Modules (reference)

What is already shipped in the Angular dashboard. Read when working on or integrating with existing functionality.

## Appraisal (`manage-performance/`) — 19 components
| What | Detail |
|---|---|
| Question Sets | Reusable sets of typed questions: Rating, Yes/No, Short Answer, Multiple Choice |
| Templates | Wire question sets into a named template |
| Run Appraisal | HR assigns template to departments → manager rates employees on mobile → employee accepts or rejects |
| Flow type | **Manager-review only** (not self or peer yet) |
| Routing | Appraisals → Details → Department → Employee Questions/Scoring |

Key models: `models/appraisals.interface.ts`, `models/question.model.ts`
Key services: `services/performance.service.ts`, `services/appraisal-template.service.ts`, `services/question-set.service.ts`

## KPI Plans (`manage-kpi-plans/`) — 8 components
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

## Rewards — Tier 2 & Tier 3 (Already Built)
| Tier | Type | Who | Flow |
|---|---|---|---|
| Tier 2 | Manager bonus / award | Manager → Employee | Manager sends from mobile directly |
| Tier 3 | Monetary award with approval | Manager nominates → HR approves | Mobile nomination → dashboard approval → payroll line item |

(Tier 1 — Peer-to-Peer Badges — is designed but NOT built. See `peer-recognition.md`.)

## Payroll — `payroll-v3/`
Payroll engine (built). Consumes attendance to derive deductions. Receives Rewards Tier 3 approved bonuses as additional salary line items. See memory `project_hr_payroll_v3.md`.

---

## Current Implementation State Snapshot

```
BUILT ✅
  Angular Dashboard
  ├── manage-performance/     → Appraisal (19 components)
  ├── manage-kpi-plans/       → KPI Plans (8 components)
  └── payroll-v3/             → Payroll engine

  Rewards
  ├── Tier 2 — Manager bonus       ✅ built
  └── Tier 3 — Monetary + approval ✅ built

DESIGNED — READY TO BUILD 🔴
  Rewards Tier 1 — Peer-to-Peer Badges  (manage-recognition/ module)
  360-Degree Feedback                    (upgrades to manage-performance/)
```

## What To Build Next (Phase 1 priority order)

1. **Angular: `manage-recognition/` module** — follow `manage-kpi-plans/` pattern exactly: models/ → services/ → components/ → popups/. Start with:
   - `models/badge-type.interface.ts` → name, nameAr, description, icon, endorsementThreshold, minDepartmentsRequired, requiredDepartments[], status
   - `models/recognition-settings.interface.ts` → monthlyEndorsementLimit, maxRecognitionPoints, recognitionScoreWeight, announceOnEarn
   - `models/endorsement.interface.ts` → senderId, senderDepartmentId, recipientId, badgeTypeId, message, createdAt
   - `models/badge-earn-event.interface.ts` → employeeId, badgeTypeId, earnedAt, endorsementCount, departmentCount
   - `services/badge-type.service.ts`, `services/recognition.service.ts`, `services/recognition-chart.service.ts` (`GET /api/recognition/chart?period=&departmentId=`)
   - `components/recognition-overview/` (stat cards + chart + leaderboard + table), `components/badge-catalog/` (list + create/edit modal), `components/endorsements/` (read-only table), `components/recognition-settings/`
2. **Angular: 360-Degree Feedback upgrades** — modify existing `manage-performance/`, do NOT create a new module. Touch: appraisal-template + question-set + appraisal-run components.
3. **Mobile Figma: remaining peer-to-peer screens** — Regular endorsement post card → My Details tab → Empty states. Figma file: https://www.figma.com/design/Zf77LBcrb9Bt61ICdjUZGN/NAS-HR-Dashboard → 🥅 Performance page.
