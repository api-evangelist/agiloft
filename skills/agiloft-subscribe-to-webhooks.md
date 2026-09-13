---
name: Subscribe to Agiloft record events with webhooks
description: >-
  Register an Agiloft CLM webhook over REST so a service receives HTTPS notifications when records
  are created, updated or deleted, including the Verification-Code handshake the receiver must
  implement before registration will succeed.
api: https://help.agiloft.com/space/HELP/43714342/Webhooks
operations: [webhooks]
generated: '2026-09-12'
method: generated
source: https://help.agiloft.com/space/HELP/43714342/Webhooks
---

# Subscribe to Agiloft record events

Agiloft pushes an HTTPS POST to a URL you control whenever a record is created, updated or deleted
in a table you name. Registration needs the same permissions that grant access to *Setup >
Integration > Webhooks Setup* — typically admin.

## 1. Build the receiver first — registration will fail without it

Before Agiloft will register a subscription it sends a **GET** to your URL carrying a
`Verification-Code` header. You must answer `2XX` and return that exact value either:

- in a `Verification-Code` **response header**, or
- as the value of a `Verification-Code` key in a **JSON response body**.

This is not one-time. Unless `webhook_confirmation` is `No`, **every notification** carries
`Verification-Code` too and is only counted as delivered if you answer the same way. Your URL must
be publicly reachable over HTTPS and not firewalled.

Use the handshake defensively: if the code in a request is not one you issued, refuse it. Agiloft
will then decline to register that URL.

## 2. Register

```
POST https://{hostname}/ewws/webhooks
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "title": "Contract updates to billing",
  "description": "Fires when a contract's status changes",
  "webhook_url": "https://example.com/hooks/agiloft",
  "webhook_status": "Active",
  "table_name": "{logical_table_name}",
  "event_type": "Update",
  "modification_type": ["API", "Web"],
  "entry_fields": ["id", "contract_title0", "company_name"],
  "record_filter": "company_name=Agiloft || use_as_reference=No",
  "user_permissions": "{login}",
  "webhook_confirmation": "Yes",
  "state_key": "{random-per-subscription-secret}",
  "by_rule": "No"
}
```

Success is **201** with `{"id": ..., "webhook_key": "..."}` and a `Location` header naming the new
webhook resource. Keep the `webhook_key` — later calls need it.

Required: `title`, `description`, `webhook_url`, `webhook_status`, `table_name`, `event_type`,
`user_permissions`.

Key details that bite:

- **`user_permissions` decides what you receive.** The webhook runs as that user, so fields they
  cannot see are simply absent from the payload.
- **`entry_fields` takes field NAMES, not labels**, comma delimited. Only these types serialize
  into a webhook body: Append Only Text, Calculated Result, Choice, Currency, Date, Date/Time,
  Email, Floating Point, Integer, Long Integer, Multi Choice, Percentage, Short Text,
  Telephone/Fax, Text, Time, URL, and Linked fields convertible to text. Anything else must be
  fetched with a follow-up `EWRead`.
- **`state_key` is your CSRF defence** and comes back to you as a header. Max 255 characters. Use a
  distinct random value per subscription.
- **`record_filter`** uses the `EWSearch` expression grammar (`==`, `!=`, `&&`, `||`, `<`, `<=`,
  `>`, `>=`) but saved searches are not accepted.
- **`modification_type`** narrows the trigger to `Email`, `Web` or `API` edits. Leave it empty to
  fire on all. Set it to exclude `API` if you need to avoid a feedback loop with your own writes.
- **Do not use underscores in webhook headers.**

## 3. Manage

- `GET /ewws/webhooks?wh_key={key}` — one subscription
- `GET /ewws/webhooks?byStatus=Active&byEventType=Update&byTable={table}&byField={field}` — list
  (all filters optional, values URL-encoded)
- `PUT /ewws/webhooks` — update
- `DELETE` on the resource URL from the `Location` header — remove

**Always disable before deleting.** Removing an entry without disabling it leaves the webhook live
until the next KB restart.

## 4. Expect retries

A delivery Agiloft does not consider successful is retried **five times**: at 10 seconds, 30
seconds, 5 minutes, 15 minutes and 40 minutes. Make your handler idempotent — key on the record id
plus event type — because you can and will see the same event more than once.

## Errors

`400 INVALID_URL` almost always means the verification handshake failed, not that the URL is
malformed. `400 WEBHOOK_LIMIT_EXCEEDED` means the maximum number of active webhooks for that
events array is reached; Agiloft does not publish the number. Full list in
`errors/agiloft-error-codes.yml`.
