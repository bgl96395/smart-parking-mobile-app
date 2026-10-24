# Project Proposal: Trustworthy Parking Availability Monitoring for a Business Center in Astana

## 1. Project Information
- **Project title:** Smart Parking Mobile App 
- **Team members:** Ansar Keles - backend and architecture, Bigali Nugmash - frontend and machine learning
- **Supervisor:** Askar Khaimuldin
- **Repository:** https://github.com/bgl96395/smart-parking-mobile-app
- **Task tracker:** https://bigali.youtrack.cloud/projects/SPM

## 2. Problem Statement
Drivers visiting business centers in Astana often cannot know whether a parking space is actually usable when they arrive. Existing map services show availability that may be outdated, and the physical situation changes with snow, markings, reserved spaces, and temporary closures. "Free" can mean four different things: physically empty, usable, permitted for this driver, or confirmed by current data. Current solutions rarely show which of these they actually know.

Parking operators have a related problem: they cannot easily see whether the occupancy data they publish is reliable, or how much manual work is needed to keep it correct.

## 3. Target Users
- **Drivers:** visitors and employees who need a space near a specific building at a specific time.
- **Parking operator (buyer):** the property manager or operator of one partner business center who is responsible for data quality and guest experience.
- **Pilot users:** tenants, employees, and visitors of one partner business center.

## 4. Aim
To develop a mobile system that shows parking availability together with a data-trust status, so that drivers can see when information is current, unknown, or unavailable, and operators can verify and correct it.

## 5. Objectives
1. Identify the availability problem and its frequency in one pilot business center through observation and interviews.
2. Compare existing solutions (2GIS, Yandex Maps, city parking services in Astana, local vendors such as Car ID, AULA.PASS, ParkWS, and international providers such as Parquery) against consistent criteria, focusing on data freshness and status transparency.
3. Define requirements, including the four availability states and the acceptance criteria for each.
4. Design the architecture and select key technologies with justified alternatives.
5. Implement a prototype for one parking zone of 15–30 visible spaces.
6. Evaluate the prototype's classification accuracy, latency, and driver benefit against a defined baseline.

## 6. Proposed Solution
The first version focuses on one parking area with a clear data source and provides:
- A map or schematic view of spaces with the status *free*, *occupied*, *temporarily unavailable*, or *unknown*.
- The time of the last confirmed observation for each status.
- Automatic handling of stale data: if no fresh observation arrives, the status changes to *unknown*, not the last known value.
- An operator panel for temporary closures (snow, repairs, reserved spaces), manual corrections with a required reason, and a log of changes.
- A quality report: share of stale statuses, correction count, and recognition confidence over time.

Reservation, payments, barrier control, and forecasting are deliberately excluded from the first version. Each of them creates operational obligations that are not needed to test the core hypothesis.

## 7. Expected Outcomes
- A working prototype with measured classification quality across lighting and weather conditions where data is available.
- Evidence of how often drivers would have made unsuccessful trips, based on the baseline measurement.
- A documented description of the data-trust model, which can be reused for expansion.

## 8. Project Scope

### In scope
- Mobile application for drivers (platform: [Android / iOS]).
- Operator web interface for closures, corrections, and reports.
- Backend for status processing, data freshness checks, and logging.
- Occupancy detection from images of one existing camera source, with a documented permission for use.
- Pilot with one partner business center and one responsible representative.
- Evaluation against the verification plan.

### Out of scope
- Reservation, payments, and quota management.
- Barrier integration and license plate recognition.
- Full coverage of all spaces in a large parking facility.
- Forecasting of arrival-time availability.
- Multiple cities, vendors, or commercial deployment.

### Assumptions
- The partner allows image access from an existing camera with a suitable view of the spaces.
- The partner has a responsible person who confirms the reference state during evaluation.
- Personal data is avoided: the system processes space occupancy, not license plates.

### Constraints
- Time until the pre-defense dates set by the department.
- Team of two people with limited hours per week.
- Site access and camera permissions depend on the partner.

## 9. Competitive Positioning
The market already includes mature features: city apps with free-space information, parking reservation platforms, camera-based occupancy detection (e.g., Parquery), and access systems with local controllers. The project does not claim to be first in any of these categories. Its intended difference is narrower: an explicit data-trust model for one site, with unknown states shown to the driver and corrections recorded by the operator.

This positioning must be validated, not assumed. The comparison in `docs/04-systems_comparison` should test whether existing apps actually show freshness and unknown states.

## 10. Engineering Complexity
- Classification of individual spaces from images under varying lighting, snow cover, and partial occlusion.
- Handling of time-based staleness and loss of camera view.
- Correction of cumulative errors if counting-based approaches are used.
- Keeping the displayed status consistent with the operator's corrections.
