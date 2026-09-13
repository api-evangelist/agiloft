---
name: Authenticate against an Agiloft knowledgebase and discover its schema
description: >-
  Obtain a bearer token for an Agiloft CLM knowledgebase and find out what tables and fields it
  actually has, before attempting any read or write. This is the mandatory first skill for Agiloft
  because there is no fixed public schema — every KB is configured differently.
api: https://help.agiloft.com/space/HELP/43715778/REST%20Interface
operations: [EWLogin, EWTable, EWLogout]
generated: '2026-09-12'
method: generated
source: https://help.agiloft.com/space/HELP/43715778/REST%20Interface
---

# Authenticate and discover an Agiloft knowledgebase

## Before you start

You need four things and Agiloft will not give you any of them: the **hostname** of the
knowledgebase, the **KB name** (shown in the KB's top right corner next to the Help icon), a
**login and password** for a user who is in a group listed under *Setup > System > Manage Web
Services > Groups allowed for REST*, and the two-letter **language code** the KB uses.

There is no public Agiloft endpoint. Every base URL in this skill is `https://{hostname}/ewws`
where `{hostname}` is that customer's own Agiloft host.

## 1. Get a token

```
POST https://{hostname}/ewws/EWLogin?$login={login}&$password={password}&$KB={kb}&$lang=en
```

The response is JSON:

```json
{"access_token":"...","refresh_token":"...","expiration_time_unit":"minute","expires_in":15,"authentication_scheme":"Bearer "}
```

Use `authentication_scheme` + `access_token` as the `Authorization` header on every later call.
Do not hardcode `Bearer` — read the scheme the server returned.

**The token expires in 15 minutes by default** (an administrator can set `token_expires_in` up to
60). Refresh rather than re-authenticating:

```
POST https://{hostname}/ewws/EWLogin?$KB={kb}&$lang=en&refresh_token={refresh_token}
Authorization: Bearer {access_token}
```

If the KB is configured for OAuth 2.0 instead, use `/ewws/oauth` and `/ewws/otoken` with the
`permissions_for:{CONTACT_ID}` scope — see `authentication/agiloft-authentication.yml`.

## 2. Discover the schema — do not assume it

```
GET https://{hostname}/ewws/EWTable?$KB={kb}&$lang=en
Authorization: Bearer {access_token}
```

`EWTable` returns every table and field in the system. **Call it first and bind to what it
returns.** Agiloft is a no-code platform: tables and fields are configured per customer, so a
table name that worked in one KB may not exist in another. Address tables by their **Logical Table
Name** (case sensitive, dotted for subtables — `contacts.employees`) and fields by their **logical
field name**, never by the label shown in the GUI.

If you have KB credentials in a browser, the KB's own generated OpenAPI and Swagger UI are at
*Setup > System > View REST documentation*, with a **Download Open API JSON** action. That
document is the authoritative contract for that one knowledgebase.

## 3. Prefer JSON

Add the `.json` decorator or you will get JavaScript assignments back:

```
GET https://{hostname}/ewws/EWRead/.json?$KB={kb}&$table={table}&id={id}&$lang=en
```

gives `{"success":true,"message":"","result":{...}}` instead of `EWREST_company_name='Agiloft';`.

## 4. Log out when done

```
GET https://{hostname}/ewws/EWLogout
Authorization: Bearer {access_token}
```

## Rules that apply to every Agiloft call

- **Pace yourself.** There are no rate limits and no `Retry-After` header, but Agiloft inserts a
  delay after every operation — one second by default, set by the `WSDelay` global variable. Treat
  ~1 call/second as the ceiling and do not parallelise aggressively.
- **403 has two causes.** Either the user lacks record permissions, or their group is not in the
  REST allow-list. Check both; the error does not distinguish them.
- **Permissions silently shape results.** Fields the user cannot read come back empty rather than
  erroring, and a record read successfully may still be unwritable.
- Bulk work does not belong on this API. Agiloft says so explicitly and points at its import and
  environment-promotion tooling instead.
