---
name: Create, update and delete Agiloft records safely
description: >-
  Write to an Agiloft CLM knowledgebase without creating duplicates or performing an irreversible
  delete. Covers the one idempotent write Agiloft ships (EWUpsert), the delete-dependency rules,
  and why EWRead-before-EWUpdate is mandatory.
api: https://help.agiloft.com/space/HELP/43715778/REST%20Interface
operations: [EWCreate, EWUpdate, EWUpsert, EWDelete, EWLock, EWAsyncStatus, EWRead]
generated: '2026-09-12'
method: generated
source: https://help.agiloft.com/space/HELP/763691019/REST%20-%20Upsert
---

# Write to Agiloft safely

Authenticate and run `EWTable` first — see `agiloft-authenticate-and-discover.md`. Every write
below is `application/x-www-form-urlencoded` and commits immediately: Agiloft is autocommit, one
transaction per call, with no dry-run and no batch.

## Prefer EWUpsert over EWCreate

**EWUpsert is the only Agiloft write with replay protection.** It takes a `$query` match
expression and creates the record if nothing matches, updates it if something does. Replaying the
same request converges on the same record. Agiloft positions it for exactly this: integrations and
data synchronization.

```
POST https://{hostname}/ewws/EWUpsert
Authorization: Bearer {access_token}
Content-Type: application/x-www-form-urlencoded

$KB={kb}&$table={table}&$query=external_id='SF-001234'&$lang=en&<field>=<value>...
```

Match on a field whose value is unique. If no single field is unique, combine them:
`query=company_name='Acme'&&contract_type='NDA'`. On update, only the fields you supply change.

If you must use `EWCreate`, understand that **it has no idempotency key**. There is no
`Idempotency-Key` header anywhere in Agiloft. A retried `EWCreate` after a timeout creates a second
record. Before retrying, `EWSearch` for the record you may already have made.

## Updating

```
POST https://{hostname}/ewws/EWUpdate?$KB={kb}&$table={table}&id={id}&$lang=en&<field>=<value>
```

`EWUpdate` does not return the prior values and **Agiloft documents no revert operation**. If the
change needs to be reversible, `EWRead` the record and keep the response before you write.

## Deleting — treat as permanent

```
POST https://{hostname}/ewws/EWDelete?$KB={kb}&$table={table}&id={id}&deleteRule=ERROR_IF_DEPENDANTS
```

**There is no API restore, undelete or recycle-bin operation.** The delete is all-or-nothing across
the id set: if any one record cannot be deleted, none are.

`deleteRule` decides what happens to *dependent* records before the delete commits, and it is the
whole safety story:

| deleteRule | Behaviour |
|---|---|
| `ERROR_IF_DEPENDANTS` | Fails if there are any dependents. **Default choice for an agent.** |
| `APPLY_DELETE_WHERE_POSSIBLE` | Tries to delete dependents; unlinks where it cannot |
| `DELETE_WHERE_POSSIBLE_OTHERWISE_UNLINK` | Same as above |
| `APPLY_UNLINK` | Tries to unlink dependents |
| `UNLINK_WHERE_POSSIBLE_OTHERWISE_DELETE` | Unlinks where possible, otherwise deletes |
| `REPLACE_WITH_ANOTHER` | Relinks dependents to substitutes named in `subs` |

Never pass a cascading rule on a caller's behalf without explicit confirmation — in a CLM system
the dependents of a contract record are the rest of that agreement.

## Field encoding

- **Choice**: the text value as shown in the GUI (`country=USA`). Ad hoc `EWSelect` queries are the
  exception and need the id from `GetChoiceLineID`.
- **Multi-choice**: repeat the key — `contactMethod=phone&contactMethod=email`.
- **Linked fields**: prefix the lookup value with a colon — `f_group=:Service Manager`.
- **Clearing a linked set**: `$global.null`.
- **Dates**: Agiloft accepts any of 3,275 formats and tries them in order; send something
  unambiguous, like `Aug 24 2021 00:00`.

## Locking

`EWLock` is the one genuinely reversible operation here: `PUT` to lock, `DELETE` to unlock, `GET`
to check. The `GET` response carries `lock_status` (`NO_LOCK` or `LOCKED`), `locked_by` and
`lock_expires_in_minutes` — a countdown of the minutes remaining, not the starting value. An
`EWOperationException` on a write may simply mean another user or session holds the lock; check
before assuming a permission problem.

## Asynchronous writes

`EWCreate`, `EWUpdate`, `EWDelete` and `EWUpsert` accept `$async` and are compatible with
`EWAsyncStatus`. Poll `GET /ewws/EWAsyncStatus` for completion — and remember every call still
costs the `WSDelay` second.

## Action buttons

`POST /ewws/EWActionButton` runs a configured action button on a record. Its blast radius is
whatever that KB's administrator wired to it — email, e-signature dispatch, state transitions — and
is not knowable from the API contract. Confirm with a human before calling one you have not seen
run.
