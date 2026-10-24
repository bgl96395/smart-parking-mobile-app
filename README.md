# Smart Parking

A service for booking parking spaces near business centers in Astana, with entry by license plate.

> Book parking near your destination and drive in by license plate, with the price and terms clear in advance.

**Status:** preparing a pilot at one partner business center.

---

## Contents

- [Problem](#problem)
- [Who it's for](#who-its-for)
- [Solution](#solution)
- [MVP features](#mvp-features)
- [How booked entry works](#how-booked-entry-works)
- [Parking zone statuses](#parking-zone-statuses)
- [Architecture](#architecture)
- [Business model](#business-model)
- [Pilot metrics](#pilot-metrics)
- [Risks and open questions](#risks-and-open-questions)
- [Roadmap](#roadmap)
- [Team](#team)
- [Working with the repository](#working-with-the-repository)

---

## Problem

**Drivers** find it hard to know in advance whether there will be a space near the building they need. Availability data goes stale, and finding the entrance and the space itself takes time.

**Parking owners** need to balance bookings with regular drive-ins, control access, and handle temporary closures: snow removal, repairs, technical failures.

**What people use today:** 2GIS and Yandex Maps, the city parking service, showing up and hoping for the best, calling security, monthly passes, on-site parking management systems.

## Who it's for

| Segment | Who | What they get |
|---|---|---|
| Drivers | Visitors and employees of business centers who need to park at a specific building by a specific time | A guaranteed space, the exact entrance, the final price before payment |
| Partners | Business center owners and managers, parking operators | Revenue from unused quota, guest access management, booking control |

**Early adopters:** tenants, employees and visitors of one partner business center. During the pilot, a designated representative works on the parking side.

## Solution

- A parking map with time selection and the **full price** shown before payment.
- Bookings come from a **quota allocated by the partner**, not from the whole lot.
- Facility details: exact entrance, pedestrian exit, height limit, covered or heated parking.
- **Entry by license plate:** a camera reads the plate, the system checks the booking and opens the barrier.
- **Honest data:** every status shows its source and last update time. If occupancy is unknown, we say so.
- **Operator dashboard:** quota, closures, bookings, manual corrections with a stated reason, entry and exit log.

## MVP features

### Driver

- [ ] Sign up and log in
- [ ] Add a vehicle (license plate)
- [ ] Map and list of parking lots with a facility card
- [ ] Time slot selection, final price calculation
- [ ] Booking and payment
- [ ] Cancellation under the facility's rules, refunds
- [ ] Notifications about bookings, changes and closures
- [ ] Booking history

### Parking operator

- [ ] Manage the quota of bookable spaces
- [ ] Temporary closures (full and partial) with a reason
- [ ] List of bookings and their statuses
- [ ] Manual entry confirmation when the camera or barrier fails
- [ ] Manual zone status correction with a required reason
- [ ] Entry and exit log

### Platform

- [ ] Integration with the partner's access system (camera / barrier)
- [ ] Track confirmed future bookings separately from current occupancy
- [ ] Store event history for auditing and metrics

## How booked entry works

```
Driver                  Platform                  Facility (camera + barrier)
   |  booking + plate       |                                |
   |----------------------->|                                |
   |  confirmation, price   |                                |
   |<-----------------------|                                |
   |                        |      plate read at entrance    |
   |                        |<-------------------------------|
   |                        |  check: is there a valid       |
   |                        |  booking for this plate/time?  |
   |                        |------------------------------->|
   |                        |   yes → open barrier           |
   |                        |   no / failure → operator      |
```

**Fallback:** if the plate isn't recognized or the integration is down, the operator looks up the booking and confirms entry manually. The event is logged as a "manual entry".

## Parking zone statuses

| Status | Meaning |
|---|---|
| Spaces available | Current data shows free spaces |
| Full | Current data shows no free spaces |
| Closed | The operator closed the zone (snow, repairs, failure) |
| Unknown | No data, or the data is stale |

Each status always shows its **source** (sensor / camera / operator) and **last update time**. If the data is older than the allowed threshold, the status automatically switches to "Unknown".

## Architecture

> Draft, to be refined during development. The tech stack is not decided yet.

```
┌──────────────────┐   ┌──────────────────┐
│ Mobile           │   │ Operator         │
│ app              │   │ dashboard (web)  │
└────────┬─────────┘   └────────┬─────────┘
         │        HTTPS / API   │
         └──────────┬───────────┘
            ┌───────▼────────┐
            │  Backend API   │── payment provider
            │                │── notifications (push / SMS)
            │                │── maps
            └───┬────────┬───┘
                │        │
        ┌───────▼──┐  ┌──▼─────────────────┐
        │ Database │  │ Access system      │
        │(bookings,│  │ integration gateway│
        │  log)    │  │ (camera, barrier)  │
        └──────────┘  └────────────────────┘
                │
        ┌───────▼──────────────┐
        │ ML modules           │
        │ (plate recognition,  │
        │  occupancy forecast, │
        │  data quality)       │
        └──────────────────────┘
```

**Core backend modules:**

- **Users and vehicles:** accounts, license plates.
- **Facilities:** parking lots, zones, entrances, features, rates.
- **Quotas and availability:** spaces allocated for booking, closures, overlap checks.
- **Bookings:** booking lifecycle (created → paid → fulfilled / cancelled / no-show).
- **Payments:** payment, refunds, compensation.
- **Access:** receiving camera events, checking bookings, sending the open command, manual confirmation.
- **Statuses and data:** current zone state, source, last update time, staleness rule.
- **Audit:** entry and exit log, operator manual actions with reasons.

**ML directions:**

- License plate recognition, if the facility has no ready-made solution or its quality isn't good enough.
- Forecasting occupancy and the likelihood of free spaces by time.
- Detecting stale and incorrect statuses.

## Business model

- **Main revenue:** a commission on every fulfilled paid booking, under a contract with the parking operator.
- **Additional:** a one-time facility onboarding fee.
- **Later:** a subscription for organizations to a parking management and data quality module.

The commission and rates will be set based on pilot results. Total parking payments are not the platform's revenue.

**Main costs:** facility survey, development, access system integration, servers, maps, connectivity, support, on-site visits, payment processing, refunds and compensation.

## Pilot metrics

| Group | Metric |
|---|---|
| Booking reliability | Share of fulfilled bookings among drivers who arrived within the booking terms |
| Failures | "No space" cases, denied entries, requests for manual help |
| Convenience | Time from arriving at the facility to parking |
| Retention | Repeat paid bookings, cancellations, no-shows |
| Data quality | Share of stale and incorrect statuses in spot checks, how fast the operator fixes them |
| Unit economics | Revenue per booking after variable costs, facility servicing cost, partner's willingness to continue |

## Risks and open questions

- **Integration dependency.** Without access to the partner's cameras and barrier, entry by license plate doesn't work. Equipment compatibility must be checked in advance.
- **Quota vs. regular drive-ins.** We need a mechanism that guarantees a free space for each booking, otherwise the core promise of the service breaks.
- **Personal data.** License plates and trip history are personal data. We need user consent and rules for storage and deletion.
- **Cancellation and no-show rules.** We need to define how long a booking is held, what happens when a driver is late, and who pays compensation if there's no space.
- **Payments.** Choosing a payment provider that works well for users in Kazakhstan.
- **Winter.** Snow removal and reduced capacity must be reflected in quotas and statuses quickly.
- **Defensible advantage.** There isn't one yet. It will be built through quota agreements, proven integrations and a track record of fulfilled bookings.

## Roadmap

1. **Pilot preparation:** choose the facility, survey it, agree on the quota and access to the entry system.
2. **MVP:** booking, payment, operator dashboard, manual entry confirmation.
3. **Integration:** automatic entry by license plate.
4. **Pilot:** launch at one business center, collect metrics.
5. **After the pilot:** set rates and commission, onboard more facilities, develop the ML modules.

## Team

| Member | Responsibilities |
|---|---|
| **Ansar** | Backend, system architecture |
| **Bigali** | Frontend, machine learning |

## Working with the repository

- Pull the latest changes before starting work: `git pull`.
- Everyone works in their own branch; changes reach `main` through pull requests.
