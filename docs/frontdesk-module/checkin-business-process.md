---
sidebar_position: 1
sidebar_label: Business Process
---

# Business Process — Check-in Module

The check-in module handles the complete guest arrival process — from identity verification and room assignment through to payment collection and key card issuance. All check-ins are completed by a Front Desk agent using a 6-step wizard triggered from the reservation list.

---

## Overview

Check-in transforms a **Confirmed** reservation into an **In-house** stay. It creates the guest folio, records all payments collected at arrival, assigns the physical room, and produces a timestamped audit trail of the entire transaction. The wizard enforces a sequential flow — each step must be completed before the next unlocks.

---

## Check-in Status Flow

```
Confirmed Reservation
        ↓
  Check-in wizard (6 steps)
        ↓
    Checked In
  (Folio created · Room occupied)
```

A reservation can also be marked as **No-show** from within the check-in wizard once the no-show cutoff has passed (2 hours after scheduled check-in time).

---

## The 6-Step Check-in Wizard

### Step 1 — Booking details

The agent reviews the reservation summary: guest name, check-in and check-out dates and times, number of nights, number of guests (adults and children), rate plan, and booking source. Any special requests added at reservation are displayed as an amber alert banner so the agent can action them before the guest reaches their room (e.g. baby cot, early arrival, dietary requirements).

This step is read-only — booking details cannot be edited here. If changes are needed, the agent cancels the wizard and edits the reservation directly.

### Step 2 — Identity verification

The agent verifies the guest's physical ID document against the profile on file. Identity fields pre-fill from the guest profile:

- ID type and ID number
- Full name as on ID
- Nationality

The agent checks each field against the document held by the guest and updates any field that has changed (e.g. renewed passport). If the ID expiry date is within 30 days, an amber warning is shown. If already expired, a red warning is shown. Neither blocks check-in — the agent notes the discrepancy and proceeds.

On completion, the agent marks the step as **Verified** and advances.

### Step 3 — Room assignment

The agent assigns the specific room for the stay. If a room was pre-assigned at reservation, it is shown pre-selected and the agent confirms it. If no specific room was set, the agent selects from a tile grid of all rooms of the reserved type.

The room grid shows only rooms of the correct type and filters by availability:

| Tile colour | Status | Selectable |
|---|---|---|
| Green | Available (clean) | Yes |
| Blue | Cleaning / Inspect | No |
| Amber | Occupied | No |
| Red | Dirty | No |
| Grey | Out of Order | No |

The agent can switch the view filter to see all room types if an upgrade or same-type change is needed. A **Change room** link on the confirmed room tile allows the agent to re-pick without restarting the wizard.

### Step 4 — Adds on

The agent records any services the guest is purchasing at check-in: airport transfer, welcome basket, room decoration, spa booking, or any other ad-hoc item. Each line has a free-text description and an amount in IDR.

Adds on are posted directly to the guest folio as charges on check-in confirmation. They are **not collected as cash at check-in** — the guest settles adds on charges at check-out along with any other folio balance.

The agent can skip this step if there are no adds on. If skipped, no adds on lines appear on the folio.

### Step 5 — Payment

Step 5 collects two separate payments sequentially. The agent must complete both before the check-in can be confirmed.

**Card 1 — Room charge (accommodation)**

The agent first determines how the accommodation will be settled:

- **Pay now at check-in**: The full accommodation amount (nightly rate × number of nights) is collected immediately. At check-out, the guest owes IDR 0 for the room unless new charges are added during the stay.
- **Defer to check-out**: No room charge is collected now. The full accommodation amount is added to the folio balance and settled at departure.

Once the option is selected, the agent enters the payment method (Cash, Card via EDC terminal, or QRIS), the tendered amount, and the system displays the change to return. The agent clicks **Confirm room charge collected** to lock this card and unlock the deposit card.

**Card 2 — Security deposit**

After room charge collection is confirmed, the deposit card unlocks. The agent toggles deposit collection on or off (default: on) and enters the deposit amount (default from the rate plan or reservation setting). The same payment method options are available.

