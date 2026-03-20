# Paperclip Entity Map
Generated: 2026-03-20
Source: PAPERCLIP_ENTITY_MAP_A.md + PAPERCLIP_ENTITY_MAP_B.md

---

## Core Entities

### Company
- Fields: id, name, issuePrefix, issueCounter, status, requireBoardApprovalForNewAgents, budgetMonthlyCents, spentMonthlyCents, createdAt
- States: `active` | `paused` | `archived`
- Belongs to: top-level (instance)
- Sample payload:
```json
{
  "id": "f3c95ab1-cb3a-4e8d-8004-97862775542d",
  "name": "Tenet AI",
  "issuePrefix": "TEN",
  "issueCounter": 22,
  "status": "active",
  "requireBoardApprovalForNewAgents": true,
  "budgetMonthlyCents": 0,
  "spentMonthlyCents": 0,
  "createdAt": "2026-03-18T09:10:40.360Z"
}
```

### Agent
- Fields: id, companyId, name, urlKey, role, status, adapterType, adapterConfig, runtimeConfig, budgetMonthlyCents, reportsTo, pauseReason
- States: `active` | `paused` | `idle` | `running` | `error` | `pending_approval` | `terminated`
- Roles: `ceo` | `cto` | `cmo` | `cfo` | `engineer` | `designer` | `pm` | `qa` | `devops` | `researcher` | `general`
- AdapterTypes: `process` | `http` | `claude_local` | `codex_local` | `opencode_local` | `pi_local` | `cursor` | `openclaw_gateway` | `hermes_local`
- PauseReasons: `manual` | `budget` | `system`
- Belongs to: Company
- Live instances observed (all `status: error`, all `adapterType: claude_local`):

| Name | Role | ID |
|------|------|----|
| CEO | ceo | `6a813c2b-f229-405c-bfe1-6f19062748b9` |
| Founding Engineer | engineer | `eba73e29-7309-4f8a-8d57-a0fdad280576` |
| SDR Manager | general | `615b9aab-84fd-4b19-90ae-8927272ca94c` |
| Outreach Agent | general | `0681346d-dfa7-4980-b7e4-1806901ca09d` |
| Researcher | general | `549e2a23-7c1a-48c0-9721-4d65ad6825f7` |
| Copywriter | general | `9cb55a65-7f93-4853-93e7-a38fb769415e` |

- Org chart:
```
CEO (6a813c2b)
├── Founding Engineer (eba73e29)
└── SDR Manager (615b9aab)
    ├── Outreach Agent (0681346d)
    ├── Researcher (549e2a23)
    └── Copywriter (9cb55a65)
```

### Issue
- Fields: id, companyId, status, priority, title, assignee (agent or user), runId, workspaceId, parentId, ancestor hierarchy
- States: `backlog` | `todo` | `in_progress` | `in_review` | `done` | `blocked` | `cancelled`
- Priority: `critical` | `high` | `medium` | `low`
- Belongs to: Company (optionally under Project / parent Issue)
- Live count: 22 total (2 blocked, 1 todo, 19 done)
- Related sub-entities: IssueComment, IssueAttachment, IssueLabel, IssueDocument, DocumentRevision, IssueAncestor, IssueWorkProduct

### IssueWorkProduct
- Fields: type, provider, status, reviewState, healthStatus
- Types: `preview_url` | `runtime_service` | `pull_request` | `branch` | `commit` | `artifact` | `document`
- Providers: `paperclip` | `github` | `vercel` | `s3` | `custom`
- Status: `active` | `ready_for_review` | `approved` | `changes_requested` | `merged` | `closed` | `failed` | `archived` | `draft`
- ReviewState: `none` | `needs_board_review` | `approved` | `changes_requested`
- Belongs to: Issue

### Project
- Fields: id, companyId, status, leadAgentId, goalIds, workspaces, codebase, pauseReason
- States: `backlog` | `planned` | `in_progress` | `completed` | `cancelled`
- Belongs to: Company
- Live instance: `Outbound sales` (`ff603161`, status: `planned`)
- Sub-entities: ProjectWorkspace (sourceType: `local_path` | `git_repo` | `remote_managed` | `non_git_path`), ProjectCodebase

