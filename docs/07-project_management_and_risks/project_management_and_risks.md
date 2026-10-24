# Project Management and Risks

## 1. Purpose
This document describes how the team organizes the work, who is responsible for what, the milestones and schedule, the dependencies, and the risks that could prevent the pilot from being completed or evaluated.

## 2. Team and Responsibilities

| Member | Role | Git branch | Main responsibilities |
|---|---|---|---|
| Ansar Keles | Backend and architecture | ansar-branch | System architecture, backend services, status logic, database, access to partner data, hosting and personal-data handling, local device integration |
| Bigali Nugmash | Frontend and machine learning | bigali-branch | Driver mobile app, operator web interface, image collection, labeling, classification model, evaluation and error analysis |
| Askar Khaimuldin | Supervisor | — | Reviews key deliverables, confirms decisions, provides feedback |
| [Partner representative] | Site contact (external) | — | Provides camera access, confirms reference states during evaluation |

**Shared responsibilities:** both members take part in user interviews, pilot site visits, the requirements, and the final report. Each member reviews the other member's pull requests before merging.

**Individual contributions** are tracked through assigned YouTrack tasks and Git commits. Each member commits from their own account.

## 3. Git Repository and Task Tracking

**Repository:** https://github.com/bgl96395/smart-parking-mobile-app
**Task tracker:** YouTrack project [insert link], task prefix DIP-

**Branching:**
- `main` is the reviewed baseline and contains only merged, reviewed work.
- Each task is developed in its own branch named after its YouTrack ID, for example `DIP-14-user-research`.
- Changes are merged through pull requests that reference the YouTrack task and describe how the work was checked.
- Each pull request is reviewed by the other team member before merging.

**Commit convention:** each commit starts with the task ID, for example `DIP-14: Add interview guide for drivers`.

**Traceability:** every YouTrack task has an assignee, an expected outcome, acceptance criteria, a deadline, and a link to its branch or pull request. A task is closed only when its acceptance criteria are met.

**Submission tags:** `pre-defense-1`, `pre-defense-2`, `final-pre-defense`. Tags are never moved or replaced after submission.

**Individual contributions:** each team member commits from their own account. Shared accounts are not used.

## 4. Work Breakdown

Each task in YouTrack has a responsible person, an expected outcome, acceptance criteria, and a deadline.

| Epic | Scope | Lead |
|---|---|---|
| E1. Research and relevance | Interviews, observations, client discussion | Ansar Keles |
| E2. Literature and comparisons | Literature review, system and technology comparison | Both |
| E3. Requirements and architecture | Requirements, architecture, diagrams | Ansar Keles |
| E4. Prototype and interface design | Mockups, driver app screens, operator panel design | Bigali Nugmash |
| E5. Backend and integration | Status service, database, access control logic, local device integration | Ansar Keles |
| E6. Driver app and operator panel | Driver mobile app, operator web interface | Bigali Nugmash |
| E7. Data and model | Image collection, labeling, training, validation | Bigali Nugmash |
| E8. Evaluation | Verification plan, baseline, scenarios, technical and usage evaluation | Both |
| E9. Project management | Repository, reviews, supervisor meetings, final report | Both |

## 5. Milestones

| Milestone | Target date | Deliverables | Git tag |
|---|---|---|---|
| M1. Pre-defense 1 | 24 October 2026 | Proposal, relevance evidence, literature review, comparisons, requirements, diagrams, prototype materials, verification plan, YouTrack board | pre-defense-1 |
| M2. Pre-defense 2 | [date set by department] | Local dataset collected, baseline measured, first model results, core of the app implemented | pre-defense-2 |
| M3. Final pre-defense | [date set by department] | Working pilot, technical and usage evaluation, complete report | final-pre-defense |

**Note on M1:** the project guidelines require the repository to show analysis, design, planning, and supervision. A complete application is not required at this stage.

## 6. Process and Quality Rules
- `main` is the reviewed baseline. Work happens in task branches.
- Every change is submitted as a pull request with a description, the linked YouTrack task, and how it was checked.
- Each pull request is reviewed by the other team member before merging.
- The supervisor reviews significant changes at agreed checkpoints: after M1, after the labeling protocol, after the first model results, and before M3.
- A task is marked complete only when its acceptance criteria are met.
- Commits are made regularly throughout the project, not in one batch before a pre-defense.

## 7. Dependencies

