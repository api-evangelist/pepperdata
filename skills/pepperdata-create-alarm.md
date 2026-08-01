---
name: Create a Pepperdata metric alarm
description: Programmatically create a metric alarm that fires when a query's series crosses a threshold, then confirm it and check firing status.
api: openapi/pepperdata-rest-api-openapi.yml
operations: [createAlarm, getAlarm, listFiringAlarms, listAlarms]
---

# Create a Pepperdata metric alarm

Use this when you need an alarm on a metric for a job that may no longer be
running (so the dashboard cannot create it interactively).

## Auth
Send every request with the header
`Authorization: PDAPI <API-key-id>:<API-key-token>`. Create a key at
`https://dashboard.pepperdata.com/account/apikeys`. Base URL is
`https://dashboard.pepperdata.com/{realm}/api`. TLS 1.2+ is required.

## Steps
1. **createAlarm** — `POST /alarms` with a JSON body matching the `Alarm` schema:
   - `title` (required), `queryParams` (required dashboard query for the metric),
     and `threshold` (required) with `value`, and optionally `windowMinutes`
     (default 5), `percentOfTime` (default 1), `extremumOperator`
     (`MAX` upper / `MIN` lower, default `MAX`).
   - Optionally set `enabled` (default true) and `targetEmails` (comma-separated
     override recipients). Do not send read-only fields (`id`, `createdTime`,
     `updatedTime`).
2. **getAlarm** — `GET /alarms/{alarmId}` with the returned `id` to confirm the
   stored configuration.
3. **listFiringAlarms** — `GET /alarms/status` to see whether the new alarm is
   currently firing.

## Rules
- On a later revise (`PUT /alarms/{alarmId}`), send the full object: any omitted
  field is overwritten with its default.
- No idempotency key exists; create alarms are not automatically deduplicated —
  list with `GET /alarms` first if re-running.
- `401` means a missing/invalid key; `400` means a required alarm field is
  missing or invalid.
