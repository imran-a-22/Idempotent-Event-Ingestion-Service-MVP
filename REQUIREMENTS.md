# Requirements

## Goal

Build an idempotent event ingestion API that accepts user activity events, stores valid events, prevents duplicate event storage, and exposes user-level and global event statistics.

## Required API Functionality

The API should be able to:

- Accept events
- Validate events
- Store valid events in memory
- Prevent duplicate storage
- Return all events for a specific user
- Return statistics about all stored events

## Idempotency Requirement

The service must accept repeated submissions safely. If an event with the same `eventId` already exists, the service must not store a second copy and should return a duplicate response.

## HTTP Status Choices

The API should use the following HTTP status codes:

```text
201 Created = new event stored
200 OK = duplicate event received but not stored again
400 Bad Request = invalid event body
```

## Event Object

Each event should use the following JSON structure:

```json
{
  "eventId": "evt_123",
  "userId": "user_45",
  "type": "LEVEL_COMPLETED",
  "timestamp": "2026-06-11T12:30:00Z",
  "metadata": {
    "level": 7,
    "score": 9200
  }
}
```

## Event Validation Rules

When checking the request body, the validation logic should confirm that:

```text
eventId must be a non-empty string
userId must be a non-empty string
type must be a non-empty string
timestamp must be a valid ISO date string
metadata must be a plain object
metadata must not be null
metadata must not be an array
```

If one or more validation checks fail, the event is invalid and must not be stored.

## Invalid Event Response

If the event body is invalid, the API should return `400 Bad Request`.

Example response:

```json
{
  "error": "Invalid event",
  "details": [
    "eventId is required",
    "timestamp must be a valid ISO date string"
  ]
}
```

## Duplicate Event Edge Case

There may be an edge case where the same `eventId` is submitted again, but with a different payload.

Example first request:

```json
{
  "eventId": "evt_123",
  "userId": "user_45",
  "type": "LEVEL_STARTED",
  "timestamp": "2026-06-11T12:20:00Z",
  "metadata": {
    "level": 7
  }
}
```

Example second request:

```json
{
  "eventId": "evt_123",
  "userId": "user_99",
  "type": "PURCHASE",
  "timestamp": "2026-06-11T12:30:00Z",
  "metadata": {
    "amount": 4.99
  }
}
```

Rule:

```text
If eventId already exists, treat the request as duplicate and do not overwrite the original event, even if the payload is different.
```

# Endpoint 1: POST /events

## Purpose

Submit an event.

## Request

```http
POST /events
```

## Functionality Requirements

The endpoint should:

- Receive an event from the request body
- Validate the event
- Reject invalid events with `400 Bad Request`
- Store valid events in memory
- If the same `eventId` is submitted again, not store it twice
- Return a response telling whether the event was newly stored or already existed

## New Event Response

HTTP status:

```text
201 Created
```

Response body:

```json
{
  "status": "stored",
  "eventId": "evt_123"
}
```

## Duplicate Event Response

HTTP status:

```text
200 OK
```

Response body:

```json
{
  "status": "duplicate",
  "eventId": "evt_123"
}
```

# Endpoint 2: GET /users/:userId/events

## Purpose

Get and display all events for a specific user.

## Request

```http
GET /users/:userId/events
```

Example:

```http
GET /users/user_45/events
```

## Response Format

```json
[
  {
    "eventId": "evt_123",
    "userId": "user_45",
    "type": "LEVEL_COMPLETED",
    "timestamp": "2026-06-11T12:30:00Z",
    "metadata": {
      "level": 7,
      "score": 9200
    }
  }
]
```

If the user has no events, return an empty array:

```json
[]
```

# Endpoint 3: GET /stats

## Purpose

Get and display statistics about all stored events.

## Request

```http
GET /stats
```

## Response Format

```json
{
  "totalEvents": 5,
  "uniqueUsers": 2,
  "eventsByType": {
    "LEVEL_STARTED": 2,
    "LEVEL_COMPLETED": 3
  }
}
```

If no events exist, return:

```json
{
  "totalEvents": 0,
  "uniqueUsers": 0,
  "eventsByType": {}
}
```

# Definition of Done

The MVP is complete when:

```text
POST /events stores a valid event.
POST /events rejects invalid events with 400.
POST /events does not store duplicate eventIds twice.
GET /users/:userId/events returns only that user’s events.
GET /stats returns totalEvents, uniqueUsers, and eventsByType.
All tests pass.
App starts with npm start.
```
