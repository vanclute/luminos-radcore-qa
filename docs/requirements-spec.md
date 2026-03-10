# RadCore API v2.3 — Requirements Specification

**Product:** Luminos Health RadCore
**Version:** 2.3
**Audience:** QA, Integration Partners

---

## 1. System Overview

RadCore is the backend service for the Luminos Health radiology workstation. It manages the full lifecycle of a radiology study: from patient registration and study acquisition through image measurement, report authoring, and clinical sign-off.

RadCore is used by the following staff roles within a hospital radiology department:

| Role | Description |
|------|-------------|
| `radiologist` | Reviews imaging studies and authors diagnostic reports |
| `nurse` | Assists with patient intake and study coordination |
| `technologist` | Operates imaging equipment and manages study acquisition |
| `ordering_physician` | Orders studies and receives completed reports |
| `admin` | System administration, full data access |

---

## 2. Authentication

All API requests must include an `X-API-Key` header. Keys are issued per user role. Requests with missing or invalid keys must return `401 Unauthorized`.

Admin keys provide full access to all endpoints and unredacted patient data. Non-admin keys receive redacted PII in patient responses (see Section 4).

---

## 3. Study Workflow

A study progresses through the following statuses:

| Status | Description |
|--------|-------------|
| `unread` | Study acquired, not yet reviewed |
| `in_progress` | Radiologist has opened and is reviewing |
| `complete` | Study fully reported and signed off |

Priority values: `routine` or `STAT` (urgent/emergency).

---

## 4. Patient Records

Patient records include: `id`, `mrn`, `name`, `dob`, `sex`, `phone`, `email`.

Non-admin users receive redacted PII: `name` and `dob` are hidden, `phone` and `email` are redacted. MRN and demographic fields (`sex`) remain visible to all authenticated users.

All patient `dob` values must be valid past dates. Future dates are not permitted.

---

## 5. Measurements

Measurements are recorded against individual images. Each measurement has:

- `type`: one of `length`, `area`, or `angle`
- `value`: numeric measurement value
- `unit`: the unit of measurement (required)
- `created_by`: user ID of the recording user
- `created_at`: timestamp

**All measurement values must be returned in millimetres (mm)**, converted from raw pixel values using the image's `pixel_spacing_mm` field. Values are returned to **2 decimal places**.

If `unit` is omitted from a create request, the API must return `422 Unprocessable Entity`.

---

## 6. Report Workflow

Each study may have at most one report. Reports progress through the following states in strict order:

```
draft -> in_progress -> dictated -> preliminary -> final
                                                   |
                                               addended
```

Skipping states is not permitted. Attempting an invalid transition must return `422`.

When a `final` report is transitioned to `addended`, the original report content must be preserved. The amendment is appended; the original is never overwritten.

Report status definitions:

| Status | Description |
|--------|-------------|
| `draft` | Initial state on creation |
| `in_progress` | Radiologist actively dictating |
| `dictated` | Dictation complete, awaiting transcription review |
| `preliminary` | Report issued before final attending sign-off |
| `final` | Attending radiologist has signed; report is official |
| `addended` | Post-final correction or addition appended |

Reports in `final` status are considered signed. The `signed_at` timestamp is set when transitioning to `final` and must not change on subsequent updates.

---

## 7. Worklist

`GET /worklist` returns the set of studies assigned to the authenticated user that have not yet been completed (status `unread` or `in_progress`).

Results are ordered: STAT studies first, then by acquisition time ascending.

---

## 8. Timestamps

All timestamps are stored internally in UTC. **Timestamps in API responses must be returned in the local timezone of the requesting user**, based on their account settings.

---

## 9. Study Integrity

Once a study reaches `complete` status, no new series or images may be added to it. Attempts to do so must return `409 Conflict`.

---

## 10. Pagination

List endpoints (`/patients`, `/studies`) support `page` and `page_size` query parameters. Default page size is 20. Maximum page size is 100. Page numbering begins at 1.

---

## 11. Undocumented Endpoints

The interactive API documentation (`/docs`) reflects the primary API surface. Additional endpoints exist and may be discovered through exploration. Their behaviour follows the same authentication and data-access rules described in this specification.
