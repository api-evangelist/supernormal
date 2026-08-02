---
name: Schedule a meeting for capture
description: Find an upcoming meeting on the user's calendar and toggle Supernormal capture on for it.
api: openapi/supernormal-openapi-original.json
operations:
  - GET /user/calendar_events
  - POST /calendar_events/{id}/record
---

# Schedule a meeting for capture

Use the Supernormal REST API to make sure a specific upcoming meeting gets recorded and
transcribed.

## Auth
- Base URL: `https://api.supernormal.com/api/v1`
- Send `X-API-TOKEN: <key>` and `Content-Type: application/json` on every request.
- Required scopes: `calendar_events:read` (to list) and `calendar_events:write` (to toggle capture).

## Steps
1. **List upcoming events** — `GET /user/calendar_events`. Optionally narrow with `from` and
   `to` ISO 8601 query params. Each `CalendarEvent` has `id`, `name`, `start`, `end`,
   `platform_name` (`google_meet` | `teams` | `zoom`), `is_blocked_from_capture`, and
   `scheduled_to_record`.
2. **Pick the target event** by matching `name`/`start`. Skip any event where
   `is_blocked_from_capture` is true — it cannot be recorded.
3. **Toggle capture** — `POST /calendar_events/{id}/record?record=true` using the event `id`.
   Pass `record=false` to turn capture off. A `200` means the meeting is scheduled for capture.

## Notes
- There is no idempotency key; re-issuing the toggle simply re-sets the desired state.
- After the meeting runs, the resulting notes/transcript are retrievable as a Post
  (see the "Retrieve meeting notes" skill); the event's `post_id` links to it.
