# RadCore API — Usage Guide

**Base URL:** `https://web-production-2c752.up.railway.app`
**Interactive docs:** `https://web-production-2c752.up.railway.app/docs`

---

## Authentication

All requests require the `X-API-Key` header:

```
X-API-Key: <your-key>
```

You have been issued two keys — one for the `radiologist` role, one for `admin`. Different roles have different data access levels.

---

## Quick Reference

### System
```
GET  /health                         API status and version
```

### Patients
```
GET  /patients                       List all patients (paginated)
GET  /patients/{id}                  Get a single patient
GET  /patients/{id}/studies          List studies for a patient
```

Query parameters for `/patients`: `page` (default 1), `page_size` (default 20, max 100)

### Studies
```
GET  /studies                        List all studies
GET  /studies/{id}                   Get a single study
GET  /studies/{id}/series            List image series for a study
GET  /studies/{id}/report            Get the report for a study
POST /studies/{id}/report            Create a report for a study
```

### Series & Images
```
GET  /series/{id}/images             List images in a series
GET  /images/{id}                    Get a single image
```

### Measurements
```
GET  /images/{id}/measurements       List measurements on an image
POST /images/{id}/measurements       Create a measurement
```

**Create measurement body:**
```json
{
  "type": "length",
  "value": 25.5,
  "unit": "mm",
  "created_by": 1
}
```
Valid types: `length`, `area`, `angle`

### Reports
```
PATCH /reports/{id}                  Update report content
POST  /reports/{id}/transition       Advance report workflow state
```

**Transition body:**
```json
{
  "status": "in_progress"
}
```

### Worklist
```
GET  /worklist                       Studies assigned to current user (unread/in_progress)
```

---

## Example: Full workflow

```bash
BASE="https://web-production-2c752.up.railway.app"
RAD_KEY="your-radiologist-key"
ADM_KEY="your-admin-key"

# Check health
curl "$BASE/health"

# Get worklist as radiologist
curl -H "X-API-Key: $RAD_KEY" "$BASE/worklist"

# Get a specific study
curl -H "X-API-Key: $RAD_KEY" "$BASE/studies/1"

# Get series for a study
curl -H "X-API-Key: $RAD_KEY" "$BASE/studies/1/series"

# Create a measurement
curl -X POST -H "X-API-Key: $RAD_KEY" -H "Content-Type: application/json" \
  -d '{"type":"length","value":32.0,"unit":"mm","created_by":1}' \
  "$BASE/images/1/measurements"

# Create a report
curl -X POST -H "X-API-Key: $RAD_KEY" -H "Content-Type: application/json" \
  -d '{"content":"Preliminary findings...","radiologist_id":1}' \
  "$BASE/studies/1/report"

# Advance report status
curl -X POST -H "X-API-Key: $RAD_KEY" -H "Content-Type: application/json" \
  -d '{"status":"in_progress"}' \
  "$BASE/reports/1/transition"
```

---

## Notes

- The `/docs` endpoint provides interactive Swagger UI for the documented endpoints
- Not all endpoints are listed in `/docs` — the API surface is larger than what is documented
- The spec (`requirements-spec.md`) is the authoritative reference for expected behaviour
