---
sidebar_position: 1
sidebar_label: Business Process
---

# Business Process — Guest Profile Module

The Guest Profile module maintains a central record of every guest who has stayed at or made a reservation at Pertama Property. Profiles are created and maintained by Front Desk agents and link directly to reservations, check-in identity verification, and receipt delivery.

---

## Overview

A guest profile is a persistent record that survives individual stays. It captures identity documents, contact details, emergency contacts, stay preferences, and internal staff notes. Once created, a profile is reused across all future reservations — the agent selects the existing guest rather than re-entering details each time.

---

## Guest Profile Status Lifecycle

```
Active ──→ Deactivated
```

A profile is **Active** by default. It can only be **Deactivated** (not deleted) if it has no reservation history. Profiles with reservation history cannot be removed — this preserves the audit trail for accounting and compliance.

---

## Creating a Guest Profile

### Method 1 — New guest registration page (full form)

The Front Desk agent navigates to Guest profiles → New guest. The registration form is a 6-step process:

**Step 1 — ID scan**

The agent captures the guest's ID document using one of two methods:

- **Webcam capture**: Live camera view on the front desk PC with an alignment guide overlay. The agent positions the document within the frame and clicks Capture.
- **File upload**: The agent uploads a JPEG, PNG, or PDF from the computer. Suitable for phone photos or scanned documents.

The system sends the image to Google Vision API (Document Text Detection) and returns extracted fields within 3 seconds. The photo is discarded immediately after extraction — only the text data is stored.

For passports, the MRZ (Machine Readable Zone — the two rows of characters at the bottom of the photo page) is parsed using the ICAO Doc 9303 standard, achieving 99%+ accuracy. For Indonesian KTP, full-page OCR is applied with a field position template and Bahasa Indonesia language support.

Each extracted field is shown with a confidence badge:

- **Green (≥90%)** — field pre-filled and locked. Agent clicks to unlock and edit if needed.
- **Amber (60–89%)** — field pre-filled but highlighted. Agent must verify against the physical document before saving.
- **Red (<60%)** — field not extracted or low confidence. Agent enters manually.

If scan fails or quality is poor, the agent can skip the scan and enter all details manually using the **Skip scan — enter manually** link.

**Step 2 — Identity details**

The agent reviews and completes all identity fields:

| Field | Required | OCR source |
|---|---|---|
| Full name (as on ID) | Yes | Passport: MRZ surname + given name · KTP: Nama |
| ID type | Yes | Detected from document layout |
| ID number | Yes | Passport: document number · KTP: NIK (16 digits) |
| Nationality | Yes | Passport: nationality code (e.g. NLD → Netherlands) · KTP: WNI → Indonesia |
| Date of birth | Yes | Passport: MRZ DOB · KTP: Tgl lahir |
| Gender | No | Passport: MRZ sex field · KTP: Jenis kelamin |
| ID expiry date | No | Passport: MRZ expiry date |
| Country of issue | No | Passport: MRZ issuing state |
| Place of birth | No | KTP: Tempat lahir |

**Duplicate detection** runs automatically on the ID number field. If an existing profile has the same ID type and ID number, the agent sees a warning with a link to the existing profile and can choose to use the existing profile or create a new one.

**ID expiry warnings** are shown inline: amber if the document expires within 30 days, red if already expired. The check-in wizard Step 2 also surfaces this warning. An expired document does not block check-in — the agent notes it and proceeds.

**Step 3 — Contact information**

| Field | Required |
|---|---|
| Phone number (country code + number) | Yes |
| Email address | No — used for receipt delivery |
| Country of residence | No |
| Home city / address | No |

**Step 4 — Emergency contact** (all optional)

Name, relationship (Spouse / Parent / Sibling / Friend / Travel agent / Other), and phone number. Emergency phone must differ from the guest's primary phone.

**Step 5 — Preferences & tags** (all optional, staff-only)

| Field | Options |
|---|---|
| Guest tags | VIP · Repeat guest · Corporate · Honeymoon · Family · Solo traveller |
| Room preference | Free text (e.g. "High floor, Wing B, pool view, quiet") |
| Pillow preference | Soft · Firm · Memory foam · No preference |
| Dietary requirements | Vegetarian · Vegan · Halal · No pork · No seafood · Gluten free · No dairy · Other |
| Accessibility needs | Free text |
| Internal notes | Textarea — staff only, never shown to guest |

**Step 6 — Data consent & save**

The agent confirms that the guest has been informed of and consented to Pertama Property's data collection practices. The consent timestamp and agent name are recorded automatically on save.

### Method 2 — Inline creation from the reservation wizard

During reservation creation (Step 1 — Guest), if the guest is not found in the search, the agent can click **Create new guest**. A streamlined creation form opens within the wizard — capturing the minimum required fields (name, ID type, ID number, nationality, phone). The full profile can be completed later.

---

## Searching for a Guest

The guest list supports real-time search across four fields simultaneously: full name (partial match), phone number, email address, and ID number. Results return in under 2 seconds for up to 100,000 records. Agents can also filter by nationality, ID type, guest tag, and currently in-house status.

---

## Guest Detail View

Opening a guest profile shows:

- Identity, contact, emergency contact, and preferences cards
- Stay summary: total stays, total nights, last stay date, most booked room type
- Internal notes (editable inline, auto-saves, shows last-edited-by and timestamp)
- Full stay history: all reservations with dates, room, amount, status, and booking source
- **New reservation** button — opens the reservation wizard with this guest pre-selected

---

## Editing a Profile

Any Front Desk agent can edit a guest profile at any time. All field-level changes are recorded in an audit trail: field name, old value, new value, agent, timestamp. This ensures the profile history is traceable if a guest's ID details change between stays (e.g. passport renewal).

---

## Integration with Other Modules

**Reservation wizard**: Guest search in Step 1 queries the guest profile database. Selecting a guest pre-fills the guest name and links the reservation to the profile.

**Check-in wizard Step 2 (identity verification)**: The identity fields pre-fill from the guest profile. The agent verifies against the physical document and can update any field. If the ID expiry date is within 30 days or already expired, an amber or red warning is shown.

**Receipt email**: The guest's email address from their profile is pre-filled in the check-out receipt delivery field. The agent can override it at check-out.

---

## Data Privacy

Guest personal data — name, ID number, date of birth, contact details — is stored in the HVMS database and accessible only to Front Desk, Manager, and Admin roles. ID photos uploaded for OCR scanning are processed by Google Vision API over an encrypted HTTPS connection and are not stored in HVMS after extraction completes.

---

## Roles & Permissions

| Action | Front Desk | Manager | Admin |
|---|---|---|---|
| View guest list | ✓ | ✓ | ✓ |
| Create guest profile | ✓ | ✓ | ✓ |
| Edit guest profile | ✓ | ✓ | ✓ |
| View internal notes | ✓ | ✓ | ✓ |
| Deactivate guest profile (no history) | ✗ | ✓ | ✓ |
| Export guest list | ✓ | ✓ | ✓ |
| Merge duplicate profiles | ✗ | ✓ | ✓ |
