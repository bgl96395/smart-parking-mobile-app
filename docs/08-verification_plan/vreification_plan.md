# Verification and Evaluation Plan

## 1. Purpose
This plan defines how the team will show that the pilot system meets its requirements and whether it delivers the intended benefit: drivers get trustworthy availability information, and operators can see and correct its state.

The plan answers two questions:
1. **Does the system work correctly?** Measured through classification accuracy, latency, and the handling of unknown states (technical evaluation).
2. **Does it help?** Measured through driver outcomes and operator effort compared with a baseline (usage evaluation).

**Requirements reference:** `docs/06-requirements`.
**Technology reference:** `docs/05-technology_comparison`.

## 2. Evaluation Scope
- **Site:** one parking area of one partner business center in Astana, 15–30 marked spaces.
- **Duration:** baseline period of 2 weeks, technical evaluation of 2 weeks, usage pilot of 2 weeks.
- **Conditions to cover:** daylight, night, dry surface, and snow cover. Winter conditions must be included, since public datasets do not cover them well.

## 3. Evaluation Stages

| Stage | Purpose | Data | Output |
|---|---|---|---|
| A. Public dataset check | Confirm the classifier works on a known benchmark | PKLot, CNRPark+EXT | Baseline accuracy, comparison with literature |
| B. Local dataset collection | Obtain images from the pilot camera under real conditions | Images of the pilot area, labeled by hand | Local training and test sets |
| C. Technical evaluation | Measure accuracy, false-free rate, latency, and unknown-state behavior | Local test set, live stream | Results against NFR-01, NFR-02, FR-17–FR-19 |
| D. Baseline measurement | Record how drivers and operators handle availability without the system | Observation and logs, no system in use | Reference values for usage metrics |
| E. Usage pilot | Drivers use the app; operators use the panel | App logs, operator log, short driver survey | Usage metrics against baseline |

Stage D runs before the system is shown to drivers, so the baseline is not affected by the app.

## 4. Data Preparation

### 4.1 Labeling
- Each marked space is labeled as *occupied* or *free* in every labeled frame.
- A space is labeled *unusable* (and excluded from accuracy) when it is covered by snow, blocked, or not visible. These frames test the Unknown state instead.
- Two people label the same sample of at least 300 frames independently. Agreement is measured with Cohen's kappa; the target is κ ≥ 0.8. Disagreements are resolved by discussion and recorded.

### 4.2 Dataset Split
- Training, validation, and test sets are split **by day**, not by random frames. Adjacent frames are almost identical, so a random split inflates results.
- The test set contains at least 3 full days that do not appear in training, including at least one snow day and one night.
- The validation set is used to tune the confidence threshold (FR-18) and the smoothing window (FR-17). The test set is used once, for the final result.

### 4.3 Sample Sizes
- Local training set: at least 5,000 labeled space-level samples across all conditions.
- Local test set: at least 1,000 space-level samples per condition group (daylight, night, snow).
- If local collection falls short, state the gap in the report and do not extrapolate.

## 5. Technical Evaluation Metrics

| Metric | Definition | Target | Requirement |
|---|---|---|---|
| Per-space accuracy, daylight | Share of correctly classified spaces (excluding Unknown) | ≥ 90% | NFR-01 |
| Per-space accuracy, snow | Same, for snow-cover days | ≥ 85% | NFR-01 |
| False-free rate | Share of occupied spaces shown as Free, among all spaces shown as Free | ≤ 5% | NFR-01 |
| Unknown rate | Share of time a space is shown as Unknown | Reported, no fixed target | FR-18, FR-19 |
| Stale-data display | Stale data shown as current, measured by replaying a test feed with the camera disconnected | 0 occurrences after 30 s | FR-04 |
| Smoothing robustness | Injected single-frame errors that change the displayed state | 0 changes for errors shorter than 3 frames | FR-17 |
| Loss-of-view detection | Time from camera covering or moving to Unknown on all affected spaces | ≤ 30 s | FR-19 |
| Status latency | Time from physical change to app display (median) | ≤ 3 s | NFR-02 |
| Update interval | Time between observations per space | ≤ 5 s | FR-16 |

**Why the false-free rate matters:** showing an occupied space as free is the most harmful error, because it sends the driver on a wasted trip. Accuracy alone can hide this, so it is reported separately.

**Reporting rule:** report every metric for each condition group (daylight, night, snow). Do not report only the overall average, since an average can hide poor snow performance.

## 6. Baseline

### 6.1 Technical baseline
- **Simple baseline:** the classical model (histogram features with a linear classifier) from the technology comparison.
- **Majority baseline:** always predicting the most common state in the test set, to show the model learns something real.

### 6.2 Usage baseline (Stage D)
Measured during 2 weeks before the app is introduced, using the same time windows as the usage pilot.

| Measure | How it is collected |
|---|---|
| Time from arrival at the building to parking | Driver short form or observation at entrance, sample of 30 arrivals |
| Number of drivers who searched and found no usable space | Driver short form, sample of 30 arrivals |
| Operator time spent on availability questions and corrections | Operator's own log of time, per day |
| Number of guard calls or manual help requests about parking | Guard log |

