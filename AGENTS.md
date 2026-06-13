# AGENTS.md

## Purpose

These instructions are for AI coding agents working on the Idempotent Event Ingestion Service MVP.

The agent should follow the project requirements and technical plan exactly unless explicitly instructed otherwise.

## Required Reading Before Coding

Before modifying files, read:

- README.md
- REQUIREMENTS.md
- TECHNICAL_PLAN.md
- AGENTS.md

## Working Rules

- Do not rewrite the whole project unless explicitly asked.
- Prefer small, focused changes.
- Implement one step at a time.
- Do not add new production dependencies without explaining why.
- Do not add a database.
- Do not add authentication.
- Do not add a frontend.
- Do not add Docker.
- Keep the code simple and beginner-readable.
- Follow the existing file structure unless there is a clear reason to change it.
- Do not leave placeholder code, TODO-only implementations, or fake tests.
- If requirements conflict, stop and explain the conflict before coding.

## Implementation Behaviour

For each task:

1. Read the relevant requirements.
2. Briefly state the implementation plan.
3. Make only the required changes.
4. Add or update relevant tests.
5. Run the relevant checks.
6. Summarise the final result.

## Validation Rules

Before finishing any coding task:

- Run the relevant test command.
- Run lint or type checks if they exist.
- Confirm the app starts successfully when relevant.
- Summarise what changed.
- List files changed.
- List tests/checks run.
- Mention anything not completed.

## Output Format After Each Task

Return:

1. Summary of changes
2. Files changed
3. How to run
4. Tests/checks run
5. Known limitations or follow-up tasks

## Scope Control

The MVP should only include:

- Node.js
- Express.js
- In-memory storage
- Event validation
- Idempotent event submission
- User event filtering
- Event statistics
- Unit tests
- API/integration tests

Do not add features outside this scope unless explicitly requested.

## Definition of Done

The MVP is complete when:

- `POST /events` stores a valid event.
- `POST /events` rejects invalid events with `400`.
- `POST /events` does not store duplicate `eventId`s twice.
- `GET /users/:userId/events` returns only that user’s events.
- `GET /stats` returns `totalEvents`, `uniqueUsers`, and `eventsByType`.
- All tests pass.
- App starts with `npm start`.
