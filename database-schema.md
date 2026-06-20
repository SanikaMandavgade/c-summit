# The Guest Experience Summit — Database Schema
### Transactional tables (Postgres/Prisma) — not CMS-managed

These tables back forms and workflows. They are written via server-side API routes with validation (e.g. Zod), never directly from the client, and live in the application database rather than the headless CMS.

---

## InvitationApplication

| Field | Type | Notes |
|---|---|---|
| id | uuid (PK) | |
| fullName | string | |
| workEmail | string | |
| phone | string, nullable | |
| linkedinUrl | string | |
| companyName | string | |
| jobTitle | string | |
| industry | enum | Hotel Group / Hospitality Tech / Investment / Consulting / Other |
| companySize | enum | 1–50 / 51–250 / 251–1000 / 1000+ |
| propertiesManaged | enum, nullable | N/A / 1–10 / 11–50 / 51–200 / 200+ |
| budgetAuthority | enum | Direct / Significant Influence / None |
| goalsStatement | text | |
| status | enum | Pending → UnderReview → Approved \| Waitlisted \| Declined |
| reviewedBy | string, nullable | |
| reviewedAt | datetime, nullable | |
| decisionEmailSentAt | datetime, nullable | |
| registrationConfirmedAt | datetime, nullable | |
| registrationToken | string, nullable | generated on Approved |
| source | string | UTM / referrer |
| createdAt | datetime | |
| updatedAt | datetime | |

### Approval workflow (state machine)
```
Pending ──(team starts review)──> UnderReview
UnderReview ──(meets criteria)──> Approved ──(confirms registration)──> RegistrationConfirmed
UnderReview ──(borderline / capacity-limited)──> Waitlisted ──(seat opens)──> Approved
UnderReview ──(does not meet criteria)──> Declined
```
`Approved` and `Declined` trigger transactional email via the API route (Postmark/Resend). `Approved` additionally generates `registrationToken`, used by `/request-invitation/status`.

---

## ContactSubmission

| Field | Type | Notes |
|---|---|---|
| id | uuid (PK) | |
| fullName | string | |
| workEmail | string | |
| department | enum (FK → ContactDepartment.slug) | |
| message | text | |
| status | enum | New / Replied / Closed |
| createdAt | datetime | |

---

## PartnerLead

| Field | Type | Notes |
|---|---|---|
| id | uuid (PK) | |
| fullName | string | |
| company | string | |
| workEmail | string | |
| jobTitle | string | |
| packageInterest | enum | Partner / Strategic Partner / Headline Partner / Not Sure |
| goalsStatement | text | |
| status | enum | New / Contacted / Qualified / Closed |
| createdAt | datetime | |

---

## NewsletterSubscriber

| Field | Type |
|---|---|
| id | uuid (PK) |
| email | string, unique |
| subscribedAt | datetime |
| source | string |

---

## MeetingRequest

| Field | Type | Notes |
|---|---|---|
| id | uuid (PK) | |
| requestedByEmail | string | |
| requestedWithSpeakerId | uuid, nullable (FK → Speaker) | |
| requestedWithPartnerId | uuid, nullable (FK → Partner) | |
| proposedSlots | datetime[] | |
| status | enum | Pending / Confirmed / Declined |
| createdAt | datetime | |

---

## RoundtableBooking

| Field | Type | Notes |
|---|---|---|
| id | uuid (PK) | |
| roundtableSessionId | uuid (FK → CMS RoundtableSession) | |
| attendeeEmail | string | |
| attendeeName | string | |
| companyName | string | |
| bookedAt | datetime | |

---

## Relationship summary

```
InvitationApplication — standalone, no FK to CMS content
ContactSubmission *──1 ContactDepartment (slug reference, CMS-defined enum)
PartnerLead — standalone
NewsletterSubscriber — standalone
MeetingRequest *──1 Speaker (CMS reference, nullable)
MeetingRequest *──1 Partner (CMS reference, nullable)
RoundtableBooking *──1 RoundtableSession (CMS reference)
```

## Implementation notes

- All write endpoints (`/api/invitations`, `/api/contact`, `/api/partner-leads`, `/api/newsletter`, `/api/meeting-requests`, `/api/roundtable-bookings`) validate server-side before insert.
- `InvitationApplication.status` changes are the only writes that trigger outbound transactional email.
- Indexes: `workEmail` on InvitationApplication and ContactSubmission; unique index on `NewsletterSubscriber.email`.
- Recommended ORM: Prisma against Postgres; schema above maps directly to a `schema.prisma` model set.
