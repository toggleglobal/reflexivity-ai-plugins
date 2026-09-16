# Reflexivity AI Plugins

Bring [Reflexivity](https://reflexivity.com) research into Claude Code, Codex, and Cursor.

This plugin connects your coding agent to the Reflexivity research MCP server
and adds focused workflows on top of it: brief a company, find the companies
exposed to a theme, search published catalyst, earnings and scenario insights,
and diagnose the connection.

## Highlights

- Connect and sign in to the Reflexivity research MCP server with OAuth; no tokens in chat.
- Run read-only diagnostics to verify plugin, server, sign-in and entitlement.
- Brief a company: themes, macro and financial exposures, products, geographies, ranked competitors and the evidence behind each link.
- Find the companies exposed to a theme, or screen a watchlist or basket against it.
- Search and read published insights: company and market catalysts, earnings previews and recaps, and scenario forecasts.

Every tool is read-only. Nothing the plugin does can change your Reflexivity data.

## Installation

### Claude Code

```bash
claude plugin marketplace add toggleglobal/reflexivity-ai-plugins
claude plugin install reflexivity@reflexivity
```

Start a new session and run:

```text
/reflexivity:setup
```

### Codex

```bash
codex plugin marketplace add toggleglobal/reflexivity-ai-plugins
codex
```

Open `/plugins`, install Reflexivity Research, start a new task, and ask:

```text
Set up Reflexivity Research for me.
```

### Cursor

Register the [toggleglobal/reflexivity-ai-plugins](https://github.com/toggleglobal/reflexivity-ai-plugins)
repository in your Cursor team marketplace, then install Reflexivity Research from
**Cursor Settings → Plugins** and sign in from **Cursor Settings → Tools & MCP**.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `setup` | Connect, sign in and verify the Reflexivity research server |
| `doctor` | Run read-only readiness diagnostics |
| `company` | Brief a company: exposures, competitors, evidence, recent insights |
| `theme` | Find companies exposed to a theme, or screen a watchlist against it |
| `insights` | Search and read catalyst, earnings and scenario insights |

## The Reflexivity research MCP server

The workflows call the `reflexivity-research` server at
`https://api.reflexivity.com/external-research-mcp/mcp` over MCP Streamable
HTTP. Sign-in uses OAuth 2.1 with PKCE through `identity.reflexivity.com`; the
host registers itself as a client and opens the browser for you.

Access requires a Reflexivity account whose plan includes research MCP access
(Plus, Pro or admin). A signed-in account without it receives `FORBIDDEN`.

The server exposes seven read-only tools:

| Tool | Intent |
| --- | --- |
| `list_saved_universes` | Which watchlists and baskets can I reference? |
| `get_company_relationships` | Theme, product, country and region exposures for companies |
| `get_company_competitors` | Competitive sets for companies |
| `find_theme_companies` | Which companies map to this theme? |
| `get_relationship_evidence` | Evidence and provenance for specific relationship edges |
| `search_insights` | Discover insights by subject, type and time window |
| `get_insights` | Read a small set of insights in full |

Free-text names are resolved server-side; ambiguous names come back as
candidates rather than guesses, and every response reports what was requested,
resolved and executed so nothing is dropped silently.
