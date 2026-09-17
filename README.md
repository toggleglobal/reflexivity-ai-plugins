# Reflexivity

**Thematic, company, and market research**

Understand company exposures and competitive relationships through Reflexivity’s Knowledge Graph. Find companies connected to a theme, explore supporting evidence, and read published research on earnings, catalysts and scenarios. Focus your research using saved watchlists and baskets.

Use [Reflexivity](https://reflexivity.com) in Claude Code, Codex, and Cursor.
You need a Reflexivity account with research MCP access. The integration is
read-only: it cannot change your account, watchlists, or other Reflexivity data.

## Try a question

- Explain NVIDIA’s main exposures and competitors.
- Which companies are exposed to electric vehicles?
- Summarize recent earnings for my watchlist.

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

Open `/plugins`, install Reflexivity, start a new task, and ask:

```text
Set up Reflexivity for me.
```

### Cursor

Register the [toggleglobal/reflexivity-ai-plugins](https://github.com/toggleglobal/reflexivity-ai-plugins)
repository in your Cursor team marketplace, then install Reflexivity from
**Cursor Settings → Plugins** and sign in from **Cursor Settings → Tools & MCP**.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `company` | Brief a company: exposures, competitors, evidence, recent insights |
| `theme` | Find companies exposed to a theme, or screen a watchlist against it |
| `insights` | Search and read catalyst, earnings and scenario insights |
| `setup` | Connect, sign in and verify your access |
| `doctor` | Check the connection without changing it |

## Connection and tools

The workflows call the `reflexivity-research` server at
`https://api.reflexivity.com/external-research-mcp/mcp` over MCP Streamable
HTTP. Sign-in uses OAuth 2.1 with PKCE through `identity.reflexivity.com`; the
host registers itself as a client and opens the browser for you.

Access requires a Reflexivity account with research MCP access. A signed-in
account without it receives `FORBIDDEN`.

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
candidates rather than guesses. Responses include resolution and coverage
information to help interpret the results.

## Support

For help, contact [support@reflexivity.com](mailto:support@reflexivity.com).
