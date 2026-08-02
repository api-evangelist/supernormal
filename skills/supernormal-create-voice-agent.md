---
name: Create and run an AI voice agent
description: Create a Supernormal AI voice agent, publish it, start a session, and poll for completion.
api: openapi/supernormal-openapi-original.json
operations:
  - POST /agents
  - POST /agents/{id}/publish
  - POST /agent_sessions
  - GET /agent_sessions/{id}
---

# Create and run an AI voice agent

Stand up a Supernormal AI voice agent (e.g. an inbound-sales or survey caller) and run a session.

## Auth
- Base URL: `https://api.supernormal.com/api/v1`
- Send `X-API-TOKEN: <key>` and `Content-Type: application/json`.
- Required scopes: `agents:write`, `agent_sessions:read`, `agent_sessions:write`.

## Steps
1. **Create the agent** — `POST /agents` with an `AgentRequest` body: `name`, `description`,
   and optionally `actor_prompt` (`inbound_sales` | `survey` | `employee_satisfaction_survey`
   | `customer_satisfaction_survey` | `event_feedback_survey` | `screening_interview`),
   `actor_voice` (`archer` | `christopher` | `rachel` | `scarlet`), `custom_prompt`, `context`,
   `greeting_prompt`, `goodbye_prompt`, `is_host`, `mode` (`single_response` | `conversation`),
   and `max_call_duration`. The response `Agent` has the new `id`.
2. **Publish the agent** — `POST /agents/{id}/publish` so it can take sessions.
   (Use `POST /agents/{id}/unpublish` to take it offline.)
3. **Start a session** — `POST /agent_sessions?agent_id={id}` with optional `name`, `email`,
   and `timezone` query params for the participant. The response `AgentSession` has an `id`,
   a `meeting_url`, and a `status`.
4. **Poll for completion** — `GET /agent_sessions/{id}` until `status` reaches `done`
   (terminal states include `done`, `fatal`, `timeout_exceeded`, `never_recorded`). When
   `transcription_complete` is true, the transcript is ready.

## Notes
- Writes are not idempotent — do not blindly retry create/publish; check state first.
- List an agent's sessions with `GET /agent_sessions?agent_id={id}`.
