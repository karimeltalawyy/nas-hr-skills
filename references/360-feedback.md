# 360-Degree Feedback (reference) — Fully Designed

## Core Concept
Not a new module — an **upgrade to the existing appraisal template system** (`manage-performance/`). Backwards compatible: if only Manager rater type is checked, it behaves exactly like the current appraisal.

## Template Level — New Fields (Step 1 before adding questions)
| New Field | Type | Notes |
|---|---|---|
| `raterTypes` | checkboxes | Manager / Self / Peer / Upward |
| `maxPeerNominations` | number | Max peers employee can suggest (default 3) |
| `peerSelectionDeadlineDays` | number | Days after launch for peer selection |

## Question Level — New Field (added to existing Question model)
Each question gets a `visibilityType` enum:
| Value | Meaning |
|---|---|
| `both_sides` | Reviewer AND employee both answer this question |
| `reviewee_only_visible` | Employee answers — reviewer can see the answer |
| `reviewee_only_hidden` | Employee answers — reviewer cannot see the answer |
| `reviewer_only_visible` | Reviewer answers — employee can see the answer |
| `reviewer_only_hidden` | Reviewer answers — employee cannot see the answer |

Reference: Teamflect question-set model (confirmed by user as the right approach).

## Appraisal Run — New Fields
| New Field | Type | Notes |
|---|---|---|
| `peerSelectionDeadline` | date | Deadline for peer nominations |
| `submissionDeadline` | date | Deadline for all reviewers to submit |
| `managerScoreWeight` | number % | e.g., 50% |
| `selfScoreWeight` | number % | e.g., 20% |
| `peerScoreWeight` | number % | e.g., 30% |
| Upward score | — | Informational only — not included in employee's final score |

## Workflow — 6 Statuses
`Draft → Active → Peer Selection → Collection → Calibration → Closed`
- **Draft**: HR creates template + appraisal run, sets deadlines
- **Active**: All parties notified (push). Manager rates employees. Employees start self-assessment.
- **Peer Selection**: Employee suggests up to 3 peers on mobile → manager approves/modifies
- **Collection**: All parties submit forms. HR sees real-time completion per reviewer.
- **Calibration**: Manager reviews aggregated scores, adds calibration notes, can adjust final score
- **Closed**: Final report published, scores locked. Employee receives result on mobile.

## Peer Selection Flow
- Employee nominates up to `maxPeerNominations` colleagues (any dept)
- Manager approves / modifies the list
- System sends push notification to approved peers
- Peer responses are anonymous to the reviewed employee (names visible to manager + HR only)

## Visibility Rules — Who Sees What
| Data | Employee | Manager | HR |
|---|---|---|---|
| Own self-assessment | ✅ | ✅ | ✅ |
| Manager score | ✅ (after closed) | ✅ | ✅ |
| Peer scores — aggregated | ✅ no names | ✅ with names | ✅ |
| Individual peer responses | ❌ | ✅ | ✅ |
| Upward scores (employee rates manager) | ❌ | ❌ aggregated only | ✅ |
| Final weighted score | ✅ | ✅ | ✅ |

## Data Inputs Per Role
**HR (Dashboard):** Template rater types + question visibilityType + appraisal run weights + deadlines + completion monitoring
**Manager (Mobile):** Rate employees (existing) + approve peer list + calibration notes + optional score override
**Employee (Mobile):** Self-assessment form + nominate peers + fill peer feedback form (when nominated) + upward review (if enabled)

## Role-Permission Table
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

**Build (Dashboard):** modify existing `manage-performance/` (appraisal-template + question-set + appraisal-run components). Do NOT create a new module.

## Mobile POV
⏸ **Mobile flow not yet provided.** Known mobile touchpoints from the design: manager rates employees + approves peer list + calibration notes; employee self-assessment + nominate peers + fill peer feedback (when nominated) + upward review. Full mobile flow/screens to be folded in when the mobile PD is provided.
