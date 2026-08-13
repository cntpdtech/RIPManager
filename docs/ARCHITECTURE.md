# Architecture

## Overview

RIPManager is a two-part distributed system:

- **RIPManager Client** runs on each Windows PC associated with a large-format printer.
- **RIPManager Server** runs centrally on Linux and provides the API, database, web dashboard, realtime updates, notifications, client management, logging, and software update management.

## Client

The Windows client is installed and configured on the printer PC. During setup the operator selects the source folder containing `.prn` files. The client watches this folder, determines when a file has stopped growing, records the job lifecycle, and sends events to the server.

The client should not require the server to access its filesystem directly.

## Server

The Linux server listens on a fixed configured port and accepts authenticated client connections. It persists job events and client state and exposes a web dashboard for users.

The dashboard should support realtime updates and browser notifications for important events such as new print jobs, client offline/online state, and software/update events.

## Communication

Communication is client-initiated. The shared API and event contract lives under `protocol/`.

## Future Concerns

Authentication, client registration, heartbeats, retry behavior, idempotency, software update delivery, notification delivery, retention policies, and audit logging must be defined in versioned protocol and implementation specifications before production release.
