# PAPERCLIP ENTITY MAP - Snapshot A

Generated: 2026-03-20
Source: `pnpm dev` startup output from user's local machine (Mac)

---

## Instance Location

| Property | Value |
|----------|-------|
| Host machine | macOS (Augustass-MacBook-Air) |
| Instance dir | `/Users/augstar/.paperclip/instances/default/` |
| Config file | `/Users/augstar/.paperclip/instances/default/config.json` |
| Backup dir | `/Users/augstar/.paperclip/instances/default/data/backups` |

---

## Server Configuration

| Property | Value |
|----------|-------|
| Mode | `embedded-postgres` + `vite-dev-middleware` |
| Deploy mode | `local_trusted` (private) |
| Server port | `3100` |
| API base | `http://127.0.0.1:3100/api` |
| Health endpoint | `http://127.0.0.1:3100/api/health` |
| UI | `http://127.0.0.1:3100` |
| Auth | ready |
| Agent JWT | **missing** (needs `pnpm paperclipai onboard`) |

---

## Database

| Property | Value |
|----------|-------|
| Type | Embedded PostgreSQL |
| Reason | No `DATABASE_URL` env var set |
| Data dir | `/Users/augstar/.paperclip/instances/default/db` |
| PG port | `54329` |
| Cluster state | Already exists (skipped init) |
| Migrations | Already applied |

---

## Background Services

| Service | Status | Detail |
|---------|--------|--------|
| Heartbeat | enabled | interval: 30000ms |
| DB Backup | enabled | every 60m, retain 30 days |

---

## Known API Endpoints

| Endpoint | Purpose |
|----------|---------|
| `GET /api/health` | Health check |
| `GET /api` | API root |
| `GET /api/agents` | Agent list (unprobed) |
| `GET /api/runs` | Run list (unprobed) |
| `GET /api/events` | Event stream (unprobed) |
| `GET /api/plugins` | Plugin list (unprobed) |

### `GET /api/health` — Live Response

```json
{
  "status": "ok",
  "version": "0.3.1",
  "deploymentMode": "local_trusted",
  "deploymentExposure": "private",
  "authReady": true,
  "bootstrapStatus": "ready",
  "bootstrapInviteActive": false,
  "features": {
    "companyDeletionEnabled": true
  }
}
```

**Health fields decoded:**

| Field | Value | Meaning |
|-------|-------|---------|
| `status` | `ok` | Server healthy |
| `version` | `0.3.1` | Application version |
| `deploymentMode` | `local_trusted` | No auth required for local access |
| `deploymentExposure` | `private` | Not exposed to network |
| `authReady` | `true` | Auth subsystem initialised |
| `bootstrapStatus` | `ready` | Initial setup complete |
| `bootstrapInviteActive` | `false` | No pending invite flow |
| `features.companyDeletionEnabled` | `true` | Multi-tenant deletion feature on |

---

## Process / Build Info

| Property | Value |
|----------|-------|
| Start command | `pnpm dev` → `node scripts/dev-runner.mjs watch` |
| Server runtime | `tsx watch` (TypeScript via tsx, hot-reload) |
| Entry point | `server/src/index.ts` |
| Server package | `@paperclipai/server@0.3.1` |
| Plugin SDK | `@paperclipai/plugin-sdk@1.0.0` |
| Shared package | `@paperclipai/shared@0.3.1` |
| Cross-env vars | `PAPERCLIP_MIGRATION_PROMPT=never`, `PAPERCLIP_MIGRATION_AUTO_APPLY=true` |
| Watch ignores | `../ui/node_modules`, `../ui/.vite`, `../ui/dist` |

---

## Package / Monorepo Layout (inferred)

```
paperclip/
├── scripts/
│   └── dev-runner.mjs          # orchestrates dev build + watch
├── server/
│   └── src/
│       └── index.ts            # server entry point
├── packages/
│   ├── shared/                 # @paperclipai/shared@0.3.1
│   └── plugins/
│       └── sdk/                # @paperclipai/plugin-sdk@1.0.0
└── ui/                         # Vite frontend (dev-middleware mode)
```

---

## Filesystem State (on Mac, not accessible from sandbox)

- `~/.paperclip/instances/default/db/PG_VERSION` — confirms embedded PG cluster exists
- `~/.paperclip/instances/default/config.json` — instance config
- `~/.paperclip/instances/default/data/backups/` — automated DB backups

---

## Gaps / Missing Information

- Agent JWT not provisioned (`pnpm paperclipai onboard` not run)
- `/api/agents`, `/api/runs`, `/api/events`, `/api/plugins` responses not yet captured
- `config.json` contents not read
- No active agents or runs observed

---

SNAPSHOT COMPLETE
