---
name: manage-tracking
description: Use when the user wants to see or change what ExtensionDash tracks for one of their extensions, such as adding or removing tracked keywords (search terms) or competitor listings, or showing the current tracking setup. Requires an ExtensionDash account connection. Uses list_extensions, list_keywords, list_competitors, search_catalog, add_keyword, remove_keyword, add_competitor, remove_competitor and list_scrape_runs.
---

# Manage tracking

Show and change the keywords and competitors tracked for one of the user's
extensions.

## What the server supports

There are exactly four write tools: `add_keyword`, `remove_keyword`,
`add_competitor` and `remove_competitor`. Everything else is read-only.

Nothing here can:

- add or remove a tracked extension of the user's own;
- change plans, notes or settings;
- delete recorded history.

If the user asks for one of those, say it is done on the dashboard at
https://extensiondash.com. Do not attempt a workaround.

These tools need an account. If a call answers that "this connection has no
account", follow
[connecting an account](../review-performance/references/connecting-an-account.md).
Never ask for a token in the chat.

## Resolve before writing

1. **Extension.** Call `list_extensions` and match the user's wording to
   `name`.
   - If exactly one extension exists, use it and say which.
   - If the name is ambiguous or missing and there are several, ask.
     Never write to a guessed extension.
2. **Keyword scope.**
   - A keyword is a (term, locale) pair. `locale` defaults to `en`.
   - `add_keyword` adds the term in every store the extension is listed in;
     the response's `stores` says which.
   - `remove_keyword` removes the (term, locale) from every store.
   - If the user names a language or market ("track it in German"), pass
     that locale, such as `de` or `pt-BR`.
3. **Competitor identity.**
   - A competitor is a (store, external_id) pair. The id is Chrome's
     32-letter id or Firefox's AMO slug.
   - If the user gives a name, resolve it with `search_catalog` (one store
     per call).
   - If several listings plausibly match, show them with users and URL and
     ask.
   - Each store has its own roster, so a Chrome and a Firefox version of
     the same product are two competitors.
4. **Check what already exists.**
   - Call `list_keywords` before adding keywords. If a (term, locale) is
     already tracked, report it as already tracked instead of re-adding.
   - Call `list_competitors` before adding competitors. Skip ones already
     on the roster.
   - Terms are lowercased and whitespace-squished by the server, so compare
     normalized forms.

## Executing changes

- **Clear requests.** When the request names the extension (or only one
  exists) and the items, make the changes without asking for confirmation
  again.
- **Ambiguous requests.** When the target or scope is ambiguous, ask once,
  listing the specific choices.
- **Never write unasked.** Do not add a keyword or competitor the user did
  not ask for, including ideas that came up during research. Offer them;
  wait for a yes.
- **Limits.** If the server answers with a plan limit, stop for that item
  type. The message names the limit and, for competitors, the current
  roster. Report it, and ask what to remove if the user wants room. Do not
  retry.
- **Removals.** Removing a keyword or competitor stops tracking but keeps
  recorded history, and re-adding later restores it. Say so when removing.
- **Wrong-language removal.** If `remove_keyword` says the term was not
  tracked in that locale, its message lists the locales it is tracked in.
  Relay that and ask whether to remove one of them.

## After adding: pending results

A newly added item has no result when the add returns.

- **Keywords.** `add_keyword` returns rows whose `status` is `"pending"`
  until a background rank fetch lands. Then `status` becomes `"scanned"`.
  A pending row may still show an old position from earlier tracking;
  that is not the new result.
- **Competitors.** `add_competitor` returns `status: "pending"` until the
  listing fetch lands. Then it becomes `"scraped"`.

Polling rule:

- Check at most 3 times, roughly 20–30 seconds apart, using
  `list_keywords` or `list_competitors` for the same `extension_id`.
- Between checks, `list_scrape_runs` with `extension_id` shows whether the
  fetch is running, succeeded or failed.
- If it is still pending after that, stop. Tell the user the fetch is
  queued and they can check later with the same request. Do not keep
  polling, and do not promise a time.
- If `list_scrape_runs` shows a failure for the item, report it as a failed
  fetch with its `error_message`, not as a ranking. Do not promise when it
  will succeed.

## Showing the current setup

For "show my tracking", give a table per extension built from `list_keywords`
and `list_competitors`:

- **Keywords:** term, locale, store, status, position with `depth_scanned`.
- **Competitors:** name, store, users, rating, status.
- The plan limit from `list_competitors`' `limit_per_store` (null means no
  limit).
- Any pending or failed items.

## Report

End every change with a short ledger:

- **Added:** each item and the stores it landed in.
- **Already tracked:** skipped, no change.
- **Removed:** each item.
- **Failed:** the item and the server's reason, for example a limit.
- **Pending:** what is still fetching and how the user can check later.
