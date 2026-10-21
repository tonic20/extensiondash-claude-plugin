---
name: review-performance
description: Use when the user asks how their own tracked browser extension is doing on ExtensionDash, such as users, ratings, keyword positions over a period, which keywords improved or dropped, which competitors are overtaking them, or what to investigate first. Requires an ExtensionDash account connection. Uses list_extensions, get_extension, get_metrics_history, list_keywords, get_keyword_ranks, list_competitors, get_keyword_serp and list_scrape_runs.
---

# Review performance

Explain how the user's tracked extensions moved, using only what ExtensionDash
has recorded. Every tool used here is read-only.

## Prerequisite: an account connection

These tools need an account. If a call answers that "this connection has no
account", stop and follow [connecting an account](references/connecting-an-account.md).
Never ask the user to paste a token into the chat.

## Workflow

1. **Resolve the extension.** Call `list_extensions`. Use its `id` as
   `extension_id` everywhere else.
   - If the user named an extension, match it against `name`. If more than
     one matches, or none does, list what exists and ask.
   - If the user has exactly one extension, use it and say so.
   - `list_extensions` also gives each listing's latest `users_count`,
     `users_rounded`, `users_delta_7d`, `rating_average`, `ratings_count`
     and `last_scraped_at`. That is enough for "how is it doing right now".
2. **Fix the comparison period.** Use the user's period if they gave one,
   or 7 days by default. State the exact dates.
   - `improvement_7d` and `users_delta_7d` are fixed 7-day comparisons.
   - For any other period, pass `days` to the history tools and compare
     the first and last observed dates yourself.
3. **Users and ratings.** Call `get_metrics_history` with `days` (and
   `store` when one store is in question). Each metric is a map of ISO
   date to value.
   - Compare the earliest and latest dates actually present, and name
     them. An empty map means nothing was measured in range. It is not
     zero.
   - If `users_rounded_in_range` is true, the store published rounded
     counts, so small moves may be rounding.
   - With `include_competitors: true` or a `competitors` list, rivals'
     series arrive too. Competitors are sampled every few days, so compare
     them against the user's numbers on dates present in both, never by
     position in the map.
4. **Keyword positions.** Call `list_keywords` for the current state, and
   `get_keyword_ranks` with `days` for movement over the period. Narrow
   with `term`, `store` or `locale` as needed.
5. **Competition.**
   - Call `list_competitors` with `include_observed: true`.
     - `competitors` is the roster the user tracks.
     - `candidates` are other listings seen in the user's recorded
       results, ranked by how often they beat the user.
     - Use `ahead_of_us_on`, `terms_seen`, `terms_tracked`,
       `average_position` and `best_position` as the evidence for
       "who is overtaking me".
   - For one term, call `get_keyword_serp` with `store`, `term` and
     `locale`. It returns the recorded page with `mine`/`competitor` flags
     and `moved_up_by`. Pass `window: true` for just the leaders and the
     user's neighbourhood.
6. **Separate collection problems from performance.** If numbers are
   missing, stale, or a term looks stuck, call `list_scrape_runs` with
   `extension_id` (and `status: "failure"` if useful).
   - A failed fetch is a data-collection problem, not a ranking drop.
     Report it separately.
   - `expected_failure: true`, for example a delisted listing, is not
     signal.
7. **Report** in the format below.

## Reading rankings correctly

Each keyword row and rank point is one (term, store, locale) result. Keep
stores and locales apart. "ad blocker" in Chrome/en, Firefox/en and
Chrome/de are three separate results; never average them.

Keyword rows fall into three states. Keep them distinct:

| State | How it looks | Say |
| --- | --- | --- |
| Never scanned | `status: "pending"`, no `captured_on` | "No result yet; the first fetch has not landed." |
| Scanned and found | `position` is a number | "#7 of the 50 results scanned on 2026-10-17." |
| Scanned but absent | `status: "scanned"`, `position: null` | "Not in the top 13 results scanned." |

Rules for positions:

- **Always give the depth.** Report every position with `depth_scanned`,
  because #1 of 6 and #1 of 50 are different results.
- **Shallow scans.** `shallow: true` means the store returned fewer results
  than the usual scan depth.
- **Absent is not a rank.** Never turn "not found within N" into a rank
  such as N+1 or 51.
- **Stale positions.** A `pending` row may still carry an old position from
  earlier tracking. Check `captured_on` before quoting it.
- **Direction of movement.**
  - `improvement_7d` and `moved_up_by` are positive when the extension
    moved up the page.
  - `improvement_7d` is null when there is no 7-day baseline, or when the
    extension is absent now. Call that "insufficient history", not "no
    change".
- **Search volume.** `search_volume` is Google web-search demand (Google
  Ads), not searches inside the store. It is the same for both stores.
  Null means unknown, not zero.

## Reading user counts correctly

- **Units differ by store.** Chrome users are a rounded store figure.
  Firefox users are AMO's daily-average users. Never add them together,
  rank them against each other, or call them equivalent.
- **Rounding.** `users_rounded: true` means the store rounded the number.
  A move across a rounding boundary (for example 9,000 to 10,000) is not a
  measured gain of 1,000.
- **Rating precision.** Ratings come with their review counts. A 5.0 from
  one review is not evidence of quality.

## Causation

Report what changed and when. Listing edits, new keywords, or a competitor's
update may coincide with a move. Present those as hypotheses to check, never
as the cause, unless the data directly shows it.

## Output

1. **Period.** Exact dates, the stores and locales covered.
2. **Key changes.** The 3–6 biggest movements: users, ratings and keyword
   positions (with depth), each with its numbers.
3. **Competition.** Who is ahead of the user on the most terms, with the
   evidence fields.
4. **Data limitations.** Missing baselines, pending terms, shallow scans,
   rounded counts, failed fetches.
5. **What to investigate first.** A prioritized list of 2–4 concrete
   checks, each tied to evidence above. Suggest tracking changes as an
   offer, never as an action already taken.
