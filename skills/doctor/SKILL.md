---
name: doctor
description: Diagnose the Reflexivity plugin, MCP server connection, sign-in and entitlement without changing anything. Use when Reflexivity tools are missing, calls fail with UNAUTHORIZED, FORBIDDEN, 401 or 403, the server seems unreachable, or the user asks whether Reflexivity is ready.
---

# Check the Reflexivity Connection

Run a read-only health check for the current coding-agent host. Do not sign in,
reload plugins, edit configuration or call any tool other than
`list_saved_universes`. Report what to do; do not do it.

## Diagnostic workflow

### 1. Inspect the host plugin

Use only the check for the current host:

- Cursor: Cursor Settings > Plugins shows Reflexivity installed and enabled; Cursor Settings > Tools & MCP lists `reflexivity-research`.
- Claude Code: `claude plugin list --json` shows `reflexivity` enabled; `/mcp` lists `reflexivity-research`.
- Codex: the plugin is enabled under `/plugins`; `codex mcp list` shows `reflexivity-research`.

Record the reported plugin version. If the version is current but the visible
tool descriptions look older than this skill, recommend `/reload-plugins` in
Claude Code or a new session in the current host.

### 2. Verify the tool surface

The server should expose exactly seven tools: `list_saved_universes`,
`get_company_relationships`, `get_company_competitors`, `find_theme_companies`,
`get_relationship_evidence`, `search_insights`, `get_insights`. Fewer means the
server is not connected or an older deployment answered; more means another
server is being mistaken for it. Report either as a blocker.

### 3. Verify sign-in and entitlement

Call `list_saved_universes` with no arguments.

| Outcome | Meaning | Next action |
| --- | --- | --- |
| `universes` list plus `coverage` | Signed in and entitled | None. Note the watchlist and basket counts. |
| `UNAUTHORIZED` or HTTP 401 | Not signed in, or the token expired | Run the `setup` workflow |
| `FORBIDDEN` or HTTP 403 | Signed in; account lacks research MCP access | Check research access for the signed-in account |
| Transport error, timeout or HTTP 404 | Endpoint unreachable | Confirm the host can reach `api.reflexivity.com` and `identity.reflexivity.com`; corporate proxies are the usual cause |
| `PARTIAL_UPSTREAM_FAILURE`, or `source_status` not `ok` | Server is up; one upstream is down | Retry later, or pass `partial_ok: true` for the families that answer |

Never request or print tokens, authorization codes or passwords.

## Report

Return a compact table:

| Check | Status | Evidence or next action |
| --- | --- | --- |
| Plugin | Ready, warning or blocked | Installed version and reload guidance |
| Server | Ready or blocked | Tool count out of seven |
| Sign-in | Ready or blocked | Result code of `list_saved_universes` |
| Entitlement | Ready or blocked | Account access requirement when 403 |

Do not report overall readiness when a required check is blocked or unverified.
