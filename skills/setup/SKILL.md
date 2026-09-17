---
name: setup
description: Connect and sign in to the Reflexivity research MCP server for the current coding agent. Use when the user asks to install or connect Reflexivity, sign in, fix an UNAUTHORIZED or 401 error, or verify that the Reflexivity tools are available.
---

# Connect Reflexivity

The plugin bundles the `reflexivity-research` MCP server
(`https://api.reflexivity.com/external-research-mcp/mcp`). Sign-in is OAuth 2.1
in the browser; the host registers itself as a client and stores the token.
There is no CLI: every workflow uses the server's tools.

## Critical rules

1. Never ask the user to paste access tokens, authorization codes or passwords into chat.
2. Do not edit the host's MCP configuration by hand; the plugin owns it.
3. Do not call any tool other than `list_saved_universes` during setup.

## Setup workflow

Complete these steps in order.

### 1. Confirm the server is loaded

The `reflexivity-research` server exposes exactly seven tools:
`list_saved_universes`, `get_company_relationships`, `get_company_competitors`,
`find_theme_companies`, `get_relationship_evidence`, `search_insights`,
`get_insights`.

If none are visible, the plugin is installed but the server is not running.
Use only the instructions for the current host:

- Cursor: Cursor Settings > Tools & MCP, enable `reflexivity-research`.
- Claude Code: run `/reload-plugins`, then `/mcp` and confirm the server is listed.
- Codex: confirm the plugin is enabled under `/plugins`, then run `codex mcp list`.

### 2. Sign in

When the server reports that it needs authentication:

- Cursor: Cursor Settings > Tools & MCP, click Login next to `reflexivity-research`.
- Claude Code: run `/mcp`, select `reflexivity-research`, choose Authenticate.
- Codex: run `codex mcp login reflexivity-research`.

A browser opens on `identity.reflexivity.com`. Let the user sign in with their
Reflexivity account there. If the environment cannot open a browser, tell the
user to complete the flow from a machine that can; do not relay codes through chat.

### 3. Verify

Call `list_saved_universes` with no arguments. It is read-only and returns
metadata only.

- A `universes` list (possibly empty) with a `coverage` block: sign-in works and the account is entitled.
- `UNAUTHORIZED` or HTTP 401: sign-in did not complete or the token expired. Repeat step 2.
- `FORBIDDEN` or HTTP 403: signed in, but the account does not have research MCP access. Tell the user to check research access for the account they signed in with.
- A transport error or HTTP 404: the endpoint is unreachable. Report the URL and that the host must reach `api.reflexivity.com` and `identity.reflexivity.com`.

## Completion report

Report:

- host and whether the seven tools are visible
- sign-in: confirmed or blocked
- `list_saved_universes`: confirmed with the number of watchlists and baskets, or the error code
