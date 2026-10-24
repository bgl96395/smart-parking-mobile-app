# Existing Systems Comparison

## 1. Purpose and Method
This comparison identifies what existing solutions already offer for parking availability and where a gap remains for a trustworthy availability system at one business center in Astana.

**Method:** each system was reviewed through its official website, app store description, or published documentation. Where a feature is stated only by the vendor, it is marked **(vendor claim)**. Where it was tested directly, the date and method are given. Vendor claims are not independently verified.

## 2. Systems Selected
Five systems were chosen to cover different approaches: a general map service, a city service, a local vendor, a camera-based specialist, and an international platform.

1. **2GIS / Yandex Maps** (general map services with parking information)
2. **UIMS Parking (city parking service, Astana)**
3. **Car ID / AI Parking** (Kazakhstani platform with license plate recognition)
4. **Parquery** (camera-based occupancy detection, international)
5. **Parkopedia** (parking data and forecasting, international)

## 3. Comparison Criteria
1. **Real-time availability:** does the system show current free spaces?
2. **Data freshness indication:** does it show when the data was last updated?
3. **Unknown-state handling:** does it distinguish "no data" from "free" or "full"?
4. **Advance reservation:** can the user reserve a space in advance?
5. **Price shown before arrival:** is the full cost visible before the driver goes?
6. **Camera-based detection:** does the system use cameras for occupancy?
7. **Operator control:** can the operator correct data or mark closures?
8. **Local market presence:** is the system deployed in Astana or Kazakhstan?

## 4. Comparison Table

| Criterion | UIMS Parking (Astana) | Car ID / AI Parking (KZ) | ParkWS (KZ) | Parquery (intl.) | Parkopedia (intl.) |
|---|---|---|---|---|---|
| 1. Real-time availability | Map app shows the number of free spaces (app description) | Not stated | Not stated | Occupancy detected from existing cameras (vendor claim) | Current data combined with prediction models (vendor claim) |
| 2. Data freshness shown | Not stated | Not stated | Not stated | Not stated | Not stated |
| 3. Unknown-state handling | Not stated | Not stated | Not stated | Not stated | Not stated |
| 4. Advance reservation | Not stated (sessions, subscriptions, permits) | Not stated | Not stated | Not stated | Not stated (forecasting described) |
| 5. Price before arrival | Not stated (payment sessions) | Not stated (QR payment: Kaspi, Halyk) | Not stated (QR payment) | Not stated | Not stated |
| 6. Camera-based detection | Not stated | Yes, license plate recognition at entry | Yes, license plate recognition | Yes, occupancy from cameras | Not stated |
| 7. Operator control | Not stated | Guest access, load and revenue reports, API integrations | Access lists, event log, local controller | Dashboards and API | Not applicable (data provider) |
| 8. Presence in Astana | Yes, launched 14 September 2026 | Kazakhstani platform; Astana not stated | Not stated | Not stated | Not stated |

**Sources:** UIMS Parking app description and official announcement; Car ID / AI Parking and ParkWS vendor websites; Parquery and Parkopedia product materials. All vendor claims are unverified.

## 5. Gap
Based on the published descriptions of the reviewed systems, none of them clearly states how current its availability information is, or what it shows when the data is missing. The vendor materials describe free-space information but do not mention data freshness or an "unknown" status.

This observation is based on public descriptions only and has not been verified by direct testing. It is the basis for the proposed system, which treats data freshness and unknown states as core features.

## 6. Limitations
- Vendor claims were not independently verified unless stated.
- Availability data changes over time; the test dates must be recorded.
- Local solutions (such as ParkWS and P360) are not in the main table and can be added if tested.
- Reservation features were not evaluated in detail because the chosen direction (availability trust) excludes them.