| Dependency | Needed for | Owner | Status |
|---|---|---|---|
| Access agreement with the partner | Camera access, site visits, baseline | Ansar Keles | Open |
| Camera with a view of the spaces | Image collection, classification | Partner | Open |
| Confirmed hosting location in Kazakhstan | Storage of any personal data | Ansar Keles with supervisor | Open |
| Local image collection | Training and test sets | Bigali Nugmash | Not started |
| Operator contact for corrections | Operator panel testing | Partner | Open |
| Department confirmation of pre-defense dates | Milestones M2 and M3 | Supervisor | Open |

## 8. Risk Register

Likelihood and impact are each rated Low (1), Medium (2), or High (3). Priority = likelihood × impact.

| ID | Risk | Likelihood | Impact | Priority | Mitigation | Owner | Trigger / early warning |
|---|---|---|---|---|---|---|---|
| R1 | Partner does not grant camera or site access | Medium (2) | High (3) | 6 | Secure a signed agreement before M1; identify a second candidate site; if no camera is available, use the manual reallocation option from the proposal | Ansar Keles | No written agreement by mid-October |
| R2 | Camera view does not cover the spaces well enough | Medium (2) | High (3) | 6 | Check the view during the first site visit; adjust camera position with the partner; reduce the number of spaces in scope | Bigali Nugmash | Fewer than 15 spaces clearly visible on the site visit |
| R3 | Classification accuracy under snow is below target | High (3) | High (3) | 9 | Collect snow data early; use object detection as fallback (technology comparison, Decision 1); report honest results if targets are missed | Bigali Nugmash | Snow-day accuracy below 80% in validation |
| R4 | Not enough snow or night days during the project period | Medium (2) | Medium (2) | 4 | Start collection as early as access is granted; state the gap in the report instead of extrapolating | Bigali Nugmash | Fewer than 2 snow days collected by week 4 |
| R5 | Labeling takes longer than planned | High (3) | Medium (2) | 6 | Set up the labeling tool and protocol first; label in fixed daily batches; use the kappa check to limit rework | Bigali Nugmash | Fewer than 1,000 labeled samples by week 3 |
| R6 | Team member availability drops during the project | Medium (2) | High (3) | 6 | Keep documentation and code in the repository; each task has a clear owner and acceptance criteria | Both | Two consecutive YouTrack deadlines missed |
| R7 | Scope grows (reservations, payments, barrier control added) | Medium (2) | High (3) | 6 | Keep the out-of-scope list in the proposal; new features require supervisor approval and a change entry in YouTrack | Both | New tasks outside the requirements list |
| R8 | Personal data rules not confirmed in time, blocking data collection | Medium (2) | High (3) | 6 | Ask the supervisor for the rule and hosting decision before collection; collect occupancy-only images without license plates | Ansar Keles | Hosting decision not confirmed by M2 |
| R9 | Local device hardware fails or is incompatible with the camera | Low (1) | High (3) | 3 | Choose a device with known camera compatibility; test before installation; keep a spare if the budget allows | Ansar Keles | Device does not receive the camera stream during setup |
| R10 | Drivers do not use the app enough for usage metrics | Medium (2) | Medium (2) | 4 | Place QR codes at the site; recruit participants through the partner's tenant channels; report low usage as a finding | Both | Fewer than 10 app sessions in the first week of the pilot |
| R11 | Operator does not use the panel for corrections | Low (1) | Medium (2) | 2 | Train the operator on site; keep the panel simple; record the effort in the operator log | Bigali Nugmash | No corrections logged in the first two weeks |
| R12 | Progress is not reflected in Git and YouTrack, so it cannot be shown at defense | Medium (2) | Medium (2) | 4 | Weekly check of YouTrack and commits by both members; link every branch and pull request to its task | Both | Tasks in progress with no commits for more than one week |

**Highest priority risks:** R3 (snow accuracy), R1 (access agreement), R2 (camera view). These are reviewed at each supervisor checkpoint.

## 9. Risk Review
- Risks are reviewed every two weeks in the team meeting and recorded in YouTrack.
- A risk is closed when its trigger no longer applies or its mitigation is complete.
- New risks are added as soon as they are identified, with the same fields.

## 10. Communication
- Team meeting: once per week, 30 minutes, with a status update on each task.
- Supervisor meeting: every two weeks, plus the checkpoints listed in Section 7.
- Partner contact: as agreed in the access agreement; all decisions about site changes are recorded in YouTrack.
- Communication channel: [state the channel you use].

## 11. Assumptions
- The pilot site and its camera are available for the full evaluation period.
- The team has the same weekly availability throughout the project.
- Pre-defense dates after M1 are set by the department and will be confirmed.