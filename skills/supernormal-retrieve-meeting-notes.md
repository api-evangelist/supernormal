---
name: Retrieve meeting notes and transcript
description: List the user's Supernormal meeting posts and pull the full notes and transcript for one.
api: openapi/supernormal-openapi-original.json
operations:
  - GET /posts
  - GET /posts/{id}
---

# Retrieve meeting notes and transcript

Fetch AI-generated meeting notes and the verbatim transcript from Supernormal.

## Auth
- Base URL: `https://api.supernormal.com/api/v1`
- Send `X-API-TOKEN: <key>` and `Content-Type: application/json`.
- Required scope: `posts:read`.

## Steps
1. **List posts** — `GET /posts`. Paginate with `limit` (default 20, max 100) and `offset`
   (default 0). Use `scope=latest` for the caller's own meetings or `scope=shared` for
   meetings shared with them. Each `Post` has `id`, `title`, `published_at`, `summary`, `seen`.
2. **Select a post** by `title`/`published_at`.
3. **Get the detail** — `GET /posts/{id}`. The `PostDetail` adds:
   - `notes[]` — AI-generated notes; each `Note` has `body`, `timestamp` (seconds into the
     transcript), optional `parent_id` (nested notes), `display_name`, and `type`.
   - `transcript[]` — `TranscriptSegment`s with `start`, `end`, `content`, `author_name`.

## Notes
- Reconstruct the readable transcript by ordering `transcript[]` on `start`.
- Notes reference transcript positions via `timestamp`; join on that to show notes in context.
