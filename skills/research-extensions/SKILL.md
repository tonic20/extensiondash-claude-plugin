---
name: research-extensions
description: Use when the user wants to research a browser-extension niche, find or compare Chrome Web Store extensions or Firefox add-ons, look for market gaps (many users but weak ratings, stale listings, few languages), read a competitor's listing, or brainstorm keywords to investigate. Works without an ExtensionDash account. Uses the ExtensionDash MCP tools search_catalog and get_listing.
---

# Research extensions

Answer market and competitor questions from the ExtensionDash catalog. The
catalog holds a weekly-refreshed copy of Chrome Web Store and Firefox Add-ons
listings, so reading it never fetches the store and never waits.

## Tools

- `search_catalog`: search one store per call. Chrome is the default; pass
  `store: "firefox"` for Firefox.
  - `query`: name words, at most 8. Use `match` (`all` or `any`) to say how
    they combine. Prefix a word with `-` to exclude it.
  - `category`: one category slug of that store.
  - Inclusive ranges: `users_min`/`users_max`, `rating_min`/`rating_max`,
    `reviews_min`/`reviews_max`, `langs_min`/`langs_max`,
    `description_length_min`/`description_length_max`, and
    `screenshots_min`/`screenshots_max`.
  - Sorting: `sort` takes users, rating, reviews, langs, description_length,
    screenshots, updated or added. `dir` is desc or asc.
  - Paging: `limit` (50 at most) and `page`.
- `get_listing`: one listing in full, given `store` and `external_id` (a
  search_catalog row's `id`). Returns the description, users, rating,
  reviews, version, languages, categories, permissions, publisher and
  `refreshed_at`.

No other tool does research. There is no live store search, no store-ranking
lookup for untracked terms, and no search-volume lookup.

## Workflow

1. **Pin down the store.** Default to Chrome. Run Firefox as a separate
   search when the user asks for it, or when the niche plainly matters there.
   Never merge the two stores into one ranking: Chrome users are a rounded
   weekly count, Firefox users an exact daily average.
2. **Resolve names to ids before comparing.** Search by the name the user
   gave.
   - An exact name match comes first unless you pass `sort`.
   - If several rows plausibly match (same or near-same name, different
     publishers or very different user counts), list the candidates with
     their user counts and URLs and ask which one is meant. Do not guess.
   - If nothing matches, say so and try fewer or alternative name words
     before concluding the catalog lacks the extension.
3. **Search the niche.** Describe the niche in name words and run a few
   searches with synonyms (`match: "any"` widens). Pass `sort: "users"` when
   you want the biggest players rather than exact-name matches.
   - Use `category` when the niche maps to one. An unknown slug is refused,
     and the refusal lists the valid slugs.
   - Name words only match names. An extension whose name never uses the
     user's words will not appear, so say the search is name-based.
4. **Filter for opportunity questions.** Translate the question into ranges.
   For example, "many users but weak ratings" becomes `users_min: 10000,
   rating_max: 3.9, reviews_min: 20`. Require a minimum review count so a
   single review does not decide a "weak rating", and state the thresholds
   you picked.
5. **Read the listings that matter** with `get_listing`, usually 2–5 of
   them. Compare promise (summary and description), permissions, languages,
   last update, publisher and the users/rating/reviews figures.
6. **Report** in the format below.

## Reading the data honestly

- **Coverage.** Every search_catalog answer carries `listed`, `known` and
  `synced_on`. Quote them, for example "the Chrome catalog holds 303,293
  listed extensions as of 2026-10-17". Results cover the catalog, not
  necessarily the whole store. `total` counts matches in the catalog only.
- **Nulls.** A null `users`, `rating` or `reviews` means the store showed no
  number. It is not zero; never average it in as zero.
- **Rounding.** Chrome user counts are rounded store figures (1,000, 2,000,
  10,000, ...). Treat two listings at "10,000" as tied, and do not read
  precision into the difference between 1,000 and 2,000.
- **Freshness.** Listing copy is English and refreshed weekly. Quote
  `refreshed_at` or `last_updated_on` when it matters.
- **Facts vs hypotheses.** Label everything inferred, such as "probably
  ranks well because its title contains the term" or "likely abandoned,
  last updated 2021", as a hypothesis. Never invent search volume, revenue,
  installs-per-day, conversion rates or causes.
- **Keyword ideas are suggestions.** Terms you pull from competitors' names
  and summaries are candidates to investigate, not proven demand. Say that
  ranking and volume data only exist once a term is tracked (see the
  manage-tracking skill), and that the catalog has no store-position data
  for terms nobody tracks.
- **Reading is not tracking.** Never imply that searching or reading a
  listing adds it to the user's account. Do not call `add_competitor` or
  `add_keyword` from this skill. Offer it as a next step instead.

## Limits and account errors

- `search_catalog` and `get_listing` allow about 60 calls a minute, with or
  without an account. Budget calls; do not page through hundreds of results.
- If a call reports a per-minute limit, stop and tell the user. Do not retry in a loop.
- Rows carry `yours` and `competitor` flags only when the connection has an
  account.

## Output

Keep it concise:

1. **Answer.** The short answer to the question.
2. **Comparison table.** Name (linked to its store `url`), store, users,
   rating (with review count), languages, last update, and one notable
   observation each.
3. **Evidence.** What you searched (store, words, filters, sort) and the
   coverage figures.
4. **Limitations.** Name-based matching, catalog coverage, rounding, nulls,
   and anything that could not be resolved.
5. **Next steps.** Concrete and optional. For example: read a specific
   listing, add a listing as a competitor, or track a candidate term to get
   real rankings. Ask before doing anything that writes to the account.
