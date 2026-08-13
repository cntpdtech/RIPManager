# RIPManager Event Protocol — V1

Status: Draft / implementation contract.

RIPManager V1 is a **monitoring-only** system. It does not copy, move, queue, dispatch, or modify `.prn` files. The existing RIPSync workflow remains responsible for file processing/copying.

## Event lifecycle

The Windows client watches the configured source directory and reports observations to the server.

```text
FILE_DETECTED
    |
    +--> FILE_CHANGED (zero or more times)
    |
    +--> FILE_STABLE
             |
             +--> JOB_RECORDED
```

`FILE_STABLE` means the file size has remained unchanged for the configured stability period. It does **not** mean that the file was printed, copied, or successfully processed by RIPSync.

## Client events

### client.registered
Client registration or registration refresh.

Payload:

```json
{
  "client_id": "uuid",
  "client_name": "RIP-01",
  "hostname": "PRINT-PC-01",
  "software_version": "1.0.0",
  "printer_name": "Large Format Printer 01"
}
```

### client.online
Client has established communication with the server.

### client.offline
Server determines that a client has missed the configured heartbeat/timeout threshold. This is server-derived and may be generated without a direct client request.

### client.heartbeat
Periodic liveness/health signal.

Payload may contain CPU, memory, disk, uptime, software version, and watcher state. Hardware metrics are optional.

## File/job monitoring events

### file.detected
A new `.prn` file was detected in the configured source directory.

Required payload:

```json
{
  "job_id": "uuid",
  "file_name": "example.prn",
  "relative_path": "example.prn",
  "size_bytes": 123456,
  "detected_at": "2026-01-01T12:00:00Z"
}
```

### file.changed
The observed file changed while it was being written.

Payload should include the current size and observation timestamp. The client should not generate excessive events; implementations may debounce or batch changes.

### file.stable
The file has stopped growing for the configured stability period.

Required payload:

```json
{
  "job_id": "uuid",
  "file_name": "example.prn",
  "size_bytes": 123456,
  "stability_seconds": 10,
  "stable_at": "2026-01-01T12:00:10Z"
}
```

### job.recorded
Optional normalized server-side event indicating that the monitoring system has completed recording the observed job lifecycle. This is a monitoring event, not a print-success event.

### job.failed
Reserved for monitoring failures, such as inability to access the source directory or inability to persist a required local observation. It must not be used to claim that printing failed unless a future integration explicitly provides that information.

## Event identity and ordering

Every event must contain:

- `event_id` — globally unique UUID.
- `event_type` — stable event name.
- `client_id` — originating client.
- `job_id` — stable identifier for a detected file/job when applicable.
- `occurred_at` — client observation time in UTC.
- `sequence` — monotonically increasing client sequence number when available.
- `payload` — event-specific data.

The server records `received_at` independently.

The server must deduplicate events by `event_id`.

A client must be able to retry an event without creating duplicate journal records.

## Job identity

`job_id` must remain stable across the lifecycle of one observed file.

A recommended initial identity strategy is a client-generated UUID. File name, size, and timestamps are metadata and must not be treated as the sole globally unique identifier.

## Monitoring semantics

RIPManager must never infer any of the following from `file.stable` alone:

- RIPSync copied the file.
- The printer accepted the file.
- Printing started.
- Printing completed.
- Printing succeeded.

Those states require a future explicit integration/event source.

## Notification policy

The server/dashboard may notify users for:

- new `.prn` detected;
- `.prn` became stable;
- client online/offline;
- client software/update events;
- server/system alerts.

Notification delivery is separate from event ingestion. Failure to deliver a browser/mobile notification must not cause the original client event to be considered failed.

## Reserved future events

These names are reserved for future versions and must not be implemented as V1 claims without a real data source:

- `print.started`
- `print.completed`
- `print.failed`
- `rip.processed`
- `rip.copy.completed`
- `printer.error`

## Compatibility

Event names and required fields are part of the protocol contract. Breaking changes require a new protocol major version and coordinated client/server rollout.
