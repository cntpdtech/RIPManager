# RIPManager

RIPManager is a centralized monitoring and management system for large-format printing workflows.

## Components

- **RIPManager** — Windows client/agent installed on each large-format printer PC. It watches the configured `.prn` source folder, determines when files are ready, records job events, and sends telemetry to the server.
- **RIPManager Server** — Linux server application providing the central API, database, web dashboard, realtime status, notifications, client management, logging, and software update management.

## Repository Layout

```text
RIPManager/
├── client/       # Windows RIPManager agent
├── server/       # Linux RIPManager Server
├── protocol/     # Shared client/server API and event contract
├── docs/         # Architecture, requirements, and development documentation
├── deploy/       # Linux deployment configuration
├── AGENTS.md     # Instructions for AI coding agents
└── README.md
```

## Core Workflow

1. RIPManager runs on each printer PC and watches a configured source directory for `.prn` files.
2. A file is considered ready only after its size has stopped increasing for the configured stability period.
3. RIPManager records the job lifecycle and sends events to RIPManager Server.
4. RIPManager Server stores the journal and exposes it through the web dashboard.
5. Web users can receive realtime notifications when new print jobs or important client events occur.

## Design Principles

- The Windows client initiates communication with the Linux server.
- The server does not require direct filesystem access to Windows printer PCs.
- `protocol/` is the shared contract between client and server.
- Secrets and production credentials must never be committed.
- Breaking protocol changes must be versioned and documented.
