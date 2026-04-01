# EnvSync

EnvSync is a secure secret synchronization system for development teams.  
It replaces ad-hoc secret sharing with controlled access, auditability, and predictable local environment setup

## Problem

Most teams still share `.env` values through ad-hoc channels like chat, docs, and copy-paste.
That creates:

- inconsistent local environments
- unclear access history
- difficult secret rotation

## Solution

EnvSync provides one source of truth for project secrets with controlled access and auditability.
It combines:

- a backend API for auth, authorization, encrypted storage, and audit logs
- a CLI that syncs project secrets into local `.env` using a repeatable workflow

## How It Works

1. Developer logs in with GitHub.
2. Admin creates/binds a project to a GitHub repo.
3. Admin manages project members and secrets.
4. Developers run `envsync sync --project <name>`.
5. CLI pulls only requested keys and updates local `.env`.
6. Backend records audit events and sync version progress.

## Core Concepts

- `Project`: collaboration scope for secrets and members.
- `Member Roles`: `admin` can manage members/secrets; `member` can sync.
- `Secret Versioning`: project version increments when secrets change.
- `Sync`: pull-based update with version detection and access control.

## Architecture

- `Backend (Phoenix)`: auth, authorization, encrypted storage, audit, sync API.
- `CLI (Elixir escript)`: login flow, local `.env` reconciliation, admin actions, polling/watch mode.

## Quick User Journey

1. Login: `envsync auth login`
2. Create project: `envsync projects create --name my_app --repo owner/repo`
3. Add members: `envsync admin members add --project my_app --github-login teammate`
4. Add/update secrets: `envsync admin secrets set --project my_app --key API_KEY --value ...`
5. Sync on any machine: `envsync sync --project my_app`

## Local Development Quickstart

Backend:

```bash
cd ../envsync_backend
cp .env.example .env
set -a; source .env; set +a
mix deps.get
mix ecto.create
mix ecto.migrate
mix phx.server
```

CLI:

```bash
cd ../envsync_cli
make help
make build
sudo make install
```

## Security Model

- Secrets are encrypted at rest (Cloak, AES-GCM).
- Transport security depends on TLS/HTTPS in production.
- Access is role-based per project.
- Sync and access actions are audit logged.
- JWTs are short-lived and used for API authorization.

## Troubleshooting

- `repo verification failed: missing_token`
  - set `ENVSYNC_GITHUB_VERIFY_TOKEN` on backend and restart server
- `repo_mismatch`
  - local git `origin` does not match project’s bound repo
- `unauthorized`
  - re-login with `envsync auth login`

## Repository Guides
- Backend GitHub: https://github.com/DarynOngera/envsync_backend
- CLI GitHub: https://github.com/DarynOngera/envsync_cli


https://github.com/user-attachments/assets/79fc51e0-1399-4943-92d3-1fa5297d628b




