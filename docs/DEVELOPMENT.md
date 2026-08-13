# Development Guide

## Repository Workflow

1. Pull the latest `main` before starting work.
2. Read `AGENTS.md` and the relevant architecture/protocol documents.
3. Work only in the area owned by the agent unless an interface change requires coordination.
4. Keep commits focused and descriptive.
5. Run local tests/build checks before pushing.
6. Push completed work to `main` only when the repository workflow permits it.
7. After pushing, verify the local commit SHA equals the remote `main` SHA.

## Agent Responsibilities

### Claude on Windows

Primary implementation area: `client/`.

Responsibilities:
- Windows installer/setup.
- Source `.prn` folder watcher.
- File stability detection.
- Job lifecycle handling.
- Client configuration and local persistence.
- Communication with RIPManager Server.
- Windows packaging and testing.

### Claude CLI on Linux

Primary implementation areas: `server/` and `deploy/`.

Responsibilities:
- Linux server application.
- API and authentication.
- Database and job journal.
- Web dashboard.
- Realtime updates and notifications.
- Client management.
- Deployment, service management, backups, and operational monitoring.

## Shared Areas

`protocol/` and `docs/` are shared. Changes affecting client/server behavior must be coordinated and documented.

## Security

Never commit passwords, access tokens, private keys, production configuration, or real customer data. Use local environment files that are ignored by Git for development secrets.
