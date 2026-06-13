# Technical Plan

## Architecture

Use a simple Express API with in-memory storage.

The application should be split into clear modules:

- Server entry point
- Express app setup
- Route handlers
- Event store logic
- Event validation logic
- Unit tests
- API/integration tests

## File Structure

```text
event-ingestion-service/
  package.json
  src/
    server.js
    app.js
    routes/
      events.js
      users.js
      stats.js
    store/
      eventStore.js
    validation/
      validateEvent.js
  tests/
    eventStore.test.js
    validation.test.js
    events.routes.test.js
    users.routes.test.js
    stats.routes.test.js
```

## Storage Design

Use in-memory storage.

Recommended structure:

```js
const eventsById = new Map();
```

The `eventId` should be the key.

The full event object should be the value.

Storage rules:

- If `eventId` does not exist, store the event.
- If `eventId` already exists, do not store a second copy.
- If `eventId` already exists with a different payload, still treat it as a duplicate.
- Do not overwrite the original event.

## Validation Design

Create a function called:

```js
validateEvent(event)
```

Function purpose:

Check that:

```text
eventId must be a non-empty string
userId must be a non-empty string
type must be a non-empty string
timestamp must be a valid ISO date string
metadata must be a plain object
metadata must not be null
metadata must not be an array
```

Suggested return format for a valid event:

```js
{
  valid: true,
  errors: []
}
```

Suggested return format for an invalid event:

```js
{
  valid: false,
  errors: ["eventId is required"]
}
```

## Route Design

### GET /health

Purpose:

Confirm that the server is running.

Expected response:

```json
{
  "status": "ok"
}
```

### POST /events

Entire logic:

```text
Receive event
Validate event
If invalid, return 400 Bad Request
If eventId already exists, return 200 OK with duplicate response
Otherwise store event
Return 201 Created with stored response
```

### GET /users/:userId/events

Entire logic:

```text
Get userId from request params
Loop through stored events
Return only events matching that userId
If no events match, return an empty array
```

### GET /stats

Calculate and return:

```text
total number of stored events
number of unique users
number of events by type
```

## Implementation Steps

### Step 1 - Create the Express Server

Create an Express server using Node.js and Express.js.

Add a basic health route:

```http
GET /health
```

Expected response:

```json
{
  "status": "ok"
}
```

### Step 2 - Create Event Validation

Create the `validateEvent(event)` function.

Do not implement routes until validation is complete and tested.

### Step 3 - Create Event Store

Create the event store module.

Suggested functions:

```js
addEvent(event)
getEventsByUser(userId)
getStats()
resetStore()
```

`resetStore()` is mainly for tests so each test can start with clean in-memory storage.

### Step 4 - Program Endpoint 1: POST /events

Implement event submission.

### Step 5 - Program Endpoint 2: GET /users/:userId/events

Implement user-level event filtering.

### Step 6 - Program Endpoint 3: GET /stats

Implement statistics calculation.

### Step 7 - Run Full System Testing

System testing should confirm that:

- Valid event is stored
- Duplicate event is not stored twice
- Invalid event is rejected
- Stats are calculated correctly
- User event filtering works
- The server starts successfully

## Testing Terminology

### Unit Tests

```text
eventStore.test.js
validation.test.js
```

### API/Integration Tests

```text
events.routes.test.js
users.routes.test.js
stats.routes.test.js
```

## Testing Plan

### Validation Tests

Test that:

- A valid event passes validation
- Missing `eventId` fails validation
- Empty string fields fail validation
- Invalid timestamp fails validation
- `metadata: null` fails validation
- `metadata: []` fails validation
- Missing metadata fails validation

### Event Store Tests

Test that:

- A valid event is stored
- A duplicate event is not stored twice
- A duplicate event returns duplicate status
- A duplicate event with a different payload does not overwrite the original event
- Events can be filtered by userId
- Stats are calculated correctly
- Empty storage returns zero statistics

### POST /events API Tests

Test that:

- A valid event returns `201 Created`
- A duplicate event returns `200 OK`
- An invalid event returns `400 Bad Request`
- Invalid event response includes an error and details array
- Duplicate with different payload does not overwrite the original event

### GET /users/:userId/events API Tests

Test that:

- Events are filtered by userId
- Events belonging to other users are not returned
- A user with no events receives an empty array

### GET /stats API Tests

Test that:

- `totalEvents` is calculated correctly
- `uniqueUsers` is calculated correctly
- `eventsByType` is calculated correctly
- Empty storage returns zero statistics

## Constraints

- No database
- No frontend
- No authentication
- No Docker
- No external services
- Do not add unnecessary dependencies
- Keep implementation simple and beginner-readable
