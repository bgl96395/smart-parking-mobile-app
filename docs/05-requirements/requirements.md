# Requirements Specification

## 1. Purpose and Scope
This document lists the functional and non-functional requirements for the pilot version of the system: trustworthy parking availability monitoring for one parking area of a business center in Astana. Each requirement has a priority and an acceptance criterion that can be verified.

**Scope reference:** `docs/01-proposal/proposal.md`, Section 8.
**Decisions reference:** `docs/05-technology_comparison`.

## 2. Priority Definitions
- **Must (M):** required for the pilot; without it the system cannot be evaluated.
- **Should (S):** important for usefulness; included if time allows.
- **Could (C):** improves the system; excluded from the pilot if necessary.

## 3. Status Definitions
Every parking space is always in exactly one of four states:

| State | Meaning |
|---|---|
| Free | The latest valid observation shows the space is empty and usable |
| Occupied | The latest valid observation shows a vehicle in the space |
| Temporarily unavailable | Closed by the operator (snow, repair, reserved, or other reason) |
| Unknown | No valid observation within the freshness limit, or the camera cannot see the space |

**Freshness limit:** 30 seconds. With an observation every 5 seconds, this allows up to five missed observations before a space is marked *Unknown*. The last known value must never be shown as current.

## 4. Functional Requirements

### 4.1 Driver application

| ID | Requirement | Priority | Acceptance criterion |
|---|---|---|---|
| FR-01 | The app shows a schematic map of the parking area with each space and its state | M | For a test layout of 15–30 spaces, every space is displayed with one of the four states, and the states match the backend data |
| FR-02 | Each state is visually distinct | M | A tester identifies the four states correctly in at least 9 out of 10 trials |
| FR-03 | The app shows the time of the last valid observation for the whole area and for each space | M | Timestamp is visible on the map screen; it updates when new data arrives |
| FR-04 | The app shows "Unknown" instead of the last known state when data is stale | M | With the camera disconnected for longer than 30 seconds, all affected spaces show Unknown within 10 seconds |
| FR-05 | The app shows a warning when the whole area is in Unknown state | M | Warning appears when all spaces are Unknown; disappears when data returns |
| FR-06 | The app updates the status without requiring a manual refresh | M | A status change on the backend appears in the app within 5 seconds in 95% of test changes under normal network conditions |
| FR-07 | The app shows a note when a space is temporarily unavailable, with the reason if the operator entered one | S | Reason text is shown for closures that have one |
| FR-08 | The app works when the network is briefly lost, showing the last data with its timestamp and the Unknown state for stale items | S | With the network off for 1 minute, the app shows the timestamp and does not present old data as current |

### 4.2 Operator web interface

| ID | Requirement | Priority | Acceptance criterion |
|---|---|---|---|
| FR-09 | The operator can log in with an account and see only the areas assigned to them | M | A user without assignment to an area cannot view or edit it |
| FR-10 | The operator can mark a space or the whole area as Temporarily unavailable, with a required reason | M | Saving without a reason is rejected; the change appears in the app within 5 seconds |
| FR-11 | The operator can remove a temporary closure | M | After removal, the space returns to its detected state within 5 seconds |
| FR-12 | The operator can correct a detected state manually, with a required reason | M | The correction is shown in the app; the original detected value is kept in the log |
| FR-13 | The system keeps a log of all manual changes: who, when, what, and why | M | Every manual change creates a log entry; entries cannot be edited or deleted through the interface |
| FR-14 | The operator sees the current state of all spaces and the data freshness on one screen | M | Screen loads within 3 seconds and shows all spaces with timestamps |
| FR-15 | The operator sees a quality report for a chosen period: share of Unknown time, number of corrections, and detection confidence summary | S | Report generated for any period in the pilot with the listed values |

### 4.3 Detection and processing

| ID | Requirement | Priority | Acceptance criterion |
|---|---|---|---|
| FR-16 | The system determines the state of each space from camera images at a fixed interval | M | Each space receives an observation at least every 5 seconds while the camera is connected |
| FR-17 | The system applies temporal smoothing so a single unreliable frame does not change the state | M | On a recorded test sequence with injected single-frame errors, the state does not change for errors shorter than 3 consecutive frames |
| FR-18 | When the model's confidence is below a threshold, the system reports Unknown instead of a guess | M | Frames with confidence below 0.85 result in Unknown for that space; the threshold is documented and tuned on the validation set |
| FR-19 | The system detects loss of view (camera moved, blocked, or image dark) and reports Unknown for the affected spaces | M | Covering the camera lens or moving it produces Unknown within 30 seconds |
| FR-20 | The system stores status history with timestamps | S | History for any space can be retrieved for a chosen time range |
| FR-21 | The system does not store camera images by default | M | After a test day, no image files are present in the storage location |

## 5. Non-functional Requirements

| ID | Requirement | Priority | Acceptance criterion |
|---|---|---|---|
| NFR-01 | Classification accuracy meets the targets below, measured on local images | M | Per-space accuracy at least 90% in daylight and at least 85% in snow conditions; false-free rate (occupied spaces shown as free) at most 5%. Targets must match `docs/08-verification_plan` |
| NFR-02 | Status latency: time from a physical change to its display in the app | M | Median latency at most 3 seconds, measured on the pilot site |
| NFR-03 | The local device works without internet for at least 10 minutes and resumes sending when connection returns | S | Interrupt the connection for 10 minutes; no observations are lost after reconnection |
| NFR-04 | Operator actions are logged and the log is protected against change through the interface | M | See FR-13 |
| NFR-05 | Personal data is minimized: camera images are not stored and license plates are not processed | M | Verified by inspection of storage and code; see FR-21 |
| NFR-06 | Personal data processed by the system is stored on servers located in the Republic of Kazakhstan | M | Hosting location is documented and confirmed with the supervisor |
| NFR-07 | The driver app runs on both Android and iOS | M | Installed and tested on at least one Android and one iOS device |
| NFR-08 | The app is usable on screens from 5.0 inches (about 360 px width) | S | Layout checked on two screen sizes without overlapping elements |
| NFR-09 | Access to operator functions requires authentication; sessions expire after 15 minutes of inactivity | M | Session expires after 15 minutes; unauthenticated requests are rejected |
| NFR-10 | Service availability during the pilot | S | Available for at least 95% of pilot hours, measured from logs |
| NFR-11 | The interface is in Russian and English | C | Both languages available for all driver screens |

## 6. Traceability to the Problem

| Problem element (from proposal) | Requirements |
|---|---|
| Drivers cannot tell whether a space is usable | FR-01, FR-02, FR-03, FR-04 |
| Outdated information is shown as current | FR-03, FR-04, FR-05, FR-06, FR-18, FR-19 |
| Operators lack control over closures and corrections | FR-10, FR-11, FR-12, FR-13, FR-14 |
| Winter conditions change usable capacity | FR-10, FR-07, NFR-01 |
| Data quality is not measured | FR-15, FR-20 |

## 7. Open Items
- The values above are proposed defaults. Confirm them with the first measurements on site, and adjust if the camera's real update rate or accuracy differs.
- The accuracy targets in NFR-01 and the confidence threshold in FR-18 must be the same as in the verification plan.
- The platform choice for NFR-07 follows the team decision recorded in the technology comparison.
- NFR-06 depends on the hosting decision confirmed with the supervisor.