---
name: insights
description: Search and read Reflexivity's published research insights - company and market catalysts, earnings previews and recaps, and scenario forecasts - for companies, themes or a watchlist over a time window. Use when the user asks what is new on a name, about upcoming or past earnings, catalysts, scenarios or forecasts, or for recent research in a period.
---

# Search and Read Insights

Use the `reflexivity-research` tools as the source of truth: `search_insights`,
`get_insights`, `list_saved_universes`. All are read-only.

## Search

Call `search_insights`:

- `types`: `company_catalyst`, `market_catalyst`, `earnings_preview`, `earnings_recap`, `scenario`. Omit for all. Map the user's words: "earnings" is `earnings_preview` plus `earnings_recap`; "catalysts" is `company_catalyst`, plus `market_catalyst` when no company was named; "scenario" or "forecast" is `scenario`.
- `subjects`: securities and themes as `{ "query": ... }` or `{ "ref": ... }`. Subjects are a union: a company plus a theme returns insights about either, not their intersection.
- A watchlist or basket: `saved_universe_type` and `saved_universe_id` from `list_saved_universes`.
- `from` and `to`: RFC3339 or `YYYY-MM-DD` (whole days, UTC). Default is the last 30 days. Set `from` for "this quarter", "since the last print" and similar. Echo the returned `window` in the answer.
- Scenario insights are security-scoped. A theme subject in a scenario search yields `source_status` `partial`; report it rather than implying theme scenarios were searched.

Read `interpretations` first. `ambiguous` returns `candidates`: show them and ask; never guess. `not_found`: say so.

## Hydrate

Call `get_insights` with the refs worth reading, at most 5 per call.

- `include`: `summary`, `sections`, `statistics`. Omit for everything permitted; use `["summary"]` for a skim.
- Catalyst and earnings insights return `sections` (`hero`, `takeaway`, `quotes`, `graph`, `details_grid`, `geo`). Quote the takeaway and hero content; keep numbers as returned.
- Scenario insights return `scenario` with `best_horizon` and `forecasts` (date, median, low, high) under the `statistics` group.
- `omitted_field_groups` names each group dropped and why (`response_budget`, `rights_restricted`). Report it; never paraphrase around missing content.

## Report

1. Newest first, as returned: type, title, `published_at`, sentiment, subject label, ref.
2. Group by subject when several were requested; group by type when one subject was.
3. Close with the `window`, `data_as_of`, and anything `coverage`, `source_status` or `omitted_refs` reports as missing.

## Conventions

- Refs are canonical and opaque. Pass them back verbatim; never construct or edit one.
- Leave `partial_ok` unset unless the user accepts a partial answer. When set, report every `omitted_refs` entry, every `source_status` that is not `ok`, and the `coverage` counts.
- Fetch another page (`cursor`, every other parameter identical) only when the user asks or the first page is clearly insufficient.
- Do not invent insights, dates or figures when results are sparse.
