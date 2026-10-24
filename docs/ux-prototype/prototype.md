# Smart Parking System — Design Prototype

A prototype of a service that lets drivers **book a parking spot near a business center in Astana in advance** and **enter by license plate recognition**, with the full price and conditions shown before payment.

> The project name is temporary and will be changed later.

## Design links

| Platform | Who uses it | Link |
|---|---|---|
| 📱 **Mobile app** | Drivers: visitors and employees of business centers | [Open mobile design](https://claude.ai/artifact/U1VYAneJLSWpBs1v4F2A3g) |
| 💻 **Web dashboard** | Parking operators and business center managers | [Open web design](https://claude.ai/artifact/UBDpRmhQ1FdVo6bXsE5zDJ) |

**How to view:** open a link and press **Play** on a screen to click through it like a real app. Buttons, time selection, the duration counter and payment methods all work.

---

## 📱 Mobile app (drivers)

User flow: **Map → Parking details → Booking → Payment → Active booking**

### 1. Parking map
The driver enters a destination and sees parkings nearby.
- Search bar and filters: arrival time, covered, heated, height limit.
- Each parking shows one of four statuses:
  - 🟢 **Spots available** — free spots are confirmed by data.
  - 🔴 **Full** — no free spots.
  - ⚫ **Closed** — temporarily closed (snow removal, repairs, technical failure).
  - 🟠 **Unknown** — no fresh data. We show this honestly instead of guessing.
- Every status shows **where the data comes from** (camera, sensors) and **when it was updated**.

### 2. Parking details
Everything the driver needs to know before driving there:
- How many spots are available **for booking** (the partner's quota).
- Exact entrance, walking route to the lobby, height limit, covered or heated.
- Rates and cancellation rules.

### 3. Booking
- Choose arrival time and duration.
- Choose the vehicle by license plate. The app checks that the car fits under the height limit.
- The **full price** is shown before payment and updates instantly.
- Guarantee: if no spot is available on arrival, the driver gets a full refund plus compensation.

### 4. Payment
- Payment methods: Kaspi, bank card, or employer's corporate account.
- The total is final and will not change after payment.

### 5. Active booking and entry
- A large license plate display and the booking time window.
- The camera at the barrier recognizes the plate and opens the gate automatically.
- Step-by-step directions to the spot.
- **"Barrier didn't open?"** shows a backup entry option: a booking code and a button to call the operator.

---

## 💻 Web dashboard (parking operators)

One screen where the operator controls bookings and access:
- **Key numbers:** booking quota, bookings today, automatic plate entries, cases needing manual help.
- **Parking zones:** status of each zone, including temporary closures with reason and responsible person.
- **Upcoming bookings:** plate, time, guest type and status (expected, parked, manual entry, no-show).
- **Manual correction:** the operator can open the barrier or fix a booking. A **reason is required**, and every action is saved to the log.

---

## How we differ from existing solutions

Existing apps (for example, Parqour) focus on "drive in → pay at exit". Our focus is:
1. **Booking in advance** with a guaranteed spot from the partner's quota.
2. **Honest data** — we show the source and update time, and say "unknown" when there is no data.
3. **Backup entry** through the operator if the camera or barrier fails.
4. **Local payment** — Kaspi and corporate accounts.

> Names, prices and numbers in the design are sample data for the prototype.