---
name: Search an Agiloft table and page through results safely
description: >-
  Run a saved search, an ad hoc query, or a natural-language query against an Agiloft CLM table and
  iterate the results, accounting for Agiloft's documented pagination instability.
api: https://help.agiloft.com/space/HELP/43717730/REST%20-%20Search
operations: [EWSearch, EWNLPSearch, EWSavedSearch, EWSelect, GetChoiceLineID]
generated: '2026-09-12'
method: generated
source: https://help.agiloft.com/space/HELP/43717730/REST%20-%20Search
---

# Search an Agiloft table and page through results

Authenticate first — see `agiloft-authenticate-and-discover.md`.

## Ad hoc query

```
GET https://{hostname}/ewws/EWSearch?$KB={kb}&$table={table}&$lang=en
    &query=summary~='test'%26%26priority=3
    &field=id&field=summary&field=priority
Authorization: Bearer {access_token}
```

Each `field` parameter names one **logical field name** to return. If you specify none, Agiloft
returns only the id, type fields and the ownership field for that table.

Operators go in a single `query` parameter, URL-encoded:

| Operator | Encoded | Meaning |
|---|---|---|
| `=` | `%3D` | equals |
| `!=` | `%21%3D` | does not equal |
| `~=` | `%7E%3D` | contains |
| `&&` | `%26%26` | and |
| `\|\|` | `%7C%7C` | or |
| `<` `<=` `>` `>=` | `%3C` `%3C%3D` `%3E` `%3E%3D` | comparisons |

Single-quote values, and single-quote field labels that contain spaces. Match empty fields with
`null`. For a **Choice** field in an ad hoc query you must use the internal id, not the display
text — get it from `GetChoiceLineID` first.

## Saved search

Pass the saved search label as `search=`. `EWSavedSearch` returns the details of a saved search
defined in a table if you need to inspect one before running it.

## Natural language

`EWNLPSearch` takes `nlp_query` (plus `field[]`, `page`, `limit`) and accepts JSON or form
encoding. It cannot take structured filters or a table name — the tables it covers are fixed at
implementation time. It returns the same record shape as `EWSearch`.

## Pagination — read this before writing a loop

`page` starts at **0**. `limit` is the page size; `limit=0` means *all records*, returned on page
0 (any non-zero page number with `limit=0` returns empty).

**Agiloft's pagination is not a stable cursor, and it tells you so.** Only one open query is
allowed per client session, and the query is rebuilt and re-run whenever the table, fields, saved
search, query or limit change between calls. The REST interface opens a new session and logs out
on *every* call, so in practice the query is always rebuilt. Agiloft's own words: results may not
be fully consistent between calls, the dataset may appear to have gaps, and page boundaries may
shift as records change underneath you.

Practical consequences for an agent:

1. Keep `$table`, every `field`, `query`/`search` and `limit` **byte-identical** across pages.
2. Do not treat the per-page record count as a total — when paginating it is the count within the
   current page.
3. Deduplicate by record `id` on the client side, and expect the possibility of a missed record;
   if completeness matters, re-run the whole query rather than trusting a partial walk.
4. Do not run two paged queries in parallel on one session. Agiloft's documented answer is to open
   multiple sessions with the same credentials.
5. Pause about a second between pages — `WSDelay` adds one anyway.

## EWSelect

`EWSelect` is the other read path: limited SQL-style select returning a list of record identifiers
and the length of that list. It is not documented as paginated. Use it when you want ids and then
`EWRead` them individually.
