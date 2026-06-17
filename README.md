# Idempotent-Event-Ingestion-Service-MVP

## Overview

This is a small Node.js and Express backend service that accepts mobile app activity events and stores them in memory while preventing duplicate event storage.

## Problem

Mobile apps can send the same event more than once because of unreliable network conditions. This can result in duplicate events being stored.

The service must accept repeated submissions safely. If an event with the same `eventId` already exists, the service must not store a second copy and should return a duplicate response.

## Technologies

- Node.js - to execute JavaScript on the server
- Express.js - to abstract low-level HTTP server code and speed up development
- In-memory storage - to keep the MVP simple
- Jest and Supertest - for unit and API/integration testing

## API Endpoints

- `GET /health` - check that the server is running
- `POST /events` - submit an event
- `GET /users/:userId/events` - get all stored events for a specific user
- `GET /stats` - get statistics about all stored events

## In-Memory Storage Limitation

This MVP uses in-memory storage only.

Data does not persist after server restart.

No database is required for this MVP.

This is intentional.

## How to Run

Install dependencies:

```bash
npm install
```

Start the server:

```bash
npm start
```

Run tests:

```bash
npm test
```

For development, if a development script is added:

```bash
npm run dev
```

## Local URL

Server runs on:

```text
http://localhost:3000
```

## Example Health Check

Request:

```http
GET /health
```

Expected response:

```json
{
  "status": "ok"
}
```

## Project Status

This is an MVP-level backend service. It does not include a database, authentication, frontend, Docker setup, or deployment configuration.
