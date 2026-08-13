# RIPManager Protocol API — V1 Draft

Status: Draft. This document defines the initial client/server contract before implementation.

## Transport

- Client: Windows RIPManager agent.
- Server: Linux RIPManager Server.
- Communication is client-initiated.
- Production transport must use HTTPS/TLS.
- The server exposes a configurable fixed listening port.
- JSON is the initial payload format.

## API Version

The protocol version is carried in every request/event as `protocol_version`.
Initial value: `1`.

Breaking changes require a new protocol major version.

## Client Identity

Each client has a stable `client_id` generated/assigned during registration. The client also reports:

- `client_name`
- `hostname`
- `software_version`
- `printer_name` (optional)
- `source_directory` (server may receive a logical/display name; do not require direct filesystem access)

## Initial Endpoints

### POST /api/v1/clients/register

Registers a new Windows client or refreshes an existing registration.

### POST /api/v1/heartbeat

Reports client liveness and basic health state.

### POST /api/v1/events

Submits one or more client events. Event IDs must be unique so the server can safely retry/deduplicate delivery.

### GET /api/v1/config

Returns configuration assigned to the client. Remote configuration is optional in the first implementation but the contract reserves this endpoint.

### GET /api/v1/health

Server health endpoint for operational monitoring. This endpoint is not a client authentication mechanism.

## Event Submission

The event submission body contains:

```json
{
  "protocol_version": 1,
  "client_id": "client-uuid",
  "events": []
}
```

Each event contains at minimum:

```json
{
  "event_id": "uuid",
  "event_type": "job.created",
  "occurred_at": "2026-01-01T12:00:00Z",
  "payload": {}
}
```

## Delivery Semantics

- Client retries transient failures.
- Server processing must be idempotent by `event_id`.
- Server acknowledges accepted events.
- Client must retain unsent events locally until acknowledged or permanently rejected.
- Clock differences must not be used as the sole ordering mechanism; server receipt time is also recorded.

## Authentication

Authentication mechanism is intentionally implementation-defined at this draft stage. Production deployment must use authenticated clients over TLS. Secrets/tokens must never be committed to the repository.

## Error Model

Errors should use a stable machine-readable code and human-readable message:

```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Request validation failed"
  }
}
```

The implementation must distinguish retryable failures (for example temporary server/network failure) from permanent validation/authentication failures.
