# Smart Parking System — Design Prototype

A prototype of a service that lets drivers **book a parking spot near a business center in Astana in advance** and **enter by license plate recognition**, with the full price and conditions shown before payment.

> The project name is temporary and will be changed later. Names, prices and numbers in the design are sample data for the prototype.

## Design links

| Platform | Who uses it | Link |
|---|---|---|
| 📱 **Mobile app** | Drivers: visitors and employees of business centers | [Open mobile design](https://claude.ai/artifact/U1VYAneJLSWpBs1v4F2A3g) |
| 💻 **Web dashboard** | Parking operators and business center managers | [Open web design](https://claude.ai/artifact/UBDpRmhQ1FdVo6bXsE5zDJ) |

All screens are also shown below, so the design can be reviewed right here on GitHub.

## Contents

- [User flow](#user-flow)
- [Mobile app (drivers)](#-mobile-app-drivers)
  - [1. Parking map](#1-parking-map)
  - [2. Parking details](#2-parking-details)
  - [3. Booking](#3-booking)
  - [4. Payment](#4-payment)
  - [5. My booking and entry](#5-my-booking-and-entry)
- [Web dashboard (parking operators)](#-web-dashboard-parking-operators)
- [Parking statuses used across the design](#parking-statuses-used-across-the-design)
- [How we differ from existing solutions](#how-we-differ-from-existing-solutions)

---

## User flow

```
Parking map → Parking details → Booking → Payment → My booking → Entry by plate
                                                                    │
                                                    camera fails ───┴──→ Operator confirms entry manually
```

The driver goes from searching for parking to driving through the barrier in five screens. The operator sees every booking and steps in only when automatic entry fails.

---

## 📱 Mobile app (drivers)

### 1. Parking map

<img src="screens/01-parking-map.png" alt="Parking map" width="300">

**Purpose:** show the driver which parkings near their destination they can use, and how reliable that information is.

**What is on the screen:**

- **Search bar** at the top with the destination: *Orken Tower BC, Mangilik El Ave*. The destination is marked on the map with a star.
- **Filters** under the search bar:
  - **Today 2:00 PM**: planned arrival time. Availability is calculated for this time, not just for "now".
  - **Covered**, **Heated**: important in Astana's winter.
  - **Height 2 m+**: for vans and tall cars.
- **Map pins** with the status of each parking:
  - 🟢 **8 to book**: the number of spots still available from the partner's booking quota.
  - 🔴 **Full**: no free spots.
  - ⚫ **Closed**: temporarily closed by the operator.
  - 🟠 **Unknown** (dashed outline, "?"): no fresh data.
- **"Near your destination"** bottom sheet with a list of 3 parkings. Each card shows:
  - name, price per hour (**400 ₸/h**, **300 ₸/h**, **500 ₸/h**);
  - type and walking distance: *Underground · heated · 90 m to entrance*;
  - status badge plus **data source and age**: *camera · 2 min ago*, *sensors · 5 min ago*, *no data for 1 h*.
- **Bottom navigation:** Map, My bookings, Profile.

**Key design decision:** a parking with stale data is shown as **"Occupancy unknown"**, not as free. The driver always sees where the status came from and how old it is, so they can decide whether to trust it.

---

### 2. Parking details

<img src="screens/02-parking-details.png" alt="Parking details" width="300">

**Purpose:** give the driver everything they need to know **before** driving there, so there are no surprises at the entrance.

**What is on the screen:**

- **Header** with a schematic view of the parking level, the *Partner parking* label and the spot marker **B-14**.
- **Name and location:** *Orken Tower BC Parking · Mangilik El Ave · underground, level −1*.
- **Availability block** (green):
  - **Spots available · 8 of 20 for booking**, with a progress bar;
  - source and freshness: *Source: entry camera · updated 2 min ago*;
  - *5 bookings already confirmed for 2:00 PM*. Confirmed future bookings are shown separately from current occupancy.
- **Four facility cards:**
  - **Height 2.1 m**: entry limit;
  - **Covered, warm**: heated in winter;
  - **Entry from Syganak St**: barrier with camera, i.e. the exact entrance the driver needs;
  - **Lift to lobby**: 90 m to the business center entrance.
- **Rates:**
  - weekdays 8 AM–8 PM: **400 ₸ / hour**;
  - evenings and weekends: **250 ₸ / hour**;
  - cancellation: **free up to 30 min before**.
- **Bottom bar:** *from 400 ₸/h* and the main button **Book a spot**.

**Key design decision:** the screen answers the driver's usual questions in advance: will my car fit, which entrance, how far to the lobby, how much it costs, and how to cancel.

---

### 3. Booking

<img src="screens/03-booking.png" alt="Booking" width="300">

**Purpose:** choose time and vehicle, and see the **final price** before paying.

**What is on the screen:**

- **Arrival time · today:** quick slots **1:30**, **2:00** (selected), **2:30**, **3:00**.
- **Duration:** a counter with **−** and **+**, currently **2 h**, with the end time shown: *until 4:00 PM*.
- **Vehicle:** a card with a Kazakhstani license plate **777 ABA 01** (KZ flag strip) and *Toyota Camry · height 1.45 m · fits*. The app checks the car's height against the parking's height limit.
- **Price breakdown:**
  - Parking 2 h × 400 ₸: **800 ₸**
  - Service fee: **100 ₸**
  - **Total: 900 ₸**
- **Guarantee note** (green shield): *Your spot is guaranteed from the partner's quota. If no spot is available, you get a full refund plus compensation. Free cancellation until 1:30 PM.*
- **Button:** **Continue to payment · 900 ₸**.

**Key design decision:** the price updates instantly when the time or duration changes, and the service fee is shown as a separate line. There are no hidden costs.

---

### 4. Payment

<img src="screens/04-payment.png" alt="Payment" width="300">

**Purpose:** pay with a method that people in Kazakhstan actually use.

**What is on the screen:**

- **Booking summary card** (dark): *Orken Tower BC Parking*, **Booking** badge, **Today, 2:00–4:00 PM**, plate *777 ABA 01 · 2 h*, total **900 ₸**.
- **Payment method:**
  - **Kaspi**: pay in the Kaspi.kz app (selected by default as the most popular local method);
  - **Card •• 4821**: saved bank card;
  - **Employer account**: corporate payment, *corporate limit: 15 000 ₸*. This is for business center employees whose company pays for parking.
- **Note:** *The total will not change after payment. The receipt goes to your email and to "My bookings".*
- **Button:** **Pay 900 ₸**.

**Key design decision:** the corporate account option targets the main early adopters: employees of tenant companies in the business center.

---

### 5. My booking and entry

<img src="screens/05-active-booking.png" alt="My booking" width="300">

**Purpose:** get the driver from the street to the parking spot without stopping at the barrier.

**What is on the screen:**

- **Booking confirmed** badge.
- **Large license plate** **777 ABA 01**: the plate the camera will look for.
- **Instruction:** *Drive up to the barrier — the camera will recognize your plate and open the gate automatically.*
- **Three info tiles:**
  - **Entry from 1:45 PM**: the driver can enter 15 minutes before the booking starts;
  - **Booking 2:00–4:00**;
  - **Zone B · −1**.
- **How to get to your spot**, step by step:
  1. Enter from Syganak St, right lane. Height limit 2.1 m.
  2. Level −1, zone B: spots marked "Reserved".
  3. Lift B goes straight to the business center lobby.
- **Two actions at the bottom:**
  - **Barrier didn't open?**: backup entry. It shows a booking code and a button to call the operator, who confirms entry manually. The event is logged as a "manual entry".
  - **Cancel booking**: under the cancellation rules from the details screen.

**Key design decision:** automatic entry by plate is the main path, but there is always a backup. A failed camera never leaves the driver stuck at the barrier.

---

## 💻 Web dashboard (parking operators)

<img src="screens/06-operator-dashboard.png" alt="Operator dashboard" width="800">

**Purpose:** one screen where the operator of the partner business center controls the booking quota, zones, bookings and entry problems.

**Navigation (left sidebar):** Overview, Bookings, Quota and rates, Zone closures, Entry log, Reports.

**Header:** *Orken Tower BC Parking*, *Today, October 24 · camera data updated 1 min ago*, and the main action **+ Temporary closure**.

**Key numbers (4 cards):**

| Card | Value | Meaning |
|---|---|---|
| Booking quota | **20 spots**, free now: 8 | How many spots the partner gave for booking, and how many of them are free |
| Bookings today | **34**, fulfilled 21 · cancelled 3 | Daily booking volume and outcome |
| Plate-based entry | **20 of 21**, automatic | How many entries went through the camera without help |
| Manual help | **1**, plate not recognized | Highlighted in yellow: cases that need the operator's attention |

**Parking zones:**

| Zone | Details | Status |
|---|---|---|
| Zone A · −1 | tenants · 60 spots | 🟢 Available |
| Zone B · −1 (booking) | booking quota · 20 spots | 🟢 Available |
| Ground level, yard | snow removal until 12:00 PM · Aigerim S. | ⚫ Closed |
| Zone C · −2 | camera offline for 40 min | 🟠 Unknown |

A closed zone always shows the **reason** and the **responsible person**. A zone without camera data is shown as **Unknown** with the reason, never as free.

**Upcoming bookings:**

| Plate | Time | Guest | Status |
|---|---|---|---|
| 777 ABA 01 | 2:00–4:00 PM | Meeting, 12th floor | Expected |
| 125 KTA 01 | 1:30–3:30 PM | Employee | Parked |
| 482 BCD 02 | 1:00–2:00 PM | Courier | Manual entry |
| 309 MNA 01 | 12:00–1:00 PM | Visitor | No-show |

**Manual correction** (bottom block): the operator enters a **license plate**, chooses an **action** (for example, *Open barrier*), and must fill in the **reason** (for example, *Dirty plate, camera could not read it*). The **Apply and log** button saves the action to the entry log. Without a reason, the action cannot be applied.

**Key design decision:** every manual action has a required reason and is written to the log. This keeps the operator accountable and gives data for the pilot metrics (share of manual entries, camera failures).

---

## Parking statuses used across the design

The same four statuses are used in the mobile app and the dashboard:

| Status | Look | Meaning |
|---|---|---|
| Spots available | Green | Current data shows free spots |
| Full | Red | Current data shows no free spots |
| Closed | Dark gray | The operator closed the parking or zone (snow, repairs, failure) |
| Unknown | Orange, dashed outline, "?" | No data, or the data is stale |

Each status is shown with its **source** (camera, sensors, operator) and **last update time**. Statuses differ not only by color but also by icon and outline, so they can be told apart by people with color vision deficiency.

---

## How we differ from existing solutions

Existing apps (for example, Parqour) focus on "drive in → pay at exit". Our focus is:

1. **Booking in advance** with a guaranteed spot from the partner's quota.
2. **Honest data**: we show the source and update time, and say "unknown" when there is no data.
3. **Backup entry** through the operator if the camera or barrier fails.
4. **Local payment**: Kaspi and corporate accounts.
