# Agiloft

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Agiloft is a Redwood City, California software company that sells an enterprise Contract Lifecycle Management (CLM) platform built on a no-code business process engine, used by legal, procurement, finance and IT teams to request, author, negotiate, approve, execute, store and report on agreements. Every deployment is a customer-specific knowledgebase (KB) whose tables and fields are configured per tenant, so the API surface is generated from that live configuration rather than published as one fixed catalog contract. Agiloft exposes three machine interfaces on each KB: an OpenAPI/Swagger interface generated from the tenant's own tables (browsable and downloadable inside the KB at Setup > System > View REST documentation), a stable operation-style REST interface under /ewws/ covering create, read, update, delete, upsert, select, search, attachment, lock, table introspection and action-button execution, and a legacy SOAP interface whose WSDL is likewise generated per knowledgebase. Authorization is OAuth 2.0 (authorization code, authorization code with PKCE, and client credentials) or JWT bearer tokens, with a REST operation scope list and group permissions layered on top. Agiloft also ships an outbound webhook service with URL verification and retry, and SCIM 2.0 user provisioning at /scim/v2.

**Areas:** Contract Lifecycle Management · Contract Management · Legal · Procurement · Enterprise Software · No-Code · Workflow Automation · Document Automation · Webhook · SCIM · Company

## Agiloft on APIs.io

Everything profiled for Agiloft is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/agiloft/](https://apis.io/providers/agiloft/)** — or go straight to one of the APIs or properties below.

### APIs (3)

- [Agiloft CLM REST API](https://apis.io/apis/agiloft/rest/) — The Agiloft REST interface, served from every Agiloft knowledgebase under /ewws/. It exposes twenty documented operations — EWCreate, EWRead, EWUpdate,…
- [Agiloft CLM Webhooks](https://apis.io/apis/agiloft/webhooks/) — Agiloft's outbound event surface. A webhook is registered per table and event type (Create, Update or Delete), optionally filtered by a record-filter…
- [Agiloft CLM SCIM 2.0 Service](https://apis.io/apis/agiloft/scim/) — Agiloft's SCIM 2.0 user provisioning endpoint, served per knowledgebase at /scim/v2 and authenticated with a bearer token generated from the KB's SCIM…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Agiloft Webhooks](https://apis.io/asyncapis/agiloft/agiloft-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [agiloft](https://apis.io/servers/agiloft/agiloft/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Agiloft Authentication](https://apis.io/security/agiloft/agiloft-authentication/)
- [Agiloft Domain Security](https://apis.io/security/agiloft/agiloft-domain-security/)
- [Agiloft Trust Center](https://apis.io/security/agiloft/agiloft-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Agiloft Scopes](https://apis.io/scopes/agiloft/agiloft-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Agiloft Plans Pricing](https://apis.io/plans/agiloft/agiloft-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Agiloft Rate Limits](https://apis.io/rate-limits/agiloft/agiloft-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.agiloft.com/)
- [Documentation](https://help.agiloft.com/)
- [Getting started](https://help.agiloft.com/space/HELP/929595396/OpenAPI%20Interface)
- [API reference](https://help.agiloft.com/space/HELP/43715778/REST%20Interface)
- [Pricing](https://www.agiloft.com/pricing)
- [Sign up](https://www.agiloft.com/lets-talk)
- [Support](https://community.agiloft.com/home)
- [Blog](https://www.agiloft.com/blog)
- [Compliance](https://www.agiloft.com/terms-policies/security)
- [Terms of service](https://www.agiloft.com/terms-of-service)
- [Privacy policy](https://www.agiloft.com/privacy-policy)

## API Evangelist rating

**Kin Score 51.8** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 78.6 |
| Contract quality | 39.0 |
| Contract governance | 18.2 |
| Operational transparency | 23.7 |
| Developer ergonomics | 66.1 |
| Access clarity | 60.5 |

**Agent readiness 32.2** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | yes |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 39.2** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/agiloft/](https://apis.io/providers/agiloft/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Agiloft: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/agiloft/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
