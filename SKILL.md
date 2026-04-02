---
name: hookdeck
description: Hookdeck webhook proxy — event monitoring, retry management, filtering, transformations, bulk operations, and webhook infrastructure. Auto-triggers on Hookdeck, webhook retry, webhook monitoring, and event management tasks.
disable-model-invocation: false
user-invocable: true
argument-hint: "task description"
---

# Hookdeck Webhooks Skill

Complete reference for Hookdeck webhook proxy API and management.

**As of March 2026.** API version `2025-01-01`.

---

## 2. API Base

```
https://api.hookdeck.com/2025-01-01/
```

All requests need: `Authorization: Bearer {HOOKDECK_API_KEY}`

---

## 3. Events (Webhook Deliveries)

### List Events

```bash
# All events
curl -s "https://api.hookdeck.com/2025-01-01/events" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"

# Failed events
curl -s "https://api.hookdeck.com/2025-01-01/events?status=FAILED&limit=100" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"

# Events by status code
curl -s "https://api.hookdeck.com/2025-01-01/events?response_status=502&limit=50" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"

# Events in time range
curl -s "https://api.hookdeck.com/2025-01-01/events?\
created_at[gte]=2026-03-01T00:00:00Z&\
created_at[lte]=2026-03-02T00:00:00Z" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"

# Events for a specific webhook connection
curl -s "https://api.hookdeck.com/2025-01-01/events?webhook_id=web_XXXXXXXXXXXXX" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### Get Event Details

```bash
curl -s "https://api.hookdeck.com/2025-01-01/events/{event_id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### Retry a Single Event

```bash
curl -s -X POST "https://api.hookdeck.com/2025-01-01/events/{event_id}/retry" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Length: 0"
```

### Mute an Event (stop retries)

```bash
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/events/{event_id}/mute" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

**Event statuses:** `QUEUED`, `HOLD`, `SUCCESSFUL`, `FAILED`

---

## 4. Requests (Original Incoming Webhooks)

### Get Request Details

```bash
# Includes original webhook path in data.path
curl -s "https://api.hookdeck.com/2025-01-01/requests/{request_id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### List Requests

```bash
curl -s "https://api.hookdeck.com/2025-01-01/requests?limit=25" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

Request vs Event: A **request** is the original incoming webhook. An **event** is one delivery attempt to a destination. One request can generate multiple events (fan-out or retries).

---

## 5. Bulk Operations

### Bulk Retry Events

```bash
# Retry all failed events with 502 response
curl -s -X POST "https://api.hookdeck.com/2025-01-01/bulk/events/retry" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": {"status": "FAILED", "response_status": [502]}}'

# Retry by time range
curl -s -X POST "https://api.hookdeck.com/2025-01-01/bulk/events/retry" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": {"status": "FAILED", "created_at": {"gte": "2026-03-01T00:00:00Z"}}}'
```

### Check Bulk Operation Status

```bash
curl -s "https://api.hookdeck.com/2025-01-01/bulk/events/retry/{batch_id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

---

## 6. Connections (Webhooks)

### List Connections

```bash
curl -s "https://api.hookdeck.com/2025-01-01/connections" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### Create Connection

```bash
curl -s -X POST "https://api.hookdeck.com/2025-01-01/connections" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-webhook",
    "source": {"name": "external-source"},
    "destination": {
      "name": "n8n-target",
      "url": "https://your-n8n-instance.example.com/webhook/path",
      "auth_method": {
        "type": "BEARER_TOKEN",
        "config": {"token": "secret-token"}
      }
    }
  }'
```

### Pause/Unpause Connection

```bash
# Pause (events queued but not delivered)
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/connections/{id}/pause" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"

# Unpause
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/connections/{id}/unpause" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

---

## 7. Sources & Destinations

### List Sources

```bash
curl -s "https://api.hookdeck.com/2025-01-01/sources" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### List Destinations

```bash
curl -s "https://api.hookdeck.com/2025-01-01/destinations" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY"
```

### Update Destination

```bash
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/destinations/{id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://your-n8n-instance.example.com/webhook/new-path"}'
```

---

## 8. Filters & Transformations

### Connection-Level Filter (only forward matching events)

```bash
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/connections/{id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "rules": [{
      "type": "filter",
      "body": {"event_type": "message.created"}
    }]
  }'
```

### Transformation (modify payload before delivery)

```bash
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/connections/{id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "rules": [{
      "type": "transform",
      "transformation_id": "tfm_xxx"
    }]
  }'
```

---

## 9. Retry Configuration

Default retry schedule: exponential backoff with 5 attempts.

```bash
# Custom retry on connection
curl -s -X PUT "https://api.hookdeck.com/2025-01-01/connections/{id}" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "rules": [{
      "type": "retry",
      "strategy": "exponential",
      "count": 5,
      "interval": 30000
    }]
  }'
```

Strategies: `linear`, `exponential`.

---

## 10. Common Investigation Patterns

### Diagnose Failed Webhooks

```bash
# 1. Find failed events
EVENTS=$(curl -s "https://api.hookdeck.com/2025-01-01/events?status=FAILED&limit=10" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY")

# 2. Get request details (original webhook path)
REQUEST_ID=$(echo "$EVENTS" | jq -r '.models[0].request_id')
curl -s "https://api.hookdeck.com/2025-01-01/requests/$REQUEST_ID" \
  -H "Authorization: Bearer $HOOKDECK_API_KEY" | jq '.data.path, .data.headers, .data.body'

# 3. Check response from destination
echo "$EVENTS" | jq '.models[0] | {status, response_status, next_attempt_at, attempts}'
```

### Count Failures by Status Code

```bash
for code in 500 502 503 504; do
  COUNT=$(curl -s "https://api.hookdeck.com/2025-01-01/events?status=FAILED&response_status=$code&limit=1" \
    -H "Authorization: Bearer $HOOKDECK_API_KEY" | jq '.pagination.count')
  echo "$code: $COUNT"
done
```

---

## 11. Critical Gotchas

1. **No official MCP server** — use `curl` via Bash for all Hookdeck operations
2. **API key in team vault** — `$HOOKDECK_API_KEY`
3. **Request vs Event** — one request can create multiple events (retries). Don't confuse the IDs.
4. **Bearer token auth** — Hookdeck authenticates to n8n via BEARER_TOKEN. This token must match n8n's webhook auth.
5. **Bulk retry is async** — returns a batch ID. Check status separately.
6. **Rate limiting** — API has rate limits. Use bulk operations for large batches, not loops.
7. **Event retention** — events are retained for a limited time based on plan. Export critical data.
8. **Pause vs Disable** — pausing queues events (replay later). Disabling drops them.
9. **Path preservation** — Hookdeck forwards the original webhook path to the destination URL.
10. **HTTPS required** — destination URLs must be HTTPS in production.

