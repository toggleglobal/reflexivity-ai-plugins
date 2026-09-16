---
name: theme
description: Find the companies exposed to a theme, trend or topic in Reflexivity's Knowledge Graph, or screen a watchlist or basket against it. Use when the user asks which companies are exposed to a theme (electric vehicles, GLP-1, data centres), who the players in a trend are, or which names in their list are linked to a topic.
---

# Find Companies Exposed to a Theme

Use the `reflexivity-research` tools as the source of truth:
`find_theme_companies`, `list_saved_universes`, `get_relationship_evidence`,
`search_insights`. All are read-only.

## Resolve the theme

Pass the user's wording: `theme: { "query": "electric vehicles" }`. When a
previous response gave a theme ref (a relationship target or an
interpretation), pass `theme: { "ref": "theme:<uuid>" }`. Theme ids are UUIDs,
never names or slugs; do not construct one.

Read `interpretations` before the results:

- `resolved`: continue. The `score` shows how confident the match was; mention it when it is close to the threshold.
- `ambiguous`: show the `candidates` with their labels and scores and ask the user to choose. A query that is a theme's exact name always resolves, so suggest the exact name when the user knows it.
- `not_found`: say so. Do not pick a neighbouring theme.

## Choose the scope

- Whole market: no filter.
- The user's watchlist or a shared basket: call `list_saved_universes` (metadata only: type, id, name, count), pick the one the user named, and pass `saved_universe_type` and `saved_universe_id`. If the user names one that is not listed, say so instead of guessing.
- Specific companies: `securities: [{ "query": "..." }, ...]`.

## Report

1. Companies in `rank` order (1 = strongest): label, ticker, ref, and the relationship `ref` for evidence.
2. For a screen, state how many of the universe's `count` members came back. Members not returned are not linked to the theme in the Knowledge Graph; do not describe them as unexposed with certainty.
3. When the user asks why, hydrate the top edges with `get_relationship_evidence` (at most 10 refs per call) and quote `description` and `source_evidence`.
4. For what is new on the theme, call `search_insights` with `subjects: [{ "ref": "theme:<uuid>" }]`.
5. Close with `data_as_of` and anything `coverage` or `source_status` reports as missing.

## Conventions

- Refs are canonical and opaque. Pass them back verbatim; never construct or edit one.
- Leave `partial_ok` unset unless the user accepts a partial answer. When set, report every `omitted_refs` entry, every `source_status` that is not `ok`, and the `coverage` counts.
- Keep the server's ordering. Cite refs so the user can ask for evidence.
- Fetch another page (`cursor`, every other parameter identical) only when the user asks or the first page is clearly insufficient.
- Do not invent exposures when results are sparse.
