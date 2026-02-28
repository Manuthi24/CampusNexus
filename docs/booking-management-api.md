# Module B – Booking Management

## Workflow
- PENDING → APPROVED / REJECTED
- APPROVED → CANCELLED

Rules:
- New booking requests are created as PENDING
- REJECTED requires an admin reason
- CANCELLED allowed only for booking owner (USER) or ADMIN
- Illegal transitions are blocked (e.g., approving a CANCELLED booking)

## Conflict checking (Overlapping time ranges)
A conflict exists for the same resource when:
(newStart < existingEnd) AND (newEnd > existingStart)

Conflict check applies to bookings with status:
- PENDING, APPROVED
Ignore:
- REJECTED, CANCELLED

Return:
- 409 Conflict when overlapping

## API Endpoints (proposed)
### USER
- POST /api/bookings
  - 201 Created (success)
  - 400 Bad Request (validation)
  - 409 Conflict (overlap)

- GET /api/bookings/my
  - 200 OK

- PUT /api/bookings/{id}/cancel
  - 200 OK
  - 403 Forbidden (not owner)
  - 404 Not Found

### ADMIN
- PATCH /api/bookings/{id}/approve
  - 200 OK
  - 409 Conflict (overlap at approval)

- PATCH /api/bookings/{id}/reject
  - 200 OK (requires reason)

- GET /api/bookings?status=&resourceId=&from=&to=
  - 200 OK (filters)