### Goal
- Fields: id, companyId, title, description, level, status, parentId, ownerAgentId
- States: `planned` | `active` | `achieved` | `cancelled`
- Levels: `company` | `team` | `agent` | `task`
- Belongs to: Company (hierarchical, parentId links to parent Goal)

### HeartbeatRun
- Fields: agentId, status, invocationSource, logStore, startedAt, log refs, token usage
- States: `queued` | `running` | `succeeded` | `failed` | `cancelled` | `timed_out`
- InvocationSources: `timer` | `assignment` | `on_demand` | `automation`
- Belongs to: Agent
- Live state: most recent runs `failed` ("Process lost -- server may have restarted"), logStore: `local_file`, last startedAt: `2026-03-20T06:21:27Z`
- Sub-entities: HeartbeatRunEvent, AgentRuntimeState, AgentTaskSession, AgentWakeupRequest, InstanceSchedulerHeartbeatAgent

### AgentWakeupRequest
- States: `queued` | `deferred_issue_execution` | `claimed` | `coalesced` | `skipped` | `completed` | `failed` | `cancelled`
- TriggerDetails: `manual` | `ping` | `callback` | `system`
- Belongs to: Agent / HeartbeatRun

### LiveEvent
- Fields: id, companyId, type (LiveEventType), createdAt, payload
- Types: `heartbeat.run.queued` | `heartbeat.run.status` | `heartbeat.run.event` | `heartbeat.run.log` | `agent.status` | `activity.logged` | `plugin.ui.updated` | `plugin.worker.crashed` | `plugin.worker.restarted`
- Belongs to: Company (streamed via SSE)
- Live state: no active events observed (`recentEvents: []`)

### ActivityEvent
- Fields: actorType (agent|user|system), action (string), entityType (string), entityId, runId, details
- Belongs to: Company (flat log)

### Approval
- Fields: type, status, payload, decisionNote, decidedByUserId
- Types: `hire_agent` | `approve_ceo_strategy` | `budget_override_required`
- States: `pending` | `revision_requested` | `approved` | `rejected` | `cancelled`
- Belongs to: Company
- Sub-entities: ApprovalComment

### BudgetPolicy
- Fields: scopeType, metric, windowKind, amount, warnPercent, hardStopEnabled
- ScopeTypes: `company` | `agent` | `project`
- WindowKinds: `calendar_month_utc` | `lifetime`
- Summary status: `ok` | `warning` | `hard_stop`
- Belongs to: Company / Agent / Project
- Sub-entities: BudgetPolicySummary, BudgetIncident (`open` | `resolved` | `dismissed`), BudgetOverview
- Live state: budgetMonthlyCents: 0, spentMonthlyCents: 0

### CostEvent
- Fields: agentId, issueId, projectId, billingType, model, inputTokens, outputTokens, costCents
- BillingTypes: `metered_api` | `subscription_included` | `subscription_overage` | `credits` | `fixed` | `unknown`
- Belongs to: Company / Agent / Issue / Project
- Sub-entities: CostSummary, CostByAgent, CostByProviderModel, CostByBiller, CostByAgentModel, CostWindowSpendRow, CostByProject

### PluginRecord
- Fields: id, pluginKey, packageName, version, apiVersion, status, installOrder, packagePath, installedAt, capabilities, UI slots, jobs, webhooks, tools
- States: `installed` | `ready` | `disabled` | `error` | `upgrade_pending` | `uninstalled`
- Categories: `connector` | `workspace` | `automation` | `ui`
- Belongs to: Instance
- Live instance: `budget-lens` by Tenet AI (`342a341a`, status: `ready`, worker running)
  - UI slots: dashboardWidget, detailTab(issue), detailTab(agent), sidebar
  - Jobs: `daily-snapshot` (cron `0 0 * * *`)
  - Capabilities: costs.read, companies.read, agents.read, issues.read, projects.read, events.subscribe, plugin.state.read/write, jobs.schedule, ui.* registrations
- Sub-entities: PluginStateRecord, PluginConfig, PluginEntityRecord, PluginJobRecord, PluginJobRunRecord, PluginWebhookDeliveryRecord

### CompanyMembership / Access
- Entities: CompanyMembership, PrincipalPermissionGrant, Invite, JoinRequest, InstanceUserRoleGrant
- Principal types: `user` | `agent`
- MembershipStatus: `pending` | `active` | `suspended`
- JoinRequestStatus: `pending_approval` | `approved` | `rejected`
- Belongs to: Instance / Company

