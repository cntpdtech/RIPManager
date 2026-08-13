# AI Agent Instructions — RIPManager

Read this file and the relevant `docs/` and `protocol/` files before making changes.

## Ownership

- `client/`: Windows RIPManager client. Primary owner: Claude on Windows.
- `server/`: Linux RIPManager Server. Primary owner: Claude CLI on Linux.
- `deploy/`: Linux deployment configuration. Primary owner: Claude CLI on Linux.
- `protocol/`: shared client/server contract. Coordinate changes between both agents.
- `docs/`: shared architecture and requirements.

## Rules

- Run `git status` and pull the latest changes before implementation work.
- Do not silently change the architecture.
- Do not modify another agent's implementation area unless an interface change requires it.
- Never commit passwords, tokens, private keys, production `.env` files, or other secrets.
- Keep commits focused and descriptive.
- Update documentation when behavior or architecture changes.
- Preserve compatibility for released protocol versions unless a versioned breaking change is approved.
- Before reporting a push as successful, verify that the local commit SHA equals the remote branch SHA.

## Architecture

Windows clients watch configured `.prn` source folders, detect file readiness, record job lifecycle events, and send events and heartbeats to the Linux server. The server stores the journal and provides the web dashboard, realtime status, notifications, client management, logging, and update management.
