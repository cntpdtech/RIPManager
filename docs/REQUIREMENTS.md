# Requirements

## Functional Requirements

### Windows Client

- Configure the `.prn` source directory during setup.
- Watch for new `.prn` files.
- Detect when a file is stable and ready for processing.
- Record file/job lifecycle events.
- Send events and periodic heartbeats to the Linux server.
- Identify itself with a stable machine/client ID and software version.
- Report connection and health state.
- Support future remote configuration and software updates.

### Linux Server

- Listen on a configured fixed port for client traffic.
- Authenticate and identify clients.
- Persist job events and client state.
- Provide a web dashboard.
- Show realtime client and job status.
- Provide a searchable print/job journal.
- Notify web users about new jobs and important events.
- Manage client configuration and software versions in future releases.
- Provide operational logs and health information.

## Non-Functional Requirements

- Reliable delivery with retry-safe/idempotent event handling.
- Clear protocol versioning.
- No direct server dependency on Windows filesystem access.
- Secure transport and authentication for production deployment.
- Persistent server-side data suitable for audit/history.
- Recoverable service operation after restart.