---

## Activity Signal Map

| Activity | Signal | Source | Confidence |
|---|---|---|---|
| Agent running a task | `agent.status` LiveEvent + HeartbeatRun `status: running` | SSE stream + `/api/companies/{id}/heartbeat-runs` | High |
| Agent run completed | `heartbeat.run.status` LiveEvent (status: succeeded/failed) | SSE stream | High |
| Agent run queued | `heartbeat.run.queued` LiveEvent | SSE stream | High |
| Agent run log line | `heartbeat.run.log` LiveEvent | SSE stream | High |
| Agent status change | `agent.status` LiveEvent | SSE stream | High |
| New activity logged | `activity.logged` LiveEvent | SSE stream | High |
| Plugin UI updated | `plugin.ui.updated` LiveEvent | SSE stream | High |
| Plugin worker crashed | `plugin.worker.crashed` LiveEvent | SSE stream | High |
| Agent error (process lost) | HeartbeatRun `status: failed`, Agent `status: error` | `/api/companies/{id}/heartbeat-runs` | High |
| No active runs | `/api/companies/{id}/live-runs` returns `[]` | HTTP poll | High |
| Agents idle / break room | live-overview.json `focus` field, break_room `status: active` | Filesystem cache | Medium |
| Issue state change | NOT OBSERVED as a distinct signal — inferred from issue list diffs | HTTP poll `/api/companies/{id}/issues` | Medium |

---

## Best Observation Method

**SSE (Server-Sent Events)** for real-time signals via `LiveEvent` stream (type-safe enum of 9 event types).
**HTTP polling** for entity state snapshots (agents, issues, projects, heartbeat-runs, live-runs).
**Filesystem cache** at `~/.paperclip/cache/paperclipworld-*.json` for periodic snapshots written by the plugin worker.

Recommended poll interval: **5000 ms** (no polling interval was explicitly specified in A or B — 5 s is a safe default for agent/issue state)

API base: `http://127.0.0.1:3100`
Auth: implicit local session (`local-board`) — no token needed in `local_trusted` mode

---

## Minimum game-world data shape

```json
{
  "company": {
    "id": "f3c95ab1-cb3a-4e8d-8004-97862775542d",
    "name": "Tenet AI",
    "status": "active"
  },
  "agents": [
    {
      "id": "6a813c2b-f229-405c-bfe1-6f19062748b9",
      "name": "CEO",
      "role": "ceo",
      "status": "error",
      "reportsTo": null
    }
  ],
  "issues": [
    {
      "id": "<uuid>",
      "title": "<string>",
      "status": "blocked",
      "priority": "high",
      "assigneeAgentId": "<uuid or null>"
    }
  ],
  "liveRuns": [],
  "recentEvents": []
}
```

---

## ⚠️ What was NOT found

| Missing item | Notes |
|---|---|
| SSE endpoint URL/path | A confirmed port 3100 and REST routes; the SSE stream URL (e.g. `/api/events` or `/api/companies/{id}/events`) was **not observed** |
| Poll interval recommendation | No explicit guidance found in A or B; 5000 ms is assumed |
| Issue IDs (UUIDs) | A listed issue titles and statuses but not their UUIDs |
| Agent `adapterConfig` / `runtimeConfig` contents | Shape summarised in B but no live payload captured in A |
| AgentConfigRevision history | Schema exists (B) but no live payload observed |
| Goals | Schema exists (B); no live goals returned from API in A |
| Approvals | Schema exists (B); no live approvals observed in A |
| Budget policies | Schema exists (B); company budgets are 0/0 but no BudgetPolicy records observed |
| Cost events | Schema exists (B); no live CostEvent data observed in A |
| Activity log entries | Schema exists (B); no ActivityEvent records captured in A |
| IssueWorkProducts | Schema exists (B); no live work product records observed in A |
| Workspace files / git repos | Cache files confirm `items: []` — workspace is empty |
| Skills | Cache confirms `items: []` — no skills loaded |
| Delivery queue / schedule artifacts | Cache confirms `items: []` — queues empty |
| WebSocket support | Not observed; only SSE and HTTP REST confirmed |
| Auth token format for non-local deployments | Only `local_trusted` implicit auth was observed |
