# Technology Comparison

## 1. Purpose and Method
This document compares the alternatives for the key technical decisions of the project and justifies the choice for the pilot. Each decision is evaluated against the requirements in `docs/06-requirements`, mainly: showing parking space status with a data-freshness indicator, handling unknown states, running on a single site with a limited camera view, and operating with a team of two people.

**Evaluation basis:** the assessments below are reasoned from the project requirements and the published characteristics of each approach. They are not benchmarks. Accuracy and performance must be measured during the pilot (see `docs/08-verification_plan`).

## 2. Decision 1: How occupancy is detected from images

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Per-space classification | Each marked space is cropped from the image and classified as occupied or free | Matches the requirement to report status per space; public datasets (PKLot, CNRPark+EXT) use this format, so models can be tested on them first | Needs fixed camera position and calibrated space regions; must be redone if the camera moves | **Chosen for the pilot.** Fits a fixed camera and one site |
| B. Object detection of vehicles, then matching to spaces | A detector finds cars in the whole image; each detection is matched to a space | Less dependent on exact space outlines; can handle some camera shifts | Matching errors when cars overlap or park at an angle; more complex to build and evaluate | Second option if A fails under snow or shifted cameras |
| C. Counting entries and exits | Count vehicles passing a gate and subtract from capacity | No image processing of individual spaces; simple logic | Errors accumulate over time (missed or repeated events); does not show which space is free | Not suitable as the main method; can serve only as a consistency check |

**Decision:** Option A, with a temporal smoothing step so a single unreliable frame does not change the status. Option B is kept as a fallback if per-space accuracy is too low in winter conditions.

**Known risk:** PKLot authors state that the dataset contains no night images, so public data does not cover winter nights in Astana. Local images must be collected to validate the model.

## 3. Decision 2: Model type for classification

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Classical image features with a simple classifier (e.g., histogram features + SVM) | Hand-designed features and a conventional classifier | Fast, small, easy to explain in the report; a useful baseline | Usually less robust to lighting and snow changes | **Used as the baseline** for comparison |
| B. Small convolutional neural network (CNN) trained on the local images | A compact CNN trained on space crops | Learns lighting and weather robustness from data; runs on modest hardware | Needs enough labeled local images; training takes time | **Chosen as the main model**, trained on local data |
| C. Large pre-trained network used as is | A general-purpose vision model applied without retraining | Little training effort | Heavier hardware; not tuned for the fixed camera view; harder to run locally | Not chosen for the pilot |

**Decision:** compare B against the A baseline, using the same train/test split. The split must separate images by day and by camera, not randomly, to avoid near-duplicate frames inflating the results.

## 4. Decision 3: Where processing runs

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Cloud processing | Camera images are sent to a server that runs the model | Simple to update the model; more computing power | Sends images off site; depends on internet connection; more personal-data handling to justify | Less suitable: adds data-transfer and compliance questions |
| B. Local device at the site | A small computer near the camera processes images and sends only statuses | Images stay on site; works when internet is unstable; less data to store | Hardware to install and maintain; updates require a visit or remote access | **Chosen.** Reduces the amount of personal data transferred and matches the goal of showing status even with weak connectivity |
| C. Hybrid | Local detection, cloud for storage and the app | Balances the two above | Two components to build and maintain | Possible later; too much work for a two-person pilot |

**Decision:** local processing on one device at the site, which sends only space statuses and timestamps to the backend. The camera images are not stored by default.

## 5. Decision 4: Backend and data storage

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Relational database (PostgreSQL) | Tables for spaces, statuses, observations, operator corrections, and logs | Strong consistency; easy to query history and correction logs; well known | Requires schema design | **Chosen.** Operator corrections and the audit log need clear relationships |
| B. Document database (e.g., MongoDB) | Flexible JSON-like records | Flexible schema | Weaker for the structured audit trail; less useful for reporting | Not chosen |
| C. Time-series database (e.g., InfluxDB) | Optimized for timestamped readings | Efficient for large volumes of sensor readings | Adds a second database; the pilot volume is small | Not needed at pilot scale |

**Decision:** PostgreSQL. Storage location must comply with the Republic of Kazakhstan law on personal data, which requires databases containing personal data to be stored in Kazakhstan. Even with occupancy-only data, the server location should be chosen with this rule in mind and confirmed with the supervisor.

## 6. Decision 5: How the mobile app receives status updates

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Polling a REST API | The app requests the status at fixed intervals | Simple; standard | Status can lag; wastes battery and bandwidth if the interval is short | Acceptable baseline |
| B. Push updates (WebSocket or server push notifications) | The server sends a change as soon as it happens | Shows changes quickly; can notify about staleness | More complex to build and keep alive | **Chosen for live status**, with REST used for loading the map and history |
| C. Manual refresh only | The user pulls to refresh | Simplest | Users see outdated data without knowing it | Not suitable, since freshness is a core requirement |

**Decision:** REST for initial data and history, WebSocket for live updates. The app marks a status as *unknown* when no update has arrived within a set time, which is a rule the backend also enforces.

## 7. Decision 6: Mobile platform

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Native Android (Kotlin) | Android-only app | Full platform access; one platform to build | Excludes iOS users | Possible if the pilot users are mostly Android |
| B. Native iOS (Swift) | iOS-only app | Full platform access | Excludes Android users | Only if the pilot site confirms iOS-only users |
| C. Cross-platform (React Native or Flutter) | One codebase for both platforms | Covers both platforms with one team of two | Some platform features need extra work; learning curve | **Chosen**, to reach both platforms with limited time |

**Decision:** cross-platform (React Native or Flutter). The final choice between the two is made by the team based on the framework each member already knows. Record the choice and reason in `docs/05-technology_comparison` before development starts.

## 8. Decision 7: Operator interface

| Option | Description | Advantages | Disadvantages | Fit for the project |
|---|---|---|---|---|
| A. Separate web application | Browser-based panel for operators | Works on any computer; no installation | Needs its own build and hosting | **Chosen** |
| B. Admin screens inside the mobile app | Operators use the same app | One codebase | Poor fit for editing a schema or reviewing logs; mixes roles | Not chosen |

## 9. Summary of Choices

| Decision | Choice | Requirement it supports |
|---|---|---|
| Detection method | Per-space classification | Status per space |
| Model | Small CNN, baseline classical model | Accuracy under local conditions |
| Processing location | Local device at the site | Limited personal data; works with weak internet |
| Database | PostgreSQL | Audit log and operator corrections |
| Live updates | WebSocket + REST | Freshness indicator |
| Mobile | Cross-platform | Both platforms with a two-person team |
| Operator panel | Web app | Corrections, closures, reports |

## 10. Open Risks
- Per-space accuracy may drop under snow; fallback is object detection (Option B in Decision 1).
- Camera permissions and fixed position must be agreed with the partner before development.
- Server location must be confirmed against Kazakhstani personal-data rules with the supervisor.
- The baseline and CNN comparison depends on enough labeled local images, which must be collected during the pilot.
