# Reflexivity AI Plugin

This repository packages the Reflexivity research MCP server and its workflows
for Claude Code, Codex, and Cursor.

## Repository structure

- `skills/` contains the canonical workflow instructions. Each skill's folder name equals its frontmatter `name`.
- `plugin.json` and `mcp.json` are the portable Agent Plugins manifest and MCP config that Codex loads; OpenAI presentation lives under `extensions.com.openai`.
- `.agents/plugins/marketplace.json` exposes the repository as a Codex repo marketplace.
- `.cursor-plugin/` holds the Cursor manifest, its MCP config and the Cursor marketplace manifest.
- `.claude-plugin/` holds the Claude Code manifest, its MCP config and the Claude Code marketplace manifest.

## Design rules

- One native manifest per host. Do not make one host read another host's manifest or MCP config: Cursor and Claude Code point `mcpServers` at their own file, Codex reads the root `mcp.json`.
- The server URL `https://api.reflexivity.com/external-research-mcp/mcp` appears in `mcp.json`, `.cursor-plugin/mcp.json` and `.claude-plugin/mcp.json`. Change all three together.
- Keep the plugin identifier `reflexivity` so Claude Code skills use the `/reflexivity:<workflow>` namespace.
- Keep `doctor` read-only, and keep `setup` limited to `list_saved_universes`.
- Write skills from the server's tool schemas, not from memory: tool names, argument names, enum values and per-call limits (10 refs for evidence, 5 for insights) must match the deployed server.
- Every tool is read-only. Do not add workflows that imply the plugin can change Reflexivity data.
- Do not add a CLI wrapper, a local MCP proxy or a client secret. Sign-in is the host's OAuth flow against `identity.reflexivity.com` using dynamic client registration.
- Do not publish the bundled skills as standalone packages.
- Do not add release automation unless a distribution channel is explicitly planned.

## Validation

```bash
# Claude Code: marketplace manifest
claude plugin validate --strict .

# Claude Code: plugin manifest and skills (the marketplace manifest shadows it in-place)
tmp=$(mktemp -d) && mkdir -p "$tmp/.claude-plugin" \
  && cp .claude-plugin/plugin.json .claude-plugin/mcp.json "$tmp/.claude-plugin/" \
  && cp -R skills "$tmp/" && claude plugin validate --strict "$tmp"

# Codex: portable manifests against the Agent Plugins schemas
curl -sSo /tmp/ap-plugin.schema.json https://agent-plugins.org/schemas/1.0.0/plugin.schema.json
curl -sSo /tmp/ap-mcp.schema.json https://agent-plugins.org/schemas/1.0.0/mcp.schema.json
uv run --with 'jsonschema>=4' python3 -c '
import json, jsonschema
for inst, schema in (("plugin.json", "/tmp/ap-plugin.schema.json"), ("mcp.json", "/tmp/ap-mcp.schema.json")):
    jsonschema.Draft202012Validator(json.load(open(schema))).validate(json.load(open(inst)))
    print(inst, "OK")'

# Cursor: load the plugin locally
agent --plugin-dir .
```

## Testing against staging

The production endpoint requires a production Reflexivity account. To exercise
the workflows against staging, copy the repository, replace the URL in the
three MCP configs with `https://api.staging.rflx.co.uk/external-research-mcp/mcp`,
and load that copy locally (`~/.cursor/plugins/local/reflexivity/`,
`claude --plugin-dir <copy>`, or a Codex personal marketplace). Never commit
the staging URL.
