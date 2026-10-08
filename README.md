# drp-notification-db

Mongo database `notification`. **Migrations only.** Instance: [`drp-infra-mongo`](https://github.com/code-corhuila/drp-infra-mongo). No database container here. `drp-notification-api` must not own DDL.

This domain has **no PostgreSQL schema** (ADR-007). Collections: `notifications` (unique `sourceEventId`) and `reports` (immutable snapshots after `generatedAt`). Reports are built by calling owner HTTP APIs, never their tables.

Honest gap: `drp-infra-mongo` still has no compose on `develop`; the migrate job expects host `mongo:27017` on the shared compose network once that engine exists.

## Layout (Anexo J)

| Folder | Content |
|--------|---------|
| `01_ddl/` | Liquibase Mongo changesets (validators + indexes) |
| `02_dml/` | no Corte 2 seed |
| `03_dcl/` | users live in infra-mongo |
| `04_tcl/` | reserved |
| `05_rollbacks/` | local undo |
| `changelog/` | master changelog |
| `deploy/compose.yml` | Liquibase job only |

Control collection: `databasechangelog_notification`.

## Run

```bash
# infra-mongo first (when compose exists)
docker compose --env-file .env.example -f deploy/compose.yml run --rm notification-migrate
```

First run needs network so the job can `lpm add mongodb` (the community Liquibase image does not ship the extension).

Corte 2 UI (`drp-front`) does **not** need this migrate: it uses synthetic contract data.

## Branching

Child of `develop` named `feat/…`. Never commit on `develop` / `qa` / `main`. Promote with `cherry-pick -x`.
