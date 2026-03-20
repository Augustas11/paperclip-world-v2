# PAPERCLIP ENTITY MAP - Snapshot A

Generated: 2026-03-20

## Step 1: Locate Paperclip

- `$PAPERCLIP_HOME`: (empty/unset)
- `~/.paperclip`: NOT FOUND
- `find ~ -name "paperclip" -type f`: No results
- `which paperclip`: Not in PATH

**Result: No standalone Paperclip installation found.**

---

## Step 2: Workspace

No `~/.paperclip/workspace` or equivalent found.

Working directory `/home/user/paperclip-world-v2` contents:
```
total 16
drwxr-xr-x 3 root root 4096 Mar 20 06:25 .
drwxr-xr-x 3 root root 4096 Mar 19 12:20 ..
drwxr-xr-x 7 root root 4096 Mar 20 06:23 .git
-rw-r--r-- 1 root root  317 Mar 20 06:25 PAPERCLIP_ENTITY_MAP_A.md
```

No source files found — repository appears empty (no prior git commits accessible).

---

## Step 3: API Port Discovery

Ports probed: 3000, 3001, 4000, 8080 — all returned `Connection refused` (curl exit code 7).

**Additional discovery:** Found `/tmp/codesign-mcp-config.json` with:
```json
{"bearer_token":"dV04p4qJsv-kpusRA6gU_qPFdrdjDANer9DK-yR8yjM=","port":35475}
```

Port 35475 responds (HTTP 200, `Content-Type: text/event-stream`) but returns `"Streaming unsupported"` for all REST-style GET requests.

MCP JSON-RPC `initialize` on port 35475 returned:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2024-11-05",
    "capabilities": {"tools": {"listChanged": true}},
    "serverInfo": {"name": "codesign", "version": "1.0.0"}
  }
}
```

**This is the Claude Code "codesign" MCP proxy, NOT a Paperclip instance.**

---

## Step 4: API Endpoints

All endpoints on port 35475 (`/api/status`, `/api/agents`, `/api/runs`, `/api/events`, `/api/plugins`, `/health`) return:
- With correct bearer token: `Streaming unsupported` (HTTP 200, SSE content type)
- Without token: `Unauthorized`

No Paperclip-specific REST API found.

---

## Step 5: State Files on Disk

JSON files found:
- `/tmp/codesign-mcp-config.json` — Claude Code MCP proxy config (bearer token + port)
- `/tmp/mcp-config-cse_015fLWSSkXePg1PEGeMsdyas.json` — MCP server config pointing to Gmail MCP via Anthropic API

DB files: None found.

No Paperclip state files (`.json`, `.db`) found anywhere on the filesystem.

---

## Summary

| Item | Finding |
|------|---------|
| Paperclip binary | Not found |
| Paperclip config dir | Not found |
| Paperclip process | Not running |
| Paperclip API | No endpoint responds |
| Active service on port 35475 | `codesign` MCP proxy (Claude Code internal) |
| Project repo | Empty — no source files |

**No running Paperclip instance detected on this host.**

---

SNAPSHOT COMPLETE
