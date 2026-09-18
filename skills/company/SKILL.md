---
name: company
description: Brief a company from Reflexivity's Knowledge Graph - its themes, macro and financial exposures, products, countries and regions, ranked competitors, the evidence behind them and recent published insights. Use when the user asks what a company does or is exposed to, who its competitors or peers are, why two companies are related, or requests a company briefing.
---

# Brief a Company

Use the `reflexivity-research` tools as the source of truth:
`get_company_relationships`, `get_company_competitors`,
`get_relationship_evidence`, `search_insights`, `get_insights`. All are read-only.

## Resolve the company

Pass the user's wording: `securities: [{ "query": "Apple" }]`. Add `exchange`
(`"NASD"`) or `asset_class` (`"stock"`) when the user gave them or when the
first call came back ambiguous. When a previous response already gave a ref,
pass `{ "ref": "entity:<tag>" }` instead; it bypasses resolution.

Read `interpretations` before the results:

- `resolved`: continue with `selected.ref`.
- `ambiguous`: show the `candidates` (label, ticker, exchange) and ask the user to choose. Never guess.
- `not_found`: say so. Do not substitute a similarly named company.

Several companies fit in one call. For a whole watchlist or basket, pass
`saved_universe_type` and `saved_universe_id` from `list_saved_universes`
instead of listing constituents.

## Choose the narrowest workflow

| User need | Calls |
| --- | --- |
| What it does, what it is exposed to | `get_company_relationships`, optionally filtered with `relationship_types` (`theme`, `macro_theme`, `financial_theme`, `product`, `country`, `region`) |
| Competitors, peers, positioning | `get_company_competitors` |
| Why is it related to X, show the evidence | `get_relationship_evidence` with the edge `ref`s, at most 10 per call |
| What is new, catalysts, earnings | `search_insights` with `subjects` (default window: last 30 days), then `get_insights` for the refs worth reading, at most 5 per call |
| Full briefing | All of the above, in that order |

## Compose the briefing

1. One line identifying the company: label, ticker, exchange, ref.
2. Relationships grouped by `type`, in `rank` order (1 = strongest). Mention `exposure_direction` on macro and financial themes and `rank_change` when present. Hydrate evidence only for the edges you quote (the top three to five), not for every edge.
3. Competitors strongest first, with `competitor_of` when several companies were requested.
4. Insights newest first: type, title, `published_at`, sentiment. Hydrate with `get_insights` (`include: ["summary"]` for a skim; omit `include` for everything permitted).
5. Close with `data_as_of` and anything `coverage`, `source_status` or `omitted_refs` reports as missing.

## Conventions

- Refs are canonical and opaque (`entity:<tag>`, `theme:<uuid>`, relationship and insight refs). Pass them back verbatim; never construct or edit one.
- Leave `partial_ok` unset unless the user accepts a partial answer. When set, report every `omitted_refs` entry, every `source_status` that is not `ok`, and the `coverage` counts.
- Keep the server's ordering. Cite refs so the user can ask for evidence.
- Fetch another page (`cursor`, every other parameter identical) only when the user asks or the first page is clearly insufficient.
- Do not invent relationships, competitors or insights when results are sparse.
