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
| `GET /api/agents` | **404** — route does not exist |
| `GET /api/runs` | **404** — route does not exist |
| `GET /api/events` | Unprobed |
| `GET /api/plugins` | Plugin list ✓ |

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

### `GET /api/plugins` — Live Response

One plugin installed:

| Field | Value |
|-------|-------|
| ID | `342a341a-564d-4685-8da9-330f4c9033a1` |
| Plugin key | `budget-lens` |
| Package | `@tenet-ai/paperclip-plugin-budget-lens` |
| Version | `0.1.0` |
| API version | `1` |
| Categories | `["ui"]` |
| Status | `ready` |
| Install order | `1` |
| Installed at | `2026-03-19T08:05:44.357Z` |
| Updated at | `2026-03-19T08:06:43.631Z` |
| Package path | `/Users/augstar/paperclip/Paperclip/packages/plugins/budget-lens` |
| Last error | `null` |

#### Plugin: budget-lens — Manifest Detail

**Description:** Real-time visibility into token spend — broken down by agent, issue, and project — with proactive budget alerts before the money is gone.

**UI Slots:**

| Slot ID | Type | Export | Display Name | Entity Types |
|---------|------|--------|-------------|--------------|
| `dashboard-overview` | `dashboardWidget` | `DashboardWidget` | Budget Lens — Cost Overview | — |
| `issue-cost-tab` | `detailTab` | `IssueCostTab` | Cost | `issue` |
| `agent-cost-tab` | `detailTab` | `AgentCostTab` | Cost | `agent` |
| `budget-alerts-sidebar` | `sidebar` | `BudgetAlertsSidebar` | Budget Alerts | — |

**Scheduled Jobs:**

| Job key | Schedule | Description |
|---------|----------|-------------|
| `daily-snapshot` | `0 0 * * *` (daily midnight) | Refreshes company-level cost summaries and alert state |

**Capabilities (declared):**

```
costs.read, companies.read, agents.read, issues.read, projects.read,
events.subscribe, plugin.state.read, plugin.state.write, jobs.schedule,
ui.dashboardWidget.register, ui.detailTab.register, ui.sidebar.register
```

**Instance Config Schema:**

| Key | Type | Default | Range | Description |
|-----|------|---------|-------|-------------|
| `alertThresholdPct` | number | `80` | 10–100 | % of budget that triggers alert |
| `trackingWindowDays` | number | `7` | 1–90 | Trailing days for sparkline/summary |

**Entrypoints:**
- UI: `./dist/ui`
- Worker: `./dist/worker.js`

---

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
- `~/.paperclip/instances/default/config.json` — **does not exist** (likely written after onboarding/first login)
- `~/.paperclip/instances/default/data/backups/` — automated DB backups

---

## Entity Types (inferred from plugin capabilities + UI slots)

| Entity | Evidence |
|--------|----------|
| `company` | capability: `companies.read`; feature: `companyDeletionEnabled` |
| `agent` | capability: `agents.read`; UI slot `agent-cost-tab` targets `entityTypes: ["agent"]` |
| `issue` | capability: `issues.read`; UI slot `issue-cost-tab` targets `entityTypes: ["issue"]` |
| `project` | capability: `projects.read` |
| `cost` | capability: `costs.read`; plugin purpose is token spend tracking |
| `event` | capability: `events.subscribe` |
| `plugin.state` | capability: `plugin.state.read/write` |
| `job` | capability: `jobs.schedule`; job: `daily-snapshot` |
| `run` | endpoint `/api/runs` exists (unprobed) |

---

## Gaps / Missing Information

- Agent JWT not provisioned (`pnpm paperclipai onboard` not run)
- `/api/events` unprobed
- `config.json` does not exist on disk (likely written only after `onboard` or first login)
- Actual API route structure unknown — `/api/agents` and `/api/runs` are not valid routes; real routes TBD

---

SNAPSHOT COMPLETE