## 7. Usage Evaluation

### 7.1 Driver outcomes
| Measure | Target | How it is collected |
|---|---|---|
| Successful parking (space was usable on arrival) | Higher than baseline by at least 20 percentage points | App check-in question on arrival, optional |
| Time from arrival to parking | Lower than baseline by at least 30% | Driver short form, same sample size as baseline |
| Unsuccessful trips (drove to a space that turned out unusable) | Lower than baseline | Same as above |
| Driver trust in the status | At least 70% of respondents say they would rely on the status | Short survey after 2 weeks |

**Note:** these targets are starting expectations for a pilot. They are not guaranteed. Report the actual results, including if they fall short.

### 7.2 Operator outcomes
| Measure | Target | How it is collected |
|---|---|---|
| Time spent on availability questions and corrections | Lower than baseline | Operator log |
| Number of manual corrections needed per day | Reported; trend over the pilot | Operator panel log (FR-13) |
| Guard calls about parking | Lower than baseline | Guard log |

### 7.3 Operator usefulness check
After the pilot, the operator answers three questions: Would you use the quality report? Would you pay for this in future? What would make you stop using it? The answers go into the report as qualitative evidence.

## 8. Test Scenarios

| ID | Scenario | Expected result | Linked requirements |
|---|---|---|---|
| TS-01 | Normal operation, all spaces visible | Statuses match labels within accuracy targets | FR-01, FR-16, NFR-01 |
| TS-02 | Camera disconnected for 60 seconds | All spaces show Unknown within 30 s; area warning shown | FR-04, FR-05 |
| TS-03 | Camera lens covered by a cloth | Affected spaces show Unknown within 30 s | FR-19 |
| TS-04 | Camera moved by 10 degrees | Affected spaces show Unknown or are flagged; no false Free | FR-19, FR-18 |
| TS-05 | Single-frame headlight flare | No status change | FR-17 |
| TS-06 | Operator closes two spaces for snow with reason | Spaces show Temporarily unavailable in the app within 5 s; reason visible | FR-10, FR-07 |
| TS-07 | Operator submits closure without reason | Submission rejected | FR-10 |
| TS-08 | Operator corrects a detected state | Correction shown in app; original value kept in log | FR-12, FR-13 |
| TS-09 | Operator without assignment opens the area | Access denied | FR-09 |
| TS-10 | Operator idle for 15 minutes | Session expires | NFR-09 |
| TS-11 | Network lost on the phone for 1 minute | App shows timestamp, stale items as Unknown | FR-08 |
| TS-12 | Local device loses internet for 10 minutes | No observations lost after reconnection | NFR-03 |
| TS-13 | Report for a chosen period | Report shows Unknown share, corrections, confidence summary | FR-15 |

Each scenario is run at least 3 times. Record the date, time, result, and any deviation.

## 9. Error Analysis
For every error in the technical evaluation:
- Record the condition (lighting, snow, occlusion, camera shift).
- Classify the error as false-free, false-occupied, or unknown-where-data-existed.
- Note the most common cause in the test set.

The error analysis must be included in the report. It shows where the system fails, which is as important as the accuracy numbers.

## 10. Data Handling
- Camera images are used for labeling and evaluation only and are deleted after the evaluation period, as required by FR-21.
- Labels and metrics are stored without images.
- Driver survey responses are anonymous. No license plates are recorded (NFR-05).
- Personal data is not transferred outside the Republic of Kazakhstan (NFR-06), and this must be confirmed with the supervisor before collection starts.

## 11. Threats to Validity
- **Single site:** results may not transfer to other parking areas. This is stated as a limitation.
- **Short duration:** two weeks of snow may not represent a full winter.
- **Small driver sample:** 30 arrivals per period is enough for a pilot indication, not for statistical proof. Report confidence intervals where possible.
- **Labeling bias:** labels are made by the team, who also built the system. Mitigated by two-person labeling and the kappa check in Section 4.1.
- **Hawthorne effect:** drivers may behave differently when they know they are in a study. Mitigated by using the same collection method in baseline and pilot.

## 12. Reporting
The final report includes:
1. Technical results table for each condition group, with confidence intervals.
2. Comparison with the simple and majority baselines.
3. Scenario test results (TS-01 to TS-13) with pass or fail.
4. Usage results compared with baseline, including the cases where targets were missed.
5. Error analysis and limitations.
6. Recommendations for the next version, including whether the requirements or targets should change.

Missed targets are reported as they are. They are a result, not a failure of the evaluation.

## 13. Timeline

| Week | Activity |
|---|---|
| 1–2 | Public dataset check (Stage A); begin local collection (Stage B) |
| 3–4 | Complete labeling; kappa check; define splits |
| 5–6 | Baseline measurement (Stage D); build and tune model on validation set |
| 7 | Technical evaluation on test set (Stage C); run scenarios TS-01 to TS-13 |
| 8–9 | Usage pilot (Stage E) |
| 10 | Analysis, error analysis, report writing |

