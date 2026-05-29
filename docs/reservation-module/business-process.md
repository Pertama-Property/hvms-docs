---
sidebar_position: 1
sidebar_label: Business Process
---

# Business Process — Reservation Module

The Reservation module handles the full lifecycle of a guest booking at Pertama Property — from initial creation through to check-in handoff. All reservations are created manually by Front Desk agents. Channel manager pull (OTA sync) is planned for Phase 2.

---

## Overview

A reservation records the guest's intended stay: arrival and departure dates, room type, rate plan, number of guests, booking source, and any special requests. Each reservation progresses through a defined status lifecycle and connects directly to the check-in wizard, guest folio, and room status board.

---

## Reservation Status Lifecycle

```
Draft (Hold) → Confirmed → Checked In → Checked Out
                    ↓
                No-show
```

| Status | Description |
|---|---|
| **Draft / Hold** | Reservation created but not yet confirmed. No room assignment. Editable without restrictions. |
| **Confirmed** | Reservation confirmed. Room type selected; specific room optional (can be assigned at check-in). Rate plan locked. |
| **Checked In** | Guest has completed the check-in wizard. Room assigned and occupied. Folio active. |
| **Checked Out** | Guest has departed. Folio settled and locked. Room released to Dirty. |
| **No-show** | Guest did not arrive after the no-show cutoff (2 hours after scheduled check-in time). Room released to Available. |

---

## Creating a Reservation — 6-Step Wizard

The reservation wizard guides the Front Desk agent through all required information before confirming a booking.

### Step 1 — Guest

The agent searches for an existing guest profile by name, phone, email, or ID number. If the guest is new, they can create a profile inline without leaving the wizard. The guest profile links the reservation to stay history, preferences, and future folios.

### Step 2 — Stay details

The agent enters the core booking information:

- Check-in date and time
- Check-out date and time (minimum 1 night)
- Number of adults and children
- Booking source (Walk-in, Phone, Airbnb, Booking.com, Agoda, Expedia, Corporate, Direct, or any custom source configured in Settings)

### Step 3 — Room

The agent selects the room type. Assigning a specific room is optional at this stage — it can be done at check-in. If a specific room is selected, the system checks availability across the full stay period and prevents double-booking.

### Step 4 — Rate & charges

The system auto-selects the active rate plan matching the room type and arrival date. The agent can override the nightly rate with a mandatory reason (e.g. long-stay discount, management approval). A deposit amount can be set here — collected at check-in.

### Step 5 — Special requests

Free-text field for any guest requests: bed configuration, baby cot, early check-in, late check-out, dietary requirements, airport transfer. Special requests are surfaced as an amber banner in the check-in wizard Step 1 so the agent is reminded at arrival.

### Step 6 — Review & confirm

A full summary of all booking details before confirmation. The agent clicks **Confirm reservation** to lock the booking at **Confirmed** status with a system-generated reservation number (RES-XXXX).

---

## Reservation List

The reservation list is the Front Desk agent's primary daily workspace. It shows all reservations grouped by status across three tabs:

1. **Not yet checked in** — Confirmed reservations for today and future arrivals
2. **Checked in** — Currently in-house guests
3. **No-show** — Reservations marked as no-show

Agents can search by guest name, reservation number, or room number. A **Today** filter narrows the list to arrivals and departures for the current date.

---

## Availability Check

When a specific room is selected — either during reservation creation or at check-in — the system checks whether the room is available for every night of the stay. A room is unavailable if:

- It has a Confirmed or Checked In reservation overlapping any night of the requested period
- It is set to Out of Order for any night of the period

The room picker only shows selectable rooms that pass this check.

---

## Rate Plan Logic

Rate plans are configured in Settings and linked to a room type and a validity date range. When a reservation is created, the system auto-selects the rate plan that matches:

1. The selected room type
2. The guest's check-in date falling within the plan's validity range

If multiple rate plans match, the most specific one applies. If no rate plan matches the dates, the system warns the agent and defaults the nightly rate to IDR 0, which must be overridden manually.

---

## Booking Sources

Booking sources are configured in Settings. Three system sources cannot be edited or deleted: **Walk-in**, **Phone**, and **Direct**. Additional OTA and corporate sources can be added by an Admin. A deactivated source no longer appears in the reservation wizard dropdown but existing reservations are unaffected.

---

## No-Show Process

A reservation can be marked as No-show once the no-show cutoff has passed (2 hours after the scheduled check-in time). The agent opens the check-in wizard and selects **Mark no-show**. On confirmation:

- Reservation status → **No-show**
- Assigned room (if any) → **Available**
- Event recorded in audit log: agent name, timestamp, reason (optional)

The no-show button is disabled before the cutoff to prevent accidental marking.

---

## Editing a Reservation

A Confirmed reservation can be edited by any Front Desk agent. Changes to dates, room type, or rate plan are permitted. All edits are recorded in the audit log with the agent's name and timestamp. A Checked In or Checked Out reservation cannot have its core booking details changed — only the folio can be modified during the stay.

---

## Cancellation

Reservation cancellation is not handled within the reservation wizard in MVP. To cancel a reservation before check-in, the Front Desk agent sets the status to **Hold** (Draft) and adds a note. Full cancellation workflow with policy enforcement is planned for Phase 2.

---

## Roles & Permissions

| Action | Front Desk | Manager | Admin |
|---|---|---|---|
| Create reservation | ✓ | ✓ | ✓ |
| Edit Confirmed reservation | ✓ | ✓ | ✓ |
| Override nightly rate | ✓ (with reason) | ✓ | ✓ |
| Mark no-show | ✓ | ✓ | ✓ |
| View all reservations | ✓ | ✓ | ✓ |
| Configure rate plans | ✗ | ✗ | ✓ |
| Configure booking sources | ✗ | ✗ | ✓ |

---

## Related Modules

- **Front Desk — Check-in**: The check-in wizard is triggered from the reservation list and completes the guest arrival process.
- **Guest Profiles**: Each reservation is linked to a guest profile. Creating a reservation without an existing profile prompts inline guest creation.
- **Guest Folio**: A folio is auto-created when the check-in wizard is confirmed. Folio charges are linked to the reservation.
- **Room Status**: Room status changes to Occupied when check-in is confirmed and to Dirty when check-out is completed.
