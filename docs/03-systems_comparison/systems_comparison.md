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

| Criterion | 2GIS / Yandex Maps | UIMS Parking | Car ID / AI Parking | Parquery | Parkopedia |
|---|---|---|---|---|---|
| 1. Real-time availability | [verify] | Described as showing free spaces on a map **(vendor claim, app description)**; coverage and freshness not verified | [verify] | Vendor claim: occupancy from existing cameras **(vendor claim)** | Vendor claim: combines current data with prediction models **(vendor claim)** |
| 2. Freshness indication | [verify] | [verify] | [verify] | [verify] | [verify] |
| 3. Unknown-state handling | [verify] | [verify] | [verify] | [verify] | [verify] |
| 4. Advance reservation | [verify] | [verify] | Not confirmed in research **[verify]** | Not confirmed **[verify]** | Not confirmed **[verify]** |
| 5. Price before arrival | [verify] | [verify] | [verify] | [verify] | [verify] |
| 6. Camera-based detection | No (map data) **[verify]** | [verify] | Plate recognition at entry **(vendor claim)** | Yes **(vendor claim)** | [verify] |
| 7. Operator control | Not applicable to user-facing map **[verify]** | [verify] | Operator panel mentioned **(vendor claim)** | [verify] | [verify] |
| 8. Presence in Astana | Yes, as map services **[verify]** | Yes, launched September 2026 **(official announcement)** | Kazakhstan-based **(vendor claim)** | Not confirmed for Astana **[verify]** | Not confirmed **[verify]** |

**How to fill the [verify] cells:** open each app or site, test it on at least three days at different times, and record one of: "yes, shown", "no", "not shown", with a screenshot. Save screenshots in `docs/04-systems_comparison/screenshots/` only if they contain no personal data.

## 5. Observations from the Comparison
[Fill in after testing. Examples of what to look for:]
- Whether any system shows a timestamp with its availability data.
- Whether any system shows "unknown" when it has no current data, or instead shows the last known value.
- Whether availability in the city app is accurate compared with what you observe on site.

## 6. Gap
[Write this section only after completing the table. Do not claim a gap that the tests do not confirm.]

Current draft hypothesis: no system in the comparison clearly shows the reliability of its availability information, including when the data is unknown. This hypothesis must be tested. If the city app or 2GIS already shows freshness and unknown states, the gap must be restated or dropped.

## 7. Limitations
- Vendor claims were not independently verified unless stated.
- Availability data changes over time; the test dates must be recorded.
- Local solutions (such as ParkWS and P360) are not in the main table and can be added if tested.
- Reservation features were not evaluated in detail because the chosen direction (availability trust) excludes them.