The deposit is posted to the folio as a credit line. It is applied against any outstanding balance at check-out. Any remaining deposit after all charges are settled is refunded to the guest.

**Adds on to folio**: The adds on entered in Step 4 are not collected here. They appear in the folio summary at the bottom of Step 5 as amber "posted to folio" lines — a visual reminder to the agent of what the guest will owe at check-out.

**Folio preview strip**: The bottom of Step 5 shows the complete guest folio as it will appear after confirmation — all charge lines, payment credits, and the resulting balance due at check-out. This gives the agent a final sanity check before proceeding.

### Step 6 — Key card & confirm check-in

The agent issues physical key cards and confirms handoff. The agent selects the number of key cards issued (maximum 2 per room) and ticks the confirmation checkbox to confirm the cards have been physically handed to the guest.

A final summary shows: guest name, room, check-in and check-out dates, total collected at check-in, and the balance due at check-out.

Clicking **Confirm check-in** completes the process:

- Reservation status → **Checked In**
- Room → **Occupied** (visible immediately on the room status board)
- Guest folio auto-created with all accommodation charges, adds on charges, payment credits, and deposit credit
- Check-in timestamp and agent name recorded

---

## No-Show Process

The **Mark no-show** button is available in the check-in wizard footer once the no-show cutoff has passed (2 hours after the scheduled check-in time). It is disabled before the cutoff. On confirmation:

- Reservation status → **No-show**
- Room (if pre-assigned) → **Available**
- Audit log entry created: agent name, timestamp, optional reason

---

## Payment Methods Supported at Check-in

| Method | MVP | Notes |
|---|---|---|
| Cash | ✓ | Change calculator built in. Tendered amount must equal or exceed balance. |
| Card (EDC terminal) | Phase 1 | Agent processes on physical terminal, enters approval code and last 4 digits into HVMS. No card data stored. |
| QRIS (Midtrans) | Phase 1 | QR code generated and displayed for guest to scan. Webhook confirmation. 5-minute TTL. |

---

## Folio Auto-creation

On check-in confirmation, HVMS automatically posts the following lines to the guest folio:

1. **Accommodation charge** — nightly rate × number of nights, linked to the rate plan
2. **Adds on charges** — one line per item entered in Step 4
3. **Room charge payment** — negative amount (credit) matching the room payment collected in Step 5 Card 1
4. **Security deposit** — negative amount (credit) matching the deposit collected in Step 5 Card 2

The folio is locked for deletion after check-in. New charges can be added during the stay, but the auto-created lines can only be voided through the folio void approval process.

---

## Void Approval at Check-in

If an agent needs to void a charge above the void threshold (IDR 500,000), the void is held pending Property Manager approval. Voids below the threshold are applied immediately. See the Front Desk — Folio module documentation for the full void approval process.

---

## Room Status Impact

| Check-in event | Room status change |
|---|---|
| Check-in confirmed | Assigned room → **Occupied** |
| No-show confirmed | Assigned room (if any) → **Available** |
| Room changed in wizard | Original room → **Dirty** · New room → **Occupied** |

---

## Roles & Permissions

| Action | Front Desk | Manager | Admin |
|---|---|---|---|
| Complete check-in wizard | ✓ | ✓ | ✓ |
| Mark no-show | ✓ | ✓ | ✓ |
| Override room assignment | ✓ | ✓ | ✓ |
| Void folio charge (below threshold) | ✓ | ✓ | ✓ |
| Approve folio void (above threshold) | ✗ | ✓ | ✓ |
| Issue complimentary upgrade | ✓ (submit) | ✓ (approve) | ✓ |

---

## Related Modules

- **Reservation**: Check-in is triggered from the reservation list. The reservation must be in Confirmed status.
- **Guest Profiles**: Identity pre-fills from the linked guest profile. The agent updates any changed fields during Step 2.
- **Guest Folio**: Automatically created on check-in confirmation with all charges and payments.
- **Room Status Board**: Room changes to Occupied in real time on check-in confirmation. Visible to all roles.
- **Housekeeping**: Dirty rooms released from check-out or room change are queued for housekeeping task assignment